---
permalink: /2019/04/programming-sanitizing-your-inputs.html
tags: [databases, data access layer, dotnet, C#, linq]
---
# Programming: Sanitizing your inputs

Sometimes the users of our applications manage to enter invalid data. Other times, we create bugs that introduce invalid data. Whatever the case may be of how it was introduced, there are a series of precautionary measures we can use to prevent invalid data from affecting application functionality and performance.  
  
The first line of defense is of course "form validation". Ideally, all user entry mistakes are caught at this stage. Form validation involves configuring rules for your UI architecture (Angular/React/etc) to interpret, or writing your own validation functions. Form validation should always describe the issue to the user so that they can fix their mistake. If it doesn't do this well, then expect to receive many support phone calls and have your manager breathing down your neck about unhappy customers and expensive customer support costs.  
  
The second line of defense is "backend validation". This should include all security focused frontend validation, plus any additional validation the backend can do; The backend has access to more information about the state of the system, such as other data records that can inform further validation of the entered data. Your service architecture should provide a framework for this type of validation, but you may also end up writing your own code if your framework doesn't provide it, or it is not capable of handling certain types of validation, such as cross-referencing other records in the database.  
  
The final line of defense is "data access layer validation". This type of validation occurs right before writing a record or records to the database. It is the lightest and most rudimentary form of validation. The only concern at this layer, is whether fields that are required for properly storing the record are present and valid. The errors caught at this stage are always dev team errors. This is because the earlier validation layers failed to catch a user error, or a developer made some other mistake earlier in the call stack.  
  
You may have noticed that I made no mention of data validation-on-read. This is because you shouldn't do this. You should catch bad data before it reaches your database, or else you can expect a costly customer support incident that requires a developer to fix. Also, fixing data in place is a delicate procedure that may result in further damage to the data in the database.  
  
But don't we want to know about bad data in the database? Yes, we do. However, if you perform data validation-on-read you will prevent your users from being able to use the system or fix the issue themselves. Yes, your users are intelligent humans and might be able to fix the problem entirely on their own, but only if you let them. Also, customer support may be able to fix the issue, but only if they can retrieve the data to update it. Finally, if you have a way to detect the issue on read, then why can't you detect it on write instead? So put that data validation logic before writing to the db so that someone besides a developer can fix the problem when and if it arises.  

> your users are intelligent humans and might be able to fix the problem entirely on their own, but only if you let them.

An example of validation on read that I've seen in C# code is the use of the LINQ methods Single() and First(). Don't use these methods when reading or returning data to the end user. These methods throw exceptions and prevent the data from making it to the end user, such as when your assumption about the data turns out to be wrong. It would be better to send the user incomplete data than no data at all. They will know that there's a problem if some data is missing, and either re-enter it or call customer support to fix the issue. So use (Single/First)OrDefault instead and smooth over any potential null reference issues that might arise from that.  

> It would be better to send the user incomplete data than no data at all.

It is my hope that this article will lead to less hot database fixes, and system downtime. Maybe it will also get software developers thinking a little more in terms of how their users might be able to dig their way out of their own messes, or even perhaps your mess.
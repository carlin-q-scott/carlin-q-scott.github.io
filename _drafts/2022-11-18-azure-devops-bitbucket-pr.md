# Azure DevOps and BitBucket PR

I wanted to trigger my Azure DevOps pipeline when a PR is merged into my main branch while using trigger path filters.

The trigger path filters create a unique challenge because they cause merges to not produce a build, due to the merge not actually changing files. But thankfully we can modify the default BitBucket webhook created by Azure DevOps for building PRs, to include the merge event.

Simply do the following:
1. Open the repo in BitBucket
1. Open **Repository Settings** (on the left)
1. In the **Workflow** section (on the left), click **Webhooks**
1. In the **Repository hooks** section, edit the "web" hook that's already set up for PR events
1. Enable the **merge** event
1. Save

Now the next time you merge a PR, it will trigger a new build.
# Windows Hyper-V DNS Resolution

I've been wondering why I can't reference my guest OS by DNS name, as well as my host OS. At first I thought it was my DNS server, and there was one oddity there, but mostly it was an issue with the "default" virtual switch provided by Hyper-V. Eventually I learned through reading a dozen forum posts, that I needed to use a "public" virtual switch.

## Problem

Reference Guest/Host by DNS name.

## Solution

1. Create a public virtual switch:
    1. Open the Hyper-V Manager
    1. Under Actions on the right, select "Virtual Switch Manager"
    1. You should only see one or two Switches:
       - Default Switch - an internal switch which allows internet access but uses a private subnet for the VMs
       - WSL (optional) - An internal switch which doesn't allow internew access. Use by the Windows Subsystem for Linux 2 (WSL2)
    1. Select "New virutal network switch"
    1. Choose External for the type
    1. Click "Create Virtual Switch"
    1. OK
1. Assign the VM to the public virtual switch:
    1. Select the VM
    1. Settings...
    1. Network Adapter
    1. Virtual switch: External
    1. OK

That's it! You can now make network requests to the host and guest using their hostname.

### Still Didn't Work

If this didn't work for you, then you're going to need to ensure that your network has its own DNS server and that it is set as the primary DNS for your network.

Additionally, the DNS server itself should not be configured to forward local DNS queries. It must handle those itself.
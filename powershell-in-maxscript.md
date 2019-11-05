# Bootstrapping Powershell commands to 3DS Max's Maxscript to send notifications

If you have ever used 3DS Max to render something locally and wanted to get alerted if your render finishes or fails, you'd find out that the email notification option in the Render Setup is very lacking and can only utilize unsecured SMTP mail server.

![](/img/3dsmax_2019-11-05_19-14-07.png)

Not that this is just plain insecure and dangerous in this day an age, it usually doesn't even work as this feature is mostly still included due of legacy reasons.


Now you might have noticed that there's a scripts rollout under the email notifications rollout, but this is only for maxscript scripts.

![](/img/3dsmax_2019-11-05_19-22-54.png)

We can actually still use this for our advantage, as [Maxscript supports execution of external commands](http://help.autodesk.com/view/3DSMAX/2015/ENU/?guid=__files_GUID_846B6AB0_EFF6_43E5_8A67_8D348FF78A57_htm). This allows us to call a command prompt shell, from which we can execute powershell. Personally I'd recommend utilizing the `HiddenDOSCommand()` command with the `donotwait:true` argument to execute the command, as this will return control back to maxscript right as it executes the command. This will prevent 3DS Max from locking up if something breaks in the command or script, but it will leave a phantom shell running under 3DS Max if something breaks. For debugging purposes, I'd heavily recommend using `DOSCommand`.


Yes. We're going 3 shells deep. 


Executing PS code is pretty simple.
```DOSCommand "powershell.exe -command \"<yourcommandshere>\"``` or ```DOSCommand "powershell C:\your-script.ps1"``` for running a script.  
Yes, you can paste the whole script into it and make it work without changing your `Set-ExecutionPolicy` to `RemoteSigned`, but a more elegant solution is to still create a powershell script that maxscript just calls.

### What can you do with this?

At the start I mentioned email notifications, and [Powershell knows how to send email without having to install anything](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/send-mailmessage?view=powershell-6) or add a library. It can also send data to a webhook, as it also [supports REST API](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-6) when its combined with [ConvertTo-Json](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/convertto-json?view=powershell-6) function.


Here's my script in action utilizing Discord webhook:

![](/img/10d775.gif)

I'm utilizing this to send notifications to Discord to notify me when a rendering job has finished and harvest the vray.txt log file to see if the rendering job failed or not and if there were any warnings. This is something I have delved into only recently, so the script is still very much work in progress.

![](/img/Discord_2019-11-05_20-09-59.png)


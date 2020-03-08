# Adding AWS Shell to Windows Terminal

If you are a user of the [Windows Terminal](https://github.com/microsoft/terminal) and you also usually use AWS CLI or AWS Shell, you might be happy to hear that you can directly add it as its own selectable shell.

![](/img/a7750c.gif)

### Installing aws-shell on Windows

You will need to install Python along with pip in order to install the shell on your computer. Starting with Python 3.4, `pip` is included by default with the Python binary installers.  

After you verify that python runs fine, install the shell with the command `pip install aws-shell`. If pip is not found, you will need to add python into your PATH enviroment variables. If you upgraded from an earlier python version, make sure you use the correct PATH variables.  

### Locating the executable

First we need to locate the location of the command. If your PATH variables are configured correctly, open a new Powershell session after installing the shell and try to execute `aws-shell`. If the shell opens fine, close it and run `(Get-command aws-shell).Path`. It should output the location of the executable.

```
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\Users\Samo> (Get-command aws-shell).Path
C:\Users\Samo\AppData\Local\Programs\Python\Python38-32\Scripts\aws-shell.exe
```

### Adding it into your Windows Terminal profile

Select settings from the dropdown menu to open your profile schema and paste this into the profiles section. Replace the executable path to match yours.  

Please keep in mind that you need to escape special JSON characters, which includes a backslash. You need to escape a single backslash with double backslash.


```
        {
            "name": "AWS Shell",
            "commandline": "C:\\Users\\Samo\\AppData\\Local\\Programs\\Python\\Python38-32\\Scripts\\aws-shell.exe"
        },
```

If you wish to make it look more pretty, you can customize it by adding a custom font and icons. For example, I have added the AWS favicon as an icon and use Ubuntu font:

```
        {
            "name": "AWS Shell",
            "commandline": "C:\\Users\\Samo\\AppData\\Local\\Programs\\Python\\Python38-32\\Scripts\\aws-shell.exe",
            "icon" : "C:\\Users\\Samo\\AppData\\Local\\Programs\\Python\\Python38-32\\Scripts\\aws-logo.ico",
            "acrylicOpacity" : 0.85,
            "fontFace": "UbuntuMono NF"
        }
```

After you have finished setting this up, you should [follow the instructions on the aws-shell github how to configure it](https://github.com/awslabs/aws-shell).

Due of me not using any AWS services at this time, I'm unfortunately unable to show it off in fully in action.
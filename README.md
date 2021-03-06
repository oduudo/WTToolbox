# WTToolBox

[![PSGallery Version](https://img.shields.io/powershellgallery/v/WTToolbox.png?style=for-the-badge&logo=powershell&label=PowerShell%20Gallery)](https://www.powershellgallery.com/packages/WTToolBox/) [![PSGallery Downloads](https://img.shields.io/powershellgallery/dt/WTToolBox.png?style=for-the-badge&label=Downloads)](https://www.powershellgallery.com/packages/WTToolBox/)

![WindowsTerminal](assets/wt-icon.png)

## Windows Terminal Toolbox

A set of PowerShell functions for managing and working with the [Windows Terminal](https://www.microsoft.com/store/productId/9N0DX20HK701) application from Microsoft. You can download the module from the PowerShell Gallery. This module should work on __Windows__ platforms under Windows PowerShell and PowerShell 7.

```powershell
Install-Module WTToolBox [-scope CurrentUser]
```

Of course, it is assumed you have `Windows Terminal` installed, or you plan to install. If `Windows Terminal` is not installed, you will get a warning message when you import this module.

If you want some background information on Windows Terminal, take a look at [https://devblogs.microsoft.com/commandline/getting-started-with-windows-terminal/](https://devblogs.microsoft.com/commandline/getting-started-with-windows-terminal/).

## Module Commands

* [Backup-WTSetting](docs/Backup-WTSetting.md)
* [Get-WTKeyBinding](docs/Get-WTKeyBinding.md)
* [Get-WTReleaseNote](docs/Get-WTReleaseNote.md)
* [Get-WTProcess](docs/Get-WTProcess.md)
* [Get-WTCurrent](docs/Get-WTCurrent.md)
* [Open-WTDefault](docs/Open-WTDefault.md)
* [Test-WTVersion](docs/Test-WTVersion.md)
* [Get-WTCurrentRelease](docs/Get-WTCurrentRelease.md)
* [Install-WTRelease](docs/Install-WTRelease.md)
* [Test-IsWTPreview](docs/Test-IsWTPreview.md)

### Installing Windows Terminal

You can install the latest stable or preview release of Windows Terminal using the packages available on GitHub.

```powershell
Install-WTRelease [-preview]
```

The `Install-WTRelease` function has an alias of `Install-WindowsTerminal`.

### Displaying Key Bindings

Keeping track of all the possible keyboard shortcuts or keybindings can be difficult. `Get-WTKeyBinding` will go through all defined keybindings and display them.

```powershell
PS C:\> Get-WTKeyBinding

   Source: Defaults

Action               ActionSettings                           Keys
------               --------------                           ----
closeWindow                                                   alt+f4
toggleFullscreen                                              alt+enter
toggleFullscreen                                              f11
toggleFocusMode
toggleAlwaysOnTop
openNewTabDropdown                                            ctrl+shift+space
openSettings                                                  ctrl+,
openSettings         target = defaultsFile                    ctrl+alt+,
find                                                          ctrl+shift+f
toggleRetroEffect
...

Source: Settings

Action               ActionSettings                           Keys
------               --------------                           ----
copy                 singleLine = False                       ctrl+c
paste                                                         ctrl+v
find                                                          ctrl+shift+f
splitPane            split = auto;splitMode = duplicate       alt+shift+d
newTab               profile = Ubuntu                         ctrl+shift+u
splitPane            split = auto;profile = PS7 No            ctrl+shift+>
                     Profile;startingDirectory =
                     C:\;tabTitle = PS7 Clean
splitPane            split = auto;profile = Windows           ctrl+shift+<
                     PowerShell No Profile;startingDirectory
                     = C:\;tabTitle = PS Clean
openTabColorPicker                                            ctrl+/
```

You can also select a specific action:

```poweshell
PS C:\> Get-WTKeyBinding -Action *font* | Format-List

Source: Defaults


Action         : adjustFontSize
ActionSettings : delta = 1
Keys           : ctrl+=

Action         : adjustFontSize
ActionSettings : delta = -1
Keys           : ctrl+-

Action         : resetFontSize
ActionSettings :
Keys           : ctrl+0
```

### Getting Current Settings

Use `Get-WTCurrent` to display the settings for the current PowerShell session in Windows Terminal.

![Get-WTCurrent](assets/wtcurrent.png)

### Tracking Windows Terminal Version

Because `Windows Terminal` can silently update, it may be awkward to know if you are running a new version. You might use the `Test-WTVersion` command in your PowerShell profile script like this:

```powershell
if ( $env:wt_session -AND Test-WTVersion) {
    Write-Host "A newer version of Windows Terminal is now installed." -foreground Yellow
    Start-Process https://github.com/microsoft/terminal/releases
}
```

On a related note, you can also use `Get-WTReleaseNote,` which will get the latest release information from the `Windows Terminal` GitHub repository. If you are running PowerShell 7.x, you can pipe the command to `Show-Markdown`.

```powershell
Get-WTReleaseNote | Show-Markdown -UseBrowser
```

The document will have links to any referenced issues.

You can also use [Get-WTCurrentRelease](docs/Get-WTCurrentRelease.md) to get a quick peek at the latest online version and your locally installed version.

```powershell
PS C:\> Get-WTCurrentRelease


Name                         Version     Released              LocalVersion
----                         -------     --------              ------------
Windows Terminal v1.4.3243.0 v1.4.3243.0 11/20/2020 9:43:33 PM 1.4.3243.0
```

### Windows Terminal Processes

The `Get-WTProcess` command will get all processes associated with your Windows Terminal process. The output is a standard `System.Diagnostics.Process` object, but the default formatting has been customized to highlight the current PowerShell process.

![Get-WTProcess](assets/wtprocess-ansi.png)

`Get-WTProcess` has an alias of `gwtp`.

## Global Variables

To make it easier to see either default settings or your custom settings, when you import this module, it will define 3 global variables. Assuming, of course, that you have `Windows Terminal` installed and are using `settings.json`.

### WTSettingsPath

The path to `settings.json` is buried in your `%AppData%` folder. You can use `$WTSettingsPath` as a placeholder. Yes, you can easily open the file from `Windows Terminal`, but there may be other things you want to do with the path information.

### WTDefaults

You can use `$WTDefaults` as an object to view any number of default settings. Use `Open-WTDefaults` if you want to open the file in your code editor.

```powershell
PS C:>\ $WTDefaults | Select-Object -property initial*

initialCols initialRows
----------- -----------
        120          30
```

When you import the module, it will also create a variable called `$WTDefaultsPath,` which points to the `defaults.json` file. The variable makes it easier if you want to do something with it like make a copy. If you need to view the file, you can use the `Open-WTDefault` command.

> If you have a preview release also installed, this variable will have two objects.

### WTSettings

The last object is a customized version of the data in `settings.json`. `$WTSettings` should make it easier to see your settings.

```powershell
PS C:\> $wtsettings.profiles.list | where-object hidden

guid       : {b453ae62-4e3d-5e58-bget989-0a998ec441b8}
hidden     : True
useAcrylic : False
name       : Azure Cloud Shell
source     : Windows.Terminal.Azure

guid   : {574e775e-4f2a-5b96-ac1e-a2962a402336}
hidden : True
name   : PowerShell
source : Windows.Terminal.PowershellCore

guid   : {6e9fa4d2-a4aa-562d-b1fa-0789dc1f83d7}
hidden : True
name   : Legacy
source : Windows.Terminal.Wsl

guid   : {c6eaf9f4-32a7-5fdc-b5cf-066e8a4b1e40}
hidden : True
name   : Ubuntu-18.04
source : Windows.Terminal.Wsl
```

The object includes a few additional properties.

![wtsettings](assets/wtsettings.png)

The `LastUpdated` value is when `settings.json` was last revised. The `LastRefresh` value indicates when this object was created. Because you might modify your settings, after importing this module, there needed to be a mechanism to refresh the data. The custom object has a `Refresh()` method you can run at any time.

```powershell
PS C:\> $WTSettings.refresh()
```

The method doesn't write anything to the pipeline.

_A quick note on the `settings` and `default` objects. The JSON standard does not recognize comments, yet they are used in `Windows Terminal` settings files. You can see them with leading // characters. To avoid errors when converting the JSON to objects, these comments must be stripped out of the content. The clean-up process is done with a regular expression. PowerShell 7 is more forgiving if it detects comments. Windows PowerShell will refuse to convert the content from JSON. Although the module can handle JSON comments, the recommendation is that if you are using comments, that you insert a space after the leading slashes like this:  `// this is a comment`._

## Known Issues

The Pester tests for this module will most likely fail when run under Pester 5.x. They are designed for Pester version 4.10.1 and need to be revised to meet the latest Pester test format.

## Future Versions

If you have any suggestions for enhancements or bug reports, please use the [Issues](https://github.com/jdhitsolutions/WTToolbox/issues) section of this repository.

Last Updated *2020-12-22 15:12:37Z*

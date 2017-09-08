# Living with VisualStudioMac

I was just trying to use VisualStudio for Mac(2017) for creating macos application, and got in stuck easyly...  

1. StoryBoard doesn't work properly, or may be useless.  
It was easy to decide the way I have to aim, DONT USE IT.  
We can write all UI code in C#, like we sometimes do in Swift or Objective-C language.  
I wrote template code of program startup part so that I (and somebody in stray about same matter) can easyly startup macoses C# project.  

Before using this code, you have to remove StoryBoard file and remove its entry from info.plist.  

```csharp
/**
 * Main.cs
 * Replace your Main.cs with this code, then customize menu part for your application.
 * 
 * The code part creating application menu was originally written by Hoon H, (in Swift language).
 *  (https://github.com/eonil/CocoaProgrammaticHowtoCollection/tree/2a2611cc751a1a7784570efae57e86adf40c29bb/ComponentUsages/ApplicationMenu).
 */
using AppKit;

// Change 'TestCocoa' to your application's name.
namespace TestCocoa
{
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            var app = NSApplication.SharedApplication;
            System.Diagnostics.Debug.WriteLine("test");

            NSApplication.SharedApplication.ServicesMenu = BuildServicesMenu();
            NSApplication.SharedApplication.MainMenu = BuildMainMenu(NSApplication.SharedApplication.ServicesMenu);
            NSApplication.SharedApplication.Delegate = new AppDelegate();
            NSApplication.Main(args);
        }

        static NSMenu BuildMainMenu(NSMenu servicesMenu)
        {
            NSMenu mainMenu = new NSMenu(); // `title` really doesn't matter.
            NSMenuItem mainAppMenuItem = new NSMenuItem("Application", null, ""); // `title` really doesn't matter.
            NSMenuItem mainFileMenuItem = new NSMenuItem("File", null, "");

            mainMenu.AddItem(mainAppMenuItem);
            mainMenu.AddItem(mainFileMenuItem);

            NSMenu appMenu = new NSMenu(); // `title` really doesn't matter.

            mainAppMenuItem.Submenu = appMenu;

            NSMenu appServicesMenu = servicesMenu;

            appMenu.AddItem("About Me", null, "");
            appMenu.AddItem(NSMenuItem.SeparatorItem);
            appMenu.AddItem("Preferences...", null, ",");
            appMenu.AddItem(NSMenuItem.SeparatorItem);
            //
            NSMenuItem hideMeItem = new NSMenuItem("Hide Me", new ObjCRuntime.Selector("hide:"), "h");
            hideMeItem.Target = NSApplication.SharedApplication;
            appMenu.AddItem(hideMeItem);
            //
            NSMenuItem hideOtherItem = new NSMenuItem("Hide Others", new ObjCRuntime.Selector("hideOtherApplications:"), "h");
            hideOtherItem.KeyEquivalentModifierMask = NSEventModifierMask.CommandKeyMask/* .command */ | NSEventModifierMask.AlternateKeyMask/* .option */;
            hideOtherItem.Target = NSApplication.SharedApplication;
            appMenu.AddItem(hideOtherItem);
            //
            NSMenuItem showAllItem = new NSMenuItem("Show All", new ObjCRuntime.Selector("unhideAllApplications:"), "");
            showAllItem.Target = NSApplication.SharedApplication;
            appMenu.AddItem(showAllItem);
            //
            appMenu.AddItem(NSMenuItem.SeparatorItem);
            appMenu.AddItem("Services", null, "").Submenu = appServicesMenu;
            appMenu.AddItem(NSMenuItem.SeparatorItem);
            //
            NSMenuItem quitMeItem = new NSMenuItem("Quit Me", new ObjCRuntime.Selector("terminate:"), "q");
            quitMeItem.Target = NSApplication.SharedApplication;
            appMenu.AddItem(quitMeItem);

            //
            NSMenu fileMenu = new NSMenu("File");

            mainFileMenuItem.Submenu = fileMenu;

            // You have to setup your document type to use file menu...
            NSMenuItem newDocumentItem = new NSMenuItem("New...", new ObjCRuntime.Selector("newDocument:"), "n");
            newDocumentItem.Target = NSDocumentController.SharedDocumentController;
            fileMenu.AddItem(newDocumentItem);
    
            return mainMenu;

        }

        static NSMenu BuildServicesMenu()
        {
            return new NSMenu();
        }
    }
}

```

# Living with VisualStudioMac

### Creating macos applications...(not smartphone applications)

I was just trying to use VisualStudio for Mac(2017) for creating macos application, and got in stuck easily...  

1. StoryBoard doesn't work properly, or may be useless.  
It was easy to decide the way I have to aim, DONT USE IT.  
We can write all UI code in C#, like we sometimes do in Swift or Objective-C language.  
I wrote template code of program startup part so that I (and somebody in stray about same matter) can easily startup macoses C# project.  

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

            app.ServicesMenu = BuildServicesMenu();
            app.MainMenu = BuildMainMenu(app.ServicesMenu);
            app.Delegate = new AppDelegate();
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

```csharp
/*
 * AppDelegate.cs
 */
 using AppKit;
using Foundation;

namespace TestCocoa
{
    [Register("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        private ViewController viewController;

        public AppDelegate()
        {
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            // Insert code here to initialize your application
            // System.Diagnostics.Debug.WriteLine("test");
	    
            // Create main window
            CoreGraphics.CGRect rect = new CoreGraphics.CGRect(600, 600, 800, 600);
            NSWindowStyle style = NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable | NSWindowStyle.Titled;
            NSWindow window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            // create view controller with main window.
            this.viewController = new ViewController(window);
            // call "loadView()"
            NSView tmpView = this.viewController.View;
        }

        public override void WillTerminate(NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}

 ```
 
 ```csharp
 /*
  * ViewController.cs
  */
using System;

using AppKit;
using Foundation;

namespace TestCocoa
{
    public partial class ViewController : NSViewController
    {
        private NSWindow window;

        public ViewController(NSWindow window)
        {
            this.window = window;
        }

        public ViewController(IntPtr handle) : base(handle)
        {
        }

        public override void LoadView()
        {
            this.View = new NSView(window.Frame);
            window.ContentView = this.View;
            window.IsVisible = true;
			//
            // Sample view structure, left: NSTableView, right: NSTextView (wrapped by NSScrollView)
            CoreGraphics.CGRect rect = new CoreGraphics.CGRect(0, 12, this.View.Frame.Size.Width, this.View.Frame.Size.Height - 24);
            NSSplitView splitView = new NSSplitView(rect);
            splitView.IsVertical = true;
            NSScrollView listView = new NSScrollView();
            rect = new CoreGraphics.CGRect(0, 0, 90, this.View.Frame.Size.Height);
            NSTableView tableView = new NSTableView(rect);
            listView.AddSubview(tableView);
            splitView.AddArrangedSubview(listView);
            rect = new CoreGraphics.CGRect(0, 0, this.View.Frame.Size.Width - 90, this.View.Frame.Size.Height);
            NSScrollView scrollTextView = new NSScrollView(rect);
            NSTextView textView = new NSTextView(rect);
            scrollTextView.AddSubview(textView);
            splitView.AddArrangedSubview(scrollTextView);
            splitView.DividerStyle = NSSplitViewDividerStyle.Thick;
            this.View.AddSubview(splitView);
            splitView.AdjustSubviews();

        }

        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Do any additional setup after loading the view.
        }

        public override NSObject RepresentedObject
        {
            get
            {
                return base.RepresentedObject;
            }
            set
            {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }
    }
}
  
```

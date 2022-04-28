# 组件化开发
[输入链接说明](https://www.raywenderlich.com/17753301-creating-a-framework-for-ios#toc-anchor-016)
## Framework
#### 1. In Xcode, select File ▸ New ▸ Project…. Then choose iOS ▸ Framework & Library ▸ Framework.
![输入图片说明](%E6%88%AA%E5%B1%8F2022-04-28%20%E4%B8%8B%E5%8D%885.09.02.png)
#### 2.Click Next. Then set the Product Name to CalendarControl. Use your own Organization Name and Organization Identifier.
![输入图片说明](%E6%88%AA%E5%B1%8F2022-04-28%20%E4%B8%8B%E5%8D%885.13.13.png)
#### 3. Adding the Source Code to the Framework
![输入图片说明](%E6%88%AA%E5%B1%8F2022-04-28%20%E4%B8%8B%E5%8D%885.22.07.png)
#### 4.Select your project in the Project navigator and choose CalendarControl in Targets. Open the Build Settings. Then set Build Libraries for Distribution to yes. This produces a module interface file which shows your public API when someone jumps to definition of your module in Xcode.
![输入图片说明](%E6%88%AA%E5%B1%8F2022-04-28%20%E4%B8%8B%E5%8D%885.24.48.png)
#### 5.Build the framework project. Make sure you get Build Succeeded with no build warnings or errors.
#### 6.Access Control level
Swift has five levels of access control. Use the following rules of thumb when creating your own frameworks:
- Open and public: For code called by the app or other frameworks, such as a custom view.
- Internal: For code used between functions and classes within the framework, such as custom layers in that view.
- Fileprivate: For code used within a single file, such as a helper function that computes layout heights.
- Private: For code used within an enclosing declaration, such as a single class block and extensions of that declaration in the same file.
When CustomCalendarPicker was part of the XYGCalendarPicker app, internal access wasn’t a problem. Now that it’s in a separate module, you must make it public for the app to use it. You’ll do that in the next section.
Updating the Framework Access Level
Open CalendarPickerViewController.swift. Make the class public by adding the public keyword to the class definition, like so:

```
 public  class CalendarPickerViewController: UIViewController {

```
Now CalendarPickerViewController is visible to any app file that imports the CalendarControl framework.
Next, add the public keyword to:

```
CalendarPickerViewController.init(baseDate:selectedDateChanged:)
CalendarPickerViewController.init(coder:)
CalendarPickerViewController.viewDidLoad()
CalendarPickerViewController.viewWillTransition(to:with:)
CalendarPickerViewController.collectionView(_:numberOfItemsInSection:)
CalendarPickerViewController.collectionView(_:cellForItemAt:)
CalendarPickerViewController.collectionView(_:didSelectItemAt:)
CalendarPickerViewController.collectionView(_:layout:sizeForItemAt:)
```

Note: You might wonder why you have to declare init as public. Apple explains this and other finer points of access control in their Access Control Documentation.
#### 7. Edit target setting release
![输入图片说明](1651146831569.jpg)
#### 8.Compile the simulator and generate the framework under the simulator environment under products
![输入图片说明](1651147198393.jpg)
#### 9.Through the compilation of the device machine, the framework under the device environment is generated under products
![输入图片说明](1651147389600.jpg)
Build and run. Now you get your CustomCalendarPicker.
Congratulations! You now have a working stand-alone framework and an app that uses it!
#### 10. View the architecture information supported by the framework of simulators and devices
![输入图片说明](1651147745913.jpg)
![输入图片说明](1651147657095.jpg)
#### 11.Remove the simulator arm64 and only retain x86_64
![输入图片说明](1651148621029.jpg)
#### 12.Combine the simulator framework and the real equipment framework into a framework that supports both
![输入图片说明](1651148896558.jpg)
#### 13.congratulations! The framework is finished, and then give it to your partner to import, add and use
![输入图片说明](1651149568965.jpg)

```
import CustomClenderPicker

```
#### 14.Display effect
![输入图片说明](Simulator%20Screen%20Shot%20-%20iPhone%208%20-%202022-04-28%20at%2020.43.29.png)
## XCFramework
You might have heard about XCFramework during WWDC 2019. Yes, you’re right: This is the name of the binary framework you can generate with Xcode.
Before 2019, you only had one opportunity to make your own binary framework: Universal Static Library, also known as Fat Framework.
To support multiple architectures, like a simulator and devices, you had to combine them under one library in the fat framework. However, after this article, your frameworks don’t have to be fat anymore.
Archiving Your Framework
For this section, you’ll work with your old friend, Terminal. Woohoo!
Open your terminal and navigate to the framework folder with the following command. Alternatively, you could drag your project folder to your terminal after the cd command:
#### 1. Enter the root directory of the framework project file
![输入图片说明](1651151274991.jpg)

```
cd /Users/xyg/Desktop/CustomClenderPicker
```
#### 2. Next, start archiving your framework for the following targets:

- iOS
- Simulator
- macOS

Start with iOS. Enter the following command into the terminal:
#### 3. iOS device  xcodebuild

```
xcodebuild archive \
-scheme CustomClenderPicker \
-configuration Release \
-destination 'generic/platform=iOS' \
-archivePath './build/CustomClenderPicker.framework-iphoneos.xcarchive' \
SKIP_INSTALL=NO \
BUILD_LIBRARIES_FOR_DISTRIBUTION=YES

```
When it's done, it's like this：
![输入图片说明](1651151654438.jpg)
#### 4.Continue to complete the xcodebuild archive
Enter the following command into the terminal:

```
xcodebuild archive \
-scheme CustomClenderPicker \
-configuration Release \
-destination 'generic/platform=iOS Simulator' \
-archivePath './build/CustomClenderPicker.framework-iphonesimulator.xcarchive' \
SKIP_INSTALL=NO \
BUILD_LIBRARIES_FOR_DISTRIBUTION=YES
```
#### 5. MacOS xcodebuild
```
xcodebuild archive \
-scheme CustomClenderPicker \
-configuration Release \
-destination 'platform=macOS,arch=x86_64,variant=Mac Catalyst' \
-archivePath './build/CustomClenderPicker.framework-catalyst.xcarchive' \
SKIP_INSTALL=NO \
BUILD_LIBRARIES_FOR_DISTRIBUTION=YES
```
#### 6.As you can see in your finder and the following screenshot, you generate three different archives files from your framework.
![输入图片说明](1651152359546.jpg)
#### 7.Generating the XCFramework
Now, make the binary framework, XCFramework. Add the following command to the terminal:

```
xcodebuild -create-xcframework \
-framework './build/CustomClenderPicker.framework-iphonesimulator.xcarchive/Products/Library/Frameworks/CustomClenderPicker.framework' \
-framework './build/CustomClenderPicker.framework-iphoneos.xcarchive/Products/Library/Frameworks/CustomClenderPicker.framework' \
-framework './build/CustomClenderPicker.framework-catalyst.xcarchive/Products/Library/Frameworks/CustomClenderPicker.framework' \
-output './build/CustomClenderPicker.xcframework'
```
If this error occurs, you need to set the build library for distribution of the framework project
 **在 Build Settings ~> Build Library for Distribution 设为YES** 
![输入图片说明](1651153258529.jpg)
## Cocopods


## Swift Package Manager

## Cartthage



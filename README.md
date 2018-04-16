<p align="center">
  <img src="https://img.shields.io/cocoapods/v/AuthNavigation.svg" alt="CocoaPods Compatible" style="padding:10px"/>
  <img src="https://img.shields.io/cocoapods/p/AuthNavigation.svg" alt="Platform" style="padding:10px"/>
  <img src="https://img.shields.io/badge/Swift-4.1-orange.svg" alt="Swift Version" style="padding:10px"/>
  <img src="https://img.shields.io/cocoapods/l/AuthNavigation.svg" alt="License" style="padding:10px"/>
</p>

# AuthNavigation

AuthNavigation is an iOS library, that organizes the login process in your app, making it easy to implement auto-login. AuthNavigation automatically checks if your user needs to login or not and presents the correct screens based on the result.

AuthNavigation can also organize loading screen, i.e. if you need to make server requests.

You can setup AuthNavigation on one or more `UIViewController`'s, it can authenticate the whole screen or just parts of it.

AuthNavigation is not a replacement for `UINavigationController`, in fact you can use both side by side.

<br></br>
<p align="center">
  <img src="https://github.com/columbbus/AuthNavigation/blob/master/Assets/demo.gif?raw=true" alt="AuthNavigation Demo" height="600"/>
</p>




## What it's not
AuthNavigation does not give you ready-to-use login and loading screen templates. AuthNavigation gives the navigation structure to create a simple login process with additional loading page, the design has to be done independently.




## Flow
This is a summary of the login flow in a simple diagram:

<p align="center">
  <img src="https://github.com/columbbus/AuthNavigation/blob/master/Assets/Flow-detailed.png?raw=true" alt="Login Flow"/>
</p>




## Installation


### Manual

You can simply drag the files from the `Source` folder into you project and use the classes.


### CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Cocoa projects. To integrate AuthNavigation into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '10.0'
use_frameworks!

target '<Your Target Name>' do
    pod 'AuthNavigation', '~> 1.1'
end
```




## Usage
There are two ways to use AuthNavigation:

* **Basic (Login):** `HostVC` is protected by a login screen `LoginVC`. AuthNavigation will check whether to present this login screen or not

* **Advanced (Login + Loading):** Basic + `LoadingVC` which will be presented before `LoginVC`. May be useful if you have to request a server to check whether user should login or not.


Follow the below steps to integrate AuthNavigation in your project:



### Project setup
Create 2 VC's for *Basic* and 3 for *Advance* (Specify unique Storybard ID's for each)



### HostVC setup
First create an `AUAuthNavigator` instance (you can either use the `AUAuthNavigator.sharedInstance` or you create your own static instance). Then set the Storyboard ID's of `LoginVC` and `LoadingVC` to the corresponding properties. To actually use the authenticator, call `startAuthentication` in `viewWillAppear` and call `stopAuthentication` in `viewDidDisappear`.

```swift
    class HostVC: UIViewController {
    
        static let authNavigator = AUAuthNavigator()
    
        override func viewDidLoad() {
            super.viewDidLoad()
        
            MainViewController.authNavigator.delegate = self
            
            MainViewController.authNavigator.loginVCId = "LoginVC"
            MainViewController.authNavigator.loadingVCId = "LoadingVC"
        }

        override func viewWillAppear(_ animated: Bool) {
            MainViewController.authNavigator.startAuthentication()
        }
    
        override func viewWillDisappear(_ animated: Bool) {
            MainViewController.authNavigator.stopAuthentication()
        }
    }
```

Set the delegate of the authenticator to `self`. Therefore `HostVC` needs to coform to the `AUAuthenticatable` protocol:

```swift
    extension HostVC: AUAuthenticatable {
    
        func shouldLogin() -> Bool {
            // Implement your authentication process here. If your user needs to login, return true, if he is already logged in return false
        }
    
        func willReturnFromLoginActions(success: Bool) {
            // Additional actions after login (not necessary)
        }
    }
```



### LoadingVC setup
After your custom loading is finished, call `finishLoading()` on the static `authNavigator` instance of `HostVC`.



### LoginVC setup
After login is finished, call `finishLogin(success: Bool)` on the static `authNavigator` instance of `HostVC`. Remember that you may want to handle login failure on the `LoginVC` itself instead of callin `finishLogin(succes: false)`, because this will only show the login screen again.



### Logout
Call `logout(presentLoading: Bool)` in your to `HostVC`. If `presentLoading` is set to `true`, the loading screen will be presented, if it's set to `false`, the login screen will be presented directly.


### Additional settings

* **`hostView`:** This is the container view for your login and loading screen, setting this to a specific view in your `HostVC` can be useful if you don't want the entire screen to be covered by the authentication. By default it's set to the entire screen.
* **`overlayColor`:** The overlay is used to hide the content of your `HostVC` during authorization. If you need this overlay to have a special color, then specify it here. Default behaviour is to use the background color of the `hostView`.
* **`animationDuration`:** Here you can specify a custom duration for all animations done by AUAuthNavigator (overlay, login and loading fade in/out).




## License
See the [LICENSE](LICENSE) file for license rights and limitations (MIT).

More on the motivation for this project can be found in [this](https://medium.com/@pascal.braband/navigating-your-ios-app-through-login-51c88e2329d3) article.

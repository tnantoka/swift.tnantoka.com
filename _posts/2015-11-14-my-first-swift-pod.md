---
title: "My first library written in Swift: GitHubAuth"
---

Yesterday, I create sign in with GitHub from scratch.
Today I'll make it compatible with CocoaPods.

## API

Before starting develop, We should decide API of the library.
Here is API of GitHubAuth:

Property / Method | Description
--- | ---
shared | Get singleton instance.
signIn | Show modally sign in view controller.
signOut | Remove stored access token.
accessToken | Get stored access token.
authorize | Add HTTP header to NSMutableRequest.
isSignedIn | Return singed in or not

## Create project

Open Terminal and then run the following command.

{% highlight sh %}
$ pod lib create GitHubAuth

What language do you want to use?? [ ObjC / Swift ]
 > Swift

Would you like to include a demo application with your library? [ Yes / No ]
 > Yes

Which testing frameworks will you use? [ Quick / None ]
 > None

Would you like to do view based testing? [ Yes / No ]
 > No

{% endhighlight %}

## Create GitHubAuth class

Remove `ReplaceMe.swift` and add `GitHubAuth.swift`.

{% highlight swift %}
import UIKit

public class GitHubAuth {
    public static let shared = GitHubAuth()
}
{% endhighlight %}

## Create Sign In View Controller

Create view controller and implement signing in feature as previous article.

1. Add a new view controller class.
1. Add a new storyboard and configure custom class.
1. Write `viewDidLoad` and `UIWebViewDelegate`.

{% highlight swift %}
override func viewDidLoad() {
    super.viewDidLoad()

    // Do any additional setup after loading the view.
    self.title = NSLocalizedString("Sign in", comment: "")
    
    guard let webview = view as? UIWebView else { return }
    webview.delegate = self
    
    let params = "client_id=\(clientId)"
    let urlString = "https://github.com/login/oauth/authorize?\(params)"
    guard let url = NSURL(string: urlString) else { return }

    let req = NSURLRequest(URL: url)
    webview.loadRequest(req)
}

// MARK: - UIWebViewDelegate

func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {
    guard let url = request.URL where url.host == host else { return true }
    guard let code = url.query?.componentsSeparatedByString("code=").last else { return true }

    let req = NSMutableURLRequest(URL: tokenUrl)
    req.HTTPMethod = "POST"
    req.addValue("application/json", forHTTPHeaderField: "Content-Type")
    req.addValue("application/json", forHTTPHeaderField: "Accept")
    let params = [
        "client_id" : clientId,
        "client_secret" : clientSecret,
        "code" : code
    ]
    req.HTTPBody = try? NSJSONSerialization.dataWithJSONObject(params, options: [])
    
    let task = NSURLSession.sharedSession().dataTaskWithRequest(req) { data, response, error in
        guard let data = data else { return }
        
        do {
            let content = try NSJSONSerialization.JSONObjectWithData(data, options: [])
            if let accessToken = content["access_token"] as? String {
                // Store access token
            }
        } catch {}
    }
    task.resume()

    return false
}
{% endhighlight %}

Then add method for sign in and call from example app.

1. Add `signIn` method to `GitHubAuth`.
1. Add button to View Controller on `Main.storyboard`.
1. Call sign in from button action.

###### GitHubAuth.swift

{% highlight swift %}
public func signIn(callback: NSError? -> Void) {
    guard let rootController = UIApplication.sharedApplication().keyWindow?.rootViewController else { return }
    
    guard let path = NSBundle(forClass: self.dynamicType).pathForResource("GitHubAuth", ofType: "bundle") else { return }
    guard let bundle = NSBundle(path: path) else { return }
    
    let storyboard: UIStoryboard = UIStoryboard(name: "SignInViewController", bundle: bundle)
    guard let navController = storyboard.instantiateInitialViewController() as? UINavigationController else { return }

    rootController.presentViewController(navController, animated: true, completion: nil)
}
{% endhighlight %}

###### ViewController.swift 

{% highlight swift %}
@IBAction func signIn(sender: AnyObject) {
    GitHubAuth.shared.signIn { error in
        // Signed in
    }
}
{% endhighlight %}

Now we can sign in with GitHub in example app.

## Store access token to keychain

I always use [KeychainAccess](https://github.com/kishikawakatsumi/KeychainAccess) for keychain, but today I implement myself to reduce dependencies.

{% highlight swift %}
public internal(set) var accessToken: String {
    get {
        let query = [
            (kSecClass as String) : kSecClassGenericPassword,
            (kSecAttrAccount as String) : GitHubAuth.accessTokenKey,
            (kSecReturnData as String) : kCFBooleanTrue,
            (kSecMatchLimit as String) : kSecMatchLimitOne
        ]

        var result: AnyObject?
        let status = withUnsafeMutablePointer(&result) { SecItemCopyMatching(query, UnsafeMutablePointer($0)) }

        if status == errSecSuccess {
            guard let data = result as? NSData else { return "" }
            return String(data: data, encoding: NSUTF8StringEncoding) ?? ""
        }
        
        return ""
    }
    set {
        guard let data = newValue.dataUsingEncoding(NSUTF8StringEncoding) else { return }

        let query = [
            (kSecClass as String) : kSecClassGenericPassword,
            (kSecAttrAccount as String) : GitHubAuth.accessTokenKey,
            (kSecValueData as String) : data
        ]
        
        SecItemDelete(query as CFDictionaryRef)
        SecItemAdd(query as CFDictionaryRef, nil)
    }
}
 
public func signOut() {
    let query = [
        (kSecClass as String): kSecClassGenericPassword
    ]
    SecItemDelete(query as CFDictionaryRef)
}
{% endhighlight %}

Thanks to:

- <https://swift.unicorn.tv/articles/dead-simple-keychain-access>
- <http://www.splinter.com.au/2015/06/21/swift-keychain-wrapper/>
- <https://github.com/s-aska/KeyClip>

## Implement the rest of features

At this point, the core of library is created. 
Omit remaining features from this article, but all source codes are available on GitHub:

<https://github.com/tnantoka/GitHubAuth>

## Publish to CocoaPods

Finally, let's publish my library on CocoaPods.

Here is a docment:
https://guides.cocoapods.org/making/using-pod-lib-create.html>

{% highlight sh %}
# Local
$ pod lib lint
GitHubAuth.podspec passed validation.

# With network
$ pod spec lint GitHubAuth.podspec
GitHubAuth.podspec passed validation.

$ pod trunk register tnantoka@bornneet.com 'tnantoka' --description='mbpr'

$ pod trunk push
{% endhighlight %}

That's all.
Now, my first swift library have been published!


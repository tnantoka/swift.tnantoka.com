---
title: Sign in with GitHub without 3rd party libraries
---

I use the GitHub API a lot to write example codes.
There are many oAuth libs written in Swift, but they're too heavy for my use.
I just want to use only GitHub API, not require OAuth 1, refresh token, etc.

Today, let's try GitHub login from scratch!

## Register GitHub Application

Create oAuth application on GitHub (<https://github.com/settings/applications/new>) and note down your client id, client secret.
The callback URL is `https://exmaple.com/`.

![](/images/github-login-from-scratch/0-register_application.png)

## Create Project
  
1. Launch Xcode.
1. Menu -> File -> New -> Project...
1. iOS -> Application -> Single View Application
1. Choose project options as the following screenshot. 
  ![](/images/github-login-from-scratch/1-create_project.png)
1. Save project to anywhere.

## Add Button to Sign in

1. Open `Main.stroyboard`.
1. Select `iPhone 4.7-inch` size for simulated metrics on attributes inspector.
  ![](/images/github-login-from-scratch/2-view_controller_size.png)
1. Drag and drop a `Button` from object library.
  ![](/images/github-login-from-scratch/3-add_button.png)
1. Add auto layout settings for centering.
  ![](/images/github-login-from-scratch/4-autolayout1.png)
  ![](/images/github-login-from-scratch/5-autolayout2.png)

## Add Sign In View Controller

1. Drag and drop a `View Controller` from object library.
  ![](/images/github-login-from-scratch/6-add_view_controller.png)
1. Hold control key and drag and drop from button to new view controller. Then select `Present Modally`.
  ![](/images/github-login-from-scratch/7-modally_segue.png)
1. Right click on `GitHubLoginFromScratch` and select `New File...`.
1. iOS -> Source -> Cocoa Touch Class
1. Add `SignInViewController`.
  ![](/images/github-login-from-scratch/8-create_sign_in_controller.png)
1. Configure custom class on storyboard. 
  ![](/images/github-login-from-scratch/9-custom_class.png)
  ![](/images/github-login-from-scratch/10-webview.png)

## Write code for sign in

Declare delegate and constants.

{% highlight swift %}
class SignInViewController: UIViewController, UIWebViewDelegate {

    let clientId = "YOUR_CLIENT_ID"
    let clientSecret = "YOUR_CLIENT_SECRET"
{% endhighlight %}

Add delegate method for hook http request. If url is `example.com` (callback URL), extract a autorization code and send a request to get access token.

{% highlight swift %}
func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {
    if let url = request.URL where url.host == "example.com" {
        if let code = url.query?.componentsSeparatedByString("code=").last {
            let urlString = "https://github.com/login/oauth/access_token"
            if let tokenUrl = NSURL(string: urlString) {
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
                    if let data = data {
                        do {
                            if let content = try NSJSONSerialization.JSONObjectWithData(data, options: []) as? [String: AnyObject] {
                                if let accessToken = content["access_token"] as? String {
                                    self.getUser(accessToken)
                                }
                            }
                        } catch {}
                    }
                }
                task.resume()
            }
        }
        return false
    }
    return true
}
{% endhighlight %}

This `getUser` method is a test for access token.
Adding `Authorization: token ACCESS_TOKEN` to HTTP Header to use GitHub API. 

{% highlight swift %}
func getUser(accessToken: String) {
    let urlString = "https://api.github.com/user"
    if let url = NSURL(string: urlString) {
        let req = NSMutableURLRequest(URL: url)
        req.addValue("application/json", forHTTPHeaderField: "Accept")
        req.addValue("token \(accessToken)", forHTTPHeaderField: "Authorization")
        let task = NSURLSession.sharedSession().dataTaskWithRequest(req) { data, response, error in
            if let data = data {
                if let content = String(data: data, encoding: NSUTF8StringEncoding) {
                    dispatch_async(dispatch_get_main_queue()) {
                        print(content)
                        self.presentingViewController?.dismissViewControllerAnimated(true, completion: nil)
                    }
                }
            }
        }
        task.resume()
    }
}
{% endhighlight %}
  
## Run

1. Click `Build and then run the current scheme` or press `command + R` key.
  ![](/images/github-login-from-scratch/11-launch.png)
1. Sign in to GitHub on webview.
  ![](/images/github-login-from-scratch/12-github.png)

If we successfully sign in, we can see the following messages.

{% highlight json %}
{"login":"tnantoka","id":213169,"avatar_url":"https://avatars.githubusercontent.com/u/213169?v=3","gravatar_id":"","url":"https://api.github.com/users/tnantoka","html_url":"https://github.com/tnantoka","followers_url":"https://api.github.com/users/tnantoka/followers","following_url":"https://api.github.com/users/tnantoka/following{/other_user}","gists_url":"https://api.github.com/users/tnantoka/gists{/gist_id}","starred_url":"https://api.github.com/users/tnantoka/starred{/owner}{/repo}","subscriptions_url":"https://api.github.com/users/tnantoka/subscriptions","organizations_url":"https://api.github.com/users/tnantoka/orgs","repos_url":"https://api.github.com/users/tnantoka/repos","events_url":"https://api.github.com/users/tnantoka/events{/privacy}","received_events_url":"https://api.github.com/users/tnantoka/received_events","type":"User","site_admin":false,"name":"Tatsuya Tobioka","company":null,"blog":"http://tnantoka.com/","location":"Tokyo, Japan","email":"tnantoka@bornneet.com","hireable":true,"bio":null,"public_repos":108,"public_gists":6,"followers":56,"following":0,"created_at":"2010-03-01T14:24:36Z","updated_at":"2015-11-12T02:04:52Z"}
{% endhighlight %}

That's all for today.

## List of references

- <https://developer.github.com/v3/oauth/#web-application-flow>

## Source code

- <https://github.com/tnantoka/swift-examples/tree/master/GitHubLoginFromScratch>

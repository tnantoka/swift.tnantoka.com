---
title: Using SFSafariViewController
---

To display web contents in the app, it sounds like SFSafariViewController is useful, but I have never used it.
So today, I'll write some example codes.

## Create project

1. Create sigle view application and open storyboard.
1. Editor -> Embed in... -> Navigation Controller 
1. Add 3 buttons and connect to action.
  ![](/images/safari-view-controller/1-buttons-and-actions.png)

## Write examples

Add 3 methods to view controller.

### Push

{% highlight swift %}
@IBAction func push(sender: AnyObject) {
    navigationController?.pushViewController(createSafariController(), animated: true)
}
{% endhighlight %}

### Present modaly

{% highlight swift %}
@IBAction func present(sender: AnyObject) {
    let safariController = createSafariController()
    safariController.delegate = self
    presentViewController(safariController, animated: true, completion: nil)
}
{% endhighlight %}

### Show with custom right button item

{% highlight swift %}
@IBAction func showWithRightButtonItem(sender: AnyObject) {
    let safariController = createSafariController()
    
    let barButtonItem = UIBarButtonItem(barButtonSystemItem: .Action, target: self, action: "barButtonItemDidTap:")
    safariController.navigationItem.rightBarButtonItem = barButtonItem
    
    navigationController?.pushViewController(safariController, animated: true)
}
{% endhighlight %}

Then run project...It's nice!

![](/images/safari-view-controller/2-right-button-item.png)

That's all. Sure enough, safari view controller is very convenient. I'll use this in the next app.

## Source code

- <https://github.com/tnantoka/swift-examples/tree/master/SafariViewControllerExample>

## References

- <https://developer.apple.com/library/ios/documentation/SafariServices/Reference/SFSafariViewController_Ref/index.html#//apple_ref/occ/cl/SFSafariViewController>

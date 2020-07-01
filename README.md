# Html5 starter app with Swift
This is an [Xcode] project that builds an [iOS] app such that all the [UI] for the app is powered by HTML elements that communicate with a [Swift] backend.

This project came to be when I was working on my first iOS app; [My Day Todos], which is a iOS app with UI powered by Html5. You can download My Day Todos or My Day Todos Lite(free) from the [app store] and have a look at the app or if you want to know more about it? or see how it works you can checkout the [app website].
The motivation behind this is,
- to serve as an easier transition into the iOS development for those coming from a Java full stack development background
- demonstrate an app with a HTML5 powered UI that can potentially use native platform capabilities that may not be there from other hybrid approaches
- to show, that for a large app, an organisation can have a front-end engineer the UI and an iOS engineer can build a native iOS backend as opposed to having 2 iOS engineers
- to showcase, how you can leverage CSS transisitions in a native iOS app

Should you have any questions on what's to be added? feel free to contact me or better yet, if you think you can add the above two things to the repo then please do and create a pull request, that would be great!

# What does it show right now?
Right now, this project shows an iOS app where all the HTML/Javasceript/CSS powered UI is hosted in a WKWebView. It shows,
- how to fetch data using XMLHTTPRequest in Javascript and send it back to Swift backend
    ````
    var xhr = new XMLHttpRequest();
    var someURL = "";
    xhr.open("GET", someURL , true);
    xhr.onload = function (e) {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
            //the code tht sends the data back to the Swift backend
            webkit.messageHandlers.quizDataFromAPI.postMessage(xhr.responseText);
            console.log("sent the messsge to the backend");
        } else {
          console.error(xhr.statusText);
        }
      }
    };
    xhr.onerror = function (e) {
      console.error(xhr.statusText);
    };
    xhr.send(null);
    ```` 
- how to initiate execution of a Javascript function from Swift backend
    ```
    let contentController = WKUserContentController()
    /* updateTitle() is one of the Javascript methods in the demo.js class that's included
     in the index.html file. We can call that method by injecting a WKUserScript show below*/
    let titleScript = WKUserScript(source: "updateTitle()",
                                  injectionTime: .atDocumentEnd,
                                  forMainFrameOnly: true)
    contentController.addUserScript(titleScript)
    ```
- how to show native iOS sharing options via UIActivityViewController 
```
    func processAction(message: WKScriptMessage, viewController: UIViewController) {
        guard let messageBody = message.body as? [String: Any] else {
            return
        }
        let task = messageBody["task"] as! String
        let time = messageBody["time"] as! String
        let activityViewController = UIActivityViewController(activityItems: ["\(task) at \(time)"], applicationActivities: nil)
        let excludeActivities = [
            UIActivity.ActivityType.assignToContact,
            ...
        ]
        activityViewController.excludedActivityTypes = excludeActivities
        viewController.present(activityViewController, animated: true, completion: nil)
    }
```
- how to reduce the use of nested if statements by implementing the Factory design pattern
    ```
    protocol Action {
        func processAction(message: WKScriptMessage, viewController: UIViewController)
    }
    class ActionFactory {
        
        private var actionsMap = [String: Action]()
        init() {
            //All the below classes conform to the Action protocol
            actionsMap["shareFromHTML"] = ShareFromHTML()
            ...
        }
        func getAction(key: String) -> Action? {
            return actionsMap[key]
        }
    }
    ```

All in all, this project aims to show how to establish a 2 way request/response communication pattern between the Swift backend and the web front-end. 

# How to use this project? 
Clone the project and replace the content in the webStuff folder with your own web code and replace the Swift functions according to your needs. If you have any questions feel free to contact me on bhuman at mydaytodo dot com

# A brief history of this project
This repo was orginally called [IonicStarterAppWithSwift] and should you want to how this repo came to be i.e. the idea, motivation etc you can have a read about it in my [blog post]. A few months after creating this repo, I made a few simple, yet significant changes to this project,
In addition to having a background in computer science research, I am also a Java developer i.e. I work with Java web apps. So when I started working on my iOS app, I wanted to reuse my existing UI(HTML5) skillset, hence I structured my iOS app in such a way that I can reuse my UI skills. Still not clear? please have a read of my blog post ([Making an iOS app like making a Java web app]) where I try to explain it all.

Hope this repository helps you, fork the repo or add it to your favourite if it does.

[Lite]: https://itunes.apple.com/us/app/my-day-todos-lite-todolist/id1066820078?mt=8

[Html5 powered widget for an iOS app..]: http://captaindanko.blogspot.com.au/2016/03/html5-based-widget-for-ios-app-today.html

[app website]: http://www.mydaytodo.com
[My Day Todos]: https://itunes.apple.com/app/my-day-todos-todolist-that/id1020072048
[app store]: https://itunes.apple.com/app/my-day-todos-todo-list-alarms/id1020072048
[Making an iOS app like making a Java web app]:http://captaindanko.blogspot.com.au/2015/06/making-ios-app-like-making-java-web-app.html
[HTML5]:https://en.wikipedia.org/wiki/HTML5
[UI]:https://en.wikipedia.org/wiki/User_interface
[iOS]: https://en.wikipedia.org/wiki/IOS
[Swift]: https://developer.apple.com/swift/
[Xcode]: https://developer.apple.com/xcode/
[IonicStarterAppWithSwift]: https://github.com/cptdanko/IonicStarterAppWithSwift
[blog post]: http://captaindanko.blogspot.com.au/2014/10/xcode-starter-project-with-ionic-html5.html

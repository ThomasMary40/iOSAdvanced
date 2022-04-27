---
layout: post
title: "Recomposing UI"
date: 2022-04-27 16:41:27 +0200
categories: UI
---

Sample of recomposing UI.
Note here we do it in a button, but you can trigger event.

```swift
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {

    guard let windowScene = (scene as? UIWindowScene) else { return }

    window = UIWindow(windowScene: windowScene)
    window?.rootViewController = makeFreeUI()
    window?.makeKeyAndVisible()
}

private func makeFreeUI() -> UIViewController {
    let vc = UIViewController()

    vc.view.backgroundColor = .yellow
    vc.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Start subscription", primaryAction: UIAction{[weak self] _ in
        self?.window?.rootViewController = self?.makePaidUI()
    })

    return UINavigationController(rootViewController: vc)
}

private func makePaidUI() -> UIViewController {
    let vc = UIViewController()

    vc.view.backgroundColor = .green
    vc.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "End subscription", primaryAction: UIAction{[weak self] _ in
        self?.window?.rootViewController = self?.makeFreeUI()
    })

    return UINavigationController(rootViewController: vc)
}
```

At first launch, rootViewController is a free UI


makeFreeUI
: Create a free UI View Controller
: Create a button bar item to switch to a paid UI


makePaidUI
: Create a paid UI View Controller
: Create a button bar item to switch to a free UI


You can [download the sample project here][1]

[1]:{{ site.url }}/iOSAdvanced/download/recomposeui.zip

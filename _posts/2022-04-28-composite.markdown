---
layout: post
title: "Composite datasource"
date: 2022-04-28 18:41:27 +0200
categories: UI
---

Composite TableView DataSource / Collection View DataSource.

This sample show how to present fruits and vegetables in the same UITableView from two distincts datasources.

This is what (very beautiful) final result looks like :

![Sample]([2])

Let's GO !

First create the fruits datasource :

```swift
class FruitsDataSource : NSObject, UITableViewDataSource {

    let fruits : [String]
    private var isShowingFruits = true

    init(fruits:[String]) {
        self.fruits = fruits
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "FruitCell") ?? UITableViewCell(style: .default, reuseIdentifier: "FruitCell")
        cell.textLabel?.text = fruits[indexPath.row]
        cell.contentView.backgroundColor = .yellow

        return cell
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        isShowingFruits ? fruits.count : 0
    }

    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        "Insane fruits"
    }

    func toggle(in tableView:UITableView) {
        isShowingFruits.toggle()
        tableView.reloadData()
    }
}
```

Then create the vegetables datasource :

```swift
class VegetablesDataSource : NSObject, UITableViewDataSource {

    let vegetables : [String]
    private var isShowingVegetables = true

    init(vegetables : [String]) {
        self.vegetables = vegetables
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "VegetableCell") ?? UITableViewCell(style: .default, reuseIdentifier: "VegetableCell")
        cell.textLabel?.text = vegetables[indexPath.row]
        cell.contentView.backgroundColor = .green

        return cell
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        isShowingVegetables ? vegetables.count : 0
    }

    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        "Pretty good vegetables"
    }

    func toggle(in tableView:UITableView) {
        isShowingVegetables.toggle()
        tableView.reloadData()
    }
}
```

Composite (magic!) in action :

```swift
class TableViewDataSourceComposite : NSObject, UITableViewDataSource {
    let dataSources: [UITableViewDataSource]

    init(dataSources:[UITableViewDataSource]) {
        self.dataSources = dataSources
    }

    func numberOfSections(in tableView: UITableView) -> Int {
        dataSources.reduce(0) { partialResult, datasource in
            partialResult + (datasource.numberOfSections?(in: tableView) ?? 1)
        }
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        dataSource(forSection: section, in: tableView).tableView(tableView, numberOfRowsInSection: section)
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        dataSource(forSection: indexPath.section, in: tableView).tableView(tableView, cellForRowAt: indexPath)
    }

    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        dataSource(forSection: section, in: tableView).tableView?(tableView, titleForHeaderInSection: section)
    }

    private func dataSource(forSection section: Int, in tableView:UITableView) -> UITableViewDataSource {
        var sectionCount = 0
        for dataSource in dataSources {
            sectionCount += dataSource.numberOfSections?(in: tableView) ?? 1

            if section < sectionCount {
                return dataSource
            }
        }
        fatalError("Datasouce not found for section \(section)")
    }
}
```

Important : please note you can (you should !!) highly improve performances with caching.

Finally, do all together :

```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    let fruits = ["grapes","lime","lemon","cherry","blueberry","banana","apple","watermelon"]
    let vegetables = ["carrot","broccoli","asparagus","cauliflower","corn","cucumber","eggplant","green pepper"]

    private lazy var fruitsDataSource = FruitsDataSource(fruits: fruits.sorted())
    private lazy var vegetablesDataSource = VegetablesDataSource(vegetables: vegetables.sorted())

    private lazy var dataSource = TableViewDataSourceComposite(dataSources: [fruitsDataSource,vegetablesDataSource])

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {

        guard let windowScene = (scene as? UIWindowScene) else { return }

        let vc = UITableViewController()
        vc.tableView.dataSource = dataSource

        vc.navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Toggle Fruits", primaryAction: UIAction {
            [weak self]
            _ in
            self?.fruitsDataSource.toggle(in: vc.tableView)
        })

        vc.navigationItem.leftBarButtonItem = UIBarButtonItem(title: "Toggle Vegetables", primaryAction: UIAction {
            [weak self]
            _ in
            self?.vegetablesDataSource.toggle(in: vc.tableView)
        })

        window = UIWindow(windowScene: windowScene)
        window?.rootViewController = UINavigationController(rootViewController: vc)
        window?.makeKeyAndVisible()
    }
}
```

Enjoy !

You can [download the sample project here][1]

[1]:{{ site.url }}/iOSAdvanced/download/composite_datasources.zip
[2]:./image/composite/composite_1.png

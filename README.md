# Dry-Run Review
## Question 1
What have you learned recently about iOS development? How did you learn it? Has it changed your approach to building apps?
## Question 2
Can you talk about a framework that you've used recently (Apple or third-party)? What did you like/dislike about the framework?
## Question 3
Describe how you would construct a Twitter feed application (here is an example of Udacity's Twitter feed) that at minimum can display a company's Twitter page. Please include information about any classes/structs that you would use in the app. Which classes/structs would be the model(s), the controller(s), and the view(s)?
## Answer 3
I would prefer to describe a process of building this application as step-by-step instruction:

1. I should integrate Twitter API into this project. I guess that I have to register developer account and application in Twitter developer console, download Twitter Kit through Fabric and get authorization data for this application.
2. I should create models for this application:

 Model | Fields 
 --- | --- 
 TwitterFeed | Image url, date of feed, title of feed, unique id of feed, tags of feed, stars of feed, retweets of feed, shares of feed
 Company | Company name, company nickname, array of TwitterFeed models, url of company's avatar

3. I should create custom view of this app - TwitterFeedCell inherits from UITableViewCell.

 View | Subview name | Subview class
 --- | --- | ---
 TwitterFeedCell | companyAvatar | UIImageView
 TwitterFeedCell | companyName | UILabel
 TwitterFeedCell | companyNickname | UILabel
 TwitterFeedCell | feedDate | UILabel
 TwitterFeedCell | feedTitle | UILabel
 TwitterFeedCell | feedTags | UILabel
 TwitterFeedCell | feedStar | UIButton
 TwitterFeedCell | feedShare | UIButton
 TwitterFeedCell | feedRetweet | UIButton
 TwitterFeedCell | feedLink | UILabel
4. I should create view controllers for this application. I think that one view controller is enough:

 ViewController | Subview | Subview class
 --- | --- | ---
 CompanyPageViewController | companyAvatar | UIImageView
 CompanyPageViewController | companyName | UILabel
 CompanyPageViewController | companyNickname | UILabel
 CompanyPageViewController | companyFeeds | UITableView with custom cell TwitterFeedCell
 
I think that this app can be extend with contacts, feedback form and other functions.
## Question 4
Describe some techniques that can be used to ensure that a UITableView containing many UITableViewCell is displayed at 60 frames per second.
## Answer 4
This is a very interesting question and I'd like to tell you about my approach to this. My approach consist of understanding how is working UITableView and some techniques for work with one.

At first, Apple Engineers realized a good idea with dequeuing reusable cells. The basic idea here is that instead of performing complex operations to create a new cell every time we need one, we simply recycle cells and other table components when they are no longer in view, update the state of the view elements with new data and reuse it.

We should be smart and thriffty when we're working with UITableView through data source and delegate. Apple is using UITableViewDelegate and UITableViewDataSource for set content of a table. In method cellForRowAtIndexPath we can to set content of a UITableViewCell. Taking into account the fact that cells is reusable, we should to use background threads for hard tasks in cellForRowAtIndexPath method - downloading data, complex calculations, animations etc.

This approaches allow us to get above 60 FPS.
## Question 5
Imagine that you have been given a project that has this ActorViewController. The ActorViewController should be used to display information about an actor. However, to send information to other ViewControllers, it uses NSUserDefaults. Does this make sense to you? How would you send information from one ViewController to another one?
## Answer 5
I think that the use of NSUserDefaults for complex models is a very stupid solution because it's making program syntax is too complex and has a low performance. I would prefer to use Realm:
```swift
import UIKit
import RealmSwift

class Actor: Object {
    dynamic var name = ""
    dynamic var image = ""
    dynamic var bio = ""
}

class ActorViewController: UIViewController {
    
    @IBOutlet weak var actorNameLabel: UILabel!
    @IBOutlet weak var actorImageView: UIImageView!
    @IBOutlet weak var actorBio: UITextView!
    
    override func viewWillAppear(animated: Bool) {
        super.viewWillAppear(animated)
        
        // Get the default Realm
        let realm = try! Realm()
        // Query Realm for all actors
        let actors = realm.objects(Actor.self)
        
        actorNameLabel.text = actors[0].name
        actorImageView.image = UIImage(named: actors[0].image)
        actorBio.text = actors[0].bio
    }
}
```
## Question 6
Imagine that you have been given a project that has this GithubProjectViewController. The GithubProjectViewController should be used to display high-level information about a GitHub project. However, it’s also responsible for finding out if there’s network connectivity, connecting to GitHub, parsing the responses and persisting information to disk. It is also one of the biggest classes in the project.

How might you improve the design of this view controller?

### Before answering the final question, insert a job description for an iOS developer position of your choice!
Your answer for Question 7 should be targeted to the company/job-description you chose.
### Selected job
I want to work in Google so I decided to choose [iOS Software Engineer, Swift Developer](https://www.google.com/about/careers/jobs#!t=jo&jid=/google/ios-software-engineer-swift-developer-1600-amphitheatre-pkwy-mountain-view-ca-9620001&) vacancy from Google Career.
## Question 7
If you were to start your iOS developer position today, what would be your goals a year from now?

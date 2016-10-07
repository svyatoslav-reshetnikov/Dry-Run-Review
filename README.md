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
## Answer 6
I have worked with file and I have a lot of ideas how I can to improve it.

At first, we should use some persistence library for saving network data. I prefer to use Realm. Then we should add network functions in other class in order to make a good and easy-to use architecture. See code below:
```swift
import UIKit
import RealmSwift

class GitHubProjectViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UICollectionViewDelegate, UICollectionViewDataSource {
    
    // project UI
    
    @IBOutlet weak var nameLabel: UILabel!
    @IBOutlet weak var completionPercentage: UILabel!
    @IBOutlet weak var openIssues: UILabel!
    @IBOutlet weak var closedIssues: UILabel!
    @IBOutlet weak var allIssuesTable: UITableView!
    @IBOutlet weak var contributorsCollection: UICollectionView!
    
    // new issue UI
    
    @IBOutlet weak var newIssueName: UITextField!
    @IBOutlet weak var newIssueDescription: UITextView!
    @IBOutlet weak var postNewIssueButton: UIButton!
    
    // Realm
    // Get the default Realm
    let realm = try! Realm()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        allIssuesTable.delegate = self
        allIssuesTable.dataSource = self
        
        contributorsCollection.delegate = self
        contributorsCollection.dataSource = self
        
        newIssueName.hidden = true
        newIssueDescription.hidden = true
        postNewIssueButton.hidden = true
        
        callFunctionFromAPIManager()
    }
    
    func makeCallsToGithubAPI() {
        // All of the network calls happen in the github API and we can access them through the API Singleton
    }
    
    @IBAction func newIssueButtonPressed(sender: AnyObject) {
        newIssueName.hidden = false
        newIssueDescription.hidden = false
        postNewIssueButton.hidden = false
    }
    
    @IBAction func postNewIssueButtonPressed(sender: AnyObject) {
        postNewIssue()
    }
    
    func postNewIssue() {
        APIManager.instance.postNewIssue(success, error) {
            if error != nil {
                // Handle the error
            } else {
                //update UI
                // Call method reloadData() for allIssuesTable
            }
            
        }
    }
    
    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return realm.objects(Project.self)[0].issues.count
    }
    
    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("IssueCell") as! IssueCell
        // [code that stylizes the cell]
        return cell
    }
    
    func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
        // The comments and other data are being loaded by the GitHubAPI class, so we can access them via the Singleton
        // [code that pushes a new view controller with comment data]
    }
    
    func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return realm.objects(Project.self)[0].contributors.count
    }
    
    func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {
        let cell = contributorsCollection.dequeueReusableCellWithReuseIdentifier("ContributorCell", forIndexPath: indexPath) as! ContributorCell
        // [code that stylizes the cell]
        return cell
    }
    
    func collectionView(collectionView: UICollectionView, didSelectItemAtIndexPath indexPath:NSIndexPath) {
        // The code for the network Query happens in the GitHubAPI and we can access the model objects when needed.
        // [code that pushes a new view controller with contributor data]
    }
}

class Issue: Object {
    dynamic var name = ""
    dynamic var description = ""
    dynamic var additionalInfo = ""
}

class Contributor: Object {
    dynamic var name = ""
    dynamic var bio = ""
}

class Project: Object {
    dynamic var name = ""
    dynamic var completionPercentage = 0
    let issues = List<Issue>()
    let contributors = List<Contributor>()
}

class APIManager {
    
    static let instance = APIManager()
    
    typealias callback = (_ result: AnyObject!, _ error: Error?) -> Void
    
    /* This class is responsible for making the API network calls to Github and returns callbacks with the results.  */
    func taskForGETMethod(var urlString: String, parameters: [String : AnyObject]?, callback: callback) -> NSURLSessionDataTask {
        /* Make parameters for the network call
         * Create a session and task.
         * Check our status code
         * Proceed with Parsing the JSON
         * Return the task
         */
        /* GUARD: For a successful status respose*/
        
    }
    
    func taskForPostMethod(var urlString: String, parameters: [String : AnyObject]?, callback: callback) -> NSURLSessionDataTask {
        /* Make parameters for the network call
         * Create a session and task.
         * Check our status code
         * Proceed with Parsing the JSON
         * Return the task
         */
        /* GUARD: For a successful status respose*/
    }
    
    class func parseJSONDataWithCompletionHandler(data: NSData, callback: callback -> Void) {
        //Parse JSON and return it via the completion handler
    }
}

extension APIManager {
    //Convenience methods for getting data from the GithubAPI
}
```

### Before answering the final question, insert a job description for an iOS developer position of your choice!
Your answer for Question 7 should be targeted to the company/job-description you chose.
### Selected job
I want to work in Google so I decided to choose [iOS Software Engineer, Swift Developer](https://www.google.com/about/careers/jobs#!t=jo&jid=/google/ios-software-engineer-swift-developer-1600-amphitheatre-pkwy-mountain-view-ca-9620001&) vacancy from Google Career.
## Question 7
If you were to start your iOS developer position today, what would be your goals a year from now?
## Answer 7
If i was to start selected iOS developer position today, I would have a list of goals a year from now:

1. Accept the company's values and follow them. I know that Google care about user happiness and my main goal a year from now is design and implement new user-facing features in Google’s products.
2. Collaborate with Google's designers to create innovative user experiences. I really like to work with designers because synthesis of creative and technical minds creates great ideas and products.
3. Develop prototypes quickly to validate interactions and prove product designs. I think that this is a very lean methodology of development and I prefer to use it in my job.
4. Write client-side code to create fast, easy-to-use, high volume production applications.
5. Build the helpful libraries and frameworks for Google's products. I'm sure that I can to optimize existing architecture and make it easier and re-used.
6. Self-improving in a non-stop mode. I think that Google is a greatest tech company all over the world and this company are required people with strong tech knowledge and beautiful minds. I want to meet the company level, so I'm regularity improving basic science (Computer Science, algorythms and data structures, mathematics etc) and program languages (Swift, Objective-C, Python, Java etc). Also I'm learning new sciences (Machine learning, neural networks, data science and data analysis, deep learning, UI and UX design for mobile applicatons etc). A year from now I want to finish at least 2 Udacity's nanodegree - Machine Learning and Self-Driving cars.

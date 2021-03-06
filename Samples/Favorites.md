# Favorites

To display the favorites screen, you should wait for the StoryView to call the `favoriteCellDidSelect()` delegate method. After that, go to the screen with your favorite stories.

##### ViewController.swift
```swift
...

var storyView: StoryView!

override func viewDidLoad() {
    super.viewDidLoad()
        
    storyView = StoryView(frame: CGRect(x: 0.0, y: 100.0, width: 320.0, height: 160.0)) //initialize StoryView
    storyView.delegate = self //set StoryView delegate
    
    view.addSubview(storyView) //add object to the view
    
    storyView.create() //running internal logic
}

// MARK: - StoryViewDelegate
// triggered when clicking on the favorites cell
func favoriteCellDidSelect()
{
    // example of displaying the favorites list screen
    // the favorites screen is displayed by the developer, and can be displayed in any way
    let storyBoard: UIStoryboard = UIStoryboard(name: "Main", bundle: nil)
    let favoritesController = storyBoard.instantiateViewController(withIdentifier: "FavoritesController")
    self.show(favoritesController, sender: self)
}
...
```

The favorites screen is created in the same way as the list of stories. `StoryView` with the parameter `favorite = true`.

##### FavoritesController.swift

```swift
class FavoritesController: UIViewController {

    var favoritesView: StoryView!
    
    override func viewDidLoad() {
        super.viewDidLoad()

        favoritesView = StoryView(frame: CGRect(x: 0.0, y: 100, width: 320, height: 160.0), favorite: true) //initialize StoryView
        favoritesView.target = self //reader display controller
        favoritesView.delegate = self //set StoryView delegate
        
        view.addSubview(favoritesView) //add object to the view
                
        favoritesView.create() //running internal logic
    }
}

extension FavoritesController: StoryViewDelegate
{
    func storyViewUpdated(storyView: StoryView, widgetStories: Array<WidgetStory>?) {
        //called when the data in the StoryView is updated
    }
    
    // called when a button or SwipeUp event is triggered in the reader
    func storyView(_ storyView: StoryView, actionWith type: ActionType, for target: String) {
       if type == .swipe { // link obtained by swipeUP action
           if let url = URL(string: target) {
               let swipeContentController = SwipeContentController()
               // passing link to controller from WebView
               swipeContentController.linkURL = url
               // opening the controller over of the reader
               storyView.present(controller: swipeContentController)
           }
       } else {
            // if the processed link leads to a screen in the application, 
            // recommend to close the reader
            storyView.closeStory {
                // processing a link, for example, to follow it in safari
                if let url = URL(string: target) {
                    UIApplication.shared.open(url, options: [:], completionHandler: nil)
                }
            }
        }
    }
    
    func storyReaderWillShow() {
        // called before the reader will show
    }
    
    func storyReaderDidClose() {
        // called after closing the story reader
    }
}
``` 
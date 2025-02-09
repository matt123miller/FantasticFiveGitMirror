
# Fantabulous Five - Prototype Mobile Game



## Introduction

This project tasked our team with collaborating on the design and development of a theoretically release worthy prototype in eight months. Working together within a multidisciplinary team, we possessed a range of skills including 3d modelling and animation, programming and game design, supporting each other throughout the process. 

This page will document the designs and planned outcome of the project, followed by an evaluation of the prototype and my role in its creation. I fulfilled the roles of programmer and game designer, this page evaluates the project from those perspectives. Where relevant I have included code snippets of interesting parts. You're more than welcome to browse the code in full by [checking the repo here](https://bitbucket.org/FantasticFourDesi1212/fantastic-four), which contains the project as of our final submitted work. All code linked from now on will be hosted on my own Github, which mirrors the Bitbucket repo our group shared.

This page will include links to Youtube videos, accessible by clicking the thumbnail like the one shown below. Please be aware they will open in this tab instead of making a new one. Please click that now!

[![welcome video here.](https://img.youtube.com/vi/rVGuKL1mfjE/0.jpg)](https://www.youtube.com/watch?v=rVGuKL1mfjE)

First, the programming team.
* Myself, Matt 
* Tish, also our Producer

The art team, responsible for creating, and texturing all our 3D models. Also, where appropriate, animating and rigging them.
* Ana
* Maytham
* Aron 

Before we began discussing ideas for the project, we knew we wanted to achieve certain goals.

* Developed in Unity using C#
* Targeting Android phones, as we each had access to Android devices
* Create immersive interactions made possible by the device format
* Use the gyroscope and microphone where possible for interesting interactions

We quickly settled on the following idea.

The game is set in the house of the (presumably evil) Witch. Bought to life by some unknown sorcery, players embark on a journey to escape the house without arousing the suspicion of the Witch. Each level takes place in a different room, each a different part of the house. The level developed for this early prototype is the Witch's study, a centre for her magical work. 

The following video was recorded by Tish and gives a quick overview of the game and each mechanic created.

[![Overview video.](https://img.youtube.com/vi/G2uLNMtMSz0/0.jpg)](https://www.youtube.com/watch?v=G2uLNMtMSz0)

Whilst I focused upon the programming and systemic aspects of the game, I was also involved heavily in the games design of mechanics and theme. Throughout the process I was primarily interested in making the player feel a certain way and aimed to design mechanics that reinforce those emotions and themes. Each video should hopefully contain some discussion about why certain mechanics were developed, the design decisionds behind individual parts of a mechanic and the design goals the mechanic should fulfill. I also created the vast majority of our LevelOne scene layout, the main game scene found after character selection.

## Technical Project Management

* Unity
* Git source control
* Train everyone in using Git and SourceTree
* Cloud Build
* Setup the project structure, build settings etc.
* Be on hand to all team members for technical support regarding Git and SourceTree
 
[![Overview video.](https://img.youtube.com/vi/l7lVipuXxNo/0.jpg)](https://www.youtube.com/watch?v=l7lVipuXxNo)

During development 2 resources were very useful for diagnosing and solving problems I created in Git. These were [Oh Shit, Git!](http://ohshitgit.com/) and [On undoing, fixing, or removing commits in git](http://sethrobertson.github.io/GitFixUm/fixup.html). These are resources providing command line instructions to fix common problems encountered when using Git. These problems are typically use error, which was definitely the case with my work!

## Code Quality, Style, Design Patterns and Software Engineering Principles

* Try to maintian good code practices and patterns
* SOLID programming
* Discussed the Singleton pattern and Liskov Substituion Principle, the basis of most other patterns.


[![Overview video.](https://img.youtube.com/vi/V30I5yyePi0/0.jpg)](https://www.youtube.com/watch?v=V30I5yyePi0)

In hindsight we should've used ```DontDestroyOnLoad()``` and hooked into the [sceneLoaded event](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager-sceneLoaded.html) instead of relying on Singleton objects and their Awake() or Start() methods. This would have simplified some of our design and implementation. I learnt to use design patterns from [Robert Nystrom (2004)](http://gameprogrammingpatterns.com/contents.html) and [Head First Design Patterns (Bates, Freeman, Sierra, Robson 2004)](http://shop.oreilly.com/product/9780596007126.do). During the video discussed [SOLID](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod), a famous mnemonic created by Robert Martin (2003). I forgot the I, which is of course the [Interface Segregation Principle](https://drive.google.com/file/d/0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi/view) and completely mistook the D from memory, which is the [Dependency Inversion Principle](https://drive.google.com/file/d/0BwhCYaYDn8EgMjdlMWIzNGUtZTQ0NC00ZjQ5LTkwYzQtZjRhMDRlNTQ3ZGMz/view)!

### How To Implement A Singleton

```csharp
// The rest of this class can be found at https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Managers/GlobalGameManager.cs
public class GlobalGameManager : MonoBehaviour
{
    private static GlobalGameManager _instance;

    // Singleton object, access this via GlobalGameManager.Instance whenever you need it.
    public static GlobalGameManager Instance
    {
        get
        {
            if (_instance == null)
            {
                _instance = GameObject.FindWithTag("GlobalGameManager").GetComponent<GlobalGameManager>();
            }
            return _instance;
        }
    }
}
```

## Asynchronous Scene Loading

This is on of the first systems I created. Finishing a foundational part of the experience early on allowed me to focus development time on other mechanics as they arose. Finishing this early also meant that I could create a solid foundation to build upon, allowing other systems to hook into this as necessary. It was intentionally written to only expose the minimum methods required to hook into the scene loading functionality.Thef ollowing video discusses the  process in detail and includes a discussion of the related system I wrote for fading the screen to and from black (though and colour could be used)

[![Overview video.](https://img.youtube.com/vi/SaFemLB8ilM/0.jpg)](https://www.youtube.com/watch?v=SaFemLB8ilM)

```csharp
https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Managers/SceneTransitionManager.cs

public class SceneTransitionManager : MonoBehaviour
{
    /*
    ...
    ...
    ...
    */

    /// <summary>
    /// Asynchronously begins loading the chosen level in the build settings
    /// Assumes that the class calling the method knows which scene it wants to go to.
    /// </summary>
    /// <param name="targetScene"></param>
    public void LoadTargetLevel(int targetScene)
    {
        if (targetScene >= SceneManager.sceneCountInBuildSettings)
        {
            // returns to main menu.
            StartCoroutine(AsyncLoadLevel(0));
        }
        else
        {
            StartCoroutine(AsyncLoadLevel(targetScene));
        }
    }

    /// <summary>
    /// Asynchronously begins loading the next level in the build settings, assuming there are levels left to load.
    /// </summary>
    public void LoadNextLevel()
    {
        int targetScene = SceneManager.GetActiveScene().buildIndex + 1;
        
        if (targetScene >= SceneManager.sceneCountInBuildSettings)
        {
            // There are no scenes left. Maybe return to main menu?
            // Handle this error however you like.
            StartCoroutine(AsyncLoadLevel(0)); // returns to the main menu

        }
        else 
        {
            StartCoroutine(AsyncLoadLevel(targetScene));
        }
    }

    public void ReloadCurrentLevel()
    {
        int targetScene = SceneManager.GetActiveScene().buildIndex;
        StartCoroutine(AsyncLoadLevel(targetScene));
    }

    private IEnumerator AsyncLoadLevel(int targetScene)
    {
        fader.BeginFadeToBlack(false);

        while (fader.fadeProgress < 0.95)
        {
            yield return null;
        }
                
        fader.ToggleLoadingUIOn(true);
        
        AsyncOperation asyncLoad = SceneManager.LoadSceneAsync(targetScene);

        while (!asyncLoad.isDone)
        {
            // Here you put your loading screen code.
            fader.UpdateSlider(asyncLoad.progress);

            yield return null;
        }
        
        // You don't need to turn the text and slider back off or call BeginFadeToClear() here as the old scene will now be destroyed.
        // The new scene that was just loaded asynchonously will replace it and should have a SceneManager object in it to handle fading etc.
    }
}
```
```csharp
https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Camera/ScreenFade.cs

public class ScreenFade : MonoBehaviour
{
    /*
    ...
    ...
    ...
    */

    void Start()
    {
        // Performed in Start to allow all variables to be cached first.
        ToggleLoadingUIOn(false);
        BeginFadeToClear();
        SceneTransitionManager.Instance.fader = this;
    }

    public void ToggleLoadingUIOn(bool set)
    {
        _loadingText.gameObject.SetActive(set);
        _loadingText.enabled = set;
        _loadingSlider.gameObject.SetActive(set);
        _loadingSlider.enabled = set;
        _loadingSliderImage.gameObject.SetActive(set);
        _loadingSliderImage.enabled = set;
    }

    public void UpdateSlider(float progress)
    {
        _loadingSlider.value = progress;
        _loadingText.color = new Color(_loadingText.color.r, _loadingText.color.g, _loadingText.color.b, Mathf.PingPong(Time.time, 1));
    }

    public void BeginFadeToBlack(bool fadeToClearFlag)
    {
        GlobalGameManager.Instance.ToggleUI(false);
        StartCoroutine(FadeToBlack(_fadeStartTime, fadeToClearFlag));
    }

    public void BeginFadeToClear()
    {
        StartCoroutine(FadeToClear(_fadeStartTime));
    }

    private IEnumerator FadeToClear(float fadeStartTime)
    {
        _fadingImage.enabled = true;
        fadeStartTime += 1;

        for (float f = fadeStartTime; f >= 0; f -= ((0.1f * _fadeMultiplier) * Time.deltaTime))
        {
            fadeProgress = f;
            Color c = _fadingImage.color;
            c.a = f;
            _fadingImage.color = c;
            yield return null;
        }

        _fadingImage.enabled = false;


        if (turnUIOnAfter)
        {
            GlobalGameManager.Instance.ToggleUI(true);
        }
    }

    private IEnumerator FadeToBlack(float fadeStartTime, bool fadeToClearFlag)
    {
        _fadingImage.enabled = true;
        fadeStartTime += 1;

        for (float f = 0f; f <= fadeStartTime; f += ((0.1f * _fadeMultiplier) * Time.deltaTime))
        {
            fadeProgress = f;
            Color c = _fadingImage.color;
            c.a = f;
            _fadingImage.color = c;
            yield return null;
        }
        // Included in the unlikely event that scenes will fade to black only. 
        if (fadeToClearFlag)
        {
            BeginFadeToClear();
        }
        else
        {

        }
    }
}
```

I also created a simple prefab that will load the next level when players come into contact with it. It's bright pink to aid testing but the final version would of course be invisible, relying only on the trigger box.

[![https://gyazo.com/8c4fd1075ec9c0f68fc44ff6ce0730cb](https://i.gyazo.com/8c4fd1075ec9c0f68fc44ff6ce0730cb.png)](https://gyazo.com/8c4fd1075ec9c0f68fc44ff6ce0730cb)

## A discussion of multithreading versus coroutines

[![Overview video.](https://img.youtube.com/vi/P0kKQ6deo-I/0.jpg)](https://www.youtube.com/watch?v=P0kKQ6deo-I)

## User Interface
This includes the in game HUD, the main menu and strategies for creating and maintaining sane UI practices. 

* Focus on easy to use, extendable UI.
* Take advantage of panels for their organisational benefits, simplicity and flexibility.
* Multiple screen resolutions considered.

[![Overview video.](https://img.youtube.com/vi/_euQi67Bq6A/0.jpg)](https://www.youtube.com/watch?v=_euQi67Bq6A)


## Gyroscope Functionality


This video discusses quaternions and rotating the camera with the users phone, one of my favourite mechanics for both it's mechnical utility and novelty.


[![Rotating the phone.](https://img.youtube.com/vi/eXCnm6KLY2w/0.jpg)](https://www.youtube.com/watch?v=eXCnm6KLY2w)

```csharp
// found at the rest of this class at
// https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/DeviceScripts/DeviceRotation.cs
public static class DeviceRotation
{
    /*
    ...
    ...
    ...
    */

    // This is the real magic!
    private static Quaternion ReadGyroscopeRotation()
    {
        return new Quaternion(0.5f, 0.5f, -0.5f, 0.5f) * Input.gyro.attitude * new Quaternion(0, 0, 1, 0);
    }
}
```

## Free Rotating Camera


```csharp
//https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Camera/TiltCamera.cs
    [RequireComponent(typeof(Camera))]
    public class TiltCamera : MonoBehaviour
    {
        
        [SerializeField]
        string tiltString;
        // This class is applied to the camera, which is a child of the character controller
        // As such all rotations are localRotation, in relation to the parent object (character controller);
        [Tooltip("The speed the camera will return back to normal, higher is quicker."), Range(0.1f, 1)]
        public float lerpMultiplier = 0.5f;
        public AnimationCurve curve;
        public bool tilting = false;
        
        private Quaternion _resetRotation;
        private Quaternion _negatePhoneRotation;
        private Quaternion _currentRotation;
        private Quaternion _desiredRotation;


        // Use this for initialization
        void Start()
        {
            // Grab the current rotations as starting points.
            _resetRotation = transform.localRotation;
            _currentRotation = transform.localRotation;
            _desiredRotation = transform.localRotation;
            

            // Will this simple fix solve the first tilt on mobile?
            Input.gyro.enabled = true;
        }

        // Update is called once per frame
        void Update()
        {
            // Phone Input.
            if (CrossPlatformInputManager.GetButton(tiltString))
                TiltPhone();
            else if (CrossPlatformInputManager.GetButtonUp(tiltString))
                ResetRotation();
            
        }

        /// <summary>
        /// This rotates the camera to match the orientation of the phone
        /// </summary>
        public void TiltPhone()
        {
            // Helps to stop us getting caught in a bad change of states. Resets in ResetRotation().
            if (!tilting)
            {
                // Save our current rotation before doing anything else. This is where we'll return later.
                _resetRotation = transform.localRotation;
                // This is the opposite of the phones rotation when entering the tilt mode. 
                // We are aiming to negate by this value later.
                _negatePhoneRotation = Quaternion.Inverse(DeviceRotation.GetRotation());
                tilting = true;
                //debugText.enabled = true;
            }
            
            // None! This is 1 rotation offest by another. No idea how it works.
            // Why do you offset the right by the left? Who knows. It's magic.
            _desiredRotation = _negatePhoneRotation * DeviceRotation.GetRotation();

            // Set rotation at the end, assumes _desiredRotation has been set in 1 of the above if statements.
            transform.localRotation = _desiredRotation;
            // Cache it back into the conveniently shorter variable name.
            _currentRotation = transform.localRotation;
            //debugText.text = _desiredRotation.ToString();
        }

        public void ResetRotation()
        {
            // Include anything special that needs to be reset separate to the coroutine.
            tilting = false;
            //debugText.enabled = false;

            StartCoroutine("LerpCameraBack");
        }

        // This works perfectly in all my test so far.
        private IEnumerator LerpCameraBack()
        {
            // How do I find the return angle and rotate back to normal? So scared.
            // Quaternions are the devil
            _currentRotation = transform.localRotation;
            // We will return to this point. desiredRotaiton doesn't change throughout the whole coroutine.
            _desiredRotation = _resetRotation;
            float lerpCompletion = 0;
            
            while (lerpCompletion < 0.99) 
            {
                lerpCompletion += Time.smoothDeltaTime * lerpMultiplier;
                var curveValue = curve.Evaluate(lerpCompletion);

                transform.localRotation = Quaternion.Slerp(_currentRotation, _desiredRotation, curveValue);

                yield return null;
            }

            // The reset rotation has now finished, set any states and variables that need setting.

            // Sets the rotation back, compensate for any remaining angle changes.
            transform.localRotation = _desiredRotation;
        }
    }
```

In the code above I use ```Quaternion.Slerp(a,b,c)```, which is basically the same as the normal ```Mathf.lerp(a,b,c)```, except it performs spherical interpolation to find the rotation. The theory is the same though, you want to know the value that is ```c```% between ```a``` and ```b```. Many developers are using lerp wrong however, which is understandable as the [Unity documentation](https://docs.unity3d.com/ScriptReference/Mathf.Lerp.html) labels the method signature as ```Lerp(float a, float b, float t)```. Because lerp is often used in the context of an update loop where we often use ```Time.deltaTime``` it's a sensible leap to equate the argument ```t``` with time. It's very common to see code such as this: 

```csharp
transform.position = Vector3.lerp(transform.position, target.position, Time.deltaTime)
```

This is unwise however, as ```transform.position``` will APPROACH the given ```target.position``` but it won't actually reach it. [This post](http://www.rorydriscoll.com/2016/03/07/frame-rate-independent-damping-using-lerp/) by Rory Driscoll argues that developers who make this mistake are actually performing something very similar to Exponential Decay, whether they are aware of it's existence or not. Instead, [this forum post](http://www.blueraja.com/blog/404/how-to-use-unity-3ds-linear-interpolation-vector3-lerp-correctly) by user BlueRaja explains the correct use of lerp in a clearer fashion than the documentation. 


In the video above discussing quaternions and phone rotation I don't describe the benefits of [animation curves](https://docs.unity3d.com/ScriptReference/AnimationCurve.html). ```AnimationCurve``` can be utilised for all manner of things that require changing a float value over time. 

[![https://gyazo.com/b12ed89ba2d704cb963b50ccf8f486cd](https://i.gyazo.com/b12ed89ba2d704cb963b50ccf8f486cd.png)](https://gyazo.com/b12ed89ba2d704cb963b50ccf8f486cd)

Declaring a public AnimationCurve will allow users to create complicated and detailed curves for a variety of reasons.

```csharp
public AnimationCurve curve;

void Start() {
    curve = GetComponent<AnimationCurve>();
}
```

These curves represent a mathematical formula, hidden from the user, and plot the Y value as X changes. This formula can be accessed using ``` curve.Evaluate(float) ```. This returns the value of Y at the provided X argument value. This was employed to create a smooth transition  between the rotation the player has provided when moving their phone around and the rotation the game would like to return to. This allowed me to avoid the issues described by Driscoll and follow the advice of BlueRaj's forum post to get the most out of lerping.

```csharp
while (lerpCompletion < 0.99) 
{
    lerpCompletion += Time.smoothDeltaTime * lerpMultiplier;
    var curveValue = curve.Evaluate(lerpCompletion);

    transform.localRotation = Quaternion.Slerp(_currentRotation, _desiredRotation, curveValue);

    yield return null
}
```

Using an ```AnimationCurve``` is much more flexible and elegant than creating a complex loop wherein a programmer writes a formula themselves and uses ```Time.deltaTime```. ```AnimationCurve``` is also more accessible to designers, as it doesn't require them to read or write code or ask an engineer to change the formula.

## Balance Beam


[![Magical Porttrait System.](https://img.youtube.com/vi/y2RY-GC1FVQ/0.jpg)](https://www.youtube.com/watch?v=y2RY-GC1FVQ)

## Immersive Environmental Interaction

### Magical Portrait System

[![Magical Porttrait System.](https://img.youtube.com/vi/fJjtDoapKW8/0.jpg)](https://www.youtube.com/watch?v=fJjtDoapKW8)

This feature was inspired by the paintings and pictures found in the Harry 
Potter universe and is intended to be a useful way of creating environmental storytelling.

[![https://gyazo.com/c7012c7d58511b1e05ff0c461339e68e](https://i.gyazo.com/c7012c7d58511b1e05ff0c461339e68e.gif)](https://gyazo.com/c7012c7d58511b1e05ff0c461339e68e)

Taken from the movie Harry Potter and the Prisoner of Azkaban (2004) directed by Alfonso Cuarón and distributed by Warner Bros. Pictures.

```csharp
// https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Level%20Design/MovingPortraits.cs
public class MovingPortraits : MonoBehaviour
{
    // TODO Maybe turn the render camera off when it's far away from the player for optimisation?
    public Camera _renderCamera;
    private Transform _player;
    private float _invSqrColliderDistance;

    public Transform pictureTexture;

    [Range(0, 5)]
    [Header("Edit me!")]
    public int textureScale = 3;

    [Header("Not me!")]
    public int texWidth;
    public int texHeight;

    void Awake()
    {
        // Get my components
        _renderCamera = GetComponentInChildren<Camera>();

        // Create and set the render texture.
        RenderTexture rTex = new RenderTexture(texWidth, texHeight, 0);
        _renderCamera.targetTexture = rTex;
        pictureTexture.GetComponent<MeshRenderer>().material.mainTexture = rTex;

    }

    void Start()
    {
        
    }
    
    // This is called whenever you change a value in the inspector
    // Therefore it's used to alter the resolution width and height as users change the slider value.
    void OnValidate()
    {
        texWidth = (int)Mathf.Pow(2, 5 + textureScale);
        texHeight = (int)Mathf.Pow(2, 5 + textureScale);

    }
}
```

### Tapping Objects

[![Magical Porttrait System.](https://img.youtube.com/vi/ShIh0fCPSZI/0.jpg)](https://www.youtube.com/watch?v=ShIh0fCPSZI)

The video was unclear, when showing the set of "drawers" in our main scene, it was using the class and functionality I described in the previous scene. The code is shown below.

```csharp
// Find the class here https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Touch%20Interaction/Tappable.cs
// This ensures that whatever object this class is attached to has an Animator and EventTrigger.
// This is useful to reduce the knowledge and memory requirements for others.
[RequireComponent(typeof(Animator))]
[RequireComponent(typeof(EventTrigger))]
public class Tappable : MonoBehaviour , ITouchInteractive {

	public Color newColour = new Color(0,1,0);
    private Color _oldColour;

    public Animator animator;
    public string triggerName;
    public bool withinRange = false;

	// Use this for initialization
	void Start () {

        _oldColour = GetComponent<MeshRenderer>().material.color;
        animator = GetComponent<Animator>();
        if(GetComponent<SphereCollider>())
            GetComponent<SphereCollider>().radius = 1;
	}

    private void OnTriggerEnter(Collider other)
    {
        if (!other.CompareTag("Player"))
            return;

        withinRange = true;
    }

    private void OnTriggerExit(Collider other)
    {
        if (!other.CompareTag("Player"))
            return;

        withinRange = false;
    }

    public void Interact()
    {
        if (!withinRange)
            return;

        print("Tap that");
        GetComponent<MeshRenderer>().material.color = newColour;

        animator.SetTrigger(triggerName);
    }

	public void Interact(Vector3 fingerPosition)
    {
        // Not showing in the EventHandler inspector?
	}

    public void FinishInteraction()
    {
        if (!withinRange)
            return;

        print("Finish tap");
        GetComponent<MeshRenderer>().material.color = _oldColour;
    }
}
```

Before deciding to use Event Triggers, raycasting was used as described in the video. It was hoped to have a variety of different interactions be powered by tapping and so I wrote a small set of systems to accomplish this using inheritance and polymorphism. [You can find the relevant code here](https://github.com/matt123miller/FantabulousFiveGitMirror/tree/master/Assets/Scripts/Touch%20Interaction), though the ```DragRigidbody.cs``` file was provided by Unity in the Standard Assets package.

## Artificial Intelligence

The code for the AI is quite long, so I won't include snippets here. However please feel free to read the code [for the controller](https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Character/AICharacterControl.cs) and for the [AIs vision.](https://github.com/matt123miller/FantabulousFiveGitMirror/blob/master/Assets/Scripts/Character/AISight.cs) The video below discusses the AI in detail and it's design goals, whilst below that are 3 GIFs showcasing certain actions the AI can take.


Appologies for the long video, but there is a lot to discuss.

[![Magical Porttrait System.](https://img.youtube.com/vi/Xw48p0yyH0E/0.jpg)](https://www.youtube.com/watch?v=Xw48p0yyH0E)

Patrolling
[![https://gyazo.com/2bbfb0b82f5a878353e967e299d46e4c](https://i.gyazo.com/2bbfb0b82f5a878353e967e299d46e4c.gif)](https://gyazo.com/2bbfb0b82f5a878353e967e299d46e4c)

Spotted, chasing, then attack.
[![https://gyazo.com/1d9ad23022f7e5d17f8f545215442ac9](https://i.gyazo.com/1d9ad23022f7e5d17f8f545215442ac9.gif)](https://gyazo.com/1d9ad23022f7e5d17f8f545215442ac9)

The witch entering, which scares the AI and makes them run away.
[![https://gyazo.com/f8abb5333578853a7b760726ea17dead](https://i.gyazo.com/f8abb5333578853a7b760726ea17dead.gif)](https://gyazo.com/f8abb5333578853a7b760726ea17dead)


## Known Problems


- The player randomly teleports to certain locations in the map - thought to be a problem with the grab trigger system with the new assets
- When reloading to a checkpoint, the invisible wall that handled leaving the spawn point is gone and won't turn back on.
- New umbrella model does not attatch to new characters properly - appears more like as shield than an umbrella
- New models for some reason will not co-operate with rope balancing system - was fine using ethan model
- Camera lerping from idle to travelling positions no longer return to idle position with the new characters
- The obj script applied on the player adds noise when performing a small jump - should be negated from the system
- Camera clips through objects. 
    - There is a Unity provided free camera rig that solves this, however because we lerp the camera between various positions depending on the players actions it wouldn't work.
- Texture on the chests of drawers cause white flickering dots
- Gap in the sofa model causes strange white pixels to appear between the base and the right arm 
- Green colour change on the tappable drawers does not appear because of the yellow outline shader
- Hitting a checkpoint then using the level skip sometimes causes the player to fall out of the world in the next scene
	- This could be fixed by checking when instantiating whether a checkpoint is in scene at the same position and if not then load to spawn point instead.
- The page turning book experiences Z fighting textures the pages get into the same position
- Patrolling AI relies on there being waypoints assigned in the inspector. If there are none assigned the AI walks on the spot and creates runtime errors.


The team had agreed early in the project with our project supervisor that 15/5/17 would be an ingroup deadline, simulating a release date. This deadline date for our group meant that no work would be performed following 15/5/17, giving us ample time to write other documentation for the project. Once this date was agreed I ensured that I provided encouragement for each member of our group. This included reminders of the importance to import work into Unity early and often, and to learn the workflow required. I also supported my fellow team members with any issues they may encounter during the project process.

Unfortunately, during the late evening of 15/5/17 the art team made our producer Tish, as well as myself, aware that they were unprepared for the previously agreed deadline. It was eventually agreed to push back release by 2 days. The Git logs (which can be [viewed here](https://1drv.ms/x/s!AkUigMlwGE0g2hmABGItT0vaFqPW) exported to an Excel file) go to show they made 20 commits between them over the course of 4 days – and all of those after our original, mutually agreed upon release date. What was uploaded appeared rushed which included: texturing all the existing assets, finishing any new assets they’d developed and importing the 2 playable characters and 2 AI characters into Unity with their textures and animations.

This last-minute rush due to poor-time management and organisation not only impacted the art teams work but also the programmers. Tish and myself were forced to rapidly try and incorporate all of the new assets produced by the art team into the existing project and mechanics that we had developed within a few hours each day. Additionally, due to the art team not able to use Unity aside from importing assets, the programmers had to learn to use art and animation tools ourselves. This led to an unnecessary blending of roles as the programmers adopted art-team skills, skills we learnt by googling the same things they had every opportunity to search for throughout the last 8 months.

It is felt that the entire experience of the last several days of the project could have been avoided if the art team had sufficiently imported early and often, shared work via Git or even amongst themselves. Nonetheless we all did our best, yet the final product has suffered due to time management and organisation failings from the art team.

## Sources used

I acknowledge the use of the following sources when my own development for this prototype:

* [Unity documentation](https://docs.unity3d.com/Manual/index.html)

* [Microsoft's C# documentation](https://docs.microsoft.com/en-gb/dotnet/)

* Code for reading the [current rotation of the phone]() was provided by [codingChris on the Unity forums](http://answers.unity3d.com/comments/842596/view.html)

* [Texture pack used for one wooden texture.](https://opengameart.org/content/free-texture-resource-pack-wood-structure-walls-and-textile?page=1)

* [5 dark wood textures](https://opengameart.org/content/dark-wood-textures)

* [Our music](https://www.dl-sounds.com/royalty-free/puzzle-theme1/)

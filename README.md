# UI Testing Espresso - Android
##### Tutorial: CodingWithMitch - UI Testing for Beginners (Espresso and androidx.test)

## Purpose: 
The purpose of this code base is to explore how UI Testing is done in Android using the Espresso library.

##### Firebase Test Lab:
You can run your tests on physical or virtual devices hosted by Google. This is done through Firebase Test Label. There is an associated cost.


## Setup
### Dependencies
Check for new version updates. 

```
    // Espresso
    def androidx_test_espresso = "3.1.0"
    androidTestImplementation "androidx.test.espresso:espresso-core:$androidx_test_espresso"

    // androidx.test
    def androidx_test = "1.1.0"
    androidTestImplementation "androidx.test:runner:$androidx_test"
    androidTestImplementation "androidx.test:core:$androidx_test"
    androidTestImplementation "androidx.test.ext:junit-ktx:$androidx_test"
    
```

### Disable animations
You need to disable animations on the simulator/device as they can disrupt the UI test.
Device: System > Developer Options > Drawing
- Window Animation Scale
- Transition Animation Scale
- Animator Duration Scale


### Navigating 
app > src > androidtest > java > com. > testfile

Note: If you cannot see the src directory, go to the top drop down menu and change it from Android to Project.

### Adding Testing Files
The quickest way to add a test file is to go to the class that you want to test, right click on the class name, click generate, then select test.
- AndroidTestFolder = UITesting
- TestFolder = UnitTesting

You need to add the following library dependencies and the runner. Sometimes they are added for your, sometimes they are not.
```
import androidx.test.internal.runner.junit4.AndroidJUnit4ClassRunner;
import org.junit.runner.RunWith;

@RunWith(AndroidJUnit4ClassRunner.class)
class {}
```

## Testing View Visibility and Properties:

#### Activity Scenario:
ActivityScenario provides APIs to start and drive an Activity's lifecycle state for testing. It is highly recommended you call the .close() method after the test has finished. ActivityScenario doesn't always clean up device state and may leave the acitivity running after the test finishes.

Testing if UIElements are in view:
```
    @Test
    public void test_isActivityInView() {
        ActivityScenario<MainActivity> scenario = ActivityScenario.launch(MainActivity.class);
        onView(withId(R.id.main)).check(matches(isDisplayed()));
    }
```

Testing if TextView text is correct:
```
    @Test
    public void test_IsTitleTextDisplayed() {
        ActivityScenario<MainActivity> scenario = ActivityScenario.launch(MainActivity.class);
        onView(withId(R.id.activity_main_title)).check(matches(withText(R.string.text_mainactivity)));
    }
```

You can set up an ActivityScenarioRule in the global scope of the class. This will be initialised at the start of every test and destroyed at the end of every test. This means that you don't need to initiliase an ActivityScenario at the start of every test.
```
    @Rule
    public ActivityScenarioRule<SecondaryActivity> activityScenario = new ActivityScenarioRule(SecondaryActivity.class);
    
    @Test
    public void test_isActivityInView() {
        onView(withId(R.id.secondary)).check(matches(isDisplayed()));
    }
```


## Testing Navigation
The method you need to be aware of is .perform(click()). As the method name implies, it will click on the view that it is invoked upon. Launching the SecondaryActivity to see if the pressBack() method return to the MainActivity will result in a failure as the MainAcitivity will not be in the navigation stack.

```
    //Navigation Testing
    @Test
    public void test_navSecondaryActivity() {
        ActivityScenario<MainActivity> scenario = ActivityScenario.launch(MainActivity.class);
        onView(withId(R.id.button_next_activity)).perform(click());
        onView(withId(R.id.secondary)).check(matches(isDisplayed()));
        scenario.close();
    }

    @Test
    public void test_backPress_toMainActivity() {
        ActivityScenario<MainActivity> scenario = ActivityScenario.launch(MainActivity.class);
        onView(withId(R.id.button_next_activity)).perform(click());
        pressBack();
        onView(withId(R.id.main)).check(matches(isDisplayed()));
    }
```

## Test Suites
Allow you to bundle up different kinds of test into groups. This makes it easier to run multiple test classes. In the example below, we have bundled up the MainActivityTest and the SecondaryActivityTest into on ActivityTestSuite class. When we want to run our 'Activity Tests', we can run the AcitivityTestSuite class.

```
@RunWith(Suite.class)
@Suite.SuiteClasses({MainActivityTest.class, SecondaryActivityTest.class})
public class ActivityTestSuite {
}

```


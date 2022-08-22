# LDSO Code Review

## Author
Heejoon Lee(heejoon1130@gmail.com)

## /
### src
#### Setting.cc
Contains global variables used as a configuration value.
Variables used for the setting start with the prefix `setting_`.
Contains patterns.

Variables used without declaration in other files are usually defined in `Setting.cc`.

#### Front-end
##### FullSystem.cc

The `FullSystem` class is defined here.

* Constructor
    1. Get a pointer to `ORBVocabulary` as an argument
    2. Initialize some variables(**TODO**)
    3. Start the mapping thread(`mappingThread`)
        * Init function is `FullSystem::mappingLoop` method with the current class instance as the context
    4. Initialize a `PixelSelector` object
    5. Initialize a selection map
    6. If loop closing is enabled, initialize a `LoopClosing` object

* blockUntilMappingIsFinished
    * Wait until the mapping thread(`mappingThread`) is finished. If loop closing in enabled, terminate loopClosing. Update globalMap.

##### PixelSelector2.cc

The `PixelSelector` class is defined here.

* Constructor
    1. Get width and height as arguments.
    2. Create a random pattern of size (width * height). Each pattern is a random byte.
    3. Set currentPotential to 3.
    4. Create gradHist, ths, and thsSmoothed(size is proportional to width * height).

##### LoopClosing.cc

The `LoopClosing` class is defined here.
Mutex `mutexKFQueue` is used to lock access to `KFqueue` which stores keyframes.


* Constructor
    1. Start a new thread called mainLoop with its `Run` method.
    2. Create idepthMap of float[width * height]

* Run
    1. Get the oldest keyframe from `KFqueue`
    2. Push the popped keyframe to `allKF`
    3. Compute the bag-of-words of the keyframe.
    4. If a loop has been detected, start the pose graph optimization thread.
    5. Sleep for 5000us, then go back to 1. 

* InsertKeyFrame
    1. Push the given `Frame` object to `KFqueue`(TODO: What is a `Frame` object?)



### Examples
### run_dso_kitti.cc 
The `main` function for *Kitti* dataset.

1. Check for any setting conflicts
2. Parse arguments
    * Step 1 and 2 should be reversed?
3. Initialize an `ImageFolderReader` with KITTI dataset, source path and calibration file
4. Call setGlobalCalibration(**TODO**)
5. Set start, end index and increment value
6. Initialize an `ORBVocabulary`(**TODO**) and load vocabulary from vocpath
7. Initialize a `FullSystem` with the vocabulary loaded in step 6
8. Set fullsystem's gamma function and linearize operation.
9. Initialize a `PangolinDSOViewer` and set it as the fullsystem's viewer
10. Run the LDSO thread
    1. For each image, add its ID and its timestamp to their respective vectors:
        * The timestamp of the first image is always 0
        * The timestamp of subsequent images are scaled by `playbackSpeed`
    2. If `preload` is set, add an `ImageAndExposure *` of each image to the vector
    3. Start timer
    4. For each image in the ID vector:
        1. If `FullSystem` is not initialized(**TODO**), restart the timer
        2. If the image has been preloaded, get the image from `ImageAndExposure *` vector. Otherwise, load the image.
        3. Skip frame settings(**TODO**)
        4. Add frame to the full system
        5. if full system's initalization failed or full reset is request, reset the full system
        6. If the full system is lost, terminate the loop
    5. Wait until the full system is finished


11. Run the viewer
12. Wait for the LDSO thread to finish
13. End


### DatasetReader.h
#### class ImageFolderReader
* Members
    * timestamps: A vector of double type containing the timestamp of each image
    * files: A vector of string type containing the file path of each image

* Constructor
    1. Get dataset type, path, calibration file path, gamma file path and vignette file path as input. 
    2. Get `undistorter` for the specified calibration file, gamma file and vignette file. Set original width/height and width/height using the `undistorter`.
    3. Load the appropriate `loadTimeStamp` function for the specified dataset type

* loadTimestampsKitti
    1. Open the text file containing timestamps
        * The path to the text file should be: `path/times.txt`
    2. Read each line and add it to the vector `timestamps`
        * Each line should be a number of type double indicating a time value
    3. Get image file paths and add it to the vector `files`

* getNumImages: Return the size of `files` vector

* getTimestamp: Return the timestamp of the given index.

* getImage: Return a pointer to an `ImageAndExposure` object created from undistorted image of the given ID(**TODO**)



## TODOs
* `FLAGS_` global variable definition(run_dso_kitti.c)

## Reference
* shared_ptr: 
* boost::format: Construct a format string where arguments for each specifier can be fed into later with the % operator.
    ex) boost::format fmt("%s/image_0/%06d.png");
        std::cout << fmt % path % i << std::endl;

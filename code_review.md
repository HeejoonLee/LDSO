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
10. Run the main thread(**TODO**)
11. Run the viewer thread
12. Wait for the main thread to finish
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


## TODOs
* `FLAGS_` global variable definition(run_dso_kitti.c)

## Reference
* shared_ptr: 
* boost::format: Construct a format string where arguments for each specifier can be fed into later with the % operator.
    ex) boost::format fmt("%s/image_0/%06d.png");
        std::cout << fmt % path % i << std::endl;

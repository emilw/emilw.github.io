---
author: emilw
comments: true
date: 2014-04-09 16:10:22+00:00
#layout: post
slug: shared-staticuniversal-library-management-in-xcode
title: Shared static library management in Xcode
wordpress_id: 125
categories:
- C++
- Xcode
---

**Intro**
In the work of creating a platform independent engine to a game, it was needed to create a library to be able to reuse the engine code on:



	
  * Iphone(arm64, armv7, armv7s)

	
  * Mac OS X(x86_64)

	
  * Iphone simulator(i386)


To get this working there are different possibilities to get it up and running:

	
  * Using a static(universal) library(Linux/Unix/Mac: *.a/Windows: *.lib) = Is hardly linked to the software that uses it.([http://www.ilkda.com/compile/Static_Versus_Dynamic.htm](http://www.ilkda.com/compile/Static_Versus_Dynamic.htm)

	
  * Using dynamic library(Linux/Unix/Mac: *.dynlib/Window: *.dll). = Can be reused by others, not bundled with the end software. See above link

	
  * Using a submodule structure in Git retrieving the code from there and by that, keep maintenance of the engine at one place. = Just a different way to include the code in the output. Same as one but with the full source included.


I selected number one.

**This is what i did**

Creation of a new project for the library:



	
  * New workspace, mainly to be able to use Xcode as "multi instances"

	
  * New project, output type library

	
  * Build the project, output is usually under: /Users/User/Library/Developer/Xcode/DerivedData/TrafficEngine-draogbhagfycjbcujyfmmminmoey/Build/Products/Debug/MyOutput.
(I find it quite annoying that it's hard to get here through the Finder). Use Go -> /Users/User/Library to navigate there.

	
  * In the consuming project, go to Project->Framework or if you are in a C/C++ project go to Project settings->Target->Build Phases->Link Binary with Libraries.
(It might be hard to select the library due to that it's not visible in the select other framework dialog picker. I did a drag and drop from finder into Xcode, that worked nice.

	
  * To find the headers in the consuming project, go into project settings and add the path to your lib in the "Header search Paths". Add it hard coded. I think that the "real" ones that are installed in the Libraries/Frameworks.


With the above configuration i was able to produce a library for a given architecture. It will not get me to the goal of having one lib for Iphone Simulator and the Iphone, that makes it quite useless for iterative testing of the engine from different platforms. The solution to this is universal library. The idea is to include all of the builds to one library, e.g. both x86 and arm7 in the same library. This means that one it's enough with one build and from the consumer point of view they can use the same library file.

Where i ended up then, was to do the following:

	
  * Add an additional Target to the project, select Aggregate

	
  * Add a new step in the Build phase called Run script

	
  * Add the following script:


`
# define output folder environment variable
UNIVERSAL_OUTPUTFOLDER=${BUILD_DIR}/${CONFIGURATION}-universal`

# Step 1. Build Device and Simulator versions
echo "Building iphoneos"
xcodebuild -target TrafficEngine ONLY_ACTIVE_ARCH=NO -configuration ${CONFIGURATION} -sdk iphoneos BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}"
echo "Building iphonesimulator"
xcodebuild -target TrafficEngine -configuration ${CONFIGURATION} -sdk iphonesimulator BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}"
#xcodebuild -target TrafficEngine -configuration ${CONFIGURATION} -sdk macosx -arch x86_64 BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}"

echo "Building done!"
# make sure the output directory exists
echo "Creating output folder for universal lib"
mkdir -p "${UNIVERSAL_OUTPUTFOLDER}"

# Step 2. Create universal binary file using lipo
lipo -create -output "${UNIVERSAL_OUTPUTFOLDER}/lib${PROJECT_NAME}.a" "${BUILD_DIR}/${CONFIGURATION}-iphoneos/lib${PROJECT_NAME}.a" "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/lib${PROJECT_NAME}.a"

# Last touch. copy the header files. Just for convenience
cp -R "${BUILD_DIR}/${CONFIGURATION}-iphoneos/include" "${UNIVERSAL_OUTPUTFOLDER}/"
Remember to check the project configuration, e.g. architectures available etc. Your project settings can limit this script due to that it reads the default from that config.

It was also helpful to get a full list of all the variables in the build process:



    
    <code>xcodebuild -project myProj.xcodeproj -target "myTarg" -showBuildSettings</code>




Warning, when doing some changes to the projects etc. and fiddling with the file Debug output paths etc. i saw that Xcode did not update the new paths to the lib correctly. In project settings->Target->Search Paths, the old path was still in the list. So it looks like if you remove a lib, the search path still stays for the one you removed.
E.g. Debug was still there, even though i removed the lib in debug and replaced it with the one in Debug-Universal.



**Remember:**

- Set the target build used in the script to have Only active architecture set to No.
- Use lipo -info libMyLib to get information about what architectures are in the library

**References**



	
  * [http://wiki.genexus.com/commwiki/servlet/hwiki?Creating+an+Universal+Library+for+iOS](http://wiki.genexus.com/commwiki/servlet/hwiki?Creating+an+Universal+Library+for+iOS)

	
  * [https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/xcodebuild.1.html](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/xcodebuild.1.html)

	
  * [http://www.raywenderlich.com/41377/creating-a-static-library-in-ios-tutorial](http://www.raywenderlich.com/41377/creating-a-static-library-in-ios-tutorial)

	
  * [http://www.blog.montgomerie.net/easy-xcode-static-library-subprojects-and-submodules](http://www.blog.montgomerie.net/easy-xcode-static-library-subprojects-and-submodules)

	
  * [http://slidetorock.com/blog/using-a-static-library-in-xcode-4.html](http://slidetorock.com/blog/using-a-static-library-in-xcode-4.html)



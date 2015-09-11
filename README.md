# Android SDK Plugin for SBT #

[![Join the chat at https://gitter.im/pfn/android-sdk-plugin](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/pfn/android-sdk-plugin?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Current version is 1.4.15

## Description ##

This is an easy-to-use plugin for existing and newly created android
projects.  It is tested and developed against 0.13.5+.

The plugin supports normal android projects and projects that reference
library projects. 3rd party libraries can be included by placing them in
`libs` as in regular projects, or they can be added by using sbt's
`libraryDependencies` feature. This build setup is 100% compatible with
Google's own `gradle` build system due to the use of the same underlying
`com.android.tools.build:builder` implementation.

NOTE: proguard 5.1 does not like old versions of scala. Projects that wish
to use Proguard 5.1 and Scala should use `scalaVersion := "2.11.5"` or newer.
For compatible scala projects and java-based projects which wish to use
proguard 5.1 (to fix issues around generic types being removed from
base-classes) a workaround is to add a local file, `project/proguard.sbt`,
containing:
`libraryDependencies += "net.sf.proguard" % "proguard-base" % "5.1"`.
See [proguard bug #549](https://sourceforge.net/p/proguard/bugs/549/) and
[SI-8931](https://issues.scala-lang.org/browse/SI-8931)

NOTE: support-v4 22.2.x triggers compilation errors, see #173 and
[SI-7741](https://issues.scala-lang.org/browse/SI-7741)

## Support and Help ##

The first line of support is reading this README, beyond that, help can be
found on the #sbt-android IRC channel on Freenode

## New features in 1.5.x ##

* `1.5.0`:
  * build outputs completely refactored, `genPath`, `binPath`, and other
    settings have been removed

## New features in 1.4.x (last version: 1.4.15) ##

* `1.4.14`:
  * Multi-project multi-dex fixes for OSX
  * Parallelized pre-dexing
* `1.4.13`:
  * `android:install` and `android:uninstall` now respect `android:allDevices`
  * add `android:clean` for clearing app data from device
* `1.4.11`:
  * include `aars` when generating `proguardConfig`
  * fix `mainDexClasses` on non-Windows platforms
  * implement `android.GradleBuild` in `"com.hanhuy.sbt" % "android-gradle-build" % "0.2"`
    * Automatic building from gradle projects without having to configure SBT
    * See the [gradle-build test cases](sbt-test/gradle-build)
      for an example of usage
    * Known issue: transitive aar libraries that are specified in both library
      and app modules will fail to build
  * use `AutoPlugin` triggers
* `1.4.10`:
  * Set `autoScalaLibrary` based on presence of scala sources
  * Set `minSdkVersion` and `targetSdkVersion` based on `platformTarget`
    unless explicitly specified in `AndroidManifest.xml`, previously defaulted
    to 1
  * Properly skip `dex` if files unchanged.
  * `retrolambdaEnable` is set false by default
  * More refactoring to support Protify, see
    [live-code demo](https://youtu.be/4MaGxkqopII)
* `1.4.9`:
  * add color to pid in `pidcat`
  * fix flavor target directory when flavoring a subproject
  * suppress lint error in androidBuildJar
  * fix `javacOptions in doc`
  * turn update checker into `updateCheck in Android` which can be set to `()`
    to squelch
  * refactoring to support [Protify](https://github.com/pfn/protify), see demos
    [Protify Layout Prototyper Demo](https://www.youtube.com/watch?v=sgT9RA4SONU)
    and [Protify Code Prototyper Demo](https://youtu.be/g63I87UZ6bg?t=3m10s)
* `1.4.8`:
  * Update to new android gradle/builder 1.3.0
    * add `libraryRequests` and `packagingOptions` settings
  * warn about performance when using generated `maindexlist.txt`
  * add `androidBuildJar` and `buildJar` for creating jar libraries without
    resources (anytime aar or apklib is not required).
  * automatically cleanup resources when aar/apklib dependencies are changed,
    no longer requires clean build after changes.
* `1.4.7`:
  * proguard + cache improvements:
    * no longer need to clean after updating proguard and/or cache rules
  * add `android:allDevices` for automatically executing `android:install`,
    `android:run`, and/or `android:test` against all connected devices
  * lots of code cleanup (remove bad uses of `sbt.State`
  * improve the output of `devices` (include api level and battery status)
  * move the output of `AndroidBuilder` into `debug`
  * re-expose `sdkPath`, also add `ndkPath` as a setting
  * fix `watchSources` to properly compile on resource, jni, ndk, etc. changes
  * deprecate `androidBuildApklib`, should use `androidBuildAar` instead
  * fail the build when trying to run library projects
  * automatically generate `maindexlist.txt` if `dexMainFileClasses in Android`
    is not set
* `1.4.6`:
  * rename `android:packageName` to `android:applicationId`,
    uses `android:packageName` if set, otherwise falls back to value in
    `android:manifest`. `android:packageName` is reverted to be a
    `SettingKey` to fix collision with `sbt-native-packager`; fixes #178
  * add `resValues in Android`; allow specifying res/values from build (for
    flavors and auto-generated resources); analogous to `resValue` from the
    gradle android plugin.
* `1.4.5`:
  * colorize `pidcat` command
  * fix several proguard cache related bugs (minor)
  * organize predex bin output better
* `1.4.4`:
  * minor NDK build improvements
  * add testSources to watchSources (automatically trigger ~ commands)
* `1.4.3`:
  * `logcat` and `pidcat` performance improvements
  * minor signing config fixes (fix clobbering of settings, only prompt
    passwords once)
  * Instrumentation testing: do not squelch `testAndroidTestCaseSetUpProperly`
* `1.4.2`:
  * multidex updates
    * Allow proguard/proguardCache in conjunction with multidex
    * Implement minSdkVersion=21+ multidex optimizations (dramatically improve
      incremental build times when using multidex)
* `1.4.1`:
  * add a dex method counter, spits out number of methods in all dex files,
    aids in deciding when to add additional proguard-cache rules, and switching
    to/from multi-dex
  * Enhanced proguard-cache jar processing, no longer messes up conflicting
    case-sensitive filenames (can now proguard-cache obfuscated jars)
  * Fix apklibs source generators when using build flavors
* `1.4.0`:
  * This version **is not entirely backward compatible** with 1.3.x; `TR.scala`
    and `proguardCache` have undergone significant changes.
  * Some code re-organization, internal settings hidden from public view (can
    still be accessed by defining SettingKey and TaskKey manually as required)
  * Add `android:bootClasspath` for use with robolectric
  * Unused resource shrinker, enable with `shrinkResources in Android := true`
    * Only runs if proguard is run, typically used for clean, release builds.
    * See the
      [resource shrinking documentation](http://tools.android.com/tech-docs/new-build-system/resource-shrinking)
  * Conversion of some settings to tasks: `packageName`, `manifest`,
    `packageForR`, `versionName`, `versionCode`, `minSdkVersion`,
    `targetSdkVersion`
    * See all [available keys](src/keys.scala)
  * TypedResource improvements, now uses value class extensions for runtime
    performance improvements (all apps must now use scala 2.10+)
    * Renamed `TypedViewHolder` in favor of a single `TypedFindView`,
      removed `TypedView`, `TypedActivity`, `TypedActivityHolder`, `TypedDialog`
  * Proguard cache improvements, no more `ProguardCache` DSL, instead, add
    package prefix strings to cache directly to `proguardCache in Android`
    (`Seq[String]`)
  * Add `extraResDirectories` setting for additional overlay-resources. For use
    with build flavors, etc.

## New features in 1.3.x (last version: 1.3.24) ##

* `1.3.24`:
  * Minor lint fix (honor min/target sdk from build file)
  * Last version to support scala 2.8.x and 2.9.x
* `1.3.23`:
  * Remove repeated lint output
  * Fix `android.Plugin.flavorOf`
    * Demonstrate usage of `flavorOf` along with junit4 instrumented testing
      in the [android-test-kit test case](sbt-test/android-sdk-plugin/android-test-kit)
  * Fix doubled update checker message
  * Update `com.android.tools.build` and `lint` dependencies
* `1.3.22`:
  * Initial lint support
    * Configured to only detect API level issues by default
    * New setting keys:
      * `android:lintEnabled`: run lint in `compile`, default true
      * `android:lintFlags`: optional flags for lint
      * `android:lintStrict`: fail the build on lint errors, default false
      * `android:lintDetectors`: lint rules to detect, default: API level issues
    * New task `android:lint`: run lint independently of compile, will not run
      compile before-hand, otherwise, behaves according to settings above.
    * Available lint detectors can be found documented at
      http://www.javadoc.io/doc/com.android.tools.lint/lint-checks/24.2.2
  * Update to `com.android.tools.build:builder:1.2.0`
  * Remove stack traces on build failures (stack traces for errors only)
* `1.3.21`:
  * proguard-cache regression fix, force non-incremental dex on first cache-hit
  * remove bad ProguardCache() overloads
* `1.3.20`:
  * Fixes for proguardCache and aars
  * No longer deduplicate jars based on name (
    filter using `dependencyClasspath` instead if necessary)
* `1.3.19`:
  * Bug fixes around retrolambda, `gen-android`, and other minor issues
  * Update to builder 1.1.3
* `1.3.18`:
  * Initial implementation of `android.Plugin.flavorOf` for build flavors.
    * Simple usage is `lazy val flavorproject = android.Plugin.flavorOf(baseproject,
     "flavor-name", flavorSettings /* copies and override baseproject settings */)`
    * The `flavorproject` is otherwise a normal sbt project and it can be treated
      as such.
* `1.3.17`:
  * Reimplemented `renderscript` task, thanks @zbsz
  * Update checker fixes
  * Fork the retrolambda process for google play services failures
  * Update to builder `1.1.1`
  * Error on ambiguous project layouts
  * Add `android:buildConfigOptions` for customizing `BuildConfig.java`
  * Add `android:apkbuildDebug` to replace `createDebug` global variable
* `1.3.16`:
  * Add `android:test-only` thanks @tek
  * Fix `gen-android` and `gen-android-sbt` to create `android.sbt` with the
    current plugin version
  * Update to builder `1.0.1`
* `1.3.15`:
  * Update checker to notify of new versions
  * Fix multi-project retrolambda build issues
  * Fix `adb-wifi` to use `adb tcpip` internally
* `1.3.14`:
  * Support for [Retrolambda](https://github.com/orfjackal/retrolambda), Java8
    lambda syntax.
    * Automatically enabled when JDK 8 and java sources are detected.
    * Manually enable by setting `retrolambdaEnable in Android := true` (or,
      conversely, `false` to disable if it was automatically enabled)
    * Sample in
      [simple-retrolambda test case](sbt-test/android-sdk-plugin/simple-retrolambda)
* `1.3.13`:
  * Update to release builder `1.0.0`
  * Attempt to fix proguard-cache delta bug
* `1.3.12`:
  * update to latest android builder `1.0.0-rc1`
  * fix javah bug #131
  * fix double-tab crash #130
  * split `test` from `android:test` (better support for robolectric)
  * manifest placeholders, set `manifestPlaceholders in Android`
    * `Map[String,String]` for `key:value`s to replace in AndroidManifest.xml
    * Placeholders are expanded using `${key}` syntax
    * Can be dynamically configured as it is implemented as an SBT task
* `1.3.11`:
  * multidex support (thank you @dant3) see
    [the android reference documentation](https://developer.android.com/reference/android/support/multidex/MultiDex.html)
    and the
   [hello-multidex test case](sbt-test/android-sdk-plugin/hello-multidex)
    for an example of usage
  * `adb-runas` command: run a command as the current development package user
* `1.3.10`:
  * `adb-kill` command: kill the currently running package process
    (if not foreground)
  * update android builder (0.14.2), proguard (5.0) and asm dependencies (5.0)
  * `1.3.7`, `1.3.8` and `1.3.9` are bad releases, moderate bugs
* `1.3.5`:
  * Last release for sbt `0.12.x`
  * unseal ProjectLayout
  * allow proguard-cache on java-only projects
  * `adb-screenon` command (turn screen of device on/unlock)
  * include renderscript generated resources in aar
* `1.3.4`: bugfixes
  * #81 add fullClasspath to javah
  * update to builder 0.12.2
  * #82 add NDK_PROJECT_PATH environment for ndkbuild
  * #84 package dependsOn managedResources
  * #85 sourceManaged = gen
  * minor ndk build fixes (apkbuild depends on *.so)
* `1.3.3`: Add `ApkSigningConfig`, `PlainSigningConfig`,
  `PromptStorepassSigningConfig` and `PromptPasswordsSigningConfig`. These
   various signing configurations allow control over prompting for keystore
   and key passwords. The default is `PlainSigningConfig` which observes the
   original behavior from ant builds (reads properties out of
   `local.properties`). Set `apkSigningConfig in Android` to one of these
   variants to perform non-default behavior.
   * Also added `androidBuildWith()` project decorator, replaces
    `androidBuild(projects)` and `dependsOn(projects)`
* `1.3.2`: add `AutoPlugin` support for `0.13.5`
  * Auto-set `localProjects` when using `android.Plugin.androidBuild(...)`
  * When `gen-android`, `gen-android`, and `android.AutoBuild` require `0.13.5`
    if on the `0.13.x` sbt line.
  * Some refactoring of classes out of `android.Keys`, should be mostly
    compatible still.
* `1.3.1`: add `android:apkbuild-pickfirsts` works like
  `android:apkbuild-excludes` but picks the first occurrence of the resource.
  * A bug in com.android.tools.build:builder,
    [android bug #73437](https://code.google.com/p/android/issues/detail?id=73437),
    prevents PackagingOptions from working correctly with JNI libraries.
  A workaround is implemented copy all JNI to a single location first.
* NDK build process, similarly to `ANDROID_HOME`, set `ANDROID_NDK_HOME` to
  the location where the Android NDK is installed. Alternatively, `ndk.dir`
  can be set in a `local.properties` file for the project.
  * libs will be generated into `binPath / "jni"` and
    obj will drop into `binPath / "obj"`
  * Pre-generated JNI libraries will no longer be pulled out of `jni`
    (nor `src/main/jni`) -- they will be taken from `libs` (or `src/main/libs`)
    * This does not apply to aar and apklib--they will be pulled out of
      appropriate locations per their spec.
  * `javah` is automatically executed on all classes that have `native` methods
    in their signatures. The header files are generated into `sourceManaged`
    and are available to include in native sources and `Android.mk` by adding
    `LOCAL_CFLAGS := -I$(SBT_SOURCE_MANAGED)`
  * `collect-jni` no longer copies libraries, it only assembles a list of
    directory names for packaging
* Global plugin installation friendly
  * For sbt 0.13, add to `~/.sbt/0.13/plugins/android.sbt`
  * `addSbtPlugin("com.hanhuy.sbt" % "android-sdk-plugin" % "1.4.15")`
* New commands, all commands have proper tab-completion:
  * `gen-android` - creates android projects from scratch with sbt plumbing
  * `gen-android-sbt` - creates SBT files for an existing android project
  * `logcat` - supports all regular options, non-polling (-d by default)
  * `pidcat` - logcat the current package or specified package with TAG filters
  * `adb-ls` - ls on-device
  * `adb-cat` - cat a file on-device
  * `adb-rm` - rm a file on-device
  * `adb-shell` - execute a shell command on-device
  * `adb-push` - push a file to device
  * `adb-pull` - pull a file from device
  * `reboot-device` renamed to `adb-reboot`
* Existing commands available globally
  * `devices`, `device`, `adb-wifi`
* `AutoBuild` support, (created automatically with `gen-android`), set your
  build to be `object Build extends android.AutoBuild` and settings will be
  automatically applied to projects as necessary.
* Update to latest `com.android.tools.build` `0.12.x`
  * Now requires android build-tools `19.1.0` or newer
* `minSdkVersion` and `targetSdkVersion` are now `SettingKey[String]` and no
  longer `SettingKey[Int]` (support android-L)
* Instrumentation tests are now located in `src/main/androidTest` instead of
  `src/main/instrumentTest` (match layout generated by android create project)
* `android:dex` task now returns a folder for the output dex not a `classes.dex`
  file.

## New features in 1.2.x (last version: 1.2.20) ##

* Add setting `android:debug-includes-tests` (default = true) to automatically
  include instrumented test cases in the debug APK instead of using a separate
  test APK. This feature improves IntelliJ testing integration.
  * As a result of this new feature, if there are any `libraryDependencies` in
    `test` that must be honored, the setting must be disabled, and a separate
    test APK must be created. An alternative is to include the test dependencies
    in the normal compile. Proguard will automatically strip these out in
    release builds if they are unused.
  * This setting may be ignored, or set to `false` if one does not have tests
    or does not want to include the test cases in the debug package.
  * If the setting is disabled, test cases will be generated into a test APK
    when running `android:test`
  * When generating release builds, it is important to `clean`, otherwise
    test artifacts may be left over and present in the released apk.
  * When using included tests, it is necessary to add the following proguard
    options, or else proguard will mistakenly remove test cases from the
    output:

    ```
    proguardOptions in Android ++= Seq(
      "-keep public class * extends junit.framework.TestCase",
      "-keepclassmembers class * extends junit.framework.TestCase { *; }"
    )
    ```
* Add ability to disable manifest merging if upstream libraries have bad
  manifest settings, set `mergeManifests in Android := false`, default is
  `true`
  * Disabling manifest merging will remove automatic import of Activities,
    Services, BroadcastReceivers, etc. from the library's manifest into the
    main application manifest
* Increase test timeout to 3 minutes, from 5 seconds, configurable by using the
  `instrumentTestTimeout` setting key, in milliseconds
* Add `apkbuildExcludes` setting to skip/ignore duplicate files, an error like
  this:
  ```
  [info] com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/LICENSE.txt
  [info]  File 1: /path1/some.jar
  [info]  File 2: /path2/some.jar
  ```
  Can be rectified by setting
  `apkbuildExcludes in Android += "META-INF/LICENSE.txt"`
* `1.2.18`: `zipalignPath` has changed from a Setting into a Task

## Example projects ##

* A variety of my own projects can be found on github that use this plugin
* In addition to this, a growing collection of tests can be found under
  [sbt-test/android-sdk-plugin/](sbt-test/android-sdk-plugin).
  These projects are examples of how to use the plugin in various
  configurations.
* Tests can be run via `sbt scripted`, they require `ANDROID_HOME` and
  `ANDROID_NDK_HOME` to be set in addition to having platform `android-17`
  installed.
* All tests have auto-generated `build.properties` and `auto_plugins.sbt`
  files that set the current version of sbt and the android-sdk-plugin to use
  for testing.

## Usage ##

1. Install sbt (from [scala-sbt.org](http://www.scala-sbt.org) or use your
   local packaging system like macports, brew, etc.) -- make sure the
   Android SDK is fully updated (minimum build-tools 19.1.0 and up)
   * (OPTIONAL) Install the plugin globally by adding the following line
   in the file `~/.sbt/0.13/plugins/android.sbt`:
    
   ```
   addSbtPlugin("com.hanhuy.sbt" % "android-sdk-plugin" % "1.4.15")
   ```
   
2. Create a new android project using `gen-android` if the plugin is installed
   globally
   * Instead of creating a new project, one can also do
     `android update project` to make sure everything is properly setup
     in an existing project.
   * Instead of keeping local.properties up-to-date, you may set the
     environment variable `ANDROID_HOME` pointing to the path where the
     Android SDK is unpacked. This will bypass the requirement of having
     to run `android update project` on existing projects.
   * When using `gen-android`, the `platformTarget` is automatically set to
     the newest version available in your local SDK, override this by setting
     `target` in a `project.properties` file, or setting
     `platformTarget in Android`
3. (N/A if globally configured) Create a directory named `project` within
   your project and add the file `project/plugins.sbt`, in it, add the
   following line:

   ```
   addSbtPlugin("com.hanhuy.sbt" % "android-sdk-plugin" % "1.4.15")
   ```

4. Create a file named `project/build.scala` and add the
   following line, (automatically performed if using `gen-android`) :
   
   ```
   object Build extends android.AutoBuild
   ```

5. Now you will be able to run SBT, some available commands in sbt are:
   * `compile`
     * Compiles all the sources in the project, java and scala
     * Compile output is automatically processed through proguard if there
       are any Scala sources, otherwise; it can be enabled manually.
   * `android:package-release`
      * Builds a release APK and signs it with a release key if configured
   * `android:package-debug`
      * Builds a debug APK and signs it using the debug key
   * `android:package`
     * Builds an APK for the project of the last type selected, by default
       `debug`
   * `android:test`
     * run instrumented android unit tests
   * `android:install`
     * Install the application to device
   * `android:run`
     * Install and run the application on-device
   * `android:uninstall`
     * Uninstall the application from device
   * Any task can be repeated continuously whenever any source code changes
     by prefixing the command with a `~`. `~ android:package-debug`
     will continuously build a debug build any time one of the project's
     source files is modified.
6. If you want android-sdk-plugin to automatically sign release packages
   add the following lines to `local.properties` (or any file.properties of
   your choice that you will not check in to source control):
   * `key.alias: KEY-ALIAS`
   * `key.alias.password: PASSWORD` (optional, defaults to `key.store.password`)
   * `key.store: /path/to/your/.keystore`
   * `key.store.password: KEYSTORE-PASSWORD`
   * `key.store.type: pkcs12` (optional, defaults to `jks`)

## Advanced Usage ##

* IDE integration
  * The primary IDE recommendation is IntelliJ, not Android Studio
    nor Eclipse.
  * When loading a project into IntelliJ, it is required that the `Android`
    and `Scala` plugins are installed
  * The best practice is to set the IDE's run task to invoke sbt
    `android:package` instead of `Make`; this is found under the Run
    Configurations
  * The SBT plugin for IntelliJ is the one from
    [orfjackal/idea-sbt-plugin](https://github.com/orfjackal/idea-sbt-plugin)
  * The `Scala` plugin is still required for non-Scala projects in order to
    edit sbt build files from inside the IDE.
  * IntelliJ 14 now includes native support for importing projects from
    `android-sdk-plugin`. The process generally works well, however there
    are still several caveats:
    * The `idea-sbt-plugin` is still required to actually perform the build
    * `classDirectory in Compile` is not automatically included as a library,
      as a result apklib classes will not resolve unless it is added manually
      (`bin/android/intermediates/classes` or
      `target/android/intermediates/classes`) as a library.
      [SCL-7973](https://youtrack.jetbrains.com/issue/SCL-7973)
    * Gradle-style layouts still aren't fully supported (resources won't
      resolve in the IDE)
      [SCL-6273](https://youtrack.jetbrains.com/issue/SCL-6273)
* Consuming apklib and aar artifacts from other projects
  * Optionally use `apklib()` or `aar()`
    * using `apklib()` and `aar()` are only necessary if there are multiple
      filetypes for the dependency, such as `jar`, etc.
  * `libraryDependencies += apklib("groupId" % "artifactId" % "version", "optionalArtifactFilename")`
    * Basically, wrap the typical dependency specification with either
      apklib() or aar() to consume the library
    * If aars or apklibs are duplicately included in a multi-project build,
      specify `transitiveAndroidLibs in Android := false`
    * `apklib` and `aar` that transitively depend on `apklib` and `aar` will
      automatically be processed. To disable set
      `transitiveAndroidLibs in Android := false`
  * Sometimes library projects and apklibs will incorrectly bundle
    android-support-v4.jar, to rectify this, add this setting, repeat for any
    other incorrectly added jars:
    ```
    unmanagedJars in Compile ~= {
      _ filterNot (_.data.getName startsWith "android-support-v4")
    }
    ```
* Using the google gms play-services aar:

    ```
    libraryDependencies +=
      "com.google.android.gms" % "play-services" % "VERSION"
    ```

* Generating apklib and/or aar artifacts
  * To specify that your project will generate and publish either an `aar`
    or `apklib` artifact simply change the `android.Plugin.androidBuild`
    line to one of the variants that will build the desired output type.
    * For `apklib` use `android.Plugin.androidBuildApklib`
    * For `aar` use `android.Plugin.androidBuildAar`
  * Alternatively, use `android.Plugin.buildAar` and/or
    `android.Plugin.buildApklib` in addition to any of the variants above
    * In build.sbt, add `android.Plugin.buildAar` and/or
      `android.Plugin.buildApklib` on a new line.
    * It could also be specified, for example, like so:
      `android.Plugin.androidBuild ++ android.Plugin.buildAar`
* Multi-project builds
  * See multi-project build examples in the test cases for an example of
    configuration.
  * Multi-project builds *must* specify
    `transitiveAndroidLibs in Android := false` if any of the subprojects
    include `aar`s or `apklib`s as dependencies.
  * `androidBuild(...)` should be used to specify all dependent library-projects
  * All sub-projects in a multi-project build must specify `exportJars := true`.
    Android projects automatically set this variable.
  * When using multi-project builds in Scala, where library projects have
    scala code, but the main project(s) do(es) not, you will need to specify
    that proguard must run. To do this, the following must be set for each
    main project: `proguardScala in Android := true`
* Configuring `android-sdk-plugin` by editing build.sbt
  * `import android.Keys._` at the top to make sure you can use the plugin's
    configuration options (not required with sbt 0.13.5+ and AutoPlugin)
  * Add configuration options according to the sbt style:
    * `useProguard in Android := true` to enable proguard. Note: if you
      disable proguard for scala, you *must* specify uses-library on a
      pre-installed scala lib on-device. Pre-dexing the scala libs is not
      supported.
  * Configurable keys can be discovered by typing `android:<tab>` at the
    sbt shell
* Configuring proguard, some options are available
  * `proguardOptions in Android += Seq("-dontobfuscate", "-dontoptimize")` -
    will tell proguard not to obfuscute nor optimize code (any valid proguard
    option is usable here)
 * `proguardConfig in Android ...` can be used to replace the entire
   proguard config included with android-sdk-plugin
* On-device unit testing, use `android:test` and see
  [Android Testing Fundamentals](http://developer.android.com/tools/testing/testing_android.html)
*  Unit testing with robolectric and Junit (use the `test` task), see how
   it works in the
   [robo-junit-test test case](sbt-test/android-sdk-plugin/robo-junit-test)
   * Be sure to set `fork in Test := true` otherwise the classloading black
     magic in robolectric will fail.
* Device Management
  * The commands `devices` and `device` are implemented. The former lists
    all connected devices. The latter command is for selecting a target
    device if there is more than one device. If there is more than one
    device, and no target is selected, all commands will execute against the
    first device in the list.
  * `android:install`, `android:run` and `android:test` are tasks that can
    be used to install, run and test the built apk on-device, respectively.
  * Type `help` for a list of all available commands.

### TODO / Known Issues ###

* `autolibs` do not properly process `apklib` and `aar` resources. If anything
  in an `autolib` uses resources from such a library, the answer is to create
  a standard multi-project build configuration rather than utilize `autolibs`.
  `autolibs` can be disabled by manually configuring `localProjects in Android`

---
type: doc
layout: reference
title: "以 Gradle 构建多平台项目"
---

# Kotlin 多平台 Gradle DSL 参考

> 在 Kotlin 1.2 与 1.3 中，多平台项目是实验性特性。所有在<!--
-->这篇文档中描述的所有语言与工具特性都可能在未来的 Kotlin 版本中改变。
{:.note}

Kotlin 多平台 Gradle 插件是用于创建 [Kotlin 多平台](multiplatform.html)
项目的工具。这里我们提供了它的参考；在为 Kotlin 多平台项目编写 Gradle 构建脚本时，
用它作提醒。关于 Kotlin 多平台项目的概念与使用插件编写构建脚本<!--
-->的说明，参见[使用 Gradle 构建多平台项目](building-mpp-with-gradle.html)。

## 目录
 
* [id 与版本](#id-与版本)
* [顶层块](#顶层块)
* [目标](#目标)
    * [公共目标配置](#公共目标配置)
    * [JVM 目标](#jvm-目标)
    * [JavaScript 目标](#javascript-目标)
    * [Native 目标](#native-目标)
    * [Android 目标](#android-目标)
* [源集](#源集)
    * [预定义源集](#预定义源集)
    * [自定义源集](#自定义源集)
    * [源集参数](#源集参数)
* [编译项](#编译项)
    * [预定义编译项](#预定义编译项)
    * [自定义编译项](#自定义编译项)
    * [编译项参数](#编译项参数)
* [依赖项](#依赖项)
* [语言设置](#语言设置)

## id 与版本

Kotlin 多平台 Gradle 插件的全限定名是 `org.jetbrains.kotlin.multiplatform`。
如果你使用 Kotlin Gradle DSL，那么你可以通过 `kotlin("multiplatform")` 来应用插件。
插件版本与 Kotlin 发行版本相匹配。最新的版本是：{{ site.data.releases.latest.version }}。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '{{ site.data.releases.latest.version }}'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
plugins {
    kotlin("multiplatform") version "{{ site.data.releases.latest.version }}"
}
```

</div>
</div>

## 顶层块

`kotlin` 是在 Gradle 构建脚本中用于配置多平台项目的顶层块。
`kotlin` 块内，你可以使用以下块：

|**块**|**介绍**|
| --- | --- |
| _\<目标名称\>_ |声明项目的特定目标，所有可用的目标名称已陈列在[目标](#目标)部分中.|
|`targets` |项目的所有目标。|
|`presets` |所有预定义的目标。使用这个同时[配置多个预定义目标](building-mpp-with-gradle.html#setting-up-targets)。|
|`sourceSets` |配置预定义和声明自定义项目的[源集](building-mpp-with-gradle.html#configuring-source-sets)。|

## 目标

_Target_ is a part of the build responsible for compiling, testing, and packaging a piece of software aimed for 
one of the [supported platforms](building-mpp-with-gradle.html#supported-platforms). The targets of a multiplatform project
 are described in the corresponding blocks inside `kotlin`, for example, `jvm`, `android`, `iosArm64`.
The complete list of available targets is the following:
 
|**Name**|**Description**| 
| --- | --- |
|`jvm`| Java Virtual Machine|
|`js`| JavaScript|
|`android`|Android (APK)|
|`androidNativeArm32`|[Android NDK](https://developer.android.com/ndk) on ARM (ARM32) platforms|
|`androidNativeArm64`|[Android NDK](https://developer.android.com/ndk) on ARM64 platforms|
|`androidNativeX86`|[Android NDK](https://developer.android.com/ndk) on x86 platforms|
|`androidNativeX64`|[Android NDK](https://developer.android.com/ndk) on x86_64 platforms|
|`iosArm32`|Apple iOS on ARM (ARM32) platforms (Apple iPhone 5 and earlier)|
|`iosArm64`|Apple iOS on ARM64 platforms (Apple iPhone 5s and newer)|
|`iosX64`|Apple iOS 64-bit simulator|
|`watchosArm32`|Apple watchOS on ARM (ARM32) platforms (Apple Watch Series 3 and earlier)|
|`watchosArm64`|Apple watchOS on ARM64_32 platforms (Apple Watch Series 4 and newer)|
|`watchosX86`|Apple watchOS simulator|
|`tvosArm64`|Apple tvOS on ARM64 platforms (Apple TV 4th generation and newer)|
|`tvosX64`|Apple tvOS simulator|
|`linuxArm64`|Linux on ARM64 platforms, for example, Raspberry Pi|
|`linuxArm32Hfp`|Linux on hard-float ARM (ARM32) platforms|
|`linuxMips32`|Linux on MIPS platforms|
|`linuxMipsel32`|Linux on little-endian MIPS (mipsel) platforms|
|`linuxX64`|Linux on x86_64 platforms|
|`macosX64`|Apple macOS|
|`mingwX64`|64-bit Microsoft Windows|
|`mingwX86`|32-bit Microsoft Windows|
|`wasm32`|WebAssembly|

<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm()
    iosX64()
    macosX64()
    js().browser()
}
```

</div>

Configuration of a target can include two parts:

* [Common configuration](#公共目标配置) available for all targets.
* Target-specific configuration.

### 公共目标配置

In any target block, you can use the following declarations:

|**Name**|**Description**| 
| --- | --- |
|`attributes`|Attributes used for [disambiguating targets](building-mpp-with-gradle.html#disambiguating-targets) for a single platform.|
|`preset`|The preset that the target has been created from, if any.|
|`platformType`|Designates the Kotlin platform of this target. Avaiable values: `jvm`, `androidJvm`, `js`, `native`, `common`.|
|`artifactsTaskName`|The name of the task that builds the resulting artifacts of this target.|
|`components`|The components used to setup Gradle publications.|

### JVM 目标

In addition to [common target configuration](#公共目标配置), jvm targets have a specific function:

|**Name**|**Description**| 
| --- | --- |
|`withJava()`|Includes Java sources into the JVM target’s compilations. |

Use this function for projects that contain both Java and Kotlin source files. Note that the default source directories for Java sources
don't follow the Java plugin's defaults. Instead, they are derived from the Kotlin source sets. For example, if the JVM target
has the default name `jvm`, the paths are `src/jvmMain/java` (for production Java sources) and `src/jvmTest/java` for test Java sources.
For more information, see [Java support in JVM targets](building-mpp-with-gradle.html#java-support-in-jvm-targets).

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm {
        withJava()
    } 
}
```

</div>

### JavaScript 目标

The `js` block describes the configuration of JavaScript targets. It can contain one of two blocks depending on the target execution environment:

|**Name**|**Description**| 
| --- | --- |
|`browser`|Configuration of the browser target.|
|`nodejs`|Configuration of the Node.js target.|

For details about configuring Kotlin/JS projects, see [Setting up a Kotlin/JS project](https://kotlinlang.org/docs/reference/js-project-setup.html).

#### Browser

`browser` can contain the following configuration blocks:

|**Name**|**Description**| 
| --- | --- |
|`testRuns`|Configuration of test execution.|
|`runTask`|Configuration of project running.|
|`webpackTask`|Configuration of project bundling with [Webpack](https://webpack.js.org/).|
|`dceTask`|Configuration of [Dead Code Elimination](javascript-dce.html).|
|`distribution`|Path to output files.|

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    js().browser {
        webpackTask { /* ... */ }
        testRuns { /* ... */ }
        dceTask {
            keep("myKotlinJsApplication.org.example.keepFromDce")
        }
        distribution {
            directory = File("$projectDir/customdir/")
        }        
    }
}
```

</div>

#### Node.js

`nodejs` can contain configurations of test and run tasks:

|**Name**|**Description**| 
| --- | --- |
|`testRuns`|Configuration of test execution.|
|`runTask`|Configuration of project running.|

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    js().nodejs {
        runTask { /* ... */ }
        testRuns { /* ... */ }
    }
}
```

</div>

### Native 目标

For native targets, the following specific blocks are available:

|**Name**|**Description**| 
| --- | --- |
|`binaries`|Configuration of [binaries](#binaries) to produce.|
|`cinterops`|Configuration of [interop with C libraries](#cinterops).|

#### Binaries

There are the following kinds of binaries:

|**Name**|**Description**| 
| --- | --- |
|`executable`|Product executable.|
|`test`|Test executable.|
|`sharedLib`|Shared library.|
|`staticLib`|Static library.|
|`framework`|Objective-C framework.|

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    linuxX64 { // Use your target instead.
        binaries {
            executable {
                // Binary configuration.
            }
        }
    }
}
```

</div>

For binaries configuration, the following parameters are available:

|**Name**|**Description**| 
| --- | --- |
|`compilation`|The compilation from which the binary is built. By default, `test` binaries are based on the `test` compilation while other binaries - on the `main` compilation.|
|`linkerOpts`|Options passed to a system linker during binary building.|
|`baseName`|Custom base name for the output file. The final file name will be formed by adding system-dependent prefix and postfix to this base name.|
|`entryPoint`|The entry point function for executable binaries. By default, it's `main()` in the root package.|
|`outputFile`|Access to the output file.|
|`linkTask`|Access to the link task.|
|`runTask`|Access to the run task for executable binaries. For targets other than `linuxX64`, `macosX64`, or `mingwX64` the value is `null`.|
|`isStatic`|For Objective-C frameworks. Includes a static library instead of a dynamic one.|

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
binaries {
    executable('my_executable', [RELEASE]) {
        // Build a binary on the basis of the test compilation.
        compilation = compilations.test

        // Custom command line options for the linker.
        linkerOpts = ['-L/lib/search/path', '-L/another/search/path', '-lmylib']

        // Base name for the output file.
        baseName = 'foo'

        // Custom entry point function.
        entryPoint = 'org.example.main'

        // Accessing the output file.
        println("Executable path: ${outputFile.absolutePath}")

        // Accessing the link task.
        linkTask.dependsOn(additionalPreprocessingTask)

        // Accessing the run task.
        // Note that the runTask is null for non-host platforms.
        runTask?.dependsOn(prepareForRun)
    }

    framework('my_framework' [RELEASE]) {
        // Include a static library instead of a dynamic one into the framework.
        isStatic = true
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
binaries {
    executable("my_executable", listOf(RELEASE)) {
        // Build a binary on the basis of the test compilation.
        compilation = compilations["test"]

        // Custom command line options for the linker.
        linkerOpts = mutableListOf("-L/lib/search/path", "-L/another/search/path", "-lmylib")

        // Base name for the output file.
        baseName = "foo"

        // Custom entry point function.
        entryPoint = "org.example.main"

        // Accessing the output file.
        println("Executable path: ${outputFile.absolutePath}")

        // Accessing the link task.
        linkTask.dependsOn(additionalPreprocessingTask)

        // Accessing the run task.
        // Note that the runTask is null for non-host platforms.
        runTask?.dependsOn(prepareForRun)
    }

    framework("my_framework" listOf(RELEASE)) {
        // Include a static library instead of a dynamic one into the framework.
        isStatic = true
    }
}
```

</div>
</div>

For more information on configuring binaries, see [Building final native binaries](building-mpp-with-gradle.html#building-final-native-binaries).

#### CInterops

`cinterops` is a collection of descriptions for interop with native libraries.
To provide an interop with a library, add an entry to `cinterops` and define its parameters:

|**Name**|**Description**| 
| --- | --- |
|`defFile`|`def` file describing the native API.|
|`packageName`|Package prefix for the generated Kotlin API.|
|`compilerOpts`|Options to pass to the compiler by the cinterop tool.|
|`includeDirs`|Directories to look for headers.|

For more information on Kotlin interop with C libraries, see [CInterop support](building-mpp-with-gradle.html#cinterop-support).

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    linuxX64 { // Replace with a target you need.
        compilations.main {
            cinterops {
                myInterop {
                    // Def-file describing the native API.
                    // The default path is src/nativeInterop/cinterop/<interop-name>.def
                    defFile project.file("def-file.def")

                    // Package to place the Kotlin API generated.
                    packageName 'org.sample'

                    // Options to be passed to compiler by cinterop tool.
                    compilerOpts '-Ipath/to/headers'

                    // Directories for header search (an analogue of the -I<path> compiler option).
                    includeDirs.allHeaders("path1", "path2")

                    // A shortcut for includeDirs.allHeaders.
                    includeDirs("include/directory", "another/directory")
                }

                anotherInterop { /* ... */ }
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    linuxX64 {  // Replace with a target you need.
        compilations.getByName("main") {
            val myInterop by cinterops.creating {
                // Def-file describing the native API.
                // The default path is src/nativeInterop/cinterop/<interop-name>.def
                defFile(project.file("def-file.def"))

                // Package to place the Kotlin API generated.
                packageName("org.sample")

                // Options to be passed to compiler by cinterop tool.
                compilerOpts("-Ipath/to/headers")

                // Directories for header search (an analogue of the -I<path> compiler option).
                includeDirs.allHeaders("path1", "path2")

                // A shortcut for includeDirs.allHeaders.
                includeDirs("include/directory", "another/directory")
            }

            val anotherInterop by cinterops.creating { /* ... */ }
        }
    }
}

```

</div>
</div>

### Android 目标

The Kotlin multiplatform plugin contains two specific functions for android targets.
Two functions help you configure [build variants](https://developer.android.com/studio/build/build-variants):

|**Name**|**Description**| 
| --- | --- |
|`publishLibraryVariants()`|Specifies build variants to publish. For usage instructions, see [Publishing Android libraries](building-mpp-with-gradle.html#publishing-android-libraries).|
|`publishAllLibraryVariants()`|Publishes all build variants.|

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    android {
        publishLibraryVariants("release", "debug")
    }
}
```

</div>

For more details about configuring Android targets of multiplatform projects, see [Android Support](building-mpp-with-gradle.html#android-support).

>Note that the `android` configuration inside `kotlin` doesn’t replace the build configuration of any Android project.
For information on writing build scripts for Android projects, see the [Android developer documentation](https://developer.android.com/studio/build).
{:.note}

## 源集

The `sourceSets` block describes source sets of the project. A source set contains Kotlin source files that participate
in compilations together, along with their resources, dependencies, and language settings. 

A multiplatform project contains [predefined](#预定义源集) source sets for its targets;
developers can also create [custom](#自定义源集) source sets for their needs.
For instructions on creating and configuring source sets, see [Configuring source sets](building-mpp-with-gradle.html#configuring-source-sets).

### 预定义源集

Predefined source sets are set up automatically upon creation of a multiplatform project.
Available predefined source sets are the following:

|**Name**|**Description**| 
| --- | --- |
|`commonMain`| Code and resources shared between all platforms. Available in all multiplatform projects. Used in all main compilations of a project.|
|`commonTest`| Test code and resources shared between all platforms. Available in all multiplatform projects. Used in all test compilations of a project.|
|_\<targetName\>\<compilationName\>_|Target-specific sources for a compilation. _\<targetName\>_ is the name of a predefined target and _\<compilationName\>_ is the name of a compilation for this target. Examples: `jsTest`, `jvmMain`.|

With Kotlin Gradle DSL, the sections of predefined source sets should be marked `by getting`.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin { 
    sourceSets { 
        commonMain { /* ... */ } 
    }
}
``` 

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin { 
    sourceSets { 
        val commonMain by getting { /* ... */ } 
    }
}
```

</div>
</div>

For more information about the predefined source sets, see [Default Project Layout](building-mpp-with-gradle.html#default-project-layout).

### 自定义源集

Custom source sets are created by the project developers manually.
To create a custom source set, add a section with its name inside the `sourceSets` section.
If using Kotlin Gradle DSL, mark custom source sets `by creating`.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin { 
    sourceSets { 
        myMain { /* ... */ } // create or configure a source set by the name 'myMain' 
    }
}
``` 

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin { 
    sourceSets { 
        val myMain by creating { /* ... */ } // create a new source set by the name 'MyMain'
    }
}
```

</div>
</div>

Note that a newly created source set isn’t connected to other ones. To use it in the project’s compilations,
connect it with other source sets as described in [Connecting source sets](building-mpp-with-gradle.html#connecting-source-sets).

### 源集参数

Configurations of source sets are stored inside the corresponding blocks of `sourceSets`. A source set has the following parameters:

|**Name**|**Description**| 
| --- | --- |
|`kotlin.srcDir`|Location of Kotlin source files inside the source set directory.|
|`resources.srcDir`|Location of resources inside the source set directory.|
|`dependsOn`|Connection with another source set. The instructions on connecting source sets are provided in [Connecting source sets](building-mpp-with-gradle.html#connecting-source-sets).|
|`dependencies`|[依赖项](#依赖项) of the source set.|
|`languageSettings`|[语言设置](building-mpp-with-gradle.html#语言设置) applied to the source set.|

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin { 
    sourceSets { 
        commonMain {
            kotlin.srcDir('src')
            resources.srcDir('res')
            
            dependencies {
                /* ... */
            }           
        } 
    }
}
``` 

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin { 
    sourceSets { 
        val commonMain by getting {
            kotlin.srcDir("src")
            resources.srcDir("res")
            
            dependencies {
                /* ... */
            } 
        } 
    }
}
```

</div>
</div>

## 编译项

A target can have one or more compilations, for example, for production or testing. There are [predefined compilations](#预定义编译项)
that are added automatically upon target creation. Developers can additionally create [custom compilations](#自定义编译项).

To refer to all or some particular compilations of a target, use the `compilations` object collection.
From `compilations`, you can refer to a compilation by its name.

### 预定义编译项

Predefined compilations are created automatically for each target of a project except for Android targets.
Available predefined compilations are the following:

|**Name**|**Description**| 
| --- | --- |
|`main`|Compilation for production sources.|
|`test`|Compilation for tests.|

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm {
        compilations.main.output // get the main compilation output
        compilations.test.runtimeDependencyFiles // get the test runtime classpath
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            output // get the main compilation output
        }
        
        compilations["test"].runtimeDependencyFiles // get the test runtime classpath
    }
}
```

</div>
</div>

### 自定义编译项

In addition to predefined compilations, developers can create their own custom compilations.
To create a custom compilation, add a new item into the `compilations` collection.
If using Kotlin Gradle DSL, mark custom compilations `by creating`.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm() {
        compilations.create('integrationTest') {
            defaultSourceSet {
                dependencies {
                    /* ... */
                }
            }

            // Create a test task to run the tests produced by this compilation:
            tasks.create('jvmIntegrationTest', Test) {
                /* ... */
            }
        }
    }
}
```

</div> 
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm() {
        compilations {
            val integrationTest by compilations.creating {
                defaultSourceSet {
                    dependencies {
                        /* ... */
                    }
                }

                // Create a test task to run the tests produced by this compilation:
                tasks.create<Test>("integrationTest") {
                    /* ... */
                }
            }
        }
    }
}
```

</div>
</div>

### 编译项参数

A compilation has the following parameters:

|**Name**|**Description**| 
| --- | --- |
|`defaultSourceSet`|The compilation’s default source set.|
|`kotlinSourceSets`|Source sets participating in the compilation.|
|`allKotlinSourceSets`|Source sets participating in the compilation and their connections via `dependsOn()`.|
|`kotlinOptions`|Compiler options applied to the compilation. For the list of available options, see [Compiler options](using-gradle.html#compiler-options).|
|`compileKotlinTask`|Gradle task for compiling Kotlin sources.|
|`compileKotlinTaskName`|Name of `compileKotlinTask`.|
|`compileAllTaskName`|Name of the Gradle task for compiling all sources of a compilation.|
|`output`|The compilation output.|
|`compileDependencyFiles`|Compile-time dependency files (classpath) of the compilation.|
|`runtimeDependencyFiles`|Runtime dependency files (classpath) of the compilation.|

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm {
        compilations.main.kotlinOptions { 
            // Setup the Kotlin compiler options for the 'main' compilation:
            jvmTarget = "1.8"
        }
        
        compilations.main.compileKotlinTask // get the Kotlin task 'compileKotlinJvm' 
        compilations.main.output // get the main compilation output
        compilations.test.runtimeDependencyFiles // get the test runtime classpath
    }
    
    // Configure all compilations of all targets:
    targets.all {
        compilations.all {
            kotlinOptions {
                allWarningsAsErrors = true
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            kotlinOptions { 
                // Setup the Kotlin compiler options for the 'main' compilation:
                jvmTarget = "1.8"
            }
        
            compileKotlinTask // get the Kotlin task 'compileKotlinJvm' 
            output // get the main compilation output
        }
        
        compilations["test"].runtimeDependencyFiles // get the test runtime classpath
    }
    
    // Configure all compilations of all targets:
    targets.all {
        compilations.all {
            kotlinOptions {
                allWarningsAsErrors = true
            }
        }
    }
}
```

</div>
</div>

## 依赖项

The dependencies block of the source set declaration contains the dependencies of this source set.
There are four kinds of dependencies:

|**Name**|**Description**| 
| --- | --- |
|`api`|Dependencies used in the API of the current module.|
|`implementation`|Dependencies used in the module but not exposed outside it.|
|`compileOnly`|Dependencies used only for compilation of the current module.|
|`runtimeOnly`|Dependencies available at runtime but not visible during compilation of any module.|

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                api 'com.example:foo-metadata:1.0'
            }
        }
        jvm6Main {
            dependencies {
                implementation 'com.example:foo-jvm6:1.0'
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```groovy
kotlin {
    sourceSets {
        val commonMain by getting {
            dependencies {
                api("com.example:foo-metadata:1.0")
            }
        }
        val jvm6Main by getting {
            dependencies {
                implementation("com.example:foo-jvm6:1.0")
            }
        }
    }
}
```

</div>
</div>

Additionally, source sets can depend on each other. In this case, the [dependsOn()](#源集参数) function is used.
Source set dependencies can also be declared in the top-level `dependencies` block of the build script.
In this case, their declarations follow the pattern `<sourceSetName><DependencyKind>`, for example, `commonMainApi`.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
dependencies {
    commonMainApi 'com.example:foo-common:1.0'
    jvm6MainApi 'com.example:foo-jvm6:1.0'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
dependencies {
    "commonMainApi"("com.example:foo-common:1.0")
    "jvm6MainApi"("com.example:foo-jvm6:1.0")
}
```

</div>
</div>

## 语言设置

The languageSettings block of a source set defines certain aspects of project analysis and build. The following language settings are available:

|**Name**|**Description**| 
| --- | --- |
|`languageVersion`|Provides source compatibility with the specified version of Kotlin.|
|`apiVersion`|Allows using declarations only from the specified version of Kotlin bundled libraries.|
|`enableLanguageFeature`|Enables the specified language feature. The available values correspond to the language features that are currently experimental or have been introduced as such at some point.|
|`useExperimentalAnnotation`|Allows using the specified [opt-in annotation](opt-in-requirements.html).|
|`progressiveMode`|Enables the [progressive mode](whatsnew13.html#progressive-mode).|

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets {
        commonMain {
            languageSettings {
                languageVersion = '1.3' // possible values: '1.0', '1.1', '1.2', '1.3'
                apiVersion = '1.3' // possible values: '1.0', '1.1', '1.2', '1.3'
                enableLanguageFeature('InlineClasses') // language feature name
                useExperimentalAnnotation('kotlin.ExperimentalUnsignedTypes') // annotation FQ-name
                progressiveMode = true // false by default
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            languageSettings.apply {
                languageVersion = "1.3" // possible values: '1.0', '1.1', '1.2', '1.3'
                apiVersion = "1.3" // possible values: '1.0', '1.1', '1.2', '1.3'
                enableLanguageFeature("InlineClasses") // language feature name
                useExperimentalAnnotation("kotlin.ExperimentalUnsignedTypes") // annotation FQ-name
                progressiveMode = true // false by default
            }
        }
    }
}
```

</div>
</div>

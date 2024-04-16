+++
title = 'Resolve the simulator running error caused by CocoaPods dependency on arm64 architecture Mac which only supports x86 architecture'
date = 2024-04-11T12:02:52+08:00
draft = false
tags = ["iOS", ""]
+++

## Introduction

The Apple M1 chip introduced a major change to Mac architecture, moving from the traditional Intel x86 architecture to ARM architecture. This change means for developers that many existing applications and dependent libraries need to adapt to the new `arm64` architecture. In this blog, we’ll explore typical error messages you may encounter when using the iOS Simulator on an M1 Mac, explain the rationale behind them, and provide practical solutions.

## M1 chip and simulator compatibility issues

### Principle analysis

The M1 chip's ARM architecture is significantly different from the x86 architecture found on earlier Macs. The M1 chip natively supports the `arm64` architecture instead of the `x86_64` architecture used by Intel chips. This change directly affects the development environment, especially when running the emulator.

On M1 Macs, the iOS simulator can run directly in the `arm64` architecture without any conversion, which can improve performance. But this also brings a problem: many existing third-party libraries and dependencies are still only compiled for the `x86_64` architecture and fail to provide an `arm64` version.

### Error reporting

When developers use the simulator to run these libraries that only support `x86_64` on an M1 Mac, they may encounter error messages similar to the following:

```
Building for 'iOS-simulator', but linking in object file built for 'iOS', incompatible with iOS-simulator arm64.
```

This indicates an attempt to run a library compiled only for the `x86_64` architecture on an emulator for the `arm64` architecture.

## Solution

### Using `EXCLUDED_ARCHS`

The `EXCLUDED_ARCHS` setting is used in Xcode to specify the architectures to exclude during compilation. By setting `EXCLUDED_ARCHS` to `arm64`, Xcode will not try to compile code for the `arm64` architecture when compiling for the simulator, thus avoiding architecture mismatch issues.

### Specific operations

1. **Open Xcode Project Settings**: Select the project file and look for the `Excluded Architectures` setting in the Build Settings tab.
2. **Set Exclude Architecture**: Add the `arm64` value to the Debug and Release configurations to ensure that this architecture is excluded when the simulator is compiled.

To set `Excluded Architectures` for all CocoaPods dependencies, you can use the post-install hook in your project's Podfile to apply this configuration globally. Doing this ensures that all libraries managed through CocoaPods will exclude the specified architecture. Here's how to implement this setting in your Podfile:

1. Open your Podfile.

2. At the bottom of the file, add a `post_install` hook.

3. Inside the hook, write a script to set `EXCLUDED_ARCHS`.

Here is sample code on how to add this hook in the Podfile:

```ruby
#Podfile

# Other Podfile configurations...

post_install do |installer|
  installer.generated_projects.each do |project|
    project.build_configurations.each do |config|
      # Set to exclude arm64 architecture
      config.build_settings['EXCLUDED_ARCHS[sdk=iphonesimulator*]'] = 'arm64'
    end
  end
end
```

This code loops through all generated Xcode projects in the `post_install` hook and sets `EXCLUDED_ARCHS` for each configuration, excluding the `arm64` architecture only for the iPhone Simulator SDK. This means that when you run your project on the simulator, Xcode will ignore the `arm64` architecture, helping resolve compatibility issues.

After making these changes, you need to run the `pod install` or `pod update` command to ensure that the changes are applied to your project. Doing this can help resolve architecture mismatch issues encountered when using the emulator on M1 Macs.

### Principle description

The idea behind this is to have Xcode ignore support for the `arm64` architecture when building the app, which means it will use Rosetta 2 to emulate the `x86_64` architecture, allowing the app to run on the simulator on an M1 Mac.

### Long term solution

While using `EXCLUDED_ARCHS` can resolve compatibility issues, it is only a temporary solution. In the long term, library maintainers will need to update their code to ensure they support the `arm64` architecture. As more developers move to M1 Macs, native support for the `arm64` architecture becomes even more important.

## Conclusion

The introduction of the M1 chip had a profound impact on Mac application development, particularly in terms of processing architecture compatibility. Understanding how these compatibility issues work, and knowing how to work around them, is critical to keeping the development process flowing. As technology advances and the ecosystem matures, developers will need to adapt to this change, updating and optimizing their applications to take full advantage of the M1 chip. In the long term, library and tool maintainers will also update their products to natively support the new architecture, ultimately eliminating these compatibility issues. So while some temporary solutions may be needed to deal with architectural compatibility issues, we can also look forward to a more unified and efficient development environment that will make app development and running on all Apple devices smoother.

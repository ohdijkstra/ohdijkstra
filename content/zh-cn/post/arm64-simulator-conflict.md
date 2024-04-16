+++
title = '解决arm64架构Mac上仅支持x86架构的CocoaPods依赖导致的模拟器运行错误'
date = 2024-04-11T12:02:52+08:00
draft = false
tags = ["iOS", ""]
+++

## 引言

Apple M1 芯片引入了对 Mac 架构的重大变革，它从传统的 Intel x86 架构转变为 ARM 架构。这一变化对于开发者意味着许多现有的应用和依赖库需要适配新的 `arm64` 架构。在这篇博客中，我们将探讨在 M1 Mac 上使用 iOS 模拟器时可能遇到的典型报错问题，解释其背后的原理，并提供实际的解决方法。

## M1 芯片与模拟器兼容性问题

### 原理解析

M1 芯片的 ARM 架构与早期 Mac 上的 x86 架构有显著差异。M1 芯片原生支持 `arm64` 架构，而不是 Intel 芯片使用的 `x86_64` 架构。这一改变直接影响到了开发环境，尤其是在运行模拟器时。

在 M1 Mac 上，iOS 模拟器可以直接以 `arm64` 架构运行，而不需要任何转换，这样可以提高性能。但这也带来了一个问题：很多现有的第三方库和依赖项仍然只编译为 `x86_64` 架构，未能提供 `arm64` 版本。

### 报错情况

开发者在 M1 Mac 上使用模拟器运行这些仅支持 `x86_64` 的库时，可能会遇到类似以下的报错信息：

```
Building for 'iOS-simulator', but linking in object file built for 'iOS', incompatible with iOS-simulator arm64.
```

这表示尝试在 `arm64` 架构的模拟器上运行一个仅为 `x86_64` 架构编译的库。

## 解决方法

### 使用 `EXCLUDED_ARCHS`

`EXCLUDED_ARCHS` 设置在 Xcode 中用于指定在编译过程中要排除的架构。通过将 `EXCLUDED_ARCHS` 设置为 `arm64`，Xcode 在编译为模拟器时不会尝试编译 `arm64` 架构的代码，从而避免了架构不匹配的问题。

### 具体操作

1. **打开 Xcode 项目设置**：选择项目文件，然后在 Build Settings 标签页中查找 `Excluded Architectures` 设置。
2. **设置排除架构**：为 Debug 和 Release 配置添加 `arm64` 值，确保模拟器编译时排除此架构。

要为所有的 CocoaPods 依赖项设置 `Excluded Architectures`，你可以在项目的 Podfile 中使用 post-install 钩子来全局地应用这个配置。这样做可以确保所有通过 CocoaPods 管理的库都将排除指定的架构。以下是在 Podfile 中实现这一设置的方法：

1. 打开你的 Podfile。

2. 在文件的底部，添加一个 `post_install` 钩子。

3. 在该钩子内部，编写脚本来设置 `EXCLUDED_ARCHS`。

下面是如何在 Podfile 中添加这个钩子的示例代码：

```ruby
# Podfile

# 其他的 Podfile 配置...

post_install do |installer|
  installer.generated_projects.each do |project|
    project.build_configurations.each do |config|
      # 设置排除 arm64 架构
      config.build_settings['EXCLUDED_ARCHS[sdk=iphonesimulator*]'] = 'arm64'
    end
  end
end
```

这段代码在 `post_install` 钩子中遍历所有生成的 Xcode 项目，并设置每个配置的 `EXCLUDED_ARCHS`，仅对 iPhone 模拟器 SDK 排除 `arm64` 架构。这意味着当你在模拟器上运行项目时，Xcode 会忽略 `arm64` 架构，帮助解决兼容性问题。

完成这些更改后，需要运行 `pod install` 或 `pod update` 命令，以确保这些更改应用到你的项目中。这样做可以帮助解决 M1 Mac 上使用模拟器时遇到的架构不匹配问题。

### 原理说明

这样做的原理是让 Xcode 在构建应用时忽略对 `arm64` 架构的支持，这意味着它将使用 Rosetta 2 来模拟 `x86_64` 架构，从而允许应用在 M1 Mac 的模拟器上运行。

### 长期解决方案

虽然使用 `EXCLUDED_ARCHS` 可以解决兼容性问题，但这只是一个临时解决方案。长期而言，库的维护者需要更新他们的代码，以确保它们支持 `arm64` 架构。随着越来越多的开发者转向 M1 Mac，对 `arm64` 架构的原生支持变得尤为重要。

## 结语

M1 芯片的引入对 Mac 应用开发产生了深远的影响，特别是在处理架构兼容性方面。理解这些兼容性问题的原理，并知道如何临时解决它们，对于保持开发流程的顺畅至关重要。随着技术的进步和生态系统的逐渐成熟，开发者需要适应这种变化，更新和优化他们的应用以充分利用 M1 芯片的优势。长期来看，库和工具的维护者也将更新他们的产品，以原生支持新架构，最终消除这些兼容性问题。因此，虽然当前可能需要一些临时的解决方案来处理架构兼容性问题，但我们也可以期待一个更加统一和高效的开发环境，使得在所有苹果设备上的应用开发和运行更为顺畅。

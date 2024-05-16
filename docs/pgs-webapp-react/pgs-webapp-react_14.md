# 第十四章：结论和下一步

“……这就是应用程序根据谷歌的评分。正如您所看到的，它符合渐进式 Web 应用的每个标准，这将与我们的全球业务目标很好地契合——”

“是的，是的，”你的朋友挥了挥手。“很酷。很棒。干得好。但枢纽呢？”

“什么？”你问道。

“你没收到备忘录吗？我一个月前就给你公司邮箱发了一封备忘录。”

“我不知道我有公司邮箱。”

“哦。”你的朋友皱起了眉头。“我以为你对技术很在行。”

“但我不知道——”

“没关系。我可以总结一下。公司已经转变了。聊天很棒，但如果我们再进一步呢？如果我们把它变成一个社交网络呢？想象一下——Facebook 的可分享性，Netflix 的视频流和 Uber 的顺风车，所有这些都在一个区块链上……”

当你走向门口时，你的朋友继续说话。

# 下一步

我们已经涵盖了将 React 应用程序转变为 PWA 所需的每一步，但是，像往常一样，还有更多要学习的。

本章分为四个部分。首先，我们将列出一些有用的资源，以继续您的 PWA 之旅。然后，我们将介绍一些重要的库，这些库将帮助自动化 PWA 开发的某些方面，或将您的应用程序提升到更高的水平。第三，我将列出一些我最喜欢的关于开发渐进式 Web 应用的文章。最后，我们将看一下一些可能的扩展目标，以便您在接受挑战后扩展和改进 Chatastrophe。

以下许多资源都是通过两个优秀的存储库发现的：**awesome-pwa** ([`github.com/hemanth/awesome-pwa`](https://github.com/hemanth/awesome-pwa)) 由 GitHub 用户*Hemanth*创建，以及**awesome-progressive-web-apps** ([`github.com/TalAter/awesome-progressive-web-apps`](https://github.com/TalAter/awesome-progressive-web-apps)) 由*TalAter*创建。

我们将看一下以下内容：

+   扩展您知识的学习资源

+   成功 PWA 的案例研究

+   可以从中获得灵感的示例应用程序

+   关于 PWA 崛起的必读文章

+   您可以使用的工具来使未来 PWA 的构建更容易

+   Chatastrophe 的扩展目标

# 学习资源

学习资源如下：

+   **渐进式 Web 应用文档**：谷歌关于渐进式 Web 应用的官方文档。这应该是您的第一站，以了解概念或阅读最佳实践。它还提供了关于 PWA 的好处的摘要，并链接到诸如 Lighthouse 之类的工具。

[`developers.google.com/web/progressive-web-apps/`](https://developers.google.com/web/progressive-web-apps/)

+   **你的第一个渐进式 Web 应用**：一个逐步教程，教你如何构建你的第一个渐进式 Web 应用，或者在你的情况下，你的第二个。如果你想看看没有 React 的情况下构建 PWA 是什么样子，可以看看这个教程。这是非常详细的，涵盖了每个概念。

[`developers.google.com/web/fundamentals/getting-started/codelabs/your-first-pwapp`](https://developers.google.com/web/fundamentals/getting-started/codelabs/your-first-pwapp)

+   **离线 Web 应用**：由 Google 创建并由 Udacity 托管的免费课程，关于离线优先的 Web 应用。内容分为三个部分：为什么优先离线、Service Workers 和缓存。一些部分，比如 service worker 部分，可能会是复习，但这门课程还深入探讨了 IndexedDB 用于本地存储。

[`www.udacity.com/course/offline-web-applications--ud899`](https://www.udacity.com/course/offline-web-applications--ud899)

+   **Service Worker 入门**：Google 对 Service Workers 的介绍。很多代码看起来会很熟悉，因为它们在本书的 service worker 部分中出现过，但它仍然是一个方便的资源。Matt Gaunt 做了很好的工作，解释了基础知识。

[`developers.google.com/web/fundamentals/getting-started/primers/service-workers`](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)

+   **Service Worker 101**：关于 service workers 的更加生动的指南，这个可爱的资源包含一系列图表，带你了解 service worker 的生命周期等内容。如果你对 service workers 不确定，可以打印出来贴在你的桌子上。

[`github.com/delapuente/service-workers-101`](https://github.com/delapuente/service-workers-101)

+   **开始使用渐进式 Web 应用**：Chrome 开发团队的 *Addy Osmani* 的一篇博客文章（我们将在这个资源部分经常看到他）。这是一个很好的高层次介绍 PWA 的好处，并介绍了一些起步的模板。

[`addyosmani.com/blog/getting-started-with-progressive-web-apps/`](https://addyosmani.com/blog/getting-started-with-progressive-web-apps/)

+   **使用 Push API**：Mozilla 开发者网络关于 Push API 的指南。如果你想在你的 PWA 中使用推送通知，而不依赖于 Firebase Cloud Notifications，就从这里开始。

[`developer.mozilla.org/en-US/docs/Web/API/Push_API/Using_the_Push_API`](https://developer.mozilla.org/en-US/docs/Web/API/Push_API/Using_the_Push_API)

+   **使用缓存 API**：Mozilla 开发者网络对缓存 API 的指南。在这里没有太多新东西，我们在缓存章节中没有涵盖到的，但鉴于缓存 API 的“实验性”状态，回头参考一下是很好的。这项技术可以从目前的状态发展，所以把它作为一个参考。

[`developer.mozilla.org/en-US/docs/Web/API/Cache`](https://developer.mozilla.org/en-US/docs/Web/API/Cache)

+   **通过应用安装横幅增加用户参与度**：应用安装横幅的如何和为什么。一个详尽的常见问题解答了你可能有的任何问题。还有一个关于推迟提示的很棒的教程，你可以用它来巩固我们在第九章中涵盖的概念，*使用清单使我们的应用可安装*。

[`developers.google.com/web/updates/2015/03/increasing-engagement-with-app-install-banners-in-chrome-for-android?hl=en`](https://developers.google.com/web/updates/2015/03/increasing-engagement-with-app-install-banners-in-chrome-for-android?hl=en)

+   **Web 基础-性能**：谷歌关于构建高性能 Web 应用的资源。值得注意的是，谷歌对性能有一个特定的哲学，属于 PWA 模型，但不一定是更好性能的唯一途径。也就是说，对于任何对速度感兴趣的人来说，这是一个很棒的（有时过于技术性的）资源。

[`developers.google.com/web/fundamentals/performance/`](https://developers.google.com/web/fundamentals/performance/)

+   **引入 RAIL：面向用户的性能模型**：这篇文章以“性能建议不胜枚举，是吗？”开篇。这是真实的话，尽管*Paul Irish*和*Paul Lewis*的建议比大多数更好。这篇文章特别关注为什么我们应该遵循这个指标来介绍 RAIL。答案？用户应该放在第一位。

[`www.smashingmagazine.com/2015/10/rail-user-centric-model-performance/`](https://www.smashingmagazine.com/2015/10/rail-user-centric-model-performance/)

+   **渐进式 Web 应用通讯简报**：我的免费通讯，让你了解渐进式 Web 应用的世界，包括教程、文章、有趣的项目等。如果你想要联系我，只需点击下一期的“回复”。我会很乐意收到你的来信。

[`pwa-newsletter.com/`](http://pwa-newsletter.com/)

+   **网站性能优化**：另一个由谷歌和 Udacity 合作的课程，这次是关于优化性能的课程。它介绍了 DevTools 并深入探讨了关键渲染路径等概念。这门课程应该需要大约一周的时间来完成。

[`www.udacity.com/course/website-performance-optimization--ud884`](https://www.udacity.com/course/website-performance-optimization--ud884)

+   **浏览器渲染优化**：这里还有一个！这门课程的副标题是“构建 60 FPS Web 应用”，这是一个值得追求的目标（正如我们的 RAIL 指标建议的那样）。它可以被认为是前面课程的更深入版本。在完成这门课程后，你可以称自己为 Web 性能专家。

[`www.udacity.com/course/browser-rendering-optimization--ud860`](https://www.udacity.com/course/browser-rendering-optimization--ud860)

+   **使用 React 构建渐进式 Web 应用**：*Addy Osmani*再次出现。在这里，他带领我们使用 React 构建 PWA。请注意，这个教程更像是一个概述，而不是一个逐步指南，但在我写这本书时，这对我来说是一个非常宝贵的资源。他还提供了许多链接到其他文章和资源，以进一步扩展你的知识。

>[`medium.com/@addyosmani/progressive-web-apps-with-react-js-part-i-introduction-50679aef2b12`](https://medium.com/@addyosmani/progressive-web-apps-with-react-js-part-i-introduction-50679aef2b12)

+   **Service Worker Cookbook**：关于 service workers 的一切你想知道的东西。说真的，这是一个了不起的资源，会让你很快成为专家。如果你对这项新技术感到兴奋并想深入了解，这是一个很好的机会。

[`serviceworke.rs/`](https://serviceworke.rs/)

+   **将你的网站改造成 PWA**：大多数公司不会立即从头开始构建 PWA。相反，他们会希望将 PWA 功能添加到他们现有的网站或应用中。这是一个很好的入门指南，并附有大量的截图。

[`www.sitepoint.com/retrofit-your-website-as-a-progressive-web-app/`](https://www.sitepoint.com/retrofit-your-website-as-a-progressive-web-app/)

# 案例研究

你需要说服老板尝试渐进式 Web 应用吗？看看以下大公司采用 PWA 的案例研究（Chatastrophe Inc.因破产而被移出列表）。

# 构建 Google I/O 2016 渐进式 Web 应用

Google I/O 2016 应用程序（昵称 IOWA）是使用 Firebase 和 Polymer 构建的。这就是他们的做法。这是一个更加技术性的指南，介绍了几个高级概念；这是一个了解下一级 PWA 的好方法。

[`developers.google.com/web/showcase/2016/iowa2016`](https://developers.google.com/web/showcase/2016/iowa2016)

# AliExpress 案例研究

AliExpress 是俄罗斯访问量最大的电子商务网站。通过转换为 PWA，他们将新用户的转化率提高了 104%。他们还将在网站上花费的时间增加了 74%。这些都是很大的数字，为 PWA 提供了一个有力的商业案例。

[`developers.google.com/web/showcase/2016/aliexpress`](https://developers.google.com/web/showcase/2016/aliexpress)

# eXtra Electronics 案例研究

这对于业务改进来说怎么样--销售额增加了 100%。这就是 eXtra Electronics 通过网络推送通知到达的用户所取得的成就。事实上，网络推送通知现在是 eXtra 最大的留存渠道，超过了电子邮件。更加努力！

[`developers.google.com/web/showcase/2016/extra`](https://developers.google.com/web/showcase/2016/extra)

# Jumia 案例研究

又一个关于网络推送通知的好消息。Jumia 的转化率增加了 9 倍。他们过去会发送电子邮件提醒顾客购物车中剩下的物品，但开启率很低。现在引入了通知。

[`developers.google.com/web/showcase/2016/jumia`](https://developers.google.com/web/showcase/2016/jumia)

# Konga 案例研究

你的用户关心他们的数据限制；不要让他们受苦。Konga 将他们的原生应用与 PWA 进行比较，将数据使用量减少了 92%。最终，用户完成第一笔交易所需的数据减少了 84%。考虑到入门的障碍降低了。

[`developers.google.com/web/showcase/2016/konga`](https://developers.google.com/web/showcase/2016/konga)

# SUUMO 案例研究

通过添加服务工作者和一些其他调整，SUUMO 团队将加载时间减少了 75%。他们还利用了推送通知的热潮，开启率达到了 31%。尝试 PWA 的决定背后的故事可能听起来很熟悉；移动体验很差，所以公司将用户推向原生应用。然而，让他们下载原生应用却很困难，所以他们尝试了 PWA。一个很好的教训--如果你的问题是留存，原生应用可能不是答案。

[`developers.google.com/web/showcase/2016/suumo`](https://developers.google.com/web/showcase/2016/suumo)

# 示例应用程序

想看看真正的渐进式 Web 应用程序是什么样子吗？看看以下任何一个。其中一些还包含 GitHub 的链接，供您查看源代码。

# PWA.rocks

这是一个渐进式 Web 应用程序的集合，也是以下大部分内容的来源。如果您需要灵感，可以将其作为第一站。我还鼓励您将您添加到列表中的任何 PWA 添加到其中。

[`pwa.rocks/`](https://pwa.rocks/)

# Flipboard

Flipboard 是 PWA 领域中最重要的参与者之一，他们的 PWA 应用程序体积小，速度快，而且美观。Flipboard 拥有功能齐全的原生应用程序，但也有 PWA，以便在用户偏好方面进行押注。如果内容丰富的 Flipboard 能够符合 PWA 的性能指南，那么天空就是极限。

[`flipboard.com/`](https://flipboard.com/)

# React Hacker News

这是一个备受欢迎的开发者项目：使用 React 克隆的 Hacker News。作为一个开源项目，ReactHN 是了解如何使用渐进式 Web 应用程序基本原理来管理复杂的前端库的好方法。我们的好朋友*Addy Osmani*再次出马。因此，ReactHN 是一个深入了解 Chrome 开发人员如何使用 JavaScript 库构建 PWA 的内部视角。

[`react-hn.appspot.com`](https://react-hn.appspot.com/#/?_k=5kbr5v)/

# Notes

这是一个很好的、体积小的渐进式 Web 应用程序的例子，值得初学者关注。您可以在网站上直接找到 GitHub 的链接，然后查看*Simon Evans*应用程序的结构。在桌面上，应用程序外壳与内容有明显的区别，这使得概念特别直观。最重要的是，该应用在 Lighthouse 上得分 94 分。

[`sii.im/playground/notes/`](https://sii.im/playground/notes/)

# Twitter

也许你听说过这个。

Twitter 是一个真正全球化应用程序的完美例子。他们的应用程序需要能够被所有大陆的用户在各种条件下访问（只需看看 Twitter 在组织阿拉伯之春中所扮演的角色）。

为了实现全球可访问性，Twitter 团队设法将他们的应用程序减小到 1MB，并添加了本文讨论的所有 PWA 功能：主屏幕安装、推送通知和离线访问。

[`lite.twitter.com/`](https://lite.twitter.com/)

# 2048 Puzzle

2048 拼图游戏的 PWA 实现，最初由 Veewo Studio 创建。它只适用于移动/触摸设备，但它是一个游戏应用程序被制作成 PWA 的例子，它快速、高效且可安装。请注意-对于未经培训的人来说，这个游戏非常容易上瘾。

这个开源项目可以在 GitHub 上找到，所以你可以查看结构（特别是 JavaScript 的结构，需要十个文件来运行游戏）。然而，这个应用的不可告人的秘密是，创作者实际上从未打过这个游戏。

[`2048-opera-pwa.surge.sh/`](https://2048-opera-pwa.surge.sh/)

# 阅读的文章

以下文章涵盖了宣言、教程和清单，都是关于 PWA 的崛起以及构建它们的最佳方法。

# 原生应用注定要失败

JavaScript 大师*Eric Elliott*对渐进式 Web 应用的热情宣言。这是对原生应用成本和 PWA 好处的深入探讨。这是一个很好的材料，可以说服正在辩论是否要构建原生应用的老板和同事。后续文章也很棒。

[`medium.com/javascript-scene/native-apps-are-doomed-ac397148a2c0`](https://medium.com/javascript-scene/native-apps-are-doomed-ac397148a2c0)

# 渐进式 Web 应用的一大堆技巧和窍门

*Dean Hume*的各种 PWA 技巧的大杂烩。看看有趣的东西，比如离线 Google Analytics 和测试服务工作者（随着我们继续前进，会有更多内容）。

[`deanhume.com/Home/BlogPost/a-big-list-of-progressive-web-app-tips-and-tricks/10160`](https://deanhume.com/Home/BlogPost/a-big-list-of-progressive-web-app-tips-and-tricks/10160)

# 测试服务工作者

服务工作者是渐进式 Web 应用功能的核心。我们希望确保它们正常工作。我们如何对它们进行单元测试？

[`medium.com/dev-channel/testing-service-workers-318d7b016b19`](https://medium.com/dev-channel/testing-service-workers-318d7b016b19)

# Twitter Lite 和高性能 React 渐进式 Web 应用的规模

Twitter Lite 工程师之一深入探讨了他们的构建过程、挑战，并在开发 Twitter 的 PWA 版本后提出了建议。这是关于部署大规模 PWA 的最接近的操作指南。

[`medium.com/@paularmstrong/twitter-lite-and-high-performance-react-progressive-web-apps-at-scale-d28a00e780a3`](https://medium.com/@paularmstrong/twitter-lite-and-high-performance-react-progressive-web-apps-at-scale-d28a00e780a3)

# 为什么应用安装横幅仍然存在？

这是一个关于当你不是市场领导者时坚持传统应用程序的成本以及渐进式 Web 应用程序如何解决这个问题的优秀总结。阅读到最后，了解一些从原生应用程序转换为 PWA 的公司的统计数据。

[`medium.com/dev-channel/why-are-app-install-banners-still-a-thing-18f3952d349a`](https://medium.com/dev-channel/why-are-app-install-banners-still-a-thing-18f3952d349a)

# 使用 Vue JS 创建渐进式 Web 应用程序

*Charles Bochet*结合了 VueJS，Webpack 和 Material Design 的元素来构建 PWA。这是一个很好的机会，可以在一个新的库中尝试 PWA 概念。

[`blog.sicara.com/a-progressive-web-application-with-vue-js-webpack-material-design-part-3-service-workers-offline-ed3184264fd1`](https://blog.sicara.com/a-progressive-web-application-with-vue-js-webpack-material-design-part-3-service-workers-offline-ed3184264fd1)

# 将现有的 Angular 应用程序转换为渐进式 Web 应用程序

将常规 Angular 应用程序转换为功能性渐进式 Web 应用程序需要什么？*Coskun Deniz*一步一步地带领我们完成了这些步骤。

[`medium.com/@cdeniz/transforming-an-existing-angular-application-into-a-progressive-web-app-d48869ba391f`](https://medium.com/@cdeniz/transforming-an-existing-angular-application-into-a-progressive-web-app-d48869ba391f)

# 推动 Web 前进

“实际上，任何网站都可以，也应该成为渐进式 Web 应用。”

*Jeremy Keith*在他的文章中提出了“推动 Web”的主要论点，他是对的。使您的应用程序（或静态站点）成为渐进式，是为所有用户提供增强体验。对于任何对跳入 PWA 世界持怀疑态度的人来说，这是一篇很好的阅读。

[`medium.com/@adactio/progressing-the-web-9ab55f63f9fa`](https://medium.com/@adactio/progressing-the-web-9ab55f63f9fa)

# 设计降级-敌对环境的 UX 模式

Chipotle 餐厅如何帮助改进您的网站。本文并不特别讨论 PWA，但与渐进增强的概念完全契合，即您的网站应该适用于所有人，然后根据他们的条件（网络速度，浏览器现代性等）变得越来越好。

[`uxdesign.cc/designed-degradations-ux-patterns-for-hostile-environments-7f308d819e50`](https://uxdesign.cc/designed-degradations-ux-patterns-for-hostile-environments-7f308d819e50)

# 使用应用程序外壳架构实现即时加载 Web 应用程序

对应用程序外壳模式的深入解释。如果你正在开发 PWA，这是必读的。

[`medium.com/google-developers/instant-loading-web-apps-with-an-application-shell-architecture-7c0c2f10c73`](https://medium.com/google-developers/instant-loading-web-apps-with-an-application-shell-architecture-7c0c2f10c73)

# 欺骗用户，让他们觉得你的网站比实际更快

一篇很棒的文章，从用户的角度出发（感知时间与实际时间），然后解释了你可以利用的基本技术，来减少应用的感知加载时间。

[`www.creativebloq.com/features/trick-users-into-thinking-your-sites-faster-than-it-is`](http://www.creativebloq.com/features/trick-users-into-thinking-your-sites-faster-than-it-is)

# 苹果拒绝支持渐进式网络应用对未来的网络是一个损害

一个令人耳目一新的诚实看待在这个时候开发渐进式网络应用的经历，以及与 iOS 相关的挣扎。如果你正在考虑生产 PWA，请阅读这篇文章。

[`m.phillydevshop.com/apples-refusal-to-support-progressive-web-apps-is-a-serious-detriment-to-future-of-the-web-e81b2be29676`](https://m.phillydevshop.com/apples-refusal-to-support-progressive-web-apps-is-a-serious-detriment-to-future-of-the-web-e81b2be29676)

# 工具

希望你将来会构建（许多）更多的渐进式网络应用。以下工具将使这个过程更容易、更快速。

# Workbox

Workbox 是“一组用于渐进式网络应用的 JavaScript 库”。更具体地说，它“使创建最佳服务工作者代码变得容易”，并以最有效的方式维护你的缓存。它很容易集成到 Webpack 中。不幸的是，文档不是很好，定制可能会很困难。

然而，Workbox 有很大的潜力，可以自动化开发的各个方面，并隐藏可能会让新开发者望而却步的复杂性。挑战在于不要用更多的复杂性来取代这种复杂性。

[`github.com/GoogleChrome/workbox`](https://github.com/GoogleChrome/workbox)

# Sw-precache

作为 Workbox 的子集，sw-precache 值得单独讨论。它可以用来自动生成一个服务工作者，以预缓存你的应用程序资产。你所需要做的就是将它纳入你的构建过程（有一个 Webpack 插件），并注册生成的服务工作者。

[`github.com/GoogleChrome/sw-precache`](https://github.com/GoogleChrome/sw-precache)

# Sw-toolbox

不适合初学者！与前面的生成工具不同，sw-toolbox 是一组辅助方法。更令人困惑的是，还有 Google Chrome 团队的 Workbox，采用了更模块化的方法。我给你的建议是，先熟悉直接与服务工作者交互，然后，如果你有一个特定的问题可以通过这些工具之一简化，那就采用它。但是，不要去寻找解决你尚未遇到的问题的工具，但像我说的，看到出现的工具来帮助管理复杂性是令人兴奋的。

[`github.com/GoogleChrome/sw-toolbox`](https://github.com/GoogleChrome/sw-toolbox)

# Offline-plugin

另一个使你的应用程序具有离线功能的插件。这个插件使用服务工作者，但为了更好的支持，会回退到 AppCache API。实现看起来简单而直接。

[`github.com/NekR/offline-plugin`](https://github.com/NekR/offline-plugin)

# Manifest-json

一个工具，可以从命令行自动生成 Web 应用程序清单。我的意思是，我觉得我的清单章节还不错，但如果你更喜欢问答式的方法，那也可以，我想。

开玩笑的，这个工具可能会在 Web App 清单发展并承担更多属性时派上用场。

[`www.npmjs.com/package/manifest-json`](https://www.npmjs.com/package/manifest-json)

# Serviceworker-rails

有一个 Ruby on Rails 项目吗？想让你的资产管道使用服务工作者来缓存资产吗？使用这个宝石。文档是 Rails 如何处理缓存和实现服务工作者方法的有趣概述。

[`github.com/rossta/serviceworker-rails`](https://github.com/rossta/serviceworker-rails)

# Sw-offline-google-analytics

前面提到的 Workbox 的一部分，但专门用于具有离线功能的应用程序的 Google Analytics。使用此软件包在连接可用时发送离线请求到 Google Analytics。

[`www.npmjs.com/package/sw-offline-google-analytics`](https://www.npmjs.com/package/sw-offline-google-analytics)

# Dynamic Service Workers (DSW)

使用 JSON 文件配置你的服务工作者；这是一种非常有趣的服务工作者方法，支持关键功能，如推送通知（尽管只能使用 Google Cloud Messaging）。

[`github.com/naschq/dsw`](https://github.com/naschq/dsw)

# UpUp

在您的网站上添加两个脚本，并使其在离线状态下工作。UpUp 是服务工作者技术的美丽实现，适用于简单的用例。当然，它并不适用于高级用户，但是是向每个人介绍服务工作者技术的绝佳方式。

[`www.talater.com/upup/`](https://www.talater.com/upup/)

# 生成渐进式 Web 应用

从命令行生成渐进式 Web 应用文件结构！这仍然是一个正在进行中的工作。

[`github.com/hemanth/generator-pwa`](https://github.com/hemanth/generator-pwa)

# 渐进式 Web 应用配置

另一个来自 Addy Osmani 的样板文件。如果您要构建非 React PWA，请参考此项目结构。

[`github.com/PolymerLabs/progressive-webapp-config`](https://github.com/PolymerLabs/progressive-webapp-config)

# 延伸目标

Chatastrophe 已经启动，但仍然非常基础。现在我们将讨论一些挑战，您可以选择接受，以拓展您的技能并改进我们的应用。

# 切换到 Preact

Preact 是 React 库的 3 KB 版本。它具有类似的 API 和功能，但没有冗余。使用它而不是 React 将提高我们应用的性能。如果您选择这条路线，请考虑使用 Webpack 别名来简化转换。

[`github.com/developit/preact`](https://github.com/developit/preact)

# 显示在线状态

告诉其他用户另一个用户何时在线。UI 由您决定。

# 显示正在输入

在聊天室中常见的功能，用于向用户指示其他人正在输入。对于 Chatastrophe，挑战将是同时表示多个用户正在输入。

# 包括文件上传

人们想要与朋友分享（可能是表情包）。为他们提供一个文件上传系统。

# 创建聊天室

您的朋友曾经有一个真正全球聊天室的愿景；那个愿景很糟糕。让我们通过允许用户创建聊天室来大大提高 Chatastrophe 的可用性。是否有一种方法可以让用户在离线状态下在房间之间导航并阅读消息？

# 无需 React 即可交互

阻碍我们性能的一个问题是需要 React 在向用户显示交互式站点之前启动。如果我们给他们一个纯 HTML 交互式外壳，然后在加载时切换到 React 版本会怎样？这里的挑战将是避免覆盖用户输入，但您可以赢得一些巨大的性能点。

# 构建自己的后端

在本教程中，我们依赖 Firebase，以便将注意力集中在前端，即 React 开发上。然而，在为 Chatastrophe 设计自己的后端 API 方面，有很大的学习机会。最大的好处是可以对页面进行服务器渲染，以获得额外的性能。

# 结束语

编程很困难。学习也很困难。在学习全新概念的同时使用实验性技术进行编程尤其困难。如果你完成了本书中的教程，甚至只是其中的某些部分，你应该为此感到自豪。

我真诚地希望你在这里学到的东西对你的职业有所帮助。这本书对我来说也是一次激动人心的旅程。当我开始时，我对渐进式 Web 应用程序的世界感到兴奋，但绝不是专家。现在，深入研究渐进式 Web 后，我对可能性比以往任何时候都更加兴奋。我希望你也有同样的感觉。

如果你想要联系我，我会很乐意听到你的反馈、批评、问题，或者只是闲聊。你可以在 Twitter 上找到我，用户名是`@scottdomes`，在 LinkedIn 上找到我，也可以在 Medium 上找到我，用户名同样是`@scottdomes`，或者在我的网站[`scottdomes.com`](http://scottdomes.com)上找到我，我在那里发布关于各种主题的 Web 开发教程。

# 总结

我希望提到的资源对你继续 PWA 之旅有所帮助。PWA 是 Web 开发中令人兴奋的一部分，发展迅速；关注前任作者和创作者将帮助你跟上变化的步伐。

在这本书中，我们涵盖了很多内容：从零到一个 React 应用程序，再从一个 React 应用程序到一个渐进式 Web 应用程序。我们从头开始构建了一个完整的应用程序，并部署到世界上可以看到它。我们还使它快速响应，并能够处理各种类型的连接。

我希望你为最终的应用感到自豪，也希望这本书对你有所帮助。祝你未来的所有 PWA 项目好运，让我们继续推动 Web 的发展。

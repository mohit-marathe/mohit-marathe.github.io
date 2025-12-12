+++
date = '2025-12-12T11:30:52+05:30'
draft = false
title = 'Implementing Canvas Slide in Impress'
+++

## Overview

With [support for differently-sized slides](https://mohit-marathe.github.io/posts/impress-multiple-slide-size/) now in place, the next step towards implementing 
[Infinite Canvas](https://nlnet.nl/project/InfiniteCanvas/) in [Collabora Online](https://www.collaboraonline.com/) was to create the canvas slide itself.
This new slide type allows slides to be arranged freely on an expansive canvas, as well as presenting the overall outline of the whole presentation in a visual way which users can intuitively grasp.

Thanks to [nlnet Foundation](https://nlnet.nl/) for sponsoring this project!


## How it works?

An uno command `.uno:InsertCanvasSlide` was created [[1]](#commit-1), which when dispatched will insert canvas slide at the top. The canvas slide contains page previews of all the slides, which initially is arranged as a grid at the center of the canvas [[2]](#commit-2).
Since users are free to move the page previews, it helps to have some kind of a visual indicator for the page order. Hence, all the previews are connected by connectors ending with arrows for denoting the order [[?]](#commit-?).
It also allows changing the order of the slides based on the position of the previews. So by moving the previews, and then dispatching `.uno:ReshufflePages` will change the order of the slides in the presentation.
For allowing quick editing (instead of scrolling the slide sorter to find the correct page), double clicking on a preview will switch to that page [[4]](#commit-4).

Since the canvas page is a "special" slide, some work needed to be done in export and import logic. For exporting to `.odp`, a "HasCanvasPage" property was added to `settings.xml` (this way we don't need to modify the ODF specs) [[6]](#commit-6).
The "HasCanvasPage" property will be processed while importing , such that it will validate the canvas slide, and repair it if necessary [[5]](#commit-5).
Importing page previews inside canvas pages also needed some special handling as while adding those shapes their corresponding pages does not exist (as canvas slide is the first slide) [[8]](#commit-8).

Now that all the functionalities are in place, next part is about ergonomics.
Since canvas slide is unusually larger than other normal slides, it makes sense to have a different zoom for canvas slide than the rest of the slides [[6]](#commit-6).
And due to the same reason, its quite useful to remember the last visible part of the canvas page (otherwise users would need to scroll the large canvas page to reach the desired part) [[7]](#commit-7)

Also, after creating the canvas page, the view will be set to the center of the canvas slide (as initially all the previews are at the center) [[?]](#commit-?)

Some core.git patches implementing new features:

<a id="commit-1"></a>[1] [sd: add uno command for inserting a canvas page](https://gerrit.libreoffice.org/c/core/+/192319)

<a id="commit-2"></a>[2] [sd: populate page previews grid in canvas page](https://gerrit.libreoffice.org/c/core/+/192767)

<a id="commit-3"></a>[3] [192940: sd: add support for shuffling slides from canvas page](https://gerrit.libreoffice.org/c/core/+/192940)

<a id="commit-4"></a>[4] [sd: switch to corresponding page on double clicking page preview](https://gerrit.libreoffice.org/c/core/+/193861)

<a id="commit-5"></a>[5] [sd: store "HasCanvasPage" boolean in settings.xml](https://gerrit.libreoffice.org/c/core/+/193243)

<a id="commit-6"></a>[6] [sd: remember canvas page zoom](https://gerrit.libreoffice.org/c/core/+/193459)

<a id="commit-7"></a>[7] [sd: remember last client visible area of canvas page](https://gerrit.libreoffice.org/c/core/+/193862)

<a id="commit-8"></a>[8] [xmloff: sd: handle import of page shapes that are inside canvas page](https://gerrit.libreoffice.org/c/core/+/195076)

<a id="commit-9"></a>[9] [sd: lok: send CanvasPageCenter command to client on canvas page creation](https://gerrit.libreoffice.org/c/core/+/195466)


# Release 2.0.1

## Features
- Preview of TensorBoard.dev uploader! Check out <https://tensorboard.dev/> for
  information and usage instructions.

# Release 2.0.0

The 2.0 minor series tracks TensorFlow 2.0.

## Breaking changes

- TensorBoard now serves on localhost only by default to avoid unintentional
  overexposure. To expose TensorBoard to the network, either use a proxy, bind
  to a specific hostname or IP address by using the `--host` flag, or explicitly
  enable the previous behavior of binding on all network interfaces by passing
  the flag `--bind_all`. See PR #2589.

- The `--logdir` flag no longer supports passing multiple comma-delimited paths,
  which means that it now *supports* paths containing literal comma and colon
  characters, like `./logs/m=10,n=20,lr=0.001` or `./logs/run_12:30:15`. To
  mimic the old behavior, prefer using a tree of symlinks as it works with more
  plugins, but as a fallback the flag `--logdir_spec` exposes the old behavior.
  See PR #2664.

- Projector plugin `visualize_embeddings()` API now takes `logdir` as its first
  parameter rather than `writer` (which only supported TF 1.x summary writers).
  For backwards compatibility TF 1.x writers will still be accepted, but passing
  the logdir explicitly is preferred since it works without any dependency on
  TF 1.x or 2.x summary writing. See PR #2665.

- The namespace `tensorboard.summary.*` now aliases the summary API symbols in
  `tensorboard.summary.v2.*` rather than those in `tensorboard.summary.v1.*`.
  The old symbols can still be accessed under the `.v1` names. Note that the
  new v2 API symbols are exposed in TF 2.0 as the new `tf.summary.*` API and
  this is normally how they should be used. See PR #2670.

## Features

- Smarter log directory polling can be used by passing `--reload_multifile=true`
  to poll all "active" event files in a directory rather than only the last one.
  This avoids problems where data written to the non-last file never appears.
  See PR #1867 for details, including how to adjust the "active" threshold.

- What-If Tool now can sort PD plots by interestingness (#2461)


# Release 1.15.0

The 1.15 minor series tracks TensorFlow 1.15.

## Features
- Embeddings projector now shows sprite images in the nearest neighbors list
  (#2543) - thanks @beasteers
- When recording hyperparameters, the trial ID can now be customized, for easier
  integration with existing tuner systems (#2442)
- Improvements to Colab and Jupyter notebook integration:
  - The `TENSORBOARD_BINARY` environment variable can now be set to invoke a
    non-default `tensorboard` binary (#2386)
  - Error messages are now clearer when the TensorBoard binary fails to launch
    (#2395)
  - The `%tensorboard` magic no longer spams log messages when a different
    version of TensorBoard is already running on the same machine (#2470)
  - The `%tensorboard` magic can now be used in Jupyter notebooks running on
    hosts other than `localhost` (#2407)
- What-If Tool improvements:
  - Errors running inference are now surfaced in the What-If Tool UI (#2414)
  - Median error stats are now displayed in addition to mean error stats (#2434)
- Mesh plugin improvements:
  - Now compatible with TensorFlow 2.0 via a new `summary_v2` module (#2443)
  - The number of vertices in the mesh can now be dynamic (#2373)
- Profile dashboard improvements:
  - Wasted time now appears in the node table, and can be used as a sort key
    (#2525)
  - Memory bandwidth utilization now appears in the dashboard header (#2525)
- Improvements for plugin developers:
  - Plugins can now be rendered in an iframe whose source is served from the
    plugin backend, eliminating the need to bundle a frontend with the
    TensorBoard binary
  - Plugins can now be discovered dynamically and loaded at runtime, by defining
    a `tensorboard_plugins` entry point
  - See our [example dynamically loaded plugin][example-plugin] for a plugin to
    use as a starting point, plus documentation
  - TensorBoard now uses Polymer 2.7 (#2392, et al.)

[example-plugin]: https://github.com/tensorflow/tensorboard/tree/1.15/tensorboard/examples/plugins/example_basic#readme

## Bug fixes
- #2614 - “Toggle All Runs” button now behaves correctly on the first click when
  many runs are loaded (PR #2633)
- Scalar charts should no longer “become tiny” on certain kinds of rendering
  failures (PR #2605)
- #2028 - TensorBoard now logs less verbosely with Werkzeug 0.15.0 and up; it
  now behaves the same across Werkzeug versions (PR #2383)
- The What-If Tool can now properly compare two regression models in the initial
  Facets Dive view (PR #2414)
- Embedding projector metadata view now wraps long strings correctly (PR #2198)


# Release 1.14.0

## Features
- New hyperparameters dashboard: see [tutorial and demo][hparams-docs] and
  [summary APIs][hparams-apis]
- New dashboard for visualizing meshes and point clouds: see
  [README][mesh-readme]
- Graph dashboard now shows Keras conceptual graph: see [tutorial and
  demo][conceptual-graph-docs]
- Embedding projector now supports the [UMAP dimensionality reduction
  algorithm][umap] ([learn more about UMAP here][umap-tutorial]) (#1901) -
  thanks @cannoneyed
- [TensorBoard notebook support][notebook-docs] is stabilized: in a Colab or
  Jupyter notebook, run `%load_ext tensorboard` followed by `%tensorboard
  --logdir ./path/to/logs`
- Profile dashboard improvements:
  - New pod viewer tool to analyze TPU performance (#2111)
  - Now allows capturing profiles from TensorBoard (#1894)
- What-If Tool improvements:
  - Now available as a notebook widget for Jupyter and Colab: see
    [demo][witwidget-demo]
  - Now shows PR curves and F1 score (#2264)
  - Now supports Cloud AI Platform, including XGBoost models (#2194)
  - Now shows feature-level attributions for individual predictions, as
    applicable (#2252)
- Image dashboard now allows scrolling for large images (#2164) - thanks @lr1d
- Scalar chart smoothing now caps at 0.999 for convenience (#1974) - thanks
  @flostim
- Scalar chart scroll-to-zoom behavior now requires holding `Alt` (#2221)
- `tensorboard` now supports a `--version` command line argument (#2097) -
  thanks @shashvatshahi1998
- Python API now defines `tensorboard.__version__` in addition to
  `tensorboard.version.VERSION` (#2026)

## Bug fixes
- Projector metadata card now formats long words properly (PR #2016) - thanks
  @makseq
- #2010 - `.tensorboard-info` is now world-writable for multi-user \*nix systems
  (PR #2131)
- #1989 - `importlib.reload(tensorboard)` now works properly (PR #2005)

[conceptual-graph-docs]: https://www.tensorflow.org/tensorboard/r2/graphs
[hparams-apis]: https://github.com/tensorflow/tensorboard/blob/1.14/tensorboard/plugins/hparams/api.py#L15
[hparams-docs]: https://www.tensorflow.org/tensorboard/r2/hyperparameter_tuning_with_hparams
[mesh-readme]: https://github.com/tensorflow/tensorboard/blob/1.14/tensorboard/plugins/mesh/README.md#mesh-plugin
[notebook-docs]: https://www.tensorflow.org/tensorboard/r2/tensorboard_in_notebooks
[umap-tutorial]: https://umap-learn.readthedocs.io/en/latest/how_umap_works.html
[umap]: https://github.com/lmcinnes/umap#umap
[witwidget-demo]: https://colab.research.google.com/github/tensorflow/tensorboard/blob/1.14/tensorboard/plugins/interactive_inference/What_If_Tool_Notebook_Usage.ipynb


# Release 1.13.1

## Bug fixes
- #1895 - Fix `strftime`-related launch error on Windows (PR #1900)
- #1794 - Fix What-If Tool loading examples without inference (PR #1898)
- #1914 - Disable the profile dashboard inside Colab, where it doesn’t work
- #1945 - Fix profile dashboard loading behavior


# Release 1.13.0

The 1.13 minor series tracks TensorFlow 1.13.

Compatibility note: As of 1.13, TensorBoard has begun transitioning its own use
of some TensorFlow APIs to align with their naming in TF 2.0, and as a result
TensorBoard 1.13+ strictly requires TensorFlow 1.13+.

## Features
- What-If tool notebook mode and general improvements
  - Now usable directly inside Jupyter and Colab notebooks (#1662, #1745, #1788)
  - Added comparison of multiple models (#1589, #1672)
  - Added CSV loading model (#1597)
  - Added global partial dependence plots (#1604)
  - Added custom prediction function support (#1842)
- (Alpha) TensorBoard can be embedded inside Juptyer and Colab notebooks via a
  `%tensorboard` magic, after loading the `tb.notebook` extension (#1813, #1822)
- Profile dashboard overview page now shows step time breakdown (PR #1683)
- Line chart "log" scale is now a true log scale (#1507)
- When no --port flag is specified, TensorBoard will now search for open ports
  near the default port (6006) if that port is already in use (#1851)

## Performance improvements
- Faster event file loading by caching runtime check (PR #1686) - thanks @abiro

## Bug fixes
- #786 (partial) - Avoid trying to smooth plots of constant y-value (PR #1698)
- #1515 - Fix image right-click accessiblity in non-Chromium browsers (PR #1561)
- #1541 - Fix --event_file flag when using --inspect
- #1566 - Fix error on trying to import "google.protobuf.pyext" (PR #1887)
- #1567 - Fix display bug on line chart after toggling series selection
- #1598 - Fix clipping in graph dashboard PNG download (PR #1600)
- #1601 - Fix chart SVG download option in Firefox
- #1623 - Fix --path_prefix interpretation
- #1838 - Fix run selector synchronization across already-loaded dashboards


# Release 1.12.2

## Bug fixes
- #1620 - Fix path_prefix flag regression (PR #1623)
- #1704 - Fix debugger sidebar resizer


# Release 1.12.1

## Bug fixes
- #1549 - Run names wrap at all character (PR #1602) - thanks @dgrahn
- #1610 - Fix Download as PNG for large graph
- #1684 - Fix bug rendering debugger plugin (PR #1550) - thanks @longouyang


# Release 1.12.0

The 1.12 minor series tracks TensorFlow 1.12.

## Features
- New download-as-SVG option for scalar dashboard charts (#1446)
- Image dashboard should now detect and render SVG images (#1440)
- What-If Tool example viewer/loader improvements:
  - Support for sampling examples to load (#1504)
  - Support for viewing SequenceExamples (#1513)
  - Improvements to saliency viewing/sorting (#1472)
- Profile tool shows per-program breakdown, idle time, and ops left out (#1470)

## Bug fixes
- #1463 - What-If tool now handles classes with blank labels (PR #1471)
- #1468 - Reduce clipping in graph plugin sidebar
- #1475 - Restore tag filter persistence to URL param and across dashboards
- #1477 - Fix bug rendering TPU profile dashboard overview page
- #1480 - Fix projector hanging due to infinite loop (PR #1481)
- #1491 - Restore spinner on line charts when loading data
- #1499 - Fix stale/incorrect line charts when filtering by tag (PR #1500)
- #1505 - Fix 404 console errors in Firefox - thanks @wdirons
- #1506 - Fix --purge_orphaned_data to allow passing false (PR #1511)
- #1508 - Make custom scalars chart ignore outliers functionality work
- #1524 - Preserve line chart zoom level when data refreshes


# Release 1.11.0

The 1.11 minor series tracks TensorFlow 1.11.

## Highlights
- New What-If Tool dashboard, which provides a simple, intuitive, and powerful
  visual interface to play with a trained ML model on a set of data with
  absolutely no code required. See for details:
  https://github.com/tensorflow/tensorboard/tree/1.11/tensorboard/plugins/interactive_inference

## Features
- Graph dashboard now supports coloring nodes by XLA cluster (PR #1336)
- Last updated time appears in tooltip for refresh button (PR #1362)
- Line charts support pan w/ shift key, zoom w/ scroll wheel (PR #1429, #1456)

## Performance improvements
- Better UI animation/scrolling performance (#1311, #1357)
- Reduced Plottable MouseInteraction overhead on hover (#1333/#1329)
- Optimized line chart tooltip redraw behavior (#1355)

## Bug fixes
- #982  - Fix spurious 404s for /[[_dataImageSrc]] or /[[_imageURL]] (PR #1315)
- #1320 - Fix port binding to disallow confusing IPv4/IPv6 port reuse (PR #1449)
- #1397 - Fix multi-part logdirs to correct expand ~ for user homedir
- #1396 - Fix "step" chart axis to show only integer ticks
- #1389 - Fix scalar card titles to omit common prefix (PR #1399)
- #1403 - Fix scalar chart shrinking problem on fast page changes
- #1406 - Fix scalar chart tooltip display to better avoid clipping


# Release 1.10.0

The 1.10 minor series tracks TensorFlow 1.10.

## Changes
- New logic for loading/launching TensorBoard (PR #1240)
  - Plugin loading now uses new TBLoader API
  - Argument parsing now uses argparse
  - New `tb.program.launch()` API to launch TB from within Python
- Sidebars adjusted to be consistent across plugins (PR #1296)
- tb.summary.image() param order fixed to h, w (PR #1262) - thanks @ppwwyyxx
- New TPU profile dashboard progress bar for loading tools (PR #1286)

## Bug fixes
- #1260 - Fix missing pie chart in TPU profile input pipeline analyzer
- #1280 - Fix TPU profile memory viewer issue with XLA compatibility
- #1287 - Fix dangling UI interaction layer issue in vz-line-chart
- #1294 - Fix custom scalar dashboard to de-duplicate charts - thanks @lgeiger


# Release 1.9.0

The 1.9 minor series tracks TensorFlow 1.9.

## Highlights

- Improved performance with log directories on GCS (Google Cloud Storage) with
  faster traversal time and reduced bandwidth consumption (PRs #1087, #1226)
- Profile dashboard improvements, including:
  - New memory viewer tool that visualizes peak memory usage (#1223)
  - Trace viewer tool now supports streaming mode, that dynamically renders a
    much longer trace (#1128)
  - Op profile tool now shows memory utilization in op details card (#1238)
  - Profile dashboard now supports visualizing data from multiple hosts (#1117)

## Features
- Graph dashboard now allows searching nodes by regex (#1130)
- New --samples_per_plugin flag to control how many samples are kept (#1138)
- Better error when --logdir/--db flag is omitted (#1189) - thanks @oxinabox
- Debugger plugin can now show single elements of string tensors (#1131)

## Bug fixes
- #1107 - Beholder plugin should no longer reserve GPU (PR #1114)
- #1190 - Beholder plugin summary placeholder no longer interferes with normal
          summary use and/or Estimator - thanks @TanUkkii007 (PR #1148)
- #427 and #588 - removed pip package deps on bleach and html5lib (PR #1142)
- #1191 - fixed debugger plugin UnboundLocalError - thanks @cfroehli
- #1200 - fixed debugger plugin binary-valued string tensor issues
- #1201 - fixed "dictionary changed size" race condition in reloader (PR #1235)


# Release 1.8.0

The 1.8 minor series tracks TensorFlow 1.8.

## Bug fixes

- #1082 - fixes rendering for certain graphs with metaedges/function nodes
- #1097 - correction to debugger plugin keras code snippet (PR #1100)
- #1111 - event reader logic now supports TF 1.8 GetNext() API (PR #1086)


# Release 1.7.0

The 1.7 minor series tracks TensorFlow 1.7.

## Highlights

- (Beta) New Beholder plugin that shows a live video feed of tensor data during
  model training, by @chrisranderson. Caveat: only currently recommended for use
  where TensorBoard and TensorFlow share a local disk. See for details:
  https://github.com/tensorflow/tensorboard/tree/1.7/tensorboard/plugins/beholder

## Features

- Debugger tensor value card improvements:
  - Entering new slice/time indices will automatically refresh view (#1017)
  - Clicking title will highlight node in other parts of the UI (#1023)
- Debugger health pills now show number of NaN/Inf values if any (#1026)

## Changes

- Audio summary playback elements no longer loop by default (PR #1061), but
  looping can be enabled for individual elements through a right-click option.

## Bug fixes

- #965 - pr_curve_streaming_op no longer results in duplicate plots (PR #1053)
- #967 - custom scalar margin plots with missing tags now indicate the run
- #970 - browser back button now works across home page (/) - thanks @brianwa84
- #990 - apple-touch-icon.png requests no longer trigger 404s - thanks @lanpa
- #1010 - content no longer intrudes into sidebar on narrow viewports
- #1016 - CTRL+C now exits TensorBoard even with debugger enabled (PR #975)
- #1021 - text plugin no longer always shows as inactive on first page load


# Release 1.6.0

NOTICE: TensorBoard 1.6.0+ has moved to the `tensorboard` package name on PyPI:
https://pypi.python.org/pypi/tensorboard. Only bugfix updates on 1.5.x will be
applied to the old package name (`tensorflow-tensorboard`). To upgrade to
TensorBoard 1.6.0+ we suggest you *first* `pip uninstall tensorflow-tensorboard`
before doing `pip install tensorboard`. See "Known Issues" below if you run into
problems using TensorBoard after upgrading.

The 1.6 minor series tracks TensorFlow 1.6.

## Highlights

- (Beta) New Profile dashboard, which provides a suite of tools for inspecting
  TPU performance.  See for details:
  https://github.com/tensorflow/tensorboard/tree/1.6/tensorboard/plugins/profile
- (Alpha) New Debugger dashboard, which provides a visual interface to `tfdbg`,
  the TensorFlow debugger. See for details:
  https://github.com/tensorflow/tensorboard/tree/1.6/tensorboard/plugins/debugger

## Known issues

- Package `tensorboard` is installed but command and/or module are missing or
  have the wrong version - this may be due to conflicts with other packages that
  provide `tensorboard` scripts or modules. Please uninstall *all* such packages
  including `tensorboard`, `tensorflow-tensorboard` and `tb-nightly` and then
  reinstall `tensorboard`.
- Bazel 0.9.0+ required to build from source - this change was necessary in order
  to add support for building at Bazel 0.10.0 and above. Please update Bazel.


# Release 1.5.1

NOTICE: TensorBoard 1.6.0+ will move to the `tensorboard` package name on PyPI,
instead of using `tensorflow-tensorboard`. Only bugfix updates on 1.5.x will be
applied to the old package name. To upgrade to TensorBoard 1.6.0+ please *first*
`pip uninstall tensorflow-tensorboard` before doing `pip install tensorboard`.

The 1.5 minor series tracks TensorFlow 1.5.

## Bug fixes

- #554 - line charts no longer "shrink" after run changes on other tabs
- #889 - fixed xComponentsCreationMethod default in vz-line-chart
- #898 - fixed offset of checkbox label in projector dashboard - thanks @marcj
- #903 - disabled margin plot smoothing to avoid line going out of bounds
- #916 - made `futures` dependency py2-only to avoid install failures
- #924 - fixed graph dashboard bug causing blank PNG download and minimap
- #926 - made projector plugin API available in pip package

## Documentation updates

- Custom scalars documentation now documents margin plots feature (#878)
- FAQ updated to describe custom scalars plugin use cases


# Release 1.5.0

The 1.5 minor series tracks TensorFlow 1.5.

## Highlights

- New Custom Scalars dashboard, which can display configurable custom line and
  margin charts based on the same data as the regular Scalars dashboard. See
  for details: https://github.com/tensorflow/tensorboard/tree/1.5/tensorboard/plugins/custom_scalar
- Many projector plugin enhancements thanks to @francoisluus, which enable
  cognitive-assisted labeling via semi-supervised t-SNE
  - t-SNE specific features: semi-supervision (#811) plus perturb (#705) and
    pause/resume (#691) buttons
  - general features: metadata editor (#753), selection edit mode (#697), edit
    box for neighbors slider (#733), 2D sprite element zooming (#696)

## Features

- Image dashboard brightness and constrast sliders (#771) - thanks @edmundtong
- Top-level dashboard tabs now scroll when there are too many to fit (#730)
- Settable browser window title with --window_title flag (#804) - thanks @tkunic
- Tag filters are now reflected in the URL, making them saveable (#787)
- Pane-based dashboards now only load charts from first two panes by default,
  which should improve responsiveness (#643 defaults tag filter search string
  to empty, and #871 makes first two panes open by default)
- Lower latency to serve TensorBoard HTML thanks to preloading in memory (#708)
- Lazy imports ("import tensorboard as tb") now work for summary APIs (#778)
- PR curve summaries now have pb (#633) and raw_data_pb (#646) versions

## Bug fixes

- #265 - fixed `--logdir` to handle Windows drive letters - thanks @shakedel
- #784 - fixed bug in find similar subgraph algo - thanks @trisolaran
- Graph plugin fixed to
  - correctly render function nodes (#817)
  - pan to nodes more reliably (#824, #837)
  - rebuild hierarchy if callbacks change to avoid race in rendering (#879)


# Release 0.4.0

The 0.4 minor series tracks TensorFlow 1.4.

## Features

- PR Curve plugin has a full-featured new dashboard (#387, #426, many others)
- PR Curve plugin has new streaming and raw summary-writing ops (#520, #587)
- Graph plugin has a new "Functions" scene group to show function libraries and
  links to function calls (#394, #395, #497, #551, others)
- Graph plugin metanodes are now colored more helpfully (#467)
- Graph plugin selected run is now persisted to URL (#505)
- Standard dashboard card header UI is more compact and readable (#430)
- Pagination limit can now be configured in settings (#535)
- Text plugin now has op and pb summary writing methods (#510)
- Reduced boilerplate and cleaner API hooks for custom plugins (#611, #620)
- Faster initial loads due to improved active plugin detection (#621, #663)
- Reuse of TCP connections with switch to using HTTP/1.1 (#617)

## Bug fixes

- #477 - fixed URLs to properly URI-encode run and tag names
- #610 - fixed smoothing algorithm initial value bias - thanks @alexirpan
- #647 - fixed text plugin decoding error that led to bad markdown processing

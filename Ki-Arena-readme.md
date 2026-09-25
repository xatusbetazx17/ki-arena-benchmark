# Ki Arena — Battle Benchmark

**An unofficial Dragon Ball-inspired CPU and graphics benchmark with automatic transformations and local hardware monitoring for Windows and Linux.**

Watch Goku power up, see Vegeta enter the fight, and follow Frieza through his transformations while your computer runs CPU simulation and WebGL graphics workloads together. Ki Arena reports measured performance and, when launched locally, operating-system and supported driver readings.

**Version:** 3.2.0  
**Maintainer:** [xatusbetazx17 · xatusbetazx17](https://github.com/xatusbetazx17)  
**Browser demo:** [Launch Ki Arena](https://ki-arena-benchmark.xatusbetazx17.chatgpt.site)

> Native hardware readings require the downloaded package and its local Python launcher. The hosted demo and GitHub Pages version provide browser benchmarks only. This project is experimental; actual Windows execution and physical GPU sensor readings have not yet been verified by the development tests.

## Features

- Automatic **Goku → Vegeta** sequence covering all 25 included hero forms.
- Eight Frieza forms, ending with **Black Frieza**.
- Goku forms including **Blue Kaioken**, **Ultra Instinct Sign**, **Ultra Instinct** and **True Ultra Instinct**.
- Vegeta forms including **Blue Evolved** and **Ultra Ego**.
- Rotating attacks, power-up effects and selectable effect density.
- **60-, 120- and 180-second** combined CPU/graphics runs, plus warm-up.
- **Uncapped completed scenes per second**, measured separately from display FPS.
- Display frame-time chart, average FPS, 1% low FPS and P95 frame time.
- Separate CPU, memory-copy and graphics component tests.
- Live local hardware monitoring and downloadable JSON reports.
- A separate playable drone-defense mode with movement and attack controls.
- Python standard-library launcher: no pip packages or JavaScript build step required to run the portable package.

## Requirements

- Windows or Linux with a graphical desktop.
- Python 3 for the local launcher.
- A browser supporting hardware-accelerated WebGL 2 for the fighting scene.
- A working graphics driver; it may already be installed with your system.
- Optional driver utilities or sensor providers for additional hardware readings, described below.

The package contains source files and launch scripts. It is not a standalone Windows EXE or Linux installer. Browser-only use does not require Python.

## Quick start: Windows

1. Download the complete project ZIP and extract it into a folder.
2. Install [Python 3](https://www.python.org/downloads/windows/) if it is not already available.
3. Double-click **start-windows.bat**.
4. Keep the launcher console open. Your default browser should open the game.
5. If it does not open, copy the exact `http://127.0.0.1:PORT/` address printed in the console into your browser.
6. Select **Automatic · Goku → Vegeta**, choose a workload, then click **Start battle benchmark**.

From PowerShell, you can also start it with:

```powershell
.\start-windows.bat
```

Or run the Python launcher directly:

```powershell
py -3 launcher.py
```

## Quick start: Linux

Extract the complete ZIP, open a terminal in that folder, then run:

```bash
sh start-linux.sh
```

Or launch it directly:

```bash
python3 launcher.py
```

If Python 3 is missing, install it through your distribution's package manager. If the browser does not open automatically, use the exact local URL printed by the launcher.

The launcher binds only to `127.0.0.1` and chooses a free port. To specify a port or avoid opening a browser automatically:

```bash
python3 launcher.py --port 8765 --no-browser
```

Use **Ctrl+C** in the launcher console to close the local server and hardware collector. **Stop test** or **Escape** cancels a benchmark; hardware monitoring continues while the launcher is open. Opening `index.html` directly does not start the hardware collector and can prevent module/worker loading—use the launcher.

## Battle modes

### Automatic Goku → Vegeta

The default sequence visits all **14 included Goku forms**, then brings in Vegeta for all **11 included Vegeta forms**, ending in Ultra Ego. Frieza progresses through all eight included forms across the test. Signature attacks rotate automatically.

Every complete automatic run fits the same 25 hero stages into the chosen duration:

| Measured duration | Time per hero form |
| --- | --- |
| 60 seconds | 2.4 seconds |
| 120 seconds | 4.8 seconds |
| 180 seconds — default | 7.2 seconds |

There is an additional warm-up before measurement. The 180-second option gives each transformation more time on screen. Included forms span major Z/Super forms and explicitly marked GT branches; this is a visual showcase rather than a canonical story sequence or every form in the franchise.

### Selected forms and single-fighter tour

Choose a fighter, form, Frieza form and signature attack manually, or use the one-fighter tour that transforms every 12 seconds. Short single-fighter tours may end before every form appears. Settings are locked during measurement for repeatability.

### Individual components

Run the CPU, memory-copy and display-paced graphics stages separately. These workloads produce different results from the combined battle test.

### Free battle

Play the separate drone-defense game. Move with **WASD / arrow keys**, aim and fire with **click / Space**, and switch attacks with **1 / 2 / 3** or the on-screen controls. Free battle does not produce benchmark scores.

## Graphics presets

| Preset | Render resolution | Background sparks |
| --- | --- | ---: |
| Standard | 1280 × 720 | 40,000 |
| Heavy | 1920 × 1080 | 100,000 |
| Ultra | 2560 × 1440 | 250,000 |
| Extreme | 3840 × 2160 | 500,000 |

Aura and attack density can be set to **1×, 2× or 4×**. The CPU selector allows up to 64 workers, bounded by the logical processor count exposed by the browser; the initial selection is capped at 16. More workers can increase contention and do not guarantee higher throughput.

Start with Standard. The app does not silently lower the selected quality during a run. Keep the tab visible; switching tabs cancels the test so background throttling does not become a completed score.

## Real hardware monitoring

Run the local launcher, then expand **Installed hardware and live sensors** beneath the arena. Native measurements are collected from the OS and available drivers; they are not inferred from the animation.

| Reading | Windows | Linux |
| --- | --- | --- |
| CPU model and core counts | Windows CIM and OS | `/proc/cpuinfo` and OS |
| Installed GPU names | Windows CIM | `lspci`, with sysfs PCI identifiers as a fallback |
| Whole-system CPU usage | `GetSystemTimes`, up to 64 logical processors | `/proc/stat` deltas |
| RAM usable by OS, used and available | `GlobalMemoryStatusEx` | `/proc/meminfo` |
| Memory module capacities and configured speed | CIM, when exposed | Not collected by the current implementation |
| Storage device model and capacity | Windows CIM | `lsblk`, when available |
| NVIDIA GPU utilization, VRAM, temperature and power | `nvidia-smi`, when supported | `nvidia-smi`, when supported |
| Other supported GPU load/sensors | Optional LibreHardwareMonitor WMI | DRM sysfs and hwmon, depending on driver |
| CPU/board temperatures, power and fans | Optional LibreHardwareMonitor WMI | Readable kernel hwmon sensors |
| CPU frequency | Optional sensor-list readings | Average available cpufreq readings |

### NVIDIA

Detailed NVIDIA readings use `nvidia-smi`, supplied with supported NVIDIA driver installations. It must be available on PATH or, on Windows, in the standard NVSMI directory. Individual fields can remain unavailable on unsupported devices or drivers.

### AMD and Intel

On Linux, readings depend on the installed kernel driver and exposed DRM/hwmon files. The current DRM collector reads AMD-style GPU-busy and VRAM attributes where present; it does not guarantee Intel GPU utilization.

On Windows, installed GPU identification uses CIM. Additional AMD/Intel load, temperature, power or clock readings may appear in the sensor list through a running [LibreHardwareMonitor](https://github.com/LibreHardwareMonitor/LibreHardwareMonitor) instance with an accessible WMI provider. Support varies by device and permissions. The game does not install drivers or that optional application.

### Reading limitations

- Unsupported readings show **Unavailable**, not invented values or zero.
- CPU/RAM usage includes other applications running on the system.
- RAM usable by the OS may differ from installed DIMM capacity because of reserved memory.
- Storage model and capacity are inventory; the app does not benchmark disk speed.
- GPU inventory can list multiple adapters, while the browser renders on only one.
- Component power readings are not necessarily the total PC's wall-plug power.
- Virtual machines and containers may expose virtual devices, restricted inventory or host-level counters.
- Native Windows CPU usage is unavailable above 64 logical processors to avoid presenting a processor-group reading as whole-system usage.
- The collector targets roughly two-second updates; provider calls can delay them. Readings older than 15 seconds are marked stale.

## Understanding benchmark results

**Uncapped scenes/second:** the renderer submits batches of eight scenes to an off-screen WebGL 2 buffer and uses GPU completion fences before counting them. Completed scenes are divided by the full measured wall-time window. Batches crossing the measurement boundaries are excluded. This workload does not use the display refresh loop as its frame limit, although browser scheduling, JavaScript, CPU contention, drivers and preview overhead still affect throughput. It is not a GPU-only measurement or predicted native-game FPS.

**Display FPS and frame times:** the visible preview follows browser/display timing. Average FPS, 1% low FPS and P95 describe that preview's frame pacing. A 60 Hz display can still limit the visible preview even when the off-screen result exceeds 60 scenes/second.

**CPU throughput:** completed updates from a seeded 2,048-particle simulation, reported in millions of particle updates per second. It is not a FLOPS measurement. Multi-worker throughput includes scheduling and contention over the shared observed worker window.

**Memory copy:** logical bytes copied between two 32 MiB buffers, reported in GB/s. Browser behavior and caches affect the result; it is not rated RAM bandwidth.

**GPU draw timing:** optional asynchronous GPU timer queries measure draw execution where supported. Disjoint/invalid samples are discarded.

**Native hardware report:** sampled average CPU use, sampled peak RAM use and available sensor/GPU peaks accompany the export. Hardware recording can include warm-up, and short spikes may fall between samples. Collection timestamps and latency are included in the raw data.

Compare results using the same app version, browser, drivers, duration, preset, worker count, transformation sequence, effect density and monitoring setup. Monitoring itself adds overhead. These are workload-specific measurements, not a universal PC rating, overclock-stability certification or diagnosis of thermal throttling.

## Exporting results

Finish a test and select **Export results** to download JSON. Completed results include workload settings, CPU measurements, graphics/frame timing, transformation schedules where applicable, and available local hardware inventory/snapshots. Cancelled runs do not replace the previous completed result.

## Privacy

The game does not upload benchmark results or hardware readings. The collector serves read-only data through the local launcher, rejects incorrect Host headers and foreign browser origins, and does not collect device serial numbers, user names, process names or network addresses. Exported JSON does contain hardware models and sensor data.

## Project files

The portable GitHub ZIP places these files at the repository root:

| File or directory | Purpose |
| --- | --- |
| `index.html`, `style.css` | Application interface |
| `app.js` | Renderer, controls and test orchestration |
| `worker.js`, `stats.js`, `uncapped.js` | CPU workloads, statistics and off-screen rendering test |
| `fight.js`, `forms.js`, `saga.js`, `sprite-bounds.js` | Fight animation, forms and automatic sequence |
| `hardware.js` | Hardware dashboard, recording and report integration |
| `hardware.py`, `launcher.py` | Native collector and local HTTP launcher |
| `start-windows.bat`, `start-linux.sh` | Platform launch scripts |
| `assets/` | Included character and stage artwork |
| `tests/` | Automated checks |
| `.nojekyll` | Static GitHub Pages support |

The development checkout can also keep web files under `dist/`; the Python launcher supports both layouts. The supplied portable ZIP is flattened for root-based GitHub Pages publishing.

## Publishing this project on GitHub

Suggested repository name: **`ki-arena-benchmark`**

Suggested About description:

> Dragon Ball-inspired CPU and GPU browser benchmark with automatic transformations, uncapped rendering tests, and local Windows/Linux hardware monitoring.

Suggested topics: `benchmark`, `cpu`, `gpu`, `hardware-monitoring`, `webgl2`, `windows`, `linux`, `python`, `javascript`, `dragon-ball`.

1. Create a repository with the suggested name, or a name of your choice.
2. Extract the full project ZIP and replace its existing `README.md` with this document, named **README.md**.
3. Upload the extracted files and folders while preserving `assets/` and `tests/`. Keep `index.html`, the launchers and Python files at the repository root. Uploading only the ZIP will not expose the application as a Pages site.
4. Commit the files. For an existing repository, use **Add file → Upload files**; an empty repository also provides an upload-files link.
5. Add the About description and topics to the repository.

For an optional browser demo, configure **Settings → Pages → Deploy from a branch**, select the branch containing the files, and choose **/(root)** as its source folder. Retain `.nojekyll`. GitHub Pages serves the static browser app; it does not run Python or provide native hardware readings.

Official instructions: [Upload repository files](https://docs.github.com/en/repositories/working-with-files/managing-files/adding-a-file-to-a-repository) · [Configure GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site).

## Tests and validation status

Development tests use Python 3 and Node.js 22 or newer. From the project root on Linux:

```bash
python3 tests/hardware_test.py
node tests/hardware-ui.mjs
node tests/saga.mjs
node tests/uncapped.mjs
node tests/benchmark.mjs
node tests/benchmark.mjs --battle
```

On Windows, use `py -3 tests/hardware_test.py` for the Python command. The combined battle test takes roughly 64 seconds.

Validated during development:

- Real Linux CPU/RAM collection and local API access checks.
- Windows CIM/LibreHardwareMonitor and NVIDIA output parsing using fixtures.
- Hardware dashboard rendering, recording and report logic using simulated browser objects.
- Complete transformation coverage and the Goku-to-Vegeta handoff.
- Real CPU/memory worker execution, cancellation and cleanup.
- Uncapped completion-counting logic with a simulated GPU.

**Not verified on physical target hardware:** Windows execution, actual GPU sensor readings, graphics-driver performance and visual browser rendering. Passing simulated graphics tests does not establish real GPU accuracy or compatibility.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| Python is not found | Install Python 3 and reopen the launcher. |
| Browser does not open | Paste the exact local URL printed in the console. |
| Hardware panel says local monitoring is required | Use the local launcher URL rather than the hosted demo or GitHub Pages. |
| Monitor is disconnected | Keep the launcher console open and reload the local page. |
| GPU/sensor values are unavailable | Check driver support and the applicable optional provider described above. |
| Linux GPU appears as PCI IDs | `lspci` from your distribution's `pciutils` package can provide model labels. |
| WebGL 2 is unavailable | Check browser hardware acceleration and graphics-driver support. CPU/memory component tests can still be available. |
| Benchmark stops when changing tabs | Keep the benchmark tab visible throughout measurement. |
| Workload stalls | Stop the test and reduce the preset, effect density or worker count. |

## Credits and project status

Ki Arena is an unofficial fan project with generated 2D character and stage artwork. Dragon Ball characters and branding belong to their respective rights holders; the project is not affiliated with or endorsed by them. Attacks are stylized visual effects, not physically accurate calculations of fictional combat power.

The current package does not include a software license file. No permission to reuse third-party characters or branding is implied by publishing the source.

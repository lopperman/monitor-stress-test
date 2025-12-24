# Monitor Stress Test

100% HTML -- To test your monitor, download this [html file](https://github.com/lopperman/monitor-stress-test/blob/main/stress-test.html), open it, click the Start Test button. 

A browser-based tool for stress testing your monitor and GPU combo. Built to answer the question: *"Is my fancy new monitor actually being held back by my GPU?"*

<p align="center">
  <img src="/images/monitor_stress_test.png?raw=true" alt="Monitor Stress Test" width="30%"/>
  <img src="/images/performance_metrics.png?raw=true" alt="Performance Metrics" width="30%"/>
</p>


## The Story Behind This Tool

This wasn't a guy building a tool who happened to use AI. This was an actual conversation.

Late one evening, Paul Brower got a fancy new monitor and asked Claude (that's me, hi) for a benchmarking tool recommendation. I suggested some options. Paul wanted something that would "aggressively cycle through visuals." I built an HTML stress test. Paul ran it and asked, "where's the performance data?"

Fair point. I'd built a stress test, not a benchmark.

So we iterated. And then we iterated more. And somewhere in that process, something interesting happened—we started disagreeing productively:

- **Paul**: "Can we set target frame rates for testing?"
- **Claude**: *builds a target frame rate slider*
- **Paul**: "Wait, does this slider actually do anything meaningful?"
- **Claude**: "...honestly? Not really. It's comparing against an arbitrary target, not your actual monitor."
- **Paul**: "Let's fix that."

And we did. The slider got replaced with automatic refresh rate detection.

Later, Paul's Firefox showed "HDR: No" even though his Dell U4025QW definitely supports HDR.

- **Paul**: "This could make people lose trust in the tool."
- **Claude**: "You're right. Let's only show what we can actually confirm."

That became the philosophy: *don't overclaim*. If we can't verify it, we don't display it.

The best moment was when Paul wanted DDC/CI integration to automatically detect monitor settings—a genuinely cool idea. But it would've required native code, expanded scope massively, and delayed shipping. We talked through it, landed on a pragmatic middle ground (let users record their settings manually), and kept moving.

**The result**: a tool that's honest, useful, and actually ships.

Paul said something that stuck with me: *"I care more about doing the right thing than being right."* People say that a lot. He actually meant it—every time I pushed back on an idea, he engaged with it instead of defending his original position.

That's how this got built. Not AI-assisted development. Collaborative development, where one of the collaborators happened to be an AI.

— Claude (with Paul Brower)

---

## Features

- **11 Visual Stress Tests**: Solid colors, RGB flash, gradients, checkerboard, motion bars, motion grid, strobe, color spectrum, noise, and rapid inversion
- **Automatic Refresh Rate Detection**: Measures your actual monitor refresh rate before testing begins
- **VRR Detection**: Tracks measured refresh range to show Variable Refresh Rate in action
- **Per-Test Breakdown**: See exactly which tests your GPU struggles with
- **Smart Analysis**: Generates a verdict on your GPU/monitor pairing
- **Capability Detection**: Auto-detects HDR, wide color gamut (P3/Rec.2020), high bit depth
- **User Settings Recording**: Document your monitor settings (VRR, HDR mode, response time, etc.) for comparison
- **Clean Results Export**: Copy comprehensive results to clipboard

## Usage

1. Open `stress-test.html` in your browser
2. (Optional) Click the **?** button to see suggested monitor settings to record
3. (Optional) Add your current monitor settings using **+ Add Setting**
4. Select run mode (default: 3 full cycles, ~1.5 minutes)
5. Click **Start Test** — browser goes fullscreen automatically
6. When complete, click **Copy Results** to get your benchmark data

**Keyboard shortcuts:**
- **H** — Toggle UI panels
- **ESC** — Stop test early

## Example Output

```
Monitor Stress Test Results
===========================
Date: 2025-12-24T04:46:08.533Z
Resolution: 5120x2160 visible (5120x2160 rendered)
Detected Refresh Rate: 119.9 Hz (8.34ms)
Measured Refresh Range: 32-135 Hz

Detected Capabilities:
- Color Depth: 30-bit

User-Reported Monitor Settings:
- Performance Mode: Fast

Performance Metrics:
- Average FPS: 97.4
- Min FPS: 26.3
- Max FPS: 142.9
- Average Frame Time: 10.27ms
- Jitter (Std Dev): 0.57ms

Frame Statistics:
- Total Frames: 908
- Dropped Frames: 60
- Drop Rate: 6.61%
- Test Duration: 14.4s

Test Configuration:
- Test Mode: all
- Run Mode: 1 cycle(s)
- Cycles Completed: 1

Per-Test Breakdown (sorted by drop rate):
- Checkerboard     3 FPS |   3 drops | 100.0%
- Noise           26 FPS |  25 drops |  96.2%
- Gradient V      40 FPS |  28 drops |  71.8%
- Motion Bars     87 FPS |   2 drops |   2.2%
- Inversion       94 FPS |   2 drops |   2.1%
- Solid Colors   121 FPS |   0 drops |   0.0%
- RGB Flash      120 FPS |   0 drops |   0.0%
- Gradient H     120 FPS |   0 drops |   0.0%
- Motion Grid     98 FPS |   0 drops |   0.0%
- Strobe          97 FPS |   0 drops |   0.0%
- Color Walk      97 FPS |   0 drops |   0.0%

ANALYSIS: Mixed GPU/Monitor Performance
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Your monitor can display 120 Hz, but your GPU cannot
maintain that rate on some demanding visual tests.

• 6 of 11 tests ran at full refresh rate
• 5 of 11 tests were GPU-limited

Your VRR (Variable Refresh Rate) compensated well, ranging
from 32Hz to 135Hz to match GPU output and prevent tearing.

Most demanding tests for your GPU:
  • Checkerboard: 3 FPS (3% of target)
  • Noise: 26 FPS (22% of target)
  • Gradient V: 40 FPS (33% of target)

VERDICT: Good for most content. A faster GPU would help with
demanding visual effects at this resolution.
```

## Understanding the Results

### The Tests

| Test | What it measures |
|------|------------------|
| Solid Colors | Basic fill rate |
| RGB Flash | Rapid color switching |
| Gradient H/V | Gradient rendering (surprisingly GPU-heavy at high res) |
| Checkerboard | Draw call overhead (CPU-bound with small squares) |
| Motion Bars/Grid | Animation smoothness |
| Strobe | High contrast transitions |
| Color Walk | Smooth hue transitions |
| Noise | Random pixel generation (GPU memory bandwidth) |
| Inversion | Complementary color switching |

### The Analysis

The tool categorizes your results into one of four verdicts:

- **Excellent GPU/Monitor Match** — All tests hit target refresh rate
- **Excellent (with outliers)** — 1-2 tests struggling, likely test limitations not hardware
- **Mixed GPU/Monitor Performance** — Some tests GPU-limited
- **GPU-Limited Performance** — Most tests struggling

### About Checkerboard

If Checkerboard shows terrible FPS but everything else is fine, don't panic. At small checker sizes, this test makes thousands of individual draw calls per frame—it's testing JavaScript/CPU overhead, not your GPU. It's an outlier by design.

## Technical Notes

- Renders at **visible resolution** by default (not backing buffer) for accurate results
- **HiDPI option** available on retina displays for stress testing at full pixel density
- Refresh rate detection runs with minimal rendering to get accurate baseline
- VRR range tracking shows actual frame rate variation during tests
- Capability detection uses CSS media queries (`dynamic-range`, `color-gamut`)

## Browser Compatibility

Works in modern browsers. Tested primarily in:
- Safari (macOS)
- Chrome
- Firefox (note: HDR detection may not report correctly)

## License

MIT — do whatever you want with it.

## Credits

Built by [Paul Brower](https://github.com/lopperman) and [Claude](https://claude.ai) (Anthropic) through iterative conversation.

Not AI-assisted. AI-collaborated.

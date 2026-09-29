# Power match

Compare two power sources recorded on the same ride. For example: power-meter pedals against a smart trainer, or a head unit against Zwift or MyWhoosh.

**Use it here: [https://niznixzar.github.io/power-compare/](https://niznixzar.github.io/power-compare/)**

Load two `.fit` files. Power match lines them up second by second and tells you how closely the two devices agree.

Your files are read in your browser and never uploaded anywhere.

---

## How to use it

1. Record the same ride on both devices, for example pedals on your Garmin and the trainer in MyWhoosh.
2. Open Power match and drop the files into the two boxes, or click a box to choose a file:
   - **A, device being tested:** the one you're checking, e.g. the trainer.
   - **B, reference:** the one you trust, e.g. the pedals.
3. The files are matched automatically and the results appear straight away.
4. Scroll down to the **Power** chart and check that the two lines rise and fall together. If they don't, see [When the power values don't match](#when-the-power-values-dont-match).

Got A and B the wrong way round? Click **Swap A and B**.

Both regular `.fit` files and the `.fit.gz` files from a Strava bulk export work.

---

## When the power values don't match

Power match first lines the files up using the time each device recorded. If one device's clock is wrong, the files can end up badly out of line. This can happen with a wrong time zone, a clock that never synced, or an app that only records time since you pressed start. You'll usually see some of these signs:

- In the **Power** chart, the peaks and efforts of one line sit to the side of the other instead of on top of it.
- A note under "Line up the files" says **the traces match poorly**.
- The **Pearson correlation** is well below 0.95.
- The mean difference or RMSE is much bigger than you'd expect.

**Fix: tick "Ignore device clocks"** in the "Line up the files" panel. Power match then ignores both clocks, lines the files up from where each one starts, and searches for the best match from there.

If they still don't line up:

- Increase **Search up to (s)**, for example from 300 to 900. This helps when one recording was started several minutes before the other.
- Nudge the match yourself with the **−10 / −1 / +1 / +10** buttons, watching the Power chart until the efforts sit on top of each other. Setting the smoothing to 10 s or 30 s makes this easier to see.
- Check both files really are from the same ride.

Once the lines sit on top of each other, the numbers can be trusted.

---

## Other settings

- **Trim start / Trim end (s):** cut extra seconds off the start or end, such as a warm-up you don't want to include. Seconds at the start and end where either device shows 0 W are already trimmed automatically. Trimmed parts are shaded grey in the Power chart.
- **Smoothing:** only changes how the Power chart looks. The numbers always use the unsmoothed 1-second data.

---

## What the numbers mean

All differences are **A minus B**, so a positive number means the device being tested reads higher than the reference.

| Value | What it tells you |
| --- | --- |
| Average power A / B | Each device's average over the matched part of the ride |
| Mean difference | How many watts higher (+) or lower (−) A reads than B on average |
| Mean difference % | The mean difference as a percentage of B's average power |
| RMSE | Typical size of the second-by-second gap, whichever direction it goes |
| SD of difference | How much the gap between the devices varies from second to second |
| Coefficient of variation | SD of difference ÷ the average power of both devices, as a % |
| Pearson correlation | How closely the two traces move together. 1.000 is perfect. |

These values use one data point per second. Files recorded faster than once a second are averaged down to one value per second first.

---

## The charts

- **Power:** both traces over the ride, after matching.
- **Bland–Altman (W and %):** each dot is a 30-second average. Windows where either device averages under 5 W, such as coasting or stops, are left out.
  - Across: the average of A and B.
  - Up: A − B, in watts or as a percentage of that average.
  - Solid pink line: the **bias**, i.e. the average difference.
  - Dashed pink lines: the **limits of agreement**, bias ± 1.96 × SD. About 95% of the dots should fall between them.
  - Green **line of best fit**: if it slopes, the gap between the devices changes as power goes up. Flat means the gap stays the same.
- **30 s mean power:** A against B, with a dashed A = B line and a green line of best fit. Windows under 5 W are left out here too.
  - Dots above the dashed line are moments where A read higher than B.
- **Time-power curve:** the best average power each device recorded for durations from 1 second to the whole ride.

The charts use 30-second averages, which smooth out second-to-second noise. That's why their limits of agreement are narrower than the 1-second SD at the top of the page.

---

## Using it offline

Download `index.html` from this repository and open it in any browser (Chrome, Edge, Safari or Firefox). Everything works without an internet connection. Only the font changes to your computer's default.

On phones, use the link above rather than a downloaded copy, since phones often won't run downloaded HTML files.

---

## How it works

For each file, Power match:

1. **Decodes** the FIT file in the browser, reading timestamp, power and cadence.
2. **Averages** readings into one value per second.
3. **Finds the best shift** by testing different shifts of file B against file A. It keeps the shift where the two power traces are most strongly correlated: first searching 5-second averages, then refining to the exact second.
4. **Trims** to the part where both files overlap, and calculates the numbers and charts.

It's a single HTML file with no libraries or external services.

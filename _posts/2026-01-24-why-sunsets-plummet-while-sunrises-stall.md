# Why Sunsets Plummet While Sunrises Stall

**TL;DR:** You might notice that in September, the evenings get dark rapidly, but the mornings stay bright. This is because the sun isn't just spending less time in the sky; the entire schedule of the day is shifting earlier. This post explains the geometry behind this "uneven" change.

## The Observation

The autumnal equinox is always around the end of September. This is the time of
year when we lose daylight faster than any other time—here in NYC, we are losing
about 3 minutes of sun every single day.

However, if you look closely at the schedule, you'll notice something peculiar:

![sunset changes more than sunrise](/images/2026.01.11.time_change_faster_slower.jpg)

We are losing a massive **2 minutes** of light in the evening, but only about **1 minute** in the morning. Why are our evenings getting shorter so much faster than our mornings?

Existing explanations often wave their hands at "axial tilt" and "elliptical orbits" without really explaining the mechanism. Let's break it down intuitively.

## The Two Engines of Change

To understand why the sunrise and sunset change at different speeds, we have to understand that there are actually **two** different engines driving the sun's motion, and right now, they are working together in a strange way.

### Engine 1: The Shrink (The Seasons)

We all know this one. Because the Earth is tilted, the sun takes a lower path across the sky in autumn. A lower path means less time in the sky.

![sun path](/images/2026.01.11.season_suns_path.jpg)

Think of this as the day **"Shrinking."** If this were the only factor, we would lose daylight evenly: 1.5 minutes off the morning, and 1.5 minutes off the evening. But that’s not what we see in the data.

### Engine 2: The Shift (The "Diver" Effect)

#### The Thought Experiment: Why the Tilt Shifts Time

To understand why the sun is "running early," it helps to first imagine a simpler version of our planet.

**Scenario A: The Boring Earth (No Tilt)**
Imagine if Earth stood perfectly upright as it orbited the sun (0° tilt).

- From our perspective, the sun would always travel directly along the Celestial Equator.
- It would move **straight East** against the background stars at a constant speed.
- Because it never wastes energy moving North or South, its eastward progress would be perfectly consistent every single day.
- **Result:** Solar Noon would effectively never change. 12:00 would always be 12:00.

**Scenario B: The Real Earth (The Diver)**
But our Earth _is_ tilted. This means the sun doesn't just move East; it also moves North (in Spring) and South (in Autumn).

![Orbit View](/images/2026.01.11.axial_tilt_image.png)
_Notice how Earth's axis (the white line) always points the same way. In September (top), we are sideways to the sun._

Right now, in September, the sun is crossing the equator at a steep angle. It is "diving" South.

- **The Vector Problem:** Think of the sun as a car with a fixed speed limit (say, 60 mph). In Scenario A, it spends 100% of that speed driving East. But in Scenario B, it has to spend a chunk of that speed diving South.
- **The Lag:** Because it is steering South, its speedometer might still read 60 mph, but it is covering **less horizontal ground (East)** per hour. It is "wasting" its speed on the vertical dive.
- **The Catch-Up:** Since the sun is dragging its feet horizontally, the spinning Earth catches up to it _sooner_ than expected.

![Diver View](/images/2026.01.11.horizontal_lag_diagram.png)
_See the difference? The 'Mean Sun' (White) represents Scenario A (Straight East). The 'True Sun' (Orange) represents Scenario B (Diving South). Notice how the Orange arrow falls behind._

**The Conclusion**
Because the Earth catches the sun early, "Solar Noon" happens early. We wrap up the "Solar Day" before 24 hours have passed on our watches.

#### The Data: Seeing the Shift

If we look at the numbers, we can see exactly how this "drag" adds up in September.

Because the sun is lagging behind, the spinning Earth "catches up" to it sooner than expected. This makes the Solar Day slightly _shorter_ than 24 hours.

If we plot this "Equation of Time," we can see exactly what happens in September:

![Equation of Time](/images/2026.01.11.equation_of_time.png)
_The "Tilt" component (Cyan line) plunges steeply in September. The total effect (Yellow line) drops sharply, meaning the sun is running faster and faster relative to our clocks._

Every day in September, Solar Noon shifts about 20 seconds earlier. Over a few weeks, this adds up to a massive 15-minute shift in our schedule. The sun is literally running early!

## Putting It Together: The "Shift & Shrink"

This is where the magic happens. We have two forces acting on the sun at the same time:

1. **The Shrink:** The day is getting shorter (squeezing in from both sides).
2. **The Shift:** The entire day is sliding earlier (moving left).

Let's visualize why this hammers the Sunset but spares the Sunrise:

![sunrise and sunset shifts and shrinks](/images/2026.01.11.sunrise_sunset_change.png)

### 1. The Sunset (Double Whammy)

The sun is trying to set earlier because the day is shrinking (Winter is coming).
_PLUS_, the sun is setting earlier because the whole day shifted earlier (Solar Noon shift).
These two effects **add up**.

- _Result:_ Sunset plummets (~2 mins earlier/day).

### 2. The Sunrise (The Cancellation)

The sun _should_ be rising later because the day is shrinking.
_BUT_, the sun is trying to rise earlier because the whole day shifted earlier.
These two effects **fight each other**.

- _Result:_ The sunrise barely moves (~1 min later/day).

## Conclusion

We tend to think of the sun's motion as a single cycle, but it's really a complex dance of tilt and orbit.

- The **Tilt** tries to squeeze the day from both ends.
- The **Orbit** slides the day earlier or later on the clock.

Right now, in early autumn, the day is sliding earlier _while_ it shrinks. The sunset takes the brunt of both forces, plummeting into darkness, while the sunrise enjoys a temporary stalemate.

So, the next time someone complains that it's getting dark so early, you can tell them: "It's not just the season; the solar clock is running fast!"

> **Note**: This post was pair-programmed with Gemini. I brought the scientific direction; the AI helped build the Python scripts and diagrams.

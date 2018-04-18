Lightning-Location-Algorithm
============================
Locating a Lightning Stroke Using Stroke Signal Detection Times

An Excel 2010 spreadsheet demo of a least-squares algorithm for locating lightning strokes given known receiver locations and times of stroke signal reception

January 3, 2015

In July of 2014, I acquired a Blitzortung lightning receiver, and a couple of weeks later began participating in the Blitzortung lightning location network. The Blitzortung system uses a network of volunteer-operated lightning receivers that feed time-stamped, digitized lightning signals to servers that compute the locations of lightning strokes, and display them on maps on the Blitzortung website, http://www.blitzortung.org, and another site, http://www.lightningmaps.org.  

A few weekends ago, I was rearranging some old files, and stumbled across some program listings dating back to 1982 and earlier. These listings were for a program to compute the position of a navigator’s LORAN-C receiver based on timing readings of LORAN-C pulses, and a program to compute the position of a navigator’s Transit satellite navigation receiver based on Doppler shift measurements of received satellite signals and satellite orbital parameters broadcast by the satellite. 

When I found those old program listings, it motivated me to think about how the Blitzortung lightning stroke location algorithm might work, i.e., how one could locate a lightning stroke if one had the precise reception time of the stroke signal at several known locations.

First, some background:  From 1975-1980, I worked for a company that developed commercial and military Transit satellte navigation receivers. My role was primarily hardware development on the digital and analog circuits of the receivers, including the receiver signal processing circuits and interfaces to various other devices, such as gyrocompasses and speed logs for shipboard receivers. Another guy did the software development, which back then, was mostly written in assembly language on various kinds of microcomputers (some of which I also designed). I was pretty interested in the software algorithms, too, but I had to brush up on my calculus and linear algebra skills before I started to really understand them. One of the software tools we used was a program that predicted the passes of the Transit satellites that were visible from our lab in Silver Spring, MD. I taught myself the Pascal programming language by writing a Pascal program to perform those predictions, based on the satellite orbital parameters our receivers received from the satellites themselves. Then I got interested in the navigation algorithm, and I wrote a Pascal program to process the Doppler shift information our receivers measured and the satellite orbital parameters to produce a navigation solution. That’s one of the listings I stumbled across the other day. It takes only four pages of 8 ½ x 11 paper.

The genesis of the Transit satellite navigation system was, perhaps ironically, the first earth-orbiting artificial satellite, the Russian Sputnik. The Sputnik satellite was launched in 1959. Two scientists at the Johns Hopkins University Applied Physics Laboratory (JHU APL), George Weiffenbach and William Guier, were able to receive the Sputnik signal, which was about 1 kHz offset from 20 MHz, and compare the signal to the WWV 20 MHz signal transmitted at that time from the National Bureau of Standards radio station in Maryland. They observed the Doppler shift of the Sputnik signal as its orbit took it past the receiving station on the ground, and decided to record the signal.

Weiffenbach and Guier figured out that they could determine when the satellite made its closest approach by finding the maximum slope of the Doppler frequency shift, as the satellite’s motion relative to the ground station went from approaching to moving away from the ground station at JHU APL. Then they moved on to the problem of determining the satellite’s orbit from the Doppler frequency shift curve from the beginning to the end of the satellite pass visible from the ground station. They succeeded in developing the necessary mathematics to perform this task. The lab director, Frank McClure, asked them if they could “invert the solution,” i.e., given the Doppler-shifted received satellite signal and a known satellite orbit, could they determine the location of the receiver on the ground. After some further work, they developed the mathematical techniques to perform this calculation. With this development in hand, Frank McClure and his friend Dick Kershner got together over a weekend and developed the essence of the Transit Satellite Navigation System. The system included a constellation of several polar-orbiting satellites that transmit two ultra-stable frequencies (about 150 MHz and 400 MHz) modulated with a message containing the satellites’ orbital parameters, a ground tracking system that used receivers at surveyed locations to determine the orbits of the satellites, and an uplink station to load the orbital parameter messages into the satellites.

The information Weiffenbach and Guier learned that they could extract from the Doppler shift of the satellite signal, which results from the motion of the satellite relative to the receiver on the ground, is the change in range of the satellite with respect to the ground receiver over measured time intervals. The next few paragraphs present an attempt to explain how that works, in English, without detailed math.

The Transit satellite has a very accurate and stable reference oscillator. Let’s assume for argument’s sake that it stays perfectly on frequency, at least for the length of time that we measure its frequency. The receiver has another very accurate and stable oscillator. (Back in the ‘70s, they were $300-$400 ovenized quartz crystal oscillators.) The satellite transmits one of its two signals (the one I’m going to talk about) on a frequency of 399.968000 MHz, i.e., 32 kHz below 400 MHz. The receiver’s oscillator runs at 10 MHz, which gets multiplied by 40 up to 400 MHz as a reference. The receiver includes superheterodyne circuits that mix the received satellite signal down to a difference frequency of 32 kHz--plus or minus the Doppler shift.

Now let’s talk about how we can measure change in distance between the satellite and the ground station using Doppler shift. The way most people think about Doppler shift is that when an ambulance passes your location, the siren sounds like it is operating at a higher pitch when the ambulance is coming toward you and a lower pitch when it is going away. When we’re talking about radio signals, the received frequency is higher when the transmitter and receiver are getting closer to one another, and lower when they’re getting farther away. If you dig a little deeper, you figure out that the reason that happens is that if the transmitter and receiver move one wavelength of the transmitted signal closer together over a period of one second, the received frequency is 1 Hz higher over that second, and if the transmitter and receiver move 10 wavelengths farther away from each other over a period of one second, the received frequency is 10 Hz lower over that second. Thus, if the number of cycles of the received satellite signal differs from the number of cycles transmitted by the satellite by, say, 40,000 cycles over a period of two minutes, the satellite moved 40,000 wavelengths of its transmitted frequency over that two minutes. In the case of the Transit 399.968 MHz signal, the wavelength is about 0.749541 meters. That means that if the number of cycles received by the ground station exceeds the number of cycles transmitted by the satellite by 40,000 over two minutes, the satellite moved about 29,981.6 meters closer to the ground receiver during that time.

How do we use the measurement of how far the satellite moved with respect to the navigator’s receiver? Well, it works like this: Let’s assume we have an estimate of the location of the receiver on the surface of the Earth. The satellite message is designed to repeat exactly every two minutes, with a time mark at the beginning of each two-minute interval that the receiver can detect. As I mentioned earlier, the satellite transmits its orbital parameters, which describe its position in space relative to the Earth as a function of time. That gives us enough information to figure out precisely where the satellite is with respect to the Earth at the beginning of each two-minute interval. So if we have an estimated position of the receiver, and a known location of the satellite at the beginning (and hence the end) of each two-minute interval, we can calculate a theoretical change in distance between the satellite and the receiver (based on the estimated receiver location) over each two-minute interval. The Doppler-shift measurement performed by the receiver gives us the measured change in distance between the satellite and the receiver over the same two-minute interval. For each two minute interval, the difference between the theoretical and measured change in distance between the satellite and the receiver gives an error value called a “residual”.
Let’s say we have at least three Doppler-shift measurements of change in satellite distance with respect to the ground receiver over an equal number of two-minute intervals. We’ll assume for now that our receiver is on the surface of the Earth, and in particular, on the ocean, so we can compute its elevation. There are three unknowns to solve for: the latitude and longitude of our receiver, and the actual offset between the satellite’s nominal 399.968 MHz frequency and our receiver’s 400 MHz local oscillator frequency. We can estimate those values, however, because we probably have a general idea of where we are, and we can use 32 kHz as the starting point for our receiver frequency offset.

Using the Doppler-shift-based measurements of change in satellite distance with respect to our receiver location, we can compute updated estimates to our estimated receiver latitude and longitude and frequency offset using a least-squares algorithm. The least-squares algorithm attempts to minimize the sum of the squares of the residuals, which, again, are the differences between the theoretical changes in distance between our receiver and the satellite over each measurement interval, based on our estimated receiver latitude, longitude, and frequency offset, and the distance changes we measure using the Doppler shift of the received signal. The least-squares algorithm uses the partial derivatives of distance change as a function of changes in the receiver’s latitude, longitude, and frequency offset, denoted ∂∕∂φ, ∂∕∂λ, and ∂∕∂f respectively, which affect the theoretical changes in distance between the satellite locations and the estimated receiver location. (Those “partials” are fairly easy to write down by hand after achieving a passing grade for a semester of differential calculus. The least squares algorithm itself is something you learn in a semester of elementary linear algebra.) The algorithm is run iteratively, i.e., in a loop. At the end of each iteration, it updates the estimates of the receiver latitude, longitude, and frequency offset and re-computes the theoretical changes in distance between the satellite locations and the receiver location based on those new values. When the receiver location and frequency offsets get smaller than a pre-determined cutoff value, the algorithm declares victory and stops. The updated estimates of latitude, longitude, and frequency offset are then pretty close to the actual values.

Sometime in the early 1980s, I became interested in adapting the navigation solution of the Transit program to LORAN-C. I first developed the equations for a differential navigation solution, i.e., a solution based on differences in time of reception of the master and slave pulses.  Then, influenced by a paper I’d read called “Hyperbolic Positioning Per Se is Passe,” by Ronald R. Hatch of Magnavox, I developed the equations for a range-range version of LORAN-C that computes a navigation solution based on the time of arrival of the master pulse and the slave pulses with respect to an internal clock. The computation disadvantage of the latter technique, which is minor, is that one also has to solve for the offset of the receiver’s internal clock with respect to the LORAN-C system clock.

LORAN-C was a system where several “chains” of transmitters spread around the coasts of the United States and other areas of the world transmitted precisely-timed 100 kHz pulses. Each chain consisted of a “master” station, which transmitted first, and up to four “slaves,” which transmitted at precise time delays after the master. The user’s receiver would listen for the master’s pulse, and use it to start a timer. It would then measure and display the time in microseconds between the reception of the master pulse and the reception of each slave pulse.

Before the availability of low-cost computers, there were special charts that navigators would use with the LORAN-C receiver measurements to determine their position. These charts were printed with curves in the shapes of hyperbolas, each hyperbola representing a curve of constant master-to-slave signal delay. (A hyperbola is a curve, each point on which has the same difference in distances from two known points.) Given at least two sets of master-slave time delay measurements, the navigator could locate his position at the intersection of two corresponding curves on the LORAN-C chart.  When the cost of computers came down to the level that allowed them to be incorporated into shipboard navigation equipment, LORAN-C receivers that could calculate latitude and longitude based on the time of reception of pulses from the LORAN-C master and slave transmitters began to be produced.

I decided to try to restructure the least-squares navigation algorithm to create a simulation of how it could work in a Blitzortung-like system. The main difference between this lightning location algorithm and the LORAN-C and Transit satellite navigation algorithms is that instead of needing the partial derivatives of changes in distance with respect to the receiver latitude, longitude, and frequency or time offset, the lightning-stroke-locator least-squares algorithm needs partial derivatives of changes in distance with respect to the lightning stroke latitude, longitude, and the precise time the stroke occurred, denoted ∂∕∂φ<sub>s</sub>, ∂∕∂λ<sub>s</sub>, and ∂∕∂t<sub>s</sub> respectively. If one starts with an initial estimate of the lightning stroke position and time, the residuals we want the least-squares algorithm to minimize are the differences between the theoretical distances from the lightning receivers to the estimated stroke location and the distances based on the measured reception times of the stroke and the estimated time of the stroke. (Note that the Blitzortung documentation at http://www.blitzortung.org/Documents/TOA_Blitzortung_RED.pdf?1420214570 says they use the hyperbolic solution, based on differences in times of arrival of the lightning signal at different receiver locations. The algorithm I developed for this simulation uses the absolute times of arrival, based on GPS time stamps applied to the received lightning signals by the Blitzortung receivers.)

The algorithm is implemented in Excel 2010 VBA macros. You can enter a latitude/longitude pair for the actual stroke location in row 8 of the spreadsheet, and for the initial estimate of the stroke location in row 28. If you want the simulation to include the effects of noise, enter the standard deviation of the noise effect in meters in row 2. You can also enter a value for the actual time of the stroke in row 3. The column numbers for these data entries will be self-evident. Enter a "1" in the "Used" column of the Stations sheet for up to 15 receiving stations to be used in the solution. CTRL-s runs the setup, and CTRL-r runs the location solution one iteration at a time. The latitudes and longitudes of the stations you select will populate rows 11-25, columns C and D of the spreadsheet when you run the setup. The computed location of the stroke will be displayed in row 35, based on the initial estimated location you entered in row 28 and the computed location deltas displayed in row 31 when you run the location solution.

I guess I should mention, for the sake of satisfying the curiosity of the reader who has extrapolated the explanations above to a more modern navigation system, that the algorithm a GPS receiver uses to compute its location is indeed similar to those described above. It's most similar to the range-range algorithm for LORAN-C. The GPS receiver has to solve for its latitude, longitude, and altitude/elevation, and the time offset between its internal clock and GPS system time, which is the time system transmitted by the GPS satellites. Each GPS satellite transmits a message that includes the information necessary to compute the precise position of the satellite as a function of time, and the approximate location of all of the other GPS satellites, to assist the receiver in figuring out which satellite signals to look for. The GPS signal contains precise timing information that can be used to determine a "pseudorange" between the satellite and the receiver. The receiver calculates theoretical ranges to the satellites based on its estimated location, and "measured" ranges based on the pseudorange timing information in the satellite signals and the receiver's internal clock. The partial derivatives used in the GPS receiver least-squares computation are changes in distance between the receiver and each satellite being received as a function of changes in the receiver's latitude, longitude, altitude/elevation, and clock offset. A concise overview of the GPS pseudorange navigation solution algorithm is posted at http://www.colorado.edu/geography/gcraft/notes/gps/gps_f.html under the heading "Pseudo-Range Navigation Solution Example".

Footnote:
After I posted this yesterday (January 2, 2015), it occurred to me that some folks might wonder about the advantages/disadvantages of the range-range least-squares algorithm versus the hyperbolic least-squares algorithm.  So I took a few hours today (January 3, 2015) to add the hyperbolic algorithm code to the Excel 2010 VBA macro code, and restructured the spreadsheet accordingly.

The only advantage I see of the hyperbolic algorithm over the range-range algorithm is that the least-squares solution only has to solve for two variables, stroke delta-latitude and stroke delta-longitude, instead of three variables, those two plus stroke delta-t.  That means you don't need to compute the 3x4 B matrix and then reduce it to the 2x3 C matrix to compute the deltas using Cramer's determinant method on two simultaneous equations and two unknowns.  The disadvantage of the hyperbolic algorithm is that in order to achieve the optimal solution using the hyperbolic algorithm, according to Hatch's paper mentioned above, is that you have to difference all of the stroke time-of-arrival measurements made by all of the receivers against each other.  Thus, if you have n stations receiving the signal, you have n!/[(n-2)!*2!] combinations of receivers, meaning you end up with that many rows in your A matrix and R vector.  For 5 stations, that's "only" 10 rows.  For 10 stations, it's 45 rows, and for 15 stations, it's 105 rows.  (Actually, if you have more than one channel per receiver, you'll have more than that, unless you somehow combine the measurements for all of the channels from each receiver!  If you have 10 receivers with 5 channels each and treat each channel's output as an independent measurement, you wind up with 1225 rows!)  The range-range algorithm is optimal as-is, according to Hatch.  Both algorithms converge to the correct solution in the same number of iterations.

Use CTRL-s to setup and CTRL-h to run the hyperbolic algorithm.  The hyperbolic algorithm computed stroke location will be displayed in row 36, and the hyperbolic algorithm deltas after each iteration will be displayed in row 32.


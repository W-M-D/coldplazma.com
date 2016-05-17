---
layout: post
title: "ERGO entropy"
date: 2016-05-12 00:59 EST
categories: ergo
---

<font color="red" > Just a quick disclaimer i am in no way a master statistician. Most of my experience with entropy and RNGs comes from creating my own RNGs in linux as well as decoding radio signals. I have also worked on the RTL-entropy project. If you are a master statistician and see that i have done something wrong please email me. I really want to be proven wrong on this one. </font> 

Hello everyone,
For a while i have been working on the next gen detector for the <a href="http://www.ergotelescope.org/" >Energetic Ray Global Observatory project</a> . As a part of this i have had access to their massive database. One day in 2015 we had this idea to try and create a HRNG using timestamp data from our ergo units. As a preliminary measure i decided to run some entropy tests on the data itself. Now the timestamps data usually looks like this.

{% highlight ruby %}
1462914872512114026
unix ts    /  nanoseconds 
1462914872 /  512114026
{% endhighlight %}
 However, our units are only accurate to ~100ns or 30 meters at the speed of light ( it's actually better than that in a <a href="https://coldplazma.com/ergo/calibration/2016/04/05/ERGO-calibration/"> controlled enviroment</a> ). So i decided to lop the last two numbers off. Also since the first 9 didn't fluctuate i cut those off too. Remember i was just going for entropy here. The first test i preformed was on a full timestamp file. I used the program<a href="http://man.cx/?page=ent ent do[go]=go"> ent </a>and got these readings for unit 522.


{% highlight ruby %}
Entropy = 3.404319 bits per byte.

Optimum compression would reduce the size
of this 52460600 byte file by 57 percent.

Chi square distribution for 52460600 samples is 1263744112.49, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 50.2359 (127.5 = random).
Monte Carlo value for Pi is 4.000000000 (error 27.32 percent).
Serial correlation coefficient is 0.025618 (totally uncorrelated = 0.0).
{% endhighlight %}

I found this weird because even urand on linux looks more like this.

{% highlight ruby %}
Entropy = 7.999998 bits per byte.

Optimum compression would reduce the size
of this 78774272 byte file by 0 percent.

Chi square distribution for 78774272 samples is 260.87, and randomly
would exceed this value 38.69 percent of the times.

Arithmetic mean value of data bytes is 127.5095 (127.5 = random).
Monte Carlo value for Pi is 3.140980627 (error 0.02 percent).
Serial correlation coefficient is 0.000049 (totally uncorrelated = 0.0).
{% endhighlight %}

While the background of this website for example looks like this.
{% highlight ruby %}
Entropy = 7.917172 bits per byte.

Optimum compression would reduce the size
of this 3839032 byte file by 1 percent.

Chi square distribution for 3839032 samples is 454864.37, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 120.2936 (127.5 = random).
Monte Carlo value for Pi is 3.347447323 (error 6.55 percent).
Serial correlation coefficient is 0.019466 (totally uncorrelated = 0.0).
{% endhighlight %}

 Then i decided to cut the last 10,000 events from a the unit and cut out the 10-17 digits so it looked like this . 

{% highlight ruby %}

72352105

75565815

13453263

37857817

49690739

50986049

09287882

17800880

23200144

25121140

{% endhighlight %}

ok looks pretty random to me right? lets run<a href="http://man.cx/?page=ent ent do[go]=go"> ent </a>again on the 10,000 lines.

{% highlight ruby %}

Entropy = 3.456024 bits per byte.

Optimum compression would reduce the size
of this 90000 byte file by 56 percent.

Chi square distribution for 90000 samples is 2015057.32, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 47.7781 (127.5 = random).
Monte Carlo value for Pi is 4.000000000 (error 27.32 percent).
Serial correlation coefficient is -0.118300 (totally uncorrelated = 0.0).
{% endhighlight %}

huh ?!?! whats going on here well maybe i need to remove new lines. so i did that. It looked like this now

{% highlight ruby %}

43283371376432192832002438563337151056461520115402516672969788140686642230157725357009465479415172015760076092646020196765365472352105755658151345326337857817496907395098604909287882178008802320014425121140

{% endhighlight %}

{% highlight ruby %}
Entropy = 3.321861 bits per byte.

Optimum compression would reduce the size
of this 80000 byte file by 58 percent.

Chi square distribution for 80000 samples is 1968189.48, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 52.5004 (127.5 = random).
Monte Carlo value for Pi is 4.000000000 (error 27.32 percent).
Serial correlation coefficient is 0.006649 (totally uncorrelated = 0.0).

{% endhighlight %}

At this point i was freaking out because i thought there was a massive hardware issue so i decided to look at one of our older versions. The first one made by some students from M.I.T and Tom Bales. I ran the same set of tests on unit 9.

Timestamps uncut:


{% highlight ruby %}
Entropy = 3.418076 bits per byte.

Optimum compression would reduce the size
of this 143130132 byte file by 57 percent.

Chi square distribution for 143130132 samples is 3376868560.68, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 50.1351 (127.5 = random).
Monte Carlo value for Pi is 4.000000000 (error 27.32 percent).
Serial correlation coefficient is 0.022287 (totally uncorrelated = 0.0).

{% endhighlight %}
Timestamps uncut last 10,000:

{% highlight ruby %}
Entropy = 3.405619 bits per byte.

Optimum compression would reduce the size
of this 200000 byte file by 57 percent.

Chi square distribution for 200000 samples is 4788478.34, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 49.9964 (127.5 = random).
Monte Carlo value for Pi is 4.000000000 (error 27.32 percent).
Serial correlation coefficient is 0.023493 (totally uncorrelated = 0.0).

{% endhighlight %}
Timestamps last 10,000 10-17:

{% highlight ruby %}
Entropy = 3.455890 bits per byte.

Optimum compression would reduce the size
of this 90000 byte file by 56 percent.

Chi square distribution for 90000 samples is 2015438.58, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 47.7480 (127.5 = random).
Monte Carlo value for Pi is 4.000000000 (error 27.32 percent).
Serial correlation coefficient is -0.121858 (totally uncorrelated = 0.0).

{% endhighlight %}
Timestamps last 10,000 cut no newlines with ent -c :

{% highlight ruby %}
Value Char Occurrences Fraction
 48   0         8325   0.104063
 49   1         7999   0.099988
 50   2         7999   0.099988
 51   3         8060   0.100750
 52   4         7989   0.099863
 53   5         8049   0.100612
 54   6         7931   0.099138
 55   7         7737   0.096713
 56   8         7998   0.099975
 57   9         7913   0.098913

Total:         80000   1.000000

Entropy = 3.321711 bits per byte.

Optimum compression would reduce the size
of this 80000 byte file by 58 percent.

Chi square distribution for 80000 samples is 1968618.41, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 52.4665 (127.5 = random).
Monte Carlo value for Pi is 4.000000000 (error 27.32 percent).
Serial correlation coefficient is -0.001575 (totally uncorrelated = 0.0).

{% endhighlight %}
Now i just did all this test with new data that you can find <a href="https://drive.google.com/folderview?id=0B3FBGbiC4drtR3ZFN2I1aWpLdlU&usp=sharing"> here </a> and it's the same as it was months ago. If anyone wants more data to play with send me an email. The only thing i have found is that 7 is the predominate number. Remember this is for two different units with two totally different GPS and detector systems. Number 522 and 9 you can view them <a href="http://data.ergotelescope.org/map/google_maps"> here </a> (when our database isn't backing up lol) . 


<h2> Update Sat May 14 08:38:17 EDT 2016: </h2>

My friend Grant Hernandez suggested that "ent is treating your ASCII timestamp sample file as binary. It doesn't know the representation of the numbers in binary." which made more sense to me than a hardware issue across versions or cosmic rays timing isn't random so we tried packing it with a <a href="https://github.com/W-M-D/int_file_packer/blob/master/int_file_packer.py">simple python script</a>.The file that we packed required newlines in order to pack each event as its own 4 byte stream. The output data from the file looked something like this. 


{% highlight ruby %}

<D6><C4>y^BD<8B>^F^A.^?i^BG۞^C<93>l5^A^MS<82>^E^E<D5>T^@>f<EF>^@o<8B>:^A<86><BD>^D^C

{% endhighlight %} 

Not human readable but this is the same thing as our no newlines file just packed into bytes. After i created the byte file we ran some tests the first one was on unit 9 cut file with newlines.

{% highlight ruby %}
Entropy = 7.360501 bits per byte.

Optimum compression would reduce the size
of this 40000 byte file by 7 percent.

Chi square distribution for 40000 samples is 104171.42, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 96.0958 (127.5 = random).
Monte Carlo value for Pi is 3.592559256 (error 14.35 percent).
Serial correlation coefficient is -0.142010 (totally uncorrelated = 0.0).
{% endhighlight %} 

Much more information dense than before. However, the Chi square test stuck out like a sore thumb lets check out what the manual for <a href="http://man.cx/?page=ent ent do[go]=go">ent</a> says about the Chi square test in this case.


{% highlight ruby %}
The chi-square test is the most commonly used test for the randomness of data, and is extremely sensitive to errors in pseudorandom sequence generators. The chi-square distribution is calculated for the stream of bytes in the file and expressed as an absolute number and a percentage which indicates how frequently a truly random sequence would exceed the value calculated. We interpret the percentage as the degree to which the sequence tested is suspected of being non-random. If the percentage is greater than 99% or less than 1%, the sequence is almost certainly not random. If the percentage is between 99% and 95% or between 1% and 5%, the sequence is suspect. Percentages between 90% and 95% and 5% and 10% indicate the sequence is "almost suspect". Note that our JPEG file, while very dense in information, is far from random as revealed by the chi-square test.
{% endhighlight %} 

Weird lets try this test with our other unit.

{% highlight ruby %}
Entropy = 7.360579 bits per byte.

Optimum compression would reduce the size
of this 40000 byte file by 7 percent.

Chi square distribution for 40000 samples is 104165.49, and randomly
would exceed this value less than 0.01 percent of the times.

Arithmetic mean value of data bytes is 96.0476 (127.5 = random).
Monte Carlo value for Pi is 3.584758476 (error 14.11 percent).
Serial correlation coefficient is -0.135314 (totally uncorrelated = 0.0).
{% endhighlight %} 


uhh that's weirdly similar... At first i thought this was an error because it looked so much like the first test. 

The -c for the second test output was pretty long so i'm going to post it here.

{% highlight ruby %}
Value Char Occurrences Fraction
  0             1777   0.044425
  1             1793   0.044825
  2             1838   0.045950
  3             1810   0.045250
  4             1811   0.045275
  5             1659   0.041475
  6              116   0.002900
  7              102   0.002550
  8              124   0.003100
  9              104   0.002600
 10              121   0.003025
 11              120   0.003000
 12              107   0.002675
 13              106   0.002650
 14              112   0.002800
 15              130   0.003250
 16              125   0.003125
 17              126   0.003150
 18              126   0.003150
 19              125   0.003125
 20              130   0.003250
 21              111   0.002775
 22              137   0.003425
 23              117   0.002925
 24              124   0.003100
 25              140   0.003500
 26               96   0.002400
 27              112   0.002800
 28              107   0.002675
 29              116   0.002900
 30              116   0.002900
 31               94   0.002350
 32              112   0.002800
 33   !          113   0.002825
 34   "          119   0.002975
 35   #          128   0.003200
 36   $          120   0.003000
 37   %          119   0.002975
 38   &          116   0.002900
 39   '          130   0.003250
 40   (          120   0.003000
 41   )          119   0.002975
 42   *          102   0.002550
 43   +          133   0.003325
 44   ,           96   0.002400
 45   -          141   0.003525
 46   .          106   0.002650
 47   /          127   0.003175
 48   0          123   0.003075
 49   1          102   0.002550
 50   2          126   0.003150
 51   3          112   0.002800
 52   4          120   0.003000
 53   5          120   0.003000
 54   6          105   0.002625
 55   7          127   0.003175
 56   8          126   0.003150
 57   9          102   0.002550
 58   :          115   0.002875
 59   ;          109   0.002725
 60   <          113   0.002825
 61   =          146   0.003650
 62   >          117   0.002925
 63   ?          113   0.002825
 64   @          102   0.002550
 65   A          112   0.002800
 66   B          119   0.002975
 67   C          106   0.002650
 68   D          123   0.003075
 69   E          121   0.003025
 70   F          127   0.003175
 71   G          116   0.002900
 72   H          102   0.002550
 73   I          111   0.002775
 74   J          120   0.003000
 75   K          117   0.002925
 76   L          118   0.002950
 77   M          144   0.003600
 78   N          127   0.003175
 79   O          127   0.003175
 80   P          132   0.003300
 81   Q          117   0.002925
 82   R          119   0.002975
 83   S          113   0.002825
 84   T          118   0.002950
 85   U          102   0.002550
 86   V          123   0.003075
 87   W          121   0.003025
 88   X          109   0.002725
 89   Y          112   0.002800
 90   Z          144   0.003600
 91   [          113   0.002825
 92   \          102   0.002550
 93   ]          126   0.003150
 94   ^          118   0.002950
 95   _          113   0.002825
 96   `          102   0.002550
 97   a          116   0.002900
 98   b          114   0.002850
 99   c          116   0.002900
100   d          119   0.002975
101   e          118   0.002950
102   f          118   0.002950
103   g          116   0.002900
104   h          124   0.003100
105   i          134   0.003350
106   j          124   0.003100
107   k          121   0.003025
108   l          125   0.003125
109   m          143   0.003575
110   n          110   0.002750
111   o          114   0.002850
112   p           99   0.002475
113   q          124   0.003100
114   r          113   0.002825
115   s          147   0.003675
116   t          100   0.002500
117   u          113   0.002825
118   v          107   0.002675
119   w          108   0.002700
120   x          131   0.003275
121   y          124   0.003100
122   z          121   0.003025
123   {          132   0.003300
124   |          123   0.003075
125   }          121   0.003025
126   ~           94   0.002350
127              113   0.002825
128              108   0.002700
129              128   0.003200
130              118   0.002950
131               99   0.002475
132              102   0.002550
133              127   0.003175
134               93   0.002325
135              114   0.002850
136              116   0.002900
137              100   0.002500
138               99   0.002475
139              121   0.003025
140              116   0.002900
141              128   0.003200
142              110   0.002750
143              115   0.002875
144              140   0.003500
145              120   0.003000
146              112   0.002800
147              126   0.003150
148              118   0.002950
149              119   0.002975
150              110   0.002750
151              118   0.002950
152              112   0.002800
153              120   0.003000
154              122   0.003050
155              124   0.003100
156              119   0.002975
157              130   0.003250
158              105   0.002625
159              129   0.003225
160              112   0.002800
161   �          122   0.003050
162   �          123   0.003075
163   �          128   0.003200
164   �          121   0.003025
165   �          107   0.002675
166   �          114   0.002850
167   �          110   0.002750
168   �          117   0.002925
169   �          118   0.002950
170   �          117   0.002925
171   �          142   0.003550
172   �          112   0.002800
173   �          126   0.003150
174   �          112   0.002800
175   �          126   0.003150
176   �          123   0.003075
177   �          113   0.002825
178   �          133   0.003325
179   �          119   0.002975
180   �          117   0.002925
181   �          124   0.003100
182   �          112   0.002800
183   �          104   0.002600
184   �          103   0.002575
185   �          129   0.003225
186   �           92   0.002300
187   �          106   0.002650
188   �          121   0.003025
189   �           94   0.002350
190   �          132   0.003300
191   �          133   0.003325
192   �          129   0.003225
193   �          125   0.003125
194   �          108   0.002700
195   �          115   0.002875
196   �          105   0.002625
197   �          127   0.003175
198   �          131   0.003275
199   �          112   0.002800
200   �          136   0.003400
201   �          119   0.002975
202   �          100   0.002500
203   �          103   0.002575
204   �          118   0.002950
205   �          112   0.002800
206   �          132   0.003300
207   �          107   0.002675
208   �          115   0.002875
209   �          133   0.003325
210   �          113   0.002825
211   �          128   0.003200
212   �          106   0.002650
213   �          118   0.002950
214   �          109   0.002725
215   �          132   0.003300
216   �          107   0.002675
217   �          131   0.003275
218   �          120   0.003000
219   �          138   0.003450
220   �          118   0.002950
221   �          111   0.002775
222   �          114   0.002850
223   �          127   0.003175
224   �          132   0.003300
225   �          105   0.002625
226   �          107   0.002675
227   �          105   0.002625
228   �          106   0.002650
229   �          108   0.002700
230   �          119   0.002975
231   �          102   0.002550
232   �          126   0.003150
233   �          115   0.002875
234   �          120   0.003000
235   �          125   0.003125
236   �          125   0.003125
237   �          128   0.003200
238   �          123   0.003075
239   �          115   0.002875
240   �          107   0.002675
241   �          121   0.003025
242   �          125   0.003125
243   �          124   0.003100
244   �          114   0.002850
245   �          113   0.002825
246   �           80   0.002000
247   �          117   0.002925
248   �          117   0.002925
249   �          101   0.002525
250   �          101   0.002525
251   �          109   0.002725
252   �          100   0.002500
253   �          124   0.003100
254   �          117   0.002925
255   �          123   0.003075

Total:         40000   1.000000
{% endhighlight %} 
so i'm going to create and run a few more tests that arn't in the scope of <a href="http://man.cx/?page=ent ent do[go]=go">ent</a> we will see what happens. 

<h2>Update Mon May 16 11:49:30 EDT 2016 Finding a needle in a haystack of needles:</h2>

I have ran a few more tests and i still can't figure out why it's (apparently) not random. Although there is some correlation with the sin(prime_numbers) for some weird reason. However, i have decided that there are some jobs that humans just arn't good at and this is one of them. I have decided to employ my knowledge of scala and this cool new <a href="https://github.com/deeplearning4j/deeplearning4j">library</a> to learn more about this data. Now apparently employing neural nets for something like this "is overkill". I could do something like subtract every event from every other event and find some patterns in there but if this is caused by build up in the GM-tube (which i'm planning on testing hardware wise) it won't be very consistant and may just be adding more entropy to the pool. Having a deep learning algorithm will also pave the way for a spacial tracking for all units that i was planning on doing with machine learning. As well as allow me to find sequences such as coordinated events within a localized group and maybe even find the power of the cosmic ray that hit this area. Anyways, i love overkill especially if it doesn't cost me anything. I am also creating a test for the Bonferroni correction that i may encorprate back into ent when i get it working. I'm also going to try isolating a unit in a farday cage to see if any Low frequency raido signals are setting off the GM-tube at a constant interval, and maybe building a CV enabled cloud chamber(Simons idea) and putting it ontop of the unit to count how may events are from radiation. 

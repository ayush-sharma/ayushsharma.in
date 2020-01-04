---
layout: post
title:  "AWS EBS Types Read/Write Benchmarks"
number: 24
date:   2017-01-19 16:00
categories: reliability
---
I wanted to run benchmarks on all the AWS EBS disk types for reference. I created a machine with the following disks:
<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}aws-ebs-disk-configuration-for-read-write-benchmarks.jpg" width="700" height="255" alt="Disk configuration for EBS read/write benchmarks.">

## Scripts
Disk reads were done using the following script:

```bash
#!/bin/bash
for i in {1..5}; do hdparm -Tt /dev/xvda; done
for i in {1..5}; do hdparm -Tt /dev/xvdb; done
for i in {1..5}; do hdparm -Tt /dev/xvdc; done
for i in {1..5}; do hdparm -Tt /dev/xvdd; done
for i in {1..5}; do hdparm -Tt /dev/xvde; done
for i in {1..5}; do hdparm -Tt /dev/xvdf; done
for i in {1..5}; do hdparm -Tt /dev/xvdg; done
```

Disk writes were done using the following script:

```bash
#!/bin/bash
for i in {1..5}; do echo "-- xvda"; dd if=/dev/zero of=/dev/xvda bs=4k count=100k; done
for i in {1..5}; do echo "-- xvdb"; dd if=/dev/zero of=/dev/xvdb bs=4k count=100k; done
for i in {1..5}; do echo "-- xvdc"; dd if=/dev/zero of=/dev/xvdc bs=4k count=100k; done
for i in {1..5}; do echo "-- xvdd"; dd if=/dev/zero of=/dev/xvdd bs=4k count=100k; done
for i in {1..5}; do echo "-- xvde"; dd if=/dev/zero of=/dev/xvde bs=4k count=100k; done
for i in {1..5}; do echo "-- xvdf"; dd if=/dev/zero of=/dev/xvdf bs=4k count=100k; done
for i in {1..5}; do echo "-- xvdg"; dd if=/dev/zero of=/dev/xvdg bs=4k count=100k; done
```

## Results

### Instance Type: m3.medium

#### Read Speeds (MBPS)
<pre>
<table>
    <tbody>
        <tr>
            <td>Mount Point</td>
            <td colspan="2">/dev/xvda</td>
            <td colspan="2">/dev/xvdb</td>
            <td colspan="2">/dev/xvdc</td>
            <td colspan="2">/dev/xvdd</td>
            <td colspan="2">/dev/xvde</td>
            <td colspan="2">/dev/xvdf</td>
            <td colspan="2">/dev/xvdg</td>
        </tr>
        <tr>
            <td>Disk Type</td>
            <td colspan="2">Root GP2</td>
            <td colspan="2">Instance Store</td>
            <td colspan="2">EBS PIOPS (IO1) 400</td>
            <td colspan="2">EBS PIOPS (IO1) 5000</td>
            <td colspan="2">EBS Cold HDD (SC1)</td>
            <td colspan="2">EBS TOHDD (ST1)</td>
            <td colspan="2">Magnetic</td>
        </tr>
        <tr>
            <td>Read Type</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
        </tr>
        <tr>
            <td rowspan="5">Reads</td>
            <td>5027.25</td>
            <td>83.99</td>
            <td>5118.29</td>
            <td>805.69</td>
            <td>5083.22</td>
            <td>83.24</td>
            <td>5041.15</td>
            <td>80.32</td>
            <td>5067.19</td>
            <td>55.79</td>
            <td>5055.38</td>
            <td>82.24</td>
            <td>4982.88</td>
            <td>83.18</td>
        </tr>
        <tr>
            <td>5054.29</td>
            <td>82.23</td>
            <td>5045.46</td>
            <td>816.08</td>
            <td>5039.56</td>
            <td>80.72</td>
            <td>5081.32</td>
            <td>79.91</td>
            <td>5033.26</td>
            <td>55.83</td>
            <td>5030.27</td>
            <td>84.34</td>
            <td>4991.25</td>
            <td>80.73</td>
        </tr>
        <tr>
            <td>5099.39</td>
            <td>83.3</td>
            <td>5071.96</td>
            <td>806.43</td>
            <td>5059.23</td>
            <td>83.94</td>
            <td>5050.78</td>
            <td>83.21</td>
            <td>4992.19</td>
            <td>55.82</td>
            <td>5006.38</td>
            <td>84.17</td>
            <td>5017.09</td>
            <td>80.66</td>
        </tr>
        <tr>
            <td>5102.44</td>
            <td>83.92</td>
            <td>5078.28</td>
            <td>803.88</td>
            <td>5057.22</td>
            <td>81.85</td>
            <td>5037.17</td>
            <td>81.68</td>
            <td>5046.49</td>
            <td>55.84</td>
            <td>4962.85</td>
            <td>84.05</td>
            <td>5016.52</td>
            <td>81.42</td>
        </tr>
        <tr>
            <td>5055.4</td>
            <td>83.29</td>
            <td>5078.03</td>
            <td>786.34</td>
            <td>5055.63</td>
            <td>83.99</td>
            <td>5012.06</td>
            <td>83.42</td>
            <td>5127.5</td>
            <td>55.84</td>
            <td>4968.41</td>
            <td>84.07</td>
            <td>4984.07</td>
            <td>80.83</td>
        </tr>
        <tr>
            <td>Average Read</td>
            <td>5067.754</td>
            <td>83.346</td>
            <td>5078.404</td>
            <td>803.684</td>
            <td>5058.972</td>
            <td>82.748</td>
            <td>5044.496</td>
            <td>81.708</td>
            <td>5053.326</td>
            <td>55.824</td>
            <td>5004.658</td>
            <td>83.774</td>
            <td>4998.362</td>
            <td>81.364</td>
        </tr>
    </tbody>
</table>
</pre>

#### Write Speeds (MBPS)
<pre>
<table>
    <tbody>
        <tr>
            <td>Mount Point</td>
            <td>/dev/xvda</td>
            <td>/dev/xvdb</td>
            <td>/dev/xvdc</td>
            <td>/dev/xvdd</td>
            <td>/dev/xvde</td>
            <td>/dev/xvdf</td>
            <td>/dev/xvdg</td>
        </tr>
        <tr>
            <td>Disk Type</td>
            <td>Root GP2</td>
            <td>Instance Store</td>
            <td>EBS PIOPS (IO1) 400</td>
            <td>EBS PIOPS (IO1) 5000</td>
            <td>EBS Cold HDD (SC1)</td>
            <td>EBS TOHDD (ST1)</td>
            <td>Magnetic</td>
        </tr>
        <tr>
            <td rowspan="5">Writes</td>
            <td>1200</td>
            <td>204</td>
            <td>31.4</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>31.1</td>
        </tr>
        <tr>
            <td>65.3</td>
            <td>259</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>31.4</td>
        </tr>
        <tr>
            <td>246</td>
            <td>232</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>31.1</td>
        </tr>
        <tr>
            <td>81.8</td>
            <td>221</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>31.8</td>
        </tr>
        <tr>
            <td>383</td>
            <td>231</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>30.6</td>
        </tr>
        <tr>
            <td>Average Write</td>
            <td>395.22</td>
            <td>229.4</td>
            <td>36.04</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>37.2</td>
            <td>31.2</td>
        </tr>
    </tbody>
</table>
</pre>

### Instance Type: c3.8xlarge

#### Read Speeds (MBPS)
<pre>
<table cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td>Mount Point</td>
            <td colspan="2">/dev/xvda</td>
            <td colspan="2">/dev/xvdb</td>
            <td colspan="2">/dev/xvdc</td>
            <td colspan="2">/dev/xvdd</td>
            <td colspan="2">/dev/xvde</td>
            <td colspan="2">/dev/xvdf</td>
            <td colspan="2">/dev/xvdg</td>
        </tr>
        <tr>
            <td>Disk Type</td>
            <td colspan="2">Root GP2</td>
            <td colspan="2">Instance Store</td>
            <td colspan="2">EBS PIOPS (IO1) 400</td>
            <td colspan="2">EBS PIOPS (IO1) 5000</td>
            <td colspan="2">EBS Cold HDD (SC1)</td>
            <td colspan="2">EBS TOHDD (ST1)</td>
            <td colspan="2">Magnetic</td>
        </tr>
        <tr>
            <td>Read Type</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
            <td>Cached Read</td>
            <td>Buffered Read</td>
        </tr>
        <tr>
            <td rowspan="5">Reads</td>
            <td>11057.17</td>
            <td>173.88</td>
            <td>10893.09</td>
            <td>953.87</td>
            <td>11069.15</td>
            <td>118.62</td>
            <td>11035.86</td>
            <td>329.37</td>
            <td>11088.44</td>
            <td>55.81</td>
            <td>11052.17</td>
            <td>116.44</td>
            <td>10998.47</td>
            <td>64.63</td>
        </tr>
        <tr>
            <td>10988.7</td>
            <td>173.88</td>
            <td>11029.77</td>
            <td>967.62</td>
            <td>11032.89</td>
            <td>118.62</td>
            <td>11061.04</td>
            <td>358.53</td>
            <td>11055.34</td>
            <td>55.66</td>
            <td>11050.93</td>
            <td>173.94</td>
            <td>10995.74</td>
            <td>90.13</td>
        </tr>
        <tr>
            <td>11056.83</td>
            <td>173.87</td>
            <td>10939.08</td>
            <td>951.2</td>
            <td>11083.63</td>
            <td>118.62</td>
            <td>11067.88</td>
            <td>358.36</td>
            <td>11024.41</td>
            <td>55.75</td>
            <td>11076.77</td>
            <td>174.03</td>
            <td>10962.87</td>
            <td>91.32</td>
        </tr>
        <tr>
            <td>11103.1</td>
            <td>173.88</td>
            <td>10931.89</td>
            <td>947.6</td>
            <td>11061.23</td>
            <td>118.62</td>
            <td>11025.63</td>
            <td>362.81</td>
            <td>11048.92</td>
            <td>55.7</td>
            <td>11035.29</td>
            <td>174.03</td>
            <td>10934.36</td>
            <td>90.62</td>
        </tr>
        <tr>
            <td>11070.23</td>
            <td>173.88</td>
            <td>10960.17</td>
            <td>889.17</td>
            <td>11060.32</td>
            <td>118.63</td>
            <td>11028.91</td>
            <td>358.85</td>
            <td>11075.36</td>
            <td>55.71</td>
            <td>11021.72</td>
            <td>99.11</td>
            <td>10974.47</td>
            <td>95.68</td>
        </tr>
        <tr>
            <td>Average Read</td>
            <td>11055.206</td>
            <td>173.878</td>
            <td>10950.8</td>
            <td>941.892</td>
            <td>11061.444</td>
            <td>118.622</td>
            <td>11043.864</td>
            <td>353.584</td>
            <td>11058.494</td>
            <td>55.726</td>
            <td>11047.376</td>
            <td>147.51</td>
            <td>10973.182</td>
            <td>86.476</td>
        </tr>
    </tbody>
</table>
</pre>

#### Write Speeds (MBPS)
<pre>
<table>
    <tbody>
        <tr>
            <td>Mount Point</td>
            <td>/dev/xvda</td>
            <td>/dev/xvdb</td>
            <td>/dev/xvdc</td>
            <td>/dev/xvdd</td>
            <td>/dev/xvde</td>
            <td>/dev/xvdf</td>
            <td>/dev/xvdg</td>
        </tr>
        <tr>
            <td>Disk Type</td>
            <td>Root GP2</td>
            <td>Instance Store</td>
            <td>EBS PIOPS (IO1) 400</td>
            <td>EBS PIOPS (IO1) 5000</td>
            <td>EBS Cold HDD (SC1)</td>
            <td>EBS TOHDD (ST1)</td>
            <td>Magnetic</td>
        </tr>
        <tr>
            <td rowspan="5">Writes</td>
            <td>2100</td>
            <td>2100</td>
            <td>114</td>
            <td>417</td>
            <td>47.8</td>
            <td>182</td>
            <td>28.6</td>
        </tr>
        <tr>
            <td>2700</td>
            <td>2800</td>
            <td>106</td>
            <td>333</td>
            <td>43.9</td>
            <td>137</td>
            <td>35.1</td>
        </tr>
        <tr>
            <td>2900</td>
            <td>2900</td>
            <td>106</td>
            <td>332</td>
            <td>43.8</td>
            <td>137</td>
            <td>33.6</td>
        </tr>
        <tr>
            <td>2900</td>
            <td>3000</td>
            <td>106</td>
            <td>332</td>
            <td>43.8</td>
            <td>137</td>
            <td>34.1</td>
        </tr>
        <tr>
            <td>2200</td>
            <td>2200</td>
            <td>106</td>
            <td>332</td>
            <td>43.8</td>
            <td>137</td>
            <td>34.8</td>
        </tr>
        <tr>
            <td>Average Write</td>
            <td>2560</td>
            <td>2600</td>
            <td>107.6</td>
            <td>349.2</td>
            <td>44.62</td>
            <td>146</td>
            <td>33.24</td>
        </tr>
    </tbody>
</table>
</pre>
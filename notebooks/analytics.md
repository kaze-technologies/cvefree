# Analytics Notebook

This notebook provides the code to locally download the CVEFree dataset and then provide some examples for visualisations that can be produced from it.

Run the following to download the CVEFree dataset to your local directory:


```python
import urllib.request
urllib.request.urlretrieve('https://kazepublic.blob.core.windows.net/cvefree/data.json ', filename='../data.json')
```




    ('../data.json', <http.client.HTTPMessage at 0x1105ba250>)



Import matplot and json to open the .json data file and prepare to make plots:


```python
import matplotlib.pyplot as plt
import json
import pandas as pd

# Opening JSON file.
f = open('../data.json')
 
# Return JSON object as a dictionary.
data = json.load(f)
```

Load Dictionary into Pandas dataframe and print a table of random CVE samples:


```python
pd.set_option('display.min_rows', 20)
df = pd.DataFrame(data['cves'])
display(df.sample(n=5))
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cve</th>
      <th>last_modified_datetime</th>
      <th>published_datetime</th>
      <th>cvssv2</th>
      <th>cvssv3</th>
      <th>epss</th>
      <th>cti_count</th>
      <th>social_media_audience</th>
      <th>vendors</th>
      <th>software_cpes</th>
      <th>v_score</th>
      <th>cisa</th>
      <th>metasploit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>232469</th>
      <td>CVE-2019-18164</td>
      <td>None</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>None</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[]</td>
      <td>[]</td>
      <td>NaN</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>120904</th>
      <td>CVE-2021-42094</td>
      <td>2021-10-14T14:44:00.000Z</td>
      <td>2021-10-07T20:15:00.000Z</td>
      <td>7.5</td>
      <td>9.8</td>
      <td>0.020550000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[zammad]</td>
      <td>[cpe:2.3:a:zammad:zammad:*:*:*:*:*:*:*:*]</td>
      <td>0.480930</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>294686</th>
      <td>CVE-2023-46858</td>
      <td>2023-11-06T19:29:00.000Z</td>
      <td>2023-10-29T01:15:00.000Z</td>
      <td>NaN</td>
      <td>5.4</td>
      <td>0.000500000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[moodle]</td>
      <td>[cpe:2.3:a:moodle:moodle:4.3.0:*:*:*:*:*:*:*]</td>
      <td>0.259800</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>270678</th>
      <td>CVE-2022-28781</td>
      <td>2022-05-11T17:22:00.000Z</td>
      <td>2022-05-03T20:15:00.000Z</td>
      <td>7.2</td>
      <td>6.7</td>
      <td>0.008850000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[google]</td>
      <td>[cpe:2.3:o:google:android:12.0:*:*:*:*:*:*:*, ...</td>
      <td>0.326300</td>
      <td>None</td>
      <td>None</td>
    </tr>
    <tr>
      <th>169615</th>
      <td>CVE-2004-2434</td>
      <td>2017-07-11T01:31:00.000Z</td>
      <td>2004-12-31T05:00:00.000Z</td>
      <td>5.0</td>
      <td>NaN</td>
      <td>0.294150000</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[microsoft]</td>
      <td>[cpe:2.3:a:microsoft:ie:6.0:sp1:*:*:*:*:*:*]</td>
      <td>0.381721</td>
      <td>None</td>
      <td>None</td>
    </tr>
  </tbody>
</table>
</div>


# Histograms

The following code does a histogram count of for a number of scores and metrics to show their distributions for various insights:


```python
df.hist()
plt.tight_layout()
```


    
![png](../resources/analytics_7_0.png)
    


# Vendor & Software Word Clouds

Installs the wordcloud package and then extracts strings of all the vendors and software associated with CVEs to create appopriate word cloud plots:

```python
from wordcloud import WordCloud

# Concatanate strings of vendors and software for format for wordcloud - This can be optimised considerably.
str_vendors = ""
str_software = ""
for i in data['cves']:
    if i['vendors'] is not None:
        for ven in i['vendors']:
            str_vendors += " " + ven.strip()
    if i['software_cpes']:
        str_software += " " + i['software_cpes'][0].split(':')[4]  # Only take first CPE. Misses values but processing all is performance heavy. Process all for accurate count.

# Use numpy for creating wordcloud circle mask.
import numpy as np
x, y = np.ogrid[:300, :300]
mask = (x - 150) ** 2 + (y - 150) ** 2 > 130 ** 2
mask = 255 * mask.astype(int)

# Create vendor count wordcloud.
wc_vendor = WordCloud(background_color = "white", width = 800, height = 400, max_words=30, collocations=False, mask=mask, colormap='viridis').generate(str_vendors)

# Create software/hardware count wordcloud.
wc_software = WordCloud(background_color = "white", width = 800, height = 400, max_words=30, collocations=False, mask=mask, colormap='viridis').generate(str_software)

fig, axes = plt.subplots(1, 2)
axes[0].set_title("Top Affected Vendors")
axes[0].imshow(wc_vendor, interpolation="bilinear")
axes[1].set_title("Top Affected Software/Hardware")
axes[1].imshow(wc_software, interpolation="bilinear")
for ax in axes:
    ax.set_axis_off()
plt.show()
```

    
![png](../resources//analytics_10_0.png)
    


# Top 10 Social Media

Sort CVEs by the top 10 trending on social media in the last 30 day window and print as table of information.


```python
# Sort list by social media audience then create dataframe.
newlist = sorted(data['cves'], key=lambda d: d['social_media_audience'] if (d['social_media_audience'] is not None) else 0, reverse=True)
df = pd.DataFrame(newlist[:10])
display(df[['cve', 'cvssv2', 'cvssv3', 'epss', 'cti_count', 'social_media_audience']])
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cve</th>
      <th>cvssv2</th>
      <th>cvssv3</th>
      <th>epss</th>
      <th>cti_count</th>
      <th>social_media_audience</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CVE-2023-50164</td>
      <td>None</td>
      <td>9.8</td>
      <td>0.097990000</td>
      <td>4.0</td>
      <td>1109757</td>
    </tr>
    <tr>
      <th>1</th>
      <td>CVE-2023-50428</td>
      <td>None</td>
      <td>5.3</td>
      <td>0.000520000</td>
      <td>NaN</td>
      <td>931232</td>
    </tr>
    <tr>
      <th>2</th>
      <td>CVE-2023-45866</td>
      <td>None</td>
      <td>6.3</td>
      <td>0.000450000</td>
      <td>5.0</td>
      <td>539671</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CVE-2023-28252</td>
      <td>None</td>
      <td>7.8</td>
      <td>0.008860000</td>
      <td>3.0</td>
      <td>321202</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CVE-2023-51385</td>
      <td>None</td>
      <td>6.5</td>
      <td>0.001550000</td>
      <td>NaN</td>
      <td>269238</td>
    </tr>
    <tr>
      <th>5</th>
      <td>CVE-2023-4863</td>
      <td>None</td>
      <td>8.8</td>
      <td>0.410100000</td>
      <td>2.0</td>
      <td>255707</td>
    </tr>
    <tr>
      <th>6</th>
      <td>CVE-2023-48795</td>
      <td>None</td>
      <td>5.9</td>
      <td>None</td>
      <td>NaN</td>
      <td>249635</td>
    </tr>
    <tr>
      <th>7</th>
      <td>CVE-2023-3390</td>
      <td>None</td>
      <td>7.8</td>
      <td>0.000420000</td>
      <td>NaN</td>
      <td>181349</td>
    </tr>
    <tr>
      <th>8</th>
      <td>CVE-2023-29357</td>
      <td>None</td>
      <td>9.8</td>
      <td>0.001240000</td>
      <td>NaN</td>
      <td>173136</td>
    </tr>
    <tr>
      <th>9</th>
      <td>CVE-2023-7102</td>
      <td>None</td>
      <td>NaN</td>
      <td>None</td>
      <td>4.0</td>
      <td>172811</td>
    </tr>
  </tbody>
</table>
</div>

---
layout: post
title: Data fingerprinting intro
subtitle: Proving ownership of your data
gh-repo: tanjascats/fingerprinting-toolbox
gh-badge: [star, fork, follow]
tags: [data]
comments: true
---
## IP protection
Data is nowadays a crucial resource in many applications because processing it is beneficial in many businesses, governmental institutions and academia, and is of great value to its creators or owners because it usually requires a lot of time, effort, money and human experts to create, clean and prepare high-quality data.
In some cases, the parties who have such valuable data, do not have enough processing power or expertise to maximise the utility of the data and hence need to share it with third parties. A great example are the hospitals with medical data that gets shared with researchers to obtain useful analyses. 
Since data is valuable to its owner, it is in their interest to protect the ownership while being able to share the data with intended third parties, for example, by "signing" their data and being able to trace the copies to their recipients. 
--- image: scenario - openly share the data with IP protection in place 

![](/assets/img/data-fingerprinting/fingerprinting_scenario.png)

The scenario at the same time describes the goals and abilities of an ownerhisp protection method called fingerprinting. 

## What is data fingerprinting
Data fingerprinting is a method of embedding a traceable mark into digital data to verify the owner and identify the recipient of a released copy of a data set. It falls under the area of steganography, methods where information is represented or hiden within another digital or physical object, in such a manner that the presence of the information is not evident to human inspection. Fingerprinting draws the motivation from a well-known method of digital content protection, watermarking. 
*insert image and source*
## Fingerprinting vs. watermarking
Digital fingerprinting is an information hiding technique that helps protecting digital intellectual property.
It encodes a secret owner-specific information and the identification of the content recipient such that it sis posssible to trace the source of unauthorised usage of a digital content and prove the ownership. 
## Fingerprinting tabular data, how does it work
Fingerprint is a bit string unique for each data recipient, that gets embedded into data via _embedding algorithim_. Fingerprint is created by some hash function that takes as an input the owner's secret key combined (e.g. concatenated) with the ID of a recipient. The embedding algorithm then applies minor pseudo-random modifications on data based on the given fingerprint.
The positions of modifications are firstly chosen by a pseudo-random number sequence generator seeded by the owner's secret key. 
Secondly, each fingerprint bit contributes to some of the modifications. 
For instance, the fingerprint bit at the position 0 will cause the value 1 to change to 0 (red square).

| Original data | After fingerprinting |
| :------------------------- |:------------------------- |
| ![](/assets/img/data-fingerprinting/table0.PNG) | ![](/assets/img/data-fingerprinting/table1.PNG) |

The _extraction algorithm_ is an inverse process of embedding - it detects the fingerprint from the fingerprinted data set. This works because the same pseudo-random sequence generator is used to find the locations of modifications (note that only the owner with their secret key can reproduce this sequence) and the modification itself then reveals a fingerprint bit. 

## Robustness
It is important to take into account malicious actions that may interfere with correct fingerprint detection. For the fingerprint to be successfully extracted from data, there needs to be an evidence of each fingerprint bit in the data. This can be compromised by removing or changing parts of data. If the fingerprint cannot be extracted from the data, it is not possible to identify the source of the unauthorised usage: 
![](/assets/img/data-fingerprinting/table4.PNG)
Therefore, each fingerprint bit is embedded with enough redundancy into the data such that, even if parts of it are removed or modified, the detection process still successfully extracts the fingerprint from the fingerprinted data:
![](/assets/img/data-fingerprinting/table5.PNG)
Intuitivelly, the more marks embedded in data, the more robust fingerprint will be. However, since more marks means modifying the original data more, this necessarily implies some utility loss. Therefore, a challenge for fingerprinting techniques is to **provide a robust ownership protection mechanism while keeping the original data utility as well as possible**.

## Fingerprinting a toy data set
- show how fingerprinting functions look like 
~~~
from scheme import Universal
scheme = Universal(gamma=3, fingerprint_bit_length=32)
fingerprinted_data = scheme.insertion(data_path, secret_key=12345678, recipient_id=0)
~~~
A snippet of original data:
|index|age|sex|bmi|children|smoker|region|charges|
|---|---|---|---|---|---|---|---|
|0|19|female|27\.9|0|yes|southwest|16884\.924|
|1|18|male|33\.77|1|no|southeast|1725\.5523|
|2|28|male|33\.0|3|no|southeast|4449\.462|
|3|33|male|22\.705|0|no|northwest|21984\.47061|
|4|32|male|28\.88|0|no|northwest|3866\.8552|

A snippet of fingerprinted data:

|index|age|sex|bmi|children|smoker|region|charges|
|---|---|---|---|---|---|---|---|
|0|19|female|27\.9|0|yes|southwest|16884\.925|
|1|18|male|33\.77|1|no|southeast|1725\.5523|
|2|28|male|33\.0|3|no|southeast|4449\.462|
|3|33|male|22\.704|0|no|northwest|21984\.47061|
|4|32|male|28\.88|0|no|southeast|3866\.8552|

Three differences are evident: (1,charges), (3, bmi) and (4, region).
Let's see the chnages overall:
- show statistics of changes in the data, distributions


Number of differences per attribute:

| |index|age|sex|bmi|children|smoker|region|charges|
|---|---|---|---|---|---|---|---|---|
|absolute|-|28|23|38|31|24|53|25|
|percentage|-|2\.09%|1\.72%|2\.84%|2\.32%|1\.79%|3\.98%|1\.87%|

How significant are the differences? Let's see the change in mean and standard deviation (we can do that only for numerical data)
| |index|age|sex|bmi|children|smoker|region|charges|
|---|---|---|---|---|---|---|---|---|
|mean|-|39.2070/39.2025|-|30.6634/30.6635|1.0949/1.0987|-|-|13270.4223/13270.4223|
|std|-|14.0500/14.0540|-|6.0982/6.0983|1.2055/1.2135|-|-|12110.0112/12110.0112|

For categorical data the changes are discreete so we can just have a look at the change of distributions:
![](/assets/img/data-fingerprinting/distributions.pdf)
I.e. minimal changes

- show detection 
~~~
scheme.detection(fingerprinted_data, secret_key=12345678)
~~~

```
scheme.detection(fingerprinted_data, secret_key=12345678)

```

~~~
## Out: Recipient with id: 0 is suspected.
~~~

For the correctness, let's check the output with a wrong secret key:
~~~
scheme.detection(fingerprinted_data, secret_key=123)

Out: No one suspected.
~~~

We can fingerprint more copies and see if there is possibility of a recipient confusion
~~~
more_fingerprinted_data_copies = []
for i in range(1,20):
  more_fingerprinted_data_copies.append(scheme.insertion(data_path, secret_key=12345678, recipient_id=i))
  
suspects = []
suspects.append(scheme.detection(fingerprinted_data, secret_key=12345678))
for fp_data in more_fingerprinted_data_copies:
  suspects.append(scheme.detection(fp_data, secret_key=12345678))
suspects

Out: \[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19\]

~~~

- show alteration example (subset/flipping) and successful detection 
Let's see what happens if the recipient alters their data. 
For example, deletes arbitrary entries (rows), deletes content of entire column and changes some values:
~~~
fp_data_modified = fingerprinted_data.dataframe.copy()
# increase all bmi values by 0.111
fp_data_modified['bmi'] = fp_data_copy['bmi'].apply(lambda x: x + 0.111)
# replace all occurences of 18 with 19 and 50 with 51
fp_data_modified.replace(18,19, inplace=True)
fp_data_modified.replace(50,51, inplace=True)
# remove 23 rows
fp_data_modified.drop([1,5,89,120,145,167,567,600,675,723,765,876,901,902,903,910,999,1000,1004,1056,1099,1176,1234], axis=0, inplace=True)
# remove the contents of entire column
fp_data_modified['children'] = None

scheme.detection(fp_data_modified, secret_key=12345678)

Out: Recipient 0 is suspected.
~~~
(maybe include the counts)

- show how much it is possible to change the data max, so that the fingerprint stays 
But what is the limit with modifications? Let's see how much the data can be altered such that the fingerprint is still extractable. 
We run this 10 times to average the randomness in removing arbitrary rows

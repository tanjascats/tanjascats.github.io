---
layout: post
title: Data fingerprinting intro
subtitle: Proving ownership of your data
gh-repo: tanjascats/fingerprinting-toolbox
gh-badge: [star, fork, follow]
tags: [data]
comments: true
---
## What is data fingerprinting
Data fingerprinting is a method of embedding a traceable mark into digital data to verify the owner and identify the recipient of a released copy of a data set. 
Fingerprinting is one of the steganography methods where information is represented or hiden within another digital or physical object, in such a manner that the presence of the information is not evident to human inspection. 
Fingerprinting draws the motivation from a well known method of digital content protection, watermarking. 
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


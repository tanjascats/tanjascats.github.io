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
# Fingerprinting tabular data, how does it work
Fingerprint is a bit string unique for each data recipient, that gets embedded into data via embedding algorithim. Fingerprint is created by some hash function that takes as an input an owner's secret key combined (e.g. concatenated) with the ID of a recipient. The embedding algorithm then applies minor pseudo-random modifications on data based on the given fingerprint.
Each fingerprint bit contributes to the pattern of modifications
The positions of modifications are firstly chosen by a pseudo-random number sequence generator seeded by the owner's secret key. 

## Fingerprinting a toy data set

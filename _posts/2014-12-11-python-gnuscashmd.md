---
layout: post
title: Creating split transactions in GnuCash with Python and a csv file	
---

I needed to enter lots of checks as the treasurer of a booster club for my daughter's high school. I didn't want to enter them all manually in the clunky user interface for register entry.
I first tried using straight csv import but it would still have required a fair bit of manual intervention. I came across [this question](http://gnucash.1415818.n4.nabble.com/QIF-import-split-transaction-mis-format-td1434153.html) 
and decided the Quicken QIF format (see [this](https://en.wikipedia.org/wiki/Quicken_Interchange_Format) and [this](http://www.respmech.com/mym2qifw/qif_new.htm)) wasn't all that complex.

So I wrote a Python script to take a csv file that created a split transaction suitable for import into GnuCash. Here is the ugly version that gets the job done. There still is some manual intervention required but at this point I feel better about manual intervention than directly modifying the gnucash file using the Python API. 


```
# -*- coding: utf-8 -*-
# -*- coding: utf-8 -*-
"""
Created on Tue Dec 08 21:17:47 2015

@author: schoub1
"""

import pandas as pd
flines = pd.read_csv('simple.csv')
str_hdr = """!Account
NChecking Account
D
TOth L
^
"""
date = "2015-12-10"
dep = 2
with open("simple.qif", "w") as foh:
    foh.write(str_hdr)
    thisdep = flines
    odep = """!Type:Oth L\nD%s\nT%s\nMCoco Deposit %s\n""" % (date, sum(thisdep.Amount), dep)
    for n, sline in thisdep.iterrows():
        print sline
        odep=odep+"""Scoco\nE%s\n$-%s\n""" % (sline.Memo, sline.Amount)
    odep=odep + "^"
    foh.write(odep)
    
```

Here is an example csv input
```
Date,Memo,Amount
12/5/2015,Ed Jones Check 5231,10
12/5/2015,Steve Marley cash,2
12/5/2015,Francis Bacon check 673,50

```


And here is the QIF output:
```
!Account
NChecking Account
D
TOth L
^
!Type:Oth L
D2015-12-10
T62
MCoco Deposit 2
Scoco
EEd Jones Check 5231
$-10
Scoco
ESteve Marley cash
$-2
Scoco
EFrancis Bacon check 673
$-50
^
```

---
title: "A Pagerank Algorithm to Mine a Website that Provides Legal Analysis of US National Security Issues"
author: Ethan Ashby
date: 09/09/2020
output: html_document
---

# Goal

I want to build a search engine inspired by Google's Pagerank for <a ref="https://www.lawfareblog.com"> Lawfare</a>, a website that provides legal analysis of issues pertaining to national security. The general idea is to exploit the linking structure of the website to assign high importance to pages that recieve links/connections from other pages. This is a prototype version of Google's PageRank algorithm. Equations were sourced from <a ref="https://galton.uchicago.edu/~lekheng/meetings/mathofranking/ref/langville.pdf">*Deeper Inside Pagerank*</a> and written in a python script `pagerank.py`.

# Task 1: The Power Method

I began by applying this PageRank algorithm to a toy dataset included in the *Deeper Inside Pagerank* paper to confirm that the algorithm was working correctly. Then I applied the algorithm to the linking structure of the Lawfare website.

### Part 1: Run PageRank on toy dataset

This dataset was sourced from the *Deeper Inside Pagerank* paper. The ranking of webpages in the output below matches the output produced by Professor Mike Izbicki's implementation. This means that my pagerank system is working correctly.

```
$ python3 pagerank.py --data=./small.csv.gz --verbose
DEBUG:root:computing indices
DEBUG:root:computing values
DEBUG:root:i:0, accuracy:0.2562914192676544
DEBUG:root:i:1, accuracy:0.11841224879026413
DEBUG:root:i:2, accuracy:0.07070135325193405
DEBUG:root:i:3, accuracy:0.03181542828679085
DEBUG:root:i:4, accuracy:0.02049657516181469
DEBUG:root:i:5, accuracy:0.01010836474597454
DEBUG:root:i:6, accuracy:0.006371539551764727
DEBUG:root:i:7, accuracy:0.0034228088334202766
DEBUG:root:i:8, accuracy:0.0020879562944173813
DEBUG:root:i:9, accuracy:0.0011749409604817629
DEBUG:root:i:10, accuracy:0.0007013420690782368
DEBUG:root:i:11, accuracy:0.00040321744745597243
DEBUG:root:i:12, accuracy:0.00023802227224223316
DEBUG:root:i:13, accuracy:0.00013810147356707603
DEBUG:root:i:14, accuracy:8.11051795608364e-05
DEBUG:root:i:15, accuracy:4.722943776869215e-05
DEBUG:root:i:16, accuracy:2.7705485990736634e-05
DEBUG:root:i:17, accuracy:1.616420195205137e-05
DEBUG:root:i:18, accuracy:9.47775970416842e-06
DEBUG:root:i:19, accuracy:5.499416602106066e-06
DEBUG:root:i:20, accuracy:3.1918063996272394e-06
DEBUG:root:i:21, accuracy:1.8999419353349367e-06
DEBUG:root:i:22, accuracy:1.1156980690429918e-06
DEBUG:root:i:23, accuracy:6.490135433523392e-07
INFO:root:rank=0 pagerank=6.6270e-01 url=4
INFO:root:rank=1 pagerank=5.2179e-01 url=6
INFO:root:rank=2 pagerank=4.1434e-01 url=5
INFO:root:rank=3 pagerank=2.3175e-01 url=2
INFO:root:rank=4 pagerank=1.8590e-01 url=3
INFO:root:rank=5 pagerank=1.6917e-01 url=1
```

## Part 2: Search Query

The `pagerank.py` file has an option for `--search-query`, which allows us to return pages related to a user-specified search term. This returns all urls that match the query string, sorted by the PageRank algorithm. The following searches correspond to searches for coronavirus (or 'corona'), Donald Trump (or 'trump'), Iran ('iran'), or Nuclear Weapons ("nuclear").

```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='corona'
INFO:root:rank=0 pagerank=1.0038e-03 url=www.lawfareblog.com/lawfare-podcast-united-nations-and-coronavirus-crisis
INFO:root:rank=1 pagerank=8.9226e-04 url=www.lawfareblog.com/house-oversight-committee-holds-day-two-hearing-government-coronavirus-response
INFO:root:rank=2 pagerank=7.0392e-04 url=www.lawfareblog.com/britains-coronavirus-response
INFO:root:rank=3 pagerank=6.9155e-04 url=www.lawfareblog.com/prosecuting-purposeful-coronavirus-exposure-terrorism
INFO:root:rank=4 pagerank=6.7042e-04 url=www.lawfareblog.com/israeli-emergency-regulations-location-tracking-coronavirus-carriers
INFO:root:rank=5 pagerank=6.6257e-04 url=www.lawfareblog.com/why-congress-conducting-business-usual-face-coronavirus
INFO:root:rank=6 pagerank=6.5047e-04 url=www.lawfareblog.com/congressional-homeland-security-committees-seek-ways-support-state-federal-responses-coronavirus
INFO:root:rank=7 pagerank=6.3621e-04 url=www.lawfareblog.com/paper-hearing-experts-debate-digital-contact-tracing-and-coronavirus-privacy-concerns
INFO:root:rank=8 pagerank=6.1249e-04 url=www.lawfareblog.com/house-subcommittee-voices-concerns-over-us-management-coronavirus
INFO:root:rank=9 pagerank=6.0188e-04 url=www.lawfareblog.com/livestream-house-oversight-committee-holds-hearing-government-coronavirus-response

$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='trump'
INFO:root:rank=0 pagerank=5.7828e-03 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=1 pagerank=5.2340e-03 url=www.lawfareblog.com/document-trump-revokes-obama-executive-order-counterterrorism-strike-casualty-reporting
INFO:root:rank=2 pagerank=5.1298e-03 url=www.lawfareblog.com/trump-administrations-worrying-new-policy-israeli-settlements
INFO:root:rank=3 pagerank=4.6600e-03 url=www.lawfareblog.com/dc-circuit-overrules-district-courts-due-process-ruling-qasim-v-trump
INFO:root:rank=4 pagerank=4.5935e-03 url=www.lawfareblog.com/donald-trump-and-politically-weaponized-executive-branch
INFO:root:rank=5 pagerank=4.3073e-03 url=www.lawfareblog.com/how-trumps-approach-middle-east-ignores-past-future-and-human-condition
INFO:root:rank=6 pagerank=4.0936e-03 url=www.lawfareblog.com/why-trump-cant-buy-greenland
INFO:root:rank=7 pagerank=3.7592e-03 url=www.lawfareblog.com/oral-argument-summary-qassim-v-trump
INFO:root:rank=8 pagerank=3.4510e-03 url=www.lawfareblog.com/dc-circuit-court-denies-trump-rehearing-mazars-case
INFO:root:rank=9 pagerank=3.4485e-03 url=www.lawfareblog.com/second-circuit-rules-mazars-must-hand-over-trump-tax-returns-new-york-prosecutors

$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='iran'
INFO:root:rank=0 pagerank=4.5747e-03 url=www.lawfareblog.com/praise-presidents-iran-tweets
INFO:root:rank=1 pagerank=4.4175e-03 url=www.lawfareblog.com/how-us-iran-tensions-could-disrupt-iraqs-fragile-peace
INFO:root:rank=2 pagerank=2.6929e-03 url=www.lawfareblog.com/cyber-command-operational-update-clarifying-june-2019-iran-operation
INFO:root:rank=3 pagerank=1.9392e-03 url=www.lawfareblog.com/aborted-iran-strike-fine-line-between-necessity-and-revenge
INFO:root:rank=4 pagerank=1.5453e-03 url=www.lawfareblog.com/parsing-state-departments-letter-use-force-against-iran
INFO:root:rank=5 pagerank=1.5358e-03 url=www.lawfareblog.com/iranian-hostage-crisis-and-its-effect-american-politics
INFO:root:rank=6 pagerank=1.5258e-03 url=www.lawfareblog.com/announcing-united-states-and-use-force-against-iran-new-lawfare-e-book
INFO:root:rank=7 pagerank=1.4221e-03 url=www.lawfareblog.com/us-names-iranian-revolutionary-guard-terrorist-organization-and-sanctions-international-criminal
INFO:root:rank=8 pagerank=1.1788e-03 url=www.lawfareblog.com/iran-shoots-down-us-drone-domestic-and-international-legal-implications
INFO:root:rank=9 pagerank=1.1463e-03 url=www.lawfareblog.com/israel-iran-syria-clash-and-law-use-force

$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='nuclear'
INFO:root:rank=0 pagerank=6.2053e-04 url=www.lawfareblog.com/house-oversight-committee-report-saudi-nuclear-transfer-deal
INFO:root:rank=1 pagerank=5.7805e-04 url=www.lawfareblog.com/digital-strangelove-cyber-dangers-nuclear-weapons
INFO:root:rank=2 pagerank=5.6458e-04 url=www.lawfareblog.com/what-make-icjs-provisional-measures-iran-v-us-nuclear-sanctions-case
INFO:root:rank=3 pagerank=5.4716e-04 url=www.lawfareblog.com/document-house-oversight-committee-report-saudi-nuclear-transfer
INFO:root:rank=4 pagerank=5.2810e-04 url=www.lawfareblog.com/iran-nuclear-deal-still-risk-after-trump-waives-sanctions
INFO:root:rank=5 pagerank=5.2793e-04 url=www.lawfareblog.com/law-and-public-intuition-use-force-part-2-threat-nuclear-weapons
INFO:root:rank=6 pagerank=5.2627e-04 url=www.lawfareblog.com/trump-withdraws-iran-nuclear-agreement-what-comes-next
INFO:root:rank=7 pagerank=5.2383e-04 url=www.lawfareblog.com/iran-still-complying-nuclear-deal-rebel-coalition-yemen-bends-doesnt-break-us-and-hezbollah-fight
INFO:root:rank=8 pagerank=5.2277e-04 url=www.lawfareblog.com/treaties-and-irrelevance-understanding-irans-suit-against-us-reimposing-nuclear-sanctions
INFO:root:rank=9 pagerank=5.2023e-04 url=www.lawfareblog.com/document-nuclear-posture-review-2018
```

## Part 3: Filtering pages with more than a specified fraction to eliminate broad pages

When we run the pagerank algorithm on all the Lawfare webpages, we tend to return really broad pages (like a homepage) that get linked to every article. Therefore these pages have really large pagerank. This is shown in the first output chunk below. 

We may not be too interested in these pages... an easy way to remove pages with too many links is the `--filter_ratio` argument. This eliminates all pages that have more links than a specified fraction. If we run on `--filter_ratio=0.2', we get the second output chunk below.

```
Ethans-MacBook-Pro-2:Project 1 PageRank ethanashby$ python3 pagerank.py --data=./lawfareblog.csv.gz
INFO:root:rank=0 pagerank=2.8741e-01 url=www.lawfareblog.com/about-lawfare-brief-history-term-and-site
INFO:root:rank=1 pagerank=2.8741e-01 url=www.lawfareblog.com/lawfare-job-board
INFO:root:rank=2 pagerank=2.8741e-01 url=www.lawfareblog.com/masthead
INFO:root:rank=3 pagerank=2.8741e-01 url=www.lawfareblog.com/litigation-documents-resources-related-travel-ban
INFO:root:rank=4 pagerank=2.8741e-01 url=www.lawfareblog.com/subscribe-lawfare
INFO:root:rank=5 pagerank=2.8741e-01 url=www.lawfareblog.com/litigation-documents-related-appointment-matthew-whitaker-acting-attorney-general
INFO:root:rank=6 pagerank=2.8741e-01 url=www.lawfareblog.com/documents-related-mueller-investigation
INFO:root:rank=7 pagerank=2.8741e-01 url=www.lawfareblog.com/our-comments-policy
INFO:root:rank=8 pagerank=2.8741e-01 url=www.lawfareblog.com/upcoming-events
INFO:root:rank=9 pagerank=2.8741e-01 url=www.lawfareblog.com/topics
```

```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2
INFO:root:rank=0 pagerank=3.4696e-01 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=1 pagerank=2.9521e-01 url=www.lawfareblog.com/livestream-nov-21-impeachment-hearings-0
INFO:root:rank=2 pagerank=2.9040e-01 url=www.lawfareblog.com/opening-statement-david-holmes
INFO:root:rank=3 pagerank=1.5179e-01 url=www.lawfareblog.com/lawfare-podcast-ben-nimmo-whack-mole-game-disinformation
INFO:root:rank=4 pagerank=1.5099e-01 url=www.lawfareblog.com/todays-headlines-and-commentary-1964
INFO:root:rank=5 pagerank=1.5099e-01 url=www.lawfareblog.com/todays-headlines-and-commentary-1963
INFO:root:rank=6 pagerank=1.5071e-01 url=www.lawfareblog.com/lawfare-podcast-week-was-impeachment
INFO:root:rank=7 pagerank=1.4957e-01 url=www.lawfareblog.com/todays-headlines-and-commentary-1962
INFO:root:rank=8 pagerank=1.4367e-01 url=www.lawfareblog.com/cyberlaw-podcast-mistrusting-google
INFO:root:rank=9 pagerank=1.4240e-01 url=www.lawfareblog.com/lawfare-podcast-bonus-edition-gordon-sondland-vs-committee-no-bull
```

## Part 4: Playing with Algorithm Parameters

When we play with 

```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --alpha=0.99999
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2 --alpha=0.99999
```

The first three commands took between 10-20 iterations to converge, while the last command took 685 iterations to converge. This is because the modified $P$ matrix has a small eigengap which requires a small alpha for fast convergence.

Note that different values of $\alpha$ give us very different page ranks:

```
$python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2
INFO:root:rank=0 pagerank=3.4696e-01 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=1 pagerank=2.9521e-01 url=www.lawfareblog.com/livestream-nov-21-impeachment-hearings-0
INFO:root:rank=2 pagerank=2.9040e-01 url=www.lawfareblog.com/opening-statement-david-holmes
INFO:root:rank=3 pagerank=1.5179e-01 url=www.lawfareblog.com/lawfare-podcast-ben-nimmo-whack-mole-game-disinformation
INFO:root:rank=4 pagerank=1.5099e-01 url=www.lawfareblog.com/todays-headlines-and-commentary-1964
INFO:root:rank=5 pagerank=1.5099e-01 url=www.lawfareblog.com/todays-headlines-and-commentary-1963
INFO:root:rank=6 pagerank=1.5071e-01 url=www.lawfareblog.com/lawfare-podcast-week-was-impeachment
INFO:root:rank=7 pagerank=1.4957e-01 url=www.lawfareblog.com/todays-headlines-and-commentary-1962
INFO:root:rank=8 pagerank=1.4367e-01 url=www.lawfareblog.com/cyberlaw-podcast-mistrusting-google
INFO:root:rank=9 pagerank=1.4240e-01 url=www.lawfareblog.com/lawfare-podcast-bonus-edition-gordon-sondland-vs-committee-no-bull

$python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2 --alpha=0.99999
INFO:root:rank=0 pagerank=7.0149e-01 url=www.lawfareblog.com/covid-19-speech-and-surveillance-response
INFO:root:rank=1 pagerank=7.0149e-01 url=www.lawfareblog.com/lawfare-live-covid-19-speech-and-surveillance
INFO:root:rank=2 pagerank=1.0552e-01 url=www.lawfareblog.com/cost-using-zero-days
INFO:root:rank=3 pagerank=3.1757e-02 url=www.lawfareblog.com/lawfare-podcast-former-congressman-brian-baird-and-daniel-schuman-how-congress-can-continue-function
INFO:root:rank=4 pagerank=2.2040e-02 url=www.lawfareblog.com/events
INFO:root:rank=5 pagerank=1.6027e-02 url=www.lawfareblog.com/water-wars-increased-us-focus-indo-pacific
INFO:root:rank=6 pagerank=1.6026e-02 url=www.lawfareblog.com/water-wars-drill-maybe-drill
INFO:root:rank=7 pagerank=1.6023e-02 url=www.lawfareblog.com/water-wars-disjointed-operations-south-china-sea
INFO:root:rank=8 pagerank=1.6020e-02 url=www.lawfareblog.com/water-wars-song-oil-and-fire
INFO:root:rank=9 pagerank=1.6020e-02 url=www.lawfareblog.com/water-wars-sinking-feeling-philippine-china-relations
```

# Task 2: The Personalization Vector

## Part 1: Comparing Personalization Vector to Queried Results

Notice that the personalization vector and search query approach return very different results. The difference is that the personalization vector designates a webpage as important if other coronavirus-related webpages ascribe importance to that webpage. The search query approach designates a webpage as important if **any** webpage designates it as important. 

```
#Personalization Vector Approach
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --personalization_vector_query='corona'
INFO:root:rank=0 pagerank=6.3127e-01 url=www.lawfareblog.com/covid-19-speech-and-surveillance-response
INFO:root:rank=1 pagerank=6.3124e-01 url=www.lawfareblog.com/lawfare-live-covid-19-speech-and-surveillance
INFO:root:rank=2 pagerank=1.5947e-01 url=www.lawfareblog.com/chinatalk-how-party-takes-its-propaganda-global
INFO:root:rank=3 pagerank=1.2209e-01 url=www.lawfareblog.com/rational-security-my-corona-edition
INFO:root:rank=4 pagerank=1.2209e-01 url=www.lawfareblog.com/brexit-not-immune-coronavirus
INFO:root:rank=5 pagerank=9.3360e-02 url=www.lawfareblog.com/trump-cant-reopen-country-over-state-objections
INFO:root:rank=6 pagerank=9.1920e-02 url=www.lawfareblog.com/prosecuting-purposeful-coronavirus-exposure-terrorism
INFO:root:rank=7 pagerank=9.1920e-02 url=www.lawfareblog.com/britains-coronavirus-response
INFO:root:rank=8 pagerank=7.7770e-02 url=www.lawfareblog.com/lawfare-podcast-united-nations-and-coronavirus-crisis
INFO:root:rank=9 pagerank=7.2888e-02 url=www.lawfareblog.com/house-oversight-committee-holds-day-two-hearing-government-coronavirus-response

#Query approach
$python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --search_query='corona'
INFO:root:rank=0 pagerank=8.1319e-03 url=www.lawfareblog.com/house-oversight-committee-holds-day-two-hearing-government-coronavirus-response
INFO:root:rank=1 pagerank=7.7908e-03 url=www.lawfareblog.com/lawfare-podcast-united-nations-and-coronavirus-crisis
INFO:root:rank=2 pagerank=5.2262e-03 url=www.lawfareblog.com/livestream-house-oversight-committee-holds-hearing-government-coronavirus-response
INFO:root:rank=3 pagerank=3.9584e-03 url=www.lawfareblog.com/britains-coronavirus-response
INFO:root:rank=4 pagerank=3.8114e-03 url=www.lawfareblog.com/prosecuting-purposeful-coronavirus-exposure-terrorism
INFO:root:rank=5 pagerank=3.3973e-03 url=www.lawfareblog.com/paper-hearing-experts-debate-digital-contact-tracing-and-coronavirus-privacy-concerns
INFO:root:rank=6 pagerank=3.3633e-03 url=www.lawfareblog.com/cyberlaw-podcast-how-israel-fighting-coronavirus
INFO:root:rank=7 pagerank=3.3557e-03 url=www.lawfareblog.com/israeli-emergency-regulations-location-tracking-coronavirus-carriers
INFO:root:rank=8 pagerank=3.2160e-03 url=www.lawfareblog.com/congress-needs-coronavirus-failsafe-its-too-late
INFO:root:rank=9 pagerank=3.1036e-03 url=www.lawfareblog.com/why-congress-conducting-business-usual-face-coronavirus
```

## Part 2: Finding webpages that are related to coronavirus, but don't mention coronavirus

```
$python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --personalization_vector_query='corona' --search_query='-corona'
INFO:root:rank=0 pagerank=6.3127e-01 url=www.lawfareblog.com/covid-19-speech-and-surveillance-response
INFO:root:rank=1 pagerank=6.3124e-01 url=www.lawfareblog.com/lawfare-live-covid-19-speech-and-surveillance
INFO:root:rank=2 pagerank=1.5947e-01 url=www.lawfareblog.com/chinatalk-how-party-takes-its-propaganda-global
INFO:root:rank=3 pagerank=9.3360e-02 url=www.lawfareblog.com/trump-cant-reopen-country-over-state-objections
INFO:root:rank=4 pagerank=7.0277e-02 url=www.lawfareblog.com/fault-lines-foreign-policy-quarantined
INFO:root:rank=5 pagerank=6.9713e-02 url=www.lawfareblog.com/lawfare-podcast-mom-and-dad-talk-clinical-trials-pandemic
INFO:root:rank=6 pagerank=6.4944e-02 url=www.lawfareblog.com/limits-world-health-organization
INFO:root:rank=7 pagerank=5.9492e-02 url=www.lawfareblog.com/chinatalk-dispatches-shanghai-beijing-and-hong-kong
INFO:root:rank=8 pagerank=5.1245e-02 url=www.lawfareblog.com/us-moves-dismiss-case-against-company-linked-ira-troll-farm
INFO:root:rank=9 pagerank=5.1245e-02 url=www.lawfareblog.com/livestream-house-armed-services-committee-holds-hearing-priorities-missile-defense
```

## Part 3: Finding webpages (national security topics) related to Russia

By including "russia" in the personalization vector query but omitting it from the search query, we obatin a list of webpages that are highly linked by Russia-related articles but don't directly mention Russia. This can provide a proxy for topics that are linked to Russia in some way. A few themes emerge from looking at these new links: mainly Trump's impeachment, Trump's tax returns, the house armed services committee, the Durham report (counterinvestigation of the Trump-Russia probe), the Mueller Report (on Russia's Interference in the US 2016 Presidential Election), David Holmes (US Diplomat to Ukraine), and Disinformation.

```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --personalization_vector_query='russia' --search_query='-russia'
INFO:root:rank=0 pagerank=3.6442e-01 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=1 pagerank=2.4348e-01 url=www.lawfareblog.com/livestream-nov-21-impeachment-hearings-0
INFO:root:rank=2 pagerank=2.4348e-01 url=www.lawfareblog.com/opening-statement-david-holmes
INFO:root:rank=3 pagerank=2.4122e-01 url=www.lawfareblog.com/mueller-report-and-national-security-investigations-and-prosecutions
INFO:root:rank=4 pagerank=2.4078e-01 url=www.lawfareblog.com/justice-department-should-share-more-information-durham-investigation
INFO:root:rank=5 pagerank=1.5502e-01 url=www.lawfareblog.com/senate-examines-threats-homeland
INFO:root:rank=6 pagerank=1.5123e-01 url=www.lawfareblog.com/lawfare-podcast-ben-nimmo-whack-mole-game-disinformation
INFO:root:rank=7 pagerank=1.5123e-01 url=www.lawfareblog.com/lawfare-podcast-week-was-impeachment
INFO:root:rank=8 pagerank=1.4814e-01 url=www.lawfareblog.com/livestream-house-armed-services-committee-hearing-f-35-program
INFO:root:rank=9 pagerank=1.4814e-01 url=www.lawfareblog.com/what-make-first-day-impeachment-hearings
```


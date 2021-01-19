---
title:  Political Tweet Aggregation
date:   2020-06-30
layout: post
categories: programming
description: Analyzing tweets from 2020 Democratic Primary contests.
---

## Are Progressives Winning the Twitter Battle?

June 23rd, 2020 will be remembered as landmark election day for the United State's Progressive movement. Democratic primary elections across all levels of government took place in in Kentucky, New York and Virginia, and the results present the best news for the American left since Bernie's 3-peat in the (now distant) 2020 presidential primary.

I am focusing my attention on two candidates in particular. Jamaal Bowman is a middle school principal running for the House in New York's 16th Congressional District. Charles Booker serves as a state lawmaker in Kentucky and is vying to represent the Democrats in their (perhaps insurmountable) quest to unseat Mitch McConnell.

Both men face steeps odds in their respective contests. Bowman matches up against Eliot Engel, a 30-year incumbent and the chairman of the powerful Foreign Affairs committee. Booker, meanwhile, competes with Amy McGrath, who in spite of not previously holding office finds herself with a juggernaut fundraising apparatus and preference from the Democratic national apparatus.

As of this writing, Bowman handily won his primary; Booker maintains a slight lead with tens of thousands of votes still to be counted. I'm interested in whether or not the winning and losing candidate's Twitter accounts might present us with an explanation, or at the least some indicators, for their surprise success.

In this piece, I will remark on a few metrics of interest in this space: the candidate's followers over time (do people want to stay in the loop?) and then take a close look at the top-performing tweets from the candidates (are they posting their way to victory?).

I'll use Wayback Machine, tweepy (a wrapper for the Twitter API), and python3 to carry this work out. The relevant code can be found on my Github.

### 1.1 Followers over Time
#### New York

Bowman joined Twitter last June when he launched his campaign; indeed, this contest saw him start from the bottom in more ways than one. Fast-forwarding to 2020, Bowman's twitter following is 25.5k strong on May 27th. Luckily, Bowman caught fire in the month of June. By the 14th he amassed 47.4k followers, which climbed to a whopping 72.1k followers the day votes were cast in NY-16. (Note: at the time of this writing, Bowman is now at 115k followers. Talk about expanding your audience!)

Now, with all respect to Engel, his campaign was clearly never a "digital-first" 
operation. From May 23rd to June 23rd, he gained exactly a thousand followers. I'll illustrate this lackluster performance with a ratio: that's about 2 new followers a day for every 1 of the incumbent's (particularly nugatory) 15 Congressional terms. Bowman clearly got hot at the right time, cresting Engels in followers a week before election day (June 17th).
#### Kentucky

The primary between McGrath and Booker began in earnest in November, following a tweet from Booker declaring he was "testing the waters" for a primary run. McGrath started the race with a massive online presence - over 300k followers - because of a previous failed Congressional run where she gained national attention and praise for her impressive resume and genteel debate performances. Her numbers dwarf Booker's November total of 13.5k followers. This makes sense considering he is a state lawmaker of relatively unknown pedigree at this point, squaring off against a favorite of the MSNBC / Pod Save circuit.

Now, McGrath's following continued to grow in 2020, particularly as Mitch McConnell provided numerous examples of why he's downright revolting. But Booker's social media team carried out diligent work and greatly expanded his online presence. And, in a bittersweet break of fate, the Kentucky primary was pushed back a month due to Covid, which provided Booker with precious extra time to become a household name.
A week before election day, Booker's following soars to over 150k. Up to this point, McGrath has shot up by about 100k followers. She reaches an election day total of 427.9k to Booker's 190k. Now, while McGrath still finished the primary with a far greater number of followers, Booker gained 180k more followers during the course of the primary! McGrath's popularity prior to the contest notwithstanding, it was Booker who landed enormous increases in social media presence when it really mattered.
### 1.2 Top Tweets 
Let's examine each of the candidates best performing tweets from the 50 days leading up to election day. I've ranked them by number of retweets, the best indicator for social-media virility.
#### New York
This was never going to be a far fight. Representative Engel never faced a serious challenge in his 15-term tenure. So we can perhaps excuse him for being caught flat-footed. Thus, Engel was no match online for Bowman's vibrant Gen-X energy; for example, Bowman loves to strike a pose while rocking a Wu-Tang mask. Tough to beat!
Bowman's digital team successfully overcame the follower gulf through popular tweets focusing on the issues and Bowman's fascinating resume. Engel, who barely visits New York anymore, never stood a chance. Let's have a look at Bowman's best.
<iframe class="prog-chart" src="/documents/prog/bowman.html" height=600 width =1000 display:block></iframe>
9 out of these 10 tweets were posted in the month of June. This demonstrates the momentum Bowman generated. We have to keep in mind that the normal operations of a campaign like door-knocking, town halls and rallies were all put on hold due to the pandemic; the very sort of close to the ground work that worked so wonderfully for Bowman's fellow Bronx-rep AOC. So the digital presence of these candidates was more important than ever.
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I guess this endorsement makes sense. Neither of them know how to play defense. <a href="https://t.co/7qi9Dj1yWO">pic.twitter.com/7qi9Dj1yWO</a></p>&mdash; Jamaal Bowman (@JamaalBowmanNY) <a href="https://twitter.com/JamaalBowmanNY/status/1273774471409795072?ref_src=twsrc%5Etfw">June 19, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>Bowman, in these popular tweets, does not shy away from criticizing one of Engel's major vulnerabilities that I alluded to earlier: for all intents and purposes, Engel was MIA from the state of New York from about March onward. In one hilarious Bowman salvo, he pillories Engel's downright laughable digital presence while also endearing himself to voters as a New Yorker through and through.
Let's have a look, then, at what Engel was cooking up from the @RepEliotEngel handle.
<iframe class="prog-chart" src="/documents/prog/engel.html" display:block height=600 width =1000 display:block></iframe>

Yes, these are tweets. And yes, a couple of them broke a thousand retweets. But they do not appear to be the digital messages of a candidate at all concerned about his upcoming primary. Where is the outreach to voters? Where is the very mention of election day?

Engel clearly did not see Bowman coming until it was too late, if at all. Case in point, most of these "hot" tweets are from May; in today's mercurial world, being last month's news means next to nothing.
Also, I'd wager the voters in NY-16 have little appetite for updates regarding the House Foreign Affairs committee. They are probably more worried about how they are going to pay expensive New York rent in a pandemic! With content like this its no wonder Engel was accruing followers at such a minuscule rate.

#### Kentucky

McGrath’s fundraising advantage over Booker (40:1 by the end of the primary) allowed her to equip herself with the best digital team the Democratic party had to offer. Unfortunately for her staff, we’re living in an unpredictable, fractured time of history. The conventional, middle of the road digital strategy (mirroring her political outlook) lost its appeal as the primary grew more contentious. Booker’s top tweets are an important facet of this fascinating reversal.
<iframe class="prog-chart" src="/documents/prog/booker.html" display:block height=600 width =1000 display:block></iframe>
Booker’s tweets dazzle. Almost all of his posts leverage “I” or, importantly, “we.” He is unafraid of directly contrasting himself with McGrath, a digital strategy that typically pays digital dividends for progressives. His tweets successfully convey his ideological platform in a crisp manner.

Moreover, most all of Booker’s most engaging content was published in the month of June, when his campaign rocketed into national headlines. His campaign effectively engaged with the news coverage regarding national protests and stood confidently alongside the movement for Black lives. McGrath, meanwhile, was busy during the protests.

Intriguingly, both Booker and Bowman’s top tweet of the cycle involved Juneteenth, which in my view beautifully evinces the nation’s expanding consciousness and commitment to racial justice.

Now I will focus on Booker’s opponent. McGrath’s tweets operate differently than Booker’s. Although she is not an incumbent, her tweets follow a pattern of sorts similar to Engel’s: rather than buttressing her personal strengths and ideals, she instead draws contrasts with the arch-GOP villain (in her case, McConnell) she opposes.

To continue, the only 3 of McGrath’s 10 most-retweeted posts came in the month of June, and none of them were posted within a week of Election day. This digital stagnation ought to have augured badly for her campaign staff.
<iframe class="prog-chart" src="/documents/prog/booker.html" display:block height=600 width =1000 display:block></iframe>
In my view, the following tweet illustrates everything special about Booker’s candidacy and his twitter presence all at once. It connects back to crucial issue — the center’s promise of “access” to healthcare as opposed to the left’s push for a bonafide national health insurance system — that came up time and again during the Presidential primaries, in a much more effective manner than Bernie Sanders’ continued reminders that the United States is the only major country on earth that does not guarantee healthcare as a human right.
<blockquote class="twitter-tweet tw-align-center" ><p lang="en" dir="ltr">I rationed my insulin so I could afford feed my daughters. It nearly killed me.<br><br>In a country as rich and powerful as the United States, that is a damn shame.<br><br>No person should have to make that decision.</p>&mdash; Charles Booker (@Booker4KY) <a href="https://twitter.com/Booker4KY/status/1259297397026500610?ref_src=twsrc%5Etfw">May 10, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
Booker’s pithy, three line story transcends statistics, ideology and even politics, by distilling an incredibly convoluted issue to human terms. Importantly, the message resonates to the scores of people in his state that lack health insurance.

McGrath’s laser focus on McConnell, in my view, lacks the persuasive energy of the personalized narratives woven into Booker’s tweets. He conveys an earnest honesty that charms even the “apolitical” digital passerby. As such, Booker’s top performing tweets garnered thousands more retweets and favorites than McGraths, which is only possible if you break out from the “political junkie” neighborhood of Twitter and bring along folks that mostly follow sports and culture topics.

In another divergence from Booker, McGrath’s top tweets mostly comprise “quote” tweets, where she chimes in on an already sent tweet. Adding context to an ongoing issue is a great way for candidates to insert themselves into the conversation. However, there’s something quite elegant about going viral through nothing but your own devices.

<i>Update, 7/1: Amy McGrath eked out a narrowing victory over Booker despite raising 40x the amount of money as his campaign. In the end, Booker’s superior posts could not get him over the finish-line; many are speculating this is due to the large number of mail in ballots sent out prior to his rise. Still, his campaign ought to be proud of the energy they generated for Booker, who should be hailed as a progressive wonder with a bright future ahead.</i>

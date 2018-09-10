# How we report bugs at Klira
This is mostly ~stolen from~ inspired by http://www.catb.org/esr/faqs/smart-questions.html#bespecific and https://www.chiark.greenend.org.uk/~sgtatham/bugs.html

Developers generally have [alot](http://hyperboleandahalf.blogspot.com/2010/04/alot-is-better-than-you-at-everything.html) 
on their minds and we have more projects going on than you might know. While your question might be obvious to you, the recipient
might lack the context and having to spend time figuring context out will not only make people less keen on trying to answer
your question, but it will also mean that doing so will take more time. So if you have context please, share it in a concise
manner so that the recipient can quickly get up to speed.

## Use meaningful, specific subjects
The first message in chat applications and the subject line in mails are possibly your most important line of communication.
Your goal is to quickly pique the interest of the most qualified person to answer it.

One good convention for subject headers, used by many tech support organizations, is “object - deviation”.
The “object” part specifies what thing or group of things is having a problem,
and the “deviation” part describes the deviation from expected behavior.

Bad:

    HELP! Website dosnt work!

Smart:

    Submition form, not loading the text on my computer
    
Smarter:
    
    Submition form, personal information is not loading, just "pleas wait", affects some people but not all.

## Provide context
If possible provide a link, or screenshot to the broken data.

Consider WHAT the problem is and use the form of data that you think is most relevant for that. 

 * If the problem is with layout, looks or interaction-elements of a program or a web-page a screenshot is probbably the most usefull thing to attach to an error report.
 * If the problem concerns [data or information](https://sv.wikipedia.org/wiki/Information) that is: some info that you are expecting is not appearing or is not correct copying and pasting the text is probably the best choice. This is because it enables us to easily work with the data without having to type it of a screenshot which in turn helps us help you faster. A screenshot might also be relevant but always consider pasting raw text if possible.
 * If you provide a link check if the problem also occurs if you paste the link in an anonymous browser window, since an annonymous browser window is more likely to act like our computers do.
 
## Volume is not precision

You need to be precise and informative. This end is not served by simply dumping huge volumes of code or data into a help request. If you have a large, complicated test case that is breaking a program, try to trim it and make it as small as possible.

This is useful for at least three reasons. One: being seen to invest effort in simplifying the question makes it more likely you'll get an answer, Two: simplifying the question makes it more likely you'll get a useful answer. Three: In the process of refining your bug report, you may develop a fix or workaround yourself.
Title: Low friction data analysis in a web browser
Date: 2018-03-23
Category: Spike sorting

I already perform too many mouse clicks each day. Therefore, when I use a web service to accomplish a task, I want to be able to start interacting with the site immediately, without having to install software, sign up, or even log in. Sites like amazon.com have become very successful because they allow users to shop around and do as much as possible before being asked to log in or enter a credit card number. As a programmer, I make good use of websites such as jsfiddle that allow you to simply type in some JavaScript code and click "Run". If you find the result useful (perhaps it is a minimal test or proof of principle) you simply share the URL with a friend or on a discussion board -- no need to register or log in!

A couple years ago, I was particularly inspired by a mind-mapping website called mindmup.com. Unfortunately this site has since become overly complex. But at the time, their home page presented a single blue bubble at the center of the browser window. Just by typing and dragging bubbles around, I could create a mind map, perfect for brainstorming or outlining a talk or a paper. I could then simply share my creation with friends or family by sending the URL.

Another fantastic resource is etherpad (I believe developed by Mozilla), which enables collaborative typing on a virtual piece of paper in the cloud. You can have dozens of people simultaneously editing the same document in real time, and each person's text is highlighted in a different color. Upon visiting the website you are assigned a brand new virtual piece of paper, and you can share the URL with anyone else you want to co-edit with.

Many other for-profit resources on the web provide free access to a subset of functionality, and understand the importance of not requiring the user to provide unnecessary information in order to use those basic resources. Signing up in order to use further resources is then optimized to be as painless as possible. This is what I mean by a low-friction experience.

With these examples in mind, let's consider data analysis in the cloud, and in particular hosting a website for validating and comparing spike sorting algorithms on a standard collection of ground-truthed datasets. Most users will probably just want to browse the hosted datasets, view results of standard processing pipelines on those examples, and perhaps view some of the associated summary plots. More advanced users may want to download the raw data files, or resulting outputs of spike sorting. Some visitors may even wish to view the processing pipelines that generated those results. With the exception of downloading the very large raw data files, users should be able to perform all these tasks without logging in.

Other user actions should require logging in (for example with a google or github account) but perhaps not signing up. These could include uploading of moderate-sized datasets for potential inclusion in the set of standard examples, running of moderately-intensive custom processing scripts, or launching a jupyterlab session (with limited resources) for more interactive exploration of the data in ipython notebooks. Only the more resource-consuming actions would require signing up, getting moderator approval, and being allocated quotas.

This type of design has advantages for administrators as well. After all, it is much easier to be a user on the client-side than to be manipulating files on the server-side. Therefore an administrator (i.e., a person maintaining the example datasets and processing pipelines, and monitoring processing jobs) could simply be a user with an unlimited quota for consuming the resources.



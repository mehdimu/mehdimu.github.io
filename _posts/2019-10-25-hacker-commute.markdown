---
layout: post
title:  "Using computer science to optimize my daily driving commute"
date:   2019-10-25
categories: web-scraper python
---

TLDR; I was able to optimize my work hours by scraping google maps everyday. :)

![401 Highway](https://images.unsplash.com/photo-1445873014904-7dc044bd92db?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1050&q=80)
*Photo from Unsplash*

<h3>Context</h3>
When I started my first full time job in 2016, I was very excited. 
The office was only 30 km away from my house and best part was that I could drive to work. 
According to GoogleMaps, with no traffic it was supposed to be easy 25 minute drive. 
However, I was supposed to be driving on the infamous highway called 401, one of the busiest highways of North America. 
On my first day to work, I was stuck in traffic for about 30 minutes, and it took me nearly 50 minutes to drive 30 km to get to work. 
I was not ready to do this on daily basis.

The best part was that my hours were flexible, so as long as I did my 8 hours, I could make my own schedule. 
It was this moment that I had the idea of figuring out the optimal time to leave home, such that I am spending the least amount of time in traffic, 
and at the same time I am not working late hours either.

<h3>Investigation</h3>
![Google Maps](https://images.unsplash.com/photo-1548345680-f5475ea5df84?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1052&q=80)
*Photo from Unsplash*

The first step to figuring out the optimal time to leave home was to collect traffic data. 
I rely on GoogleMaps at God level. I won't trust my own instincts over GoogleMaps. 
Therefore, I had to figure out how to collect traffic data from GoogleMaps.

After digging into it, I found that GoogleMaps offers driving instruction data for free, where you can simply call the API and get the directions and duration of the drive. 
However, they dont tell you realtime traffic data of how long will the drive take. And even if you wanted that data, 
you would have to sign up for business account which would cost alot of money. 
Therefore, I had to find a hacky way of scraping this data from GoogleMaps website.

After playing around with GoogleMaps website, I was able to figure out that the url always looks something like this:

`/maps/?saddr=milton&daddr=toronto`

If you notice up in the url, that you can pass in a starting-address and destination-address using 'saddr' and 'daddr'. 
Jackpot, now I can easily just call this page using my home address and office address.

Second problem: There is a toll highway that also goes parallel to 401 highway, meaning that it would be slighly faster if I take that toll highway. 
But tolls are very expensive in Canada, especially in Toronto area. And, by default GoogleMaps would have returned the fastest route, using the toll highway. 
Then again, after playing around with the interface, I figured that by adding a 'dirflg' parameter at the end and passing in 't', I was able to bypass the toll roads. 
Now, we are back in business, I have the url that I can pass in my home and work address, and get the realtime ETA time using realtime traffic.

<h3>Coding</h3>
If you google `python web scraper`, you will likely find ton of already published web scrapers. 
However, for our usecase, we need a very lightweight scraper.
We are going to write our scraper in Python, but honestly you could use any other language.
The algorithm will stay the same. 

First, we need to download two Python libraries, called `dryscrape` and `beautifulsoup`.
{% highlight python %}
import dryscrape
browser = dryscrape.new()
browser.goTo('http://www.google.ca')
{% endhighlight %}

In the above snippet, we are importing the libraries needed. Then, we are opening a http connection to `google.com`
and simply printing the http content.
Then, we will use BeautifulSoup to extract the html tag that contains the time duration. 

Wola, we are done with the parser side. Now, we can pass in any two addresses and get the realtime traffic data.


Running this in a cron session:
We have the scraper, now we need to run it on a schedule. 
Since we wanna keep scraping the data at a fixed interval, we need to find a computer/server that is online all the time.

<h3>Digital Ocean:</h3>
I had credits from before on DG, therefore I decided to create a linux droplet. 
You can learn more about droplets in the link here.

Once you have your droplet configured, its time to configure the cron to run our scraper. 
But before we do that, we have to import our script onto the server. 
The easiest way to do this is to clone your git repo onto the server. Now we can run:

We have configured our cron to run the python script every 15 minutes, Monday to Friday, between 07:00 am to 07:00 pm. 
Now, let it run for a week and our data should be saved in a file on the server. 

Please let me know if you found this helpful! Like and comment down below.

“Stay foolish, stay hungry”

---
layout: post
style: text
title: The missing dashboard
---

## Adieu, Location Dashboard

In a chilling reminder that nothing is forever, 2013 was an year that saw several google products killed. Some went away with prior notice, suggested alternatives and well deserved obituaries. Others without much of a whimper.

In this cold winter morning, let's remember one such product, of whose demise I learnt only today and which [google offically announced](http://productforums.google.com/forum/#!msg/maps/saFh0VelcE8/x28NC2HFbmYJ) only a week back. Its the Google location history dashboard. Now the name itself tells you that it never got much love inside of google, but it was one hell of a useful product. It was retired on the 13th of December, 2013.

Here's how it used to look like, in its prime.

![Location History Dashboard](http://2.bp.blogspot.com/-Hp0DMeszmB8/UsXlv3b8ziI/AAAAAAAB8l8/5o_Q-tD-yzw/s1600/google-location-dashboard-2.png)

If you use an android device, your phone regularly collects information about you all the time, and you will have a rich location history with google. The dashboard was a much more than a visulisation of the tens of thousands of those raw data points. It was immensely useful because it would tell me how much time I spent at work, at leisure and in travel, places I frequented on weekdays and weekends, and so on. <a href="#1">*</a>

In other words, that was a sort of extended memory, a permanent record of my presence. These are details google knew about you and me, and it may freak out of some of us, but the fact remains that the data still exists with google, and its only the dashboard visualisation that is gone. 

The part of it you can still access is [Location History] (https://maps.google.com/locationhistory), and I would strongly encourage you to check it out if you haven't ever before. 

As the location history itself may be closed to us in near future, without much of a notice, the subject of this post is to how to fetch and store this data elsewhere. Once you have the raw data, its easy to mine it the way dashboard did, and thats left as an excercise for the curious.

## Extracting the data

There are 2 ways you can pull out the data right now. One is to [access the location history](https://maps.google.com/locationhistory) and download data as KML, which you can then import in Google Earth.

![KML Export](/img/history.png)

There is this strange restriction that you could only download a maximum of 30 days of data from the UI, but if you change the start time and end time manually in this url, you will be able to access it for arbitrary time ranges.

*https://maps.google.com/locationhistory/b/0/kml?startTime=1387909800000&endTime=1387996200000*

The other option is to download all of your location history from [Google Data tools](https://www.google.com/settings/takeout/custom). 

![Google Takeout](/img/takeout.png)

Once you download the archive, extract the JSON file containing location info.

## Playing with the data

I converted the kml/json feed into geojson, a standard format for this kind of data. Github provides really simple visualisations for geojson files, and here I am, making my presence data public via github. Zoom in to see the geojson in its full glory, and figure out how revealing it is. <a href="#1">*</a> 

### As Line String

<script src="https://embed.github.com/view/geojson/qzaidi/geojson/gh-pages/data/locations.geojson">
</script>

If you are wondering about Iraq showing up there, I traveled to Karbala for [Arbaeen](http://en.wikipedia.org/wiki/Arba'een), which, for the record, is the largest peaceful single day gathering of humans anywhere on earth, and had an estimated 21 million people this year.

### As Points

<script src="https://embed.github.com/view/geojson/qzaidi/geojson/gh-pages/data/firstcut.geojson">
</script>

I have simplified the process of conversion from KML/JSON, should any of you want to do the same.

If you downloaded KML data, [follow this link](/geojson/) and drop the KML file in the DnD area. It would then convert the feed to geojson, which you can then use as a gist on github. As long as the extension is geojson, you will be able to visualise the data on a map, and embed it anywhere like I have above.

The process of conversion doesn't involve any server side processing (and is really fast) , but I would encourage you to inspect the source. Please do remember that I cobbled this and the blog post in under an hour, and older browsers with limited html5 support will just fail most ingraciously.

If you downloaded using the second method and downloaded json data, use [this link instead](/geojson/json.html) to generate the geojson data.

------------------------------------------------

<a name="1"></a>
1. As the dashboard went away, some of this started showing up in my google now cards.
1.Since the NSA already knows it, I suppose I can end the information disparity.

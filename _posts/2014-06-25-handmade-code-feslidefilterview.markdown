---
layout: post
title: 'Handmade Code: FeSlideFilterView'
date: '2014-06-25 07:06:25'
---

![FeSlideFilter_icon](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2014/06/FeSlideFilter_icon-e1403680938441-300x300.jpg?resize=300%2C300)


# Fe Slide Filter View

Hi everybody as an iOS developer like me ;]

I have just released my customize control in [GitHub](https://github.com/NghiaTranUIT/FeSlideFilter).

Here video as sample :  
<iframe allowfullscreen="allowfullscreen" frameborder="0" height="615" src="//www.youtube.com/embed/KUN6bs9pl74" width="820"></iframe>

# Technical

FeSlideFilter was build on top UIView. I combine UIScrollView with paging for title filter, and Mask layer technique. Here is what’s going on:

- Subclass UIView
- Using UIScrollView with paging for title filter
- Using LUT filter technique for apply effect to photo
- Conform UIScrollViewDelegate for interactive control – slide filter by your touch.
- Using Layer’s mask technique for implementing two-side previewer.
- Provide own delegate and datasource.

# License

FeSlideFilterView is under MIT license.

Feel free to do whatever you like ;]

If you use my control in your app, jut please tell me know.

# Question & Answer

I’m very glad if you drop a comment and discuss about this control. If something you don’t understand after taking a deep surgery about my control. Give me question, and I will reply soon.
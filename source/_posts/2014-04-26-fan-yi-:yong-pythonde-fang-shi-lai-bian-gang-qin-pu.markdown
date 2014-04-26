---
layout: post
title: "用python翻译钢琴卷"
date: 2014-04-26 15:02
comments: true
categories: 渣翻译
---


原文：http://zulko.github.io/blog/2014/02/12/transcribing-piano-rolls/#final_result

这篇文章我利用傅里叶变换来恢复已经被人遗忘的Gershwin钢琴谱。

钢琴卷(Piano Roll)是流行于1950s的可以放进沙龙机械钢琴的穿孔纸卷，有数千曲目（其中一些出自爵士名家）仅以钢琴卷的形式保存发表。
<!--more-->
George Gershwin在20岁时创作了[Limehouse Nights(~1918)](https://www.youtube.com/watch?feature=player_embedded&v=VjkS-XHScXU视频)。这是很酷的公开曲面，而且我也想演奏它，但是如同其他钢琴卷一样，这个曲目没有乐谱。

幸运的是，有人录制了这首曲目的视频[钢琴卷](https://www.youtube.com/watch?feature=player_embedded&v=wMsEbYCyY)视频。

在该博文里，我会利用很少的python代码将视频转换为乐谱。文章最后提供了乐谱，还有实现该功能的Python包(同样可用于MIDI文件的转换)

--------------------------
##下载视频
你可以用在终端用[youtube-dl](http://zulko.github.io/blog/2014/02/12/transcribing-piano-rolls/)从youtube下载视频

	youtube-dl wMsEbYCh7yY -o limehouse_nights.mp4
	
---------------------------
##Step 1: 分割钢琴卷
在视频的每帧都提供了一个很好的用于定位的像素线：
![分割线](http://zulko.github.io/images/rolls_transcription/watched_line.jpeg)

从视频的每帧图片中将这条线提取并堆叠在一起，我们就能得到钢琴卷的扫描图片。

		# Required Python modules
		from moviepy.editor import VideoFileClip # for video processing
		from pylab import * # for mathematics/plotting

		# load the video, keep the clip between t=2s and t= 30s
		video = VideoFileClip('./limehouse_nights.mp4').subclip(2,30)


		# extract the focus lines in the different frames, stack them.
		roll_picture = vstack([frame[[156],58:478]
                       for frame in video.iter_frames()])

		imshow( roll_picture ) # display the obtained picture	
![](http://zulko.github.io/images/rolls_transcription/roll_RGB.排列的孔洞
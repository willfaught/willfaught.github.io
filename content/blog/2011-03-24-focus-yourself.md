+++
categories = ["technology"]
date = 2011-03-24T15:19:00-07:00
tags = ["silverlight"]
title = "Focus Yourself"
+++

It took me several hours to figure out how to enable a Silverlight custom control to be "activated" by the space bar or enter keys like `Button`. It turns out when a control gets input events, it isn't automatically given focus (i.e. `OnGotFocus` isn't called). You have to call `Focus()` yourself. Hope this saves someone else some time.

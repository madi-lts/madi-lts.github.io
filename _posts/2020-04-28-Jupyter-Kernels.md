---
layout: post
title: Jupyter Kernel
category: data science
mathjax: true
tags: environment management
---

Jupyter `sys.executable` should return the path to your Anaconda version of python: `'/Users/madi/opt/anaconda3/bin/python'`. If it doesn't, there are probably overriding Jupyter kernels. In the terminal, run:

{% highlight bash %}
jupyter kernelspec list
python2             	/Users/madi/Library/Jupyter/kernels/python2
python3             	/Users/madi/Library/Jupyter/kernels/python3
{% endhighlight %}

Tell Jupyter to look for the Anaconda kernels by uninstalling the overriding kernels:

{% highlight bash %}
jupyter kernelspec uninstall python2
jupyter kernelspec uninstall python3
{% endhighlight %}

Now `sys.executable` should return the path to your Anaconda version of python.

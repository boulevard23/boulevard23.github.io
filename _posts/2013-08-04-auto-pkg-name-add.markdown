---
layout: post
title:  "Update Package Names in Batch"
date:   2013-08-04 21:04:20
categories: python
---

I was reading the [Algorithms][algorithms] these days, and chose to implement the algorithms by using VIM, not Eclipse. I must type every character in this way, which would help me understand those algorithms much better. It is highly possible that whiteboard is the only option for you in an interview. Practicing like this must be a good idea I think.

Getting rid of Eclipse is not that easy actually. I have to consider about a good structure for the whole project. It is really easy to choose since now maven is very popular. After reading the official tutorial for 5 minuites, the project is created. Maven is amazing, all the compiled class files go to `target/classes` directory. It feels so good that I can redirect the file again: 
	
	$ java com.example.input < data.txt

I really enjoy this kind of coding style, until I found every time I create a Java file, I need to specify the packge declaration. This is a boring and meaningless work, especially when I want to modify all the existed directories to categorize the Java files better. Thus, I wrote a Python script to update all my Java files' package declarations in batch.

{% highlight python %}
import os
import re

def getBasePath():
  current_path = os.path.abspath('.')
  base_path = os.path.basename(current_path)
  return base_path

def getSrcPath():
  current_path = os.path.abspath('.')
  src_path = os.path.join(current_path, 'src')
  return src_path

def visit(arg, dirname, names):
  for name in names:
    subname = os.path.join(dirname, name)
    if subname.endswith('.java'):
      #print subname
      m = arg.search(subname)
      #print m.groups()[0]
      pkg_name = 'package ' + m.groups()[0].replace('/', '.') + ';\n'

      modifyPkgName(subname, pkg_name)

def modifyPkgName(file_name, pkg_name):
  with open(file_name, 'r') as file:
    lines = file.readlines()

  if lines[0] == pkg_name: return

  with open(file_name, 'w') as file:
    if lines[0].startswith('package'):
      lines[0] = pkg_name
    else:
      file.write(pkg_name)
      file.write('\n')

    for line in lines:
      file.write(line)

pattern = re.compile(r'/java/(.+)/.+.java$')
os.path.walk(getSrcPath(), visit, pattern)
{% endhighlight %}

This snippet is stored under my project's home directory. I run it every time I want to change the Java files' package. It will find all the `.java` files under the `src` directory recursively, create the package name if there is no package declared in that file, or just simply change the packge name if the old one is not correct.

Please let me know if there are some maven plugin can also get the work done. I really appreciate it. Because I can check what that plugin is capable of, and add some awesome features to my Python script :)


[algorithms]: http://www.amazon.com/Algorithms-4th-Edition-Robert-Sedgewick/dp/032157351X/ref=sr_1_1?ie=UTF8&qid=1375665091&sr=8-1&keywords=algorithms
---
layout: post
title: JavaScript template engine in just 20 lines
description: 【JavaScript template engine in just 20 lines】
category: blog
---
<br />
I'm still working on my JavaScript based preprocessor - AbsurdJS. It started as a CSS preprocessor, but later it was expanded to CSS/HTML preprocessor. Shortly, it allows JavaScript to CSS/HTML conversion. Of course, because it generates HTML it was normal to act as a template engine. I.e. somehow to fill the markup with data.<br />
<br /><br />
So, I wanted to write a simple template engine logic, which work nicely with my current implementation. AbsurdJS is mainly distributed as a NodeJS module, but it is also ported for a client-side usage. Having this in mind, I knew that I can't really get some of the existing engines. That's because most of the them are only NodeJS based and it will be difficult to replicate them in the browser. I needed something small, written in pure JavaScript. I landed on this blog post by John Resig. It looks like the thing which I needed. I change it a bit and it fits into 20 lines. I think that it is quite interesting how the script works. In this article I'll recreate the engine step by step so you could see the wonderful idea which originally came from John.<br />
<br />
<br /><br />
Here is what we could have in the beginning:<br />
<br /><br />
var TemplateEngine = function(tpl, data) {<br />
    // magic here ...<br />
}<br />
var template = '<p>Hello, my name is <%name%>. I\'m <%age%> years old.</p>';<br />
console.log(TemplateEngine(template, {<br />
    name: "Krasimir",<br />
    age: 29<br />
}));<br />
<br /><br />
A simple function, which takes our template and a data object. As you may guess, the result which we want to achieve at the end is:<br />
<br /><br />
<p>Hello, my name is Krasimir. I'm 29 years old.</p><br />
The very first thing which we have to do is to take the dynamic blocks inside the template. Later we will replace them with the real data passed to the engine. I decided to use regular expression to achieve this. That's not my strongest part, so feel free to comment and suggest a better RegExp.<br />
<br /><br />
var re = /<%([^%>]+)?%>/g;<br />
We will catch all the pieces which start with <% and end with %>. The flag g (global) means that we will get not one, but all the matches. There are a lot of methods which accept regular expressions. However, what we need is an array containing the strings. That's what exec does.<br />
<br /><br />
var re = /<%([^%>]+)?%>/g;<br />
var match = re.exec(tpl);<br />
If we console.log the match variable we will get:<br />
<br /><br />
[<br />
    "<%name%>",<br />
    " name ", <br />
    index: 21,<br />
    input: <br />
    "<p>Hello, my name is <%name%>. I\'m <%age%> years old.</p>"<br />
]<br /><br />
So, we got the data, but as you can see the returned array has only one element. And we need to process all the matches. To do this we should wrap our logic into while loop.<br />
<br /><br />
var re = /<%([^%>]+)?%>/g, match;<br />
while(match = re.exec(tpl)) {<br />
    console.log(match);<br />
}<br /><br />
If you run the code above you will see that the both <%name%> and <%age%> are shown.<br />
<br /><br />
Now it gets interesting. We have to replace placeholders with the real data passed to the function. The most simple thing which we can use is to use .replace method against the template. We could write something like this:<br />
<br /><br />
var TemplateEngine = function(tpl, data) {<br />
    var re = /<%([^%>]+)?%>/g, match;<br />
    while(match = re.exec(tpl)) {<br />
        tpl = tpl.replace(match[0], data[match[1]])<br />
    }<br />
    return tpl;<br />
}<br />
<br /><br />
<br />
Ok, this works, but of course it is not enough. We have really simple object and it is easy to use data["property"]. But in practice we may have complex nested objects. Let's for example change our data to<br />
<br /><br />
{<br />
    name: "Krasimir Tsonev",<br />
    profile: { age: 29 }<br />
}<br /><br />
This doesn't work because when we type <%profile.age%> we will get data["profile.age"] which is actually undefined. So, we need something else. The .replace method will not work our case. The very best thing will be to put real JavaScript code between <% and %>. It will be nice if it is evaluated against the passed data. For example:<br />
var template = '<p>Hello, my name is <%this.name%>. I\'m <%this.profile.age%> years old.</p>';<br />
How is this possible? John used the new Function syntax. I.e. creating a function from strings. Let's see a simple example.<br />
<br /><br />
var fn = new Function("arg", "console.log(arg + 1);");<br />
fn(2); // outputs 3<br />
fn is a real function which takes one argument. It's body is console.log(arg + 1);. In other words the above code is equal to:<br />
<br /><br />
var fn = function(arg) {<br />
    console.log(arg + 1);<br />
}<br />
fn(2); // outputs 3<br /><br />
We are able to define a function, its arguments and its body from simple strings. That's exactly what we need. But before to create such a function we need to construct its body. The method should return the final compiled template. Let's get the string used so far and try to imagine how it will look like.<br />
<br /><br />
return <br />
"<p>Hello, my name is " + <br />
this.name + <br />
". I\'m " + <br />
this.profile.age + <br />
" years old.</p>";<br /><br />
For sure, we will split the template into text and meaningful JavaScript. As you can see above we may use a simple concatenation and produce the wanted result. However, this approach doesn't align on 100% with our needs. Because we are passing working JavaScript sooner or later we will want to make a loop. For example:<br />
<br /><br />
var template = <br />
'My skills:' + <br />
'<%for(var index in this.skills) {%>' + <br />
'<a href=""><%this.skills[index]%></a>' +<br />
'<%}%>';<br /><br />
If we use concatenation the result will be:<br />
<br /><br />
return<br />
'My skills:' + <br />
for(var index in this.skills) { +<br />
'<a href="">' + <br />
this.skills[index] +<br />
'</a>' +<br />
}<br /><br />
Of course this will produce an error. That's why I decided to follow the logic used in the John's article. I.e. put all the strings in an array and join its elements at the end.<br />
<br /><br />
var r = [];<br />
r.push('My skills:'); <br />
for(var index in this.skills) {<br />
r.push('<a href="">');<br />
r.push(this.skills[index]);<br />
r.push('</a>');<br />
}<br />
return r.join('');<br /><br />
The next logical step is to collect the different lines of for custom generated function. We already have some information extracted from the template. We know the content of the placeholders and their position. So, by using a helper variable (cursor) we are able to produce the desire result.<br />
<br /><br />
var TemplateEngine = function(tpl, data) {<br />
    var re = /<%([^%>]+)?%>/g,<br />
        code = 'var r=[];\n',<br />
        cursor = 0, match;<br />
    var add = function(line) {<br />
        code += 'r.push("' + line.replace(/"/g, '\\"') + '");\n';<br />
    }<br />
    while(match = re.exec(tpl)) {<br />
        add(tpl.slice(cursor, match.index));<br />
        add(match[1]);<br />
        cursor = match.index + match[0].length;<br />
    }<br />
    add(tpl.substr(cursor, tpl.length - cursor));<br />
    code += 'return r.join("");'; // <-- return the result<br />
    console.log(code);<br />
    return tpl;<br />
}<br /><br />
var template = '<p>Hello, my name is <%this.name%>. I\'m <%this.profile.age%> years old.</p>';<br />
console.log(TemplateEngine(template, {<br />
    name: "Krasimir Tsonev",<br />
    profile: { age: 29 }<br />
}));<br /><br />
The code variable holds the body of the function. It starts with definition of the array. As I said, cursor shows us where in the template we are. We need such a variable to go through the whole string and skip the data blocks. An additional add function is created. It's job is to append lines to the code variable. And here is something tricky. We need to escape the double quotes, because otherwise the generated script will not be valid. If we run that example and check the console we will see:<br />
<br /><br />
var r=[];<br />
r.push("<p>Hello, my name is ");<br />
r.push("this.name");<br />
r.push(". I'm ");<br />
r.push("this.profile.age");<br />
return r.join("");<br />
Hm ... not what we wanted. this.name and this.profile.age should not be quoted. A little improvement of the add method solves the problem.<br />
<br /><br />
var add = function(line, js) {<br />
    js? code += 'r.push(' + line + ');\n' :<br />
        code += 'r.push("' + line.replace(/"/g, '\\"') + '");\n';<br />
}<br />
var match;<br />
while(match = re.exec(tpl)) {<br />
    add(tpl.slice(cursor, match.index));<br />
    add(match[1], true); // <-- say that this is actually valid js<br />
    cursor = match.index + match[0].length;<br />
}<br /><br />
The placeholders' content is passed along with a boolean variable. Now this generates the correct body.<br />
<br /><br />
var r=[];<br />
r.push("<p>Hello, my name is ");<br />
r.push(this.name);<br />
r.push(". I'm ");<br />
r.push(this.profile.age);<br />
return r.join("");<br />
All we need to do is to create the function and execute it. At the end of our template engine, instead of returning tpl:<br />
<br /><br />
return new Function(code.replace(/[\r\t\n]/g, '')).apply(data);<br />
We don't even need to send any arguments to the function. We use the apply method to call it. It automatically sets the scope. That's the reason of having this.name working. The this actually points to our data.<br />
<br /><br />
We are almost done. One last thing. We need to support more complex operations, like if/else statements and loops. Let's get the same example from above and try the code so far.<br />
<br /><br />
var template = <br />
'My skills:' + <br />
'<%for(var index in this.skills) {%>' + <br />
'<a href="#"><%this.skills[index]%></a>' +<br />
'<%}%>';<br />
console.log(TemplateEngine(template, {<br />
    skills: ["js", "html", "css"]<br />
}));<br />
The result is an error Uncaught SyntaxError: Unexpected token for. If we debug a bit and print out the code variable we will see the problem.<br />
<br /><br />
var r=[];<br />
r.push("My skills:");<br />
r.push(for(var index in this.skills) {);<br />
r.push("<a href=\"\">");<br />
r.push(this.skills[index]);<br />
r.push("</a>");<br />
r.push(});<br />
r.push("");<br />
return r.join("");<br />
The line containing the for loop should not be pushed to the array. It should be just placed inside the script. To achieve that we have to make one more check before to attach something to code.<br />
<br /><br />
var re = /<%([^%>]+)?%>/g,<br />
    reExp = /(^( )?(if|for|else|switch|case|break|{|}))(.*)?/g,<br />
    code = 'var r=[];\n',<br />
    cursor = 0;<br />
var add = function(line, js) {<br />
    js? code += line.match(reExp) ? line + '\n' : 'r.push(' + line + ');\n' :<br />
        code += 'r.push("' + line.replace(/"/g, '\\"') + '");\n';<br />
}<br />
<br /><br />
A new regular expression is added. It tells us if the javascript code starts with if, for, else, switch, case, break, { or }. If yes, then it simply adds the line. Otherwise it wraps it in a push statement. The result is:<br />
<br /><br />
var r=[];<br />
r.push("My skills:");<br />
for(var index in this.skills) {<br />
r.push("<a href=\"#\">");<br />
r.push(this.skills[index]);<br />
r.push("</a>");<br />
}<br />
r.push("");<br />
return r.join("");<br />
And of course, everything is properly compiled.<br />
<br /><br />
My skills:<a href="#">js</a><a href="#">html</a><a href="#">css</a><br />
The latest fix gives us a lot of power actually. We may apply complex logic directly into the template. For example:<br />
<br /><br />
var template = <br />
'My skills:' + <br />
'<%if(this.showSkills) {%>' +<br />
    '<%for(var index in this.skills) {%>' + <br />
    '<a href="#"><%this.skills[index]%></a>' +<br />
    '<%}%>' +<br />
'<%} else {%>' +<br />
    '<p>none</p>' +<br />
'<%}%>';<br />
console.log(TemplateEngine(template, {<br />
    skills: ["js", "html", "css"],<br />
    showSkills: true<br />
}));<br />
To improve the things a bit I added a few minor optimizations and the final version looks like that:<br />
<br /><br />
var TemplateEngine = function(html, options) {<br />
    var re = /<%([^%>]+)?%>/g, reExp = /(^( )?(if|for|else|switch|case|break|{|}))(.*)?/g, code = 'var r=[];\n', cursor = 0, match;<br />
    var add = function(line, js) {<br />
        js? (code += line.match(reExp) ? line + '\n' : 'r.push(' + line + ');\n') :<br />
            (code += line != '' ? 'r.push("' + line.replace(/"/g, '\\"') + '");\n' : '');<br />
        return add;<br />
    }<br />
    while(match = re.exec(html)) {<br />
        add(html.slice(cursor, match.index))(match[1], true);<br />
        cursor = match.index + match[0].length;<br />
    }<br />
    add(html.substr(cursor, html.length - cursor));<br />
    code += 'return r.join("");';<br />
    return new Function(code.replace(/[\r\t\n]/g, '')).apply(options);<br />
}<br />
<br /><br />
It's even less - 15 lines.<br />

---
layout: post
title:  ".md ?? .markdown ?? คืออะไรกันนะ"
categories: [Markdown]
tags: [Tutorial, md]
description: "Markdown ? "
featured: true
hidden: true
---

เนื่องจากบทความใน Blog ของเราใช้ Jekyll ดังนั้นเราก็ต้องมาทำความรู้จักกับเจ้า [Markdown][markdown-docs] กันก่อน เพราะต้องใช้ความสามารถของ Markdown ในการเขียนบทความนั้นเอง
เพื่อน ๆ ที่ใช้ Git กันคงต้องเคยเห็นเจ้าไฟล์ที่ชื่อ README.md ผ่านตากันบ้างมาแล้ว ถ้าสังเกตุดี ๆ นามสกุลของมันคือ .md นั้นแหละฮะ มันก็คือเจ้า Markdown เจ้าภาพของเราในบทความนี้

Markdown เป็น text-to-HTML ก็คือมันจะทำการแปลง Syntax ของ Markdown ไปเป็นรูปแบบ HTML ให้เรานั้นเอง อ่าวแล้วทำไมไม่เขียน HTML ขึ้นมาตรง ๆ เลยละ 
ก็เพราะว่าเจ้า Markdown เป็นรูปแบบที่เข้าใจได้ง่ายต่อการอ่านและเขียน นั้นเอง

มาดูตัวอย่างการใช้งานคร่าว ๆ กัน

    {% highlight markdown %}
    
    A First Level Header
    ====================

    A Second Level Header
    ---------------------

    Now is the time for all good men to come to the aid of their country. This is just a regular paragraph.

    The quick brown fox jumped over the lazy dog's back.

    ### Header 3

    > This is a blockquote.
    > 
    > This is the second paragraph in the blockquote.
    >
    > ## This is an H2 in a blockquote
    {% endhighlight %}

ผลลัพธ์เมื่อทำการแปลงเป็น HTML

    {% highlight html %}
   
    <h1>A First Level Header</h1>

    <h2>A Second Level Header</h2>

    <p>Now is the time for all good men to come tothe aid of their country. This is just a regular paragraph.</p>

    <p>The quick brown fox jumped over the lazy dog's back.</p>

    <h3>Header 3</h3>

    <blockquote>
        <p>This is a blockquote.</p>

        <p>This is the second paragraph in the blockquote.</p>

        <h2>This is an H2 in a blockquote</h2>
    </blockquote>
    {% endhighlight %}


<br/>
### เอาละมาดูการใช้งานเบื่องต้นกันเถอะ

---

**การเน้นข้อความ**  ใช้เครื่องหมาย _ หรือ * ครอบข้อความที่ต้องการเน้น

    {% highlight markdown %}
    
    Some of these words *are emphasized*.
    Some of these words _are emphasized also_.

    Use two asterisks for **strong emphasis**.
    Or, if you prefer, __use two underscores instead__.
    {% endhighlight %}

Some of these words *are emphasized*.
Some of these words _are emphasized also_.

Use two asterisks for **strong emphasis**.
Or, if you prefer, __use two underscores instead__.

---

**ลิสรายการ (List)** ใช้เครื่องหมาย *, +, หรือ - แต่ถ้าต้องการตัวเลขเรียงลำดับให้ใช้ตัวเลขตามด้วยจุด เช่น 1. หรือ 2.

    {% highlight markdown %}
    
    *   Candy.
    *   Gum.
    *   Booze.
    
    +   Candy.
    +   Gum.
    +   Booze.
    
    -   Candy.
    -   Gum.
    -   Booze.
    
    1.  Red
    2.  Green
    3.  Blue
    {% endhighlight %}

*   Candy.
*   Gum.
*   Booze.

+   Candy.
+   Gum.
+   Booze.

-   Candy.
-   Gum.
-   Booze.

1.  Red
2.  Green
3.  Blue

---

**การสร้างลิงค์ (Link)** ใช้ [] ครอบข้อความ และใช้ () ครอบลิงค์
    {% highlight markdown %}
    
    This is an [example link](https://doopdip.me/).
    {% endhighlight %}

This is an [example link](https://doopdip.me/).

---

**แสดงรูปภาพ (Image)** คล้าย ๆ เหมือนการสร้างลิงค์คือใช้ [] ครอบชื่อรูป และใช้ () ครอบ path ของรูป ที่เพิ่มขึ้นมาก็คือต้องใส่ ! ไว้หน้าสุด
    {% highlight markdown %}
     
    ![My cat](/assets/images/posts/what-is-markdown/cat.jpg)
    {% endhighlight %}

![My cat](/assets/images/posts/what-is-markdown/cat.jpg)

---

**แทรก code ระหว่างข้อความ (Code)** โดยใช้ `` ครอบข้อความ
    {% highlight markdown %}
     
    I strongly recommend against using any `<blink>` tags.
    
    I wish SmartyPants used named entities like `&mdash;`
    instead of decimal-encoded entites like `&#8212;`.
    {% endhighlight %}

I strongly recommend against using any `<blink>` tags.

I wish SmartyPants used named entities like `&mdash;`
instead of decimal-encoded entites like `&#8212;`.

---
<br/>

เป็นไงกันบ้างงงง ง่ายใช่ไหมล่า ถ้าอยากศึกษาเพิ่มเติม ศึกษาได้จาก [ลิงค์นี้][markdown-docs] ได้เลยมีอะไรให้เล่นอีกเพียบ
<br/>
<br/>
#### แหล่งอ้างอิง
* [Daring fireball][markdown-docs]

[markdown-docs]: https://daringfireball.net/projects/markdown

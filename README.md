*Note: This article is not about methodologies like [BEM](http://getbem.com/), [OOCSS](http://oocss.org/), [SMACSS](https://smacss.com/) etc. This is by all means no be-all and en-all of naming, just a way to think about structuring naming and how this could work.*

#TLDR;

Write names based on its purpose, so everyone can understand when viewing the code. Purpose usually doesn’t change, styling does. Longer class names can define the purpose better. Using hierarchy in naming help the GZIP/deflate compressor work better for a smaller document size, so no need to shrink them.

#Introduction

Having a readable code base is essential. They ensure that everyone can understand it, or when revisiting your project later, it still makes sense. To keep code readable, one of the things to do is name things properly. [But naming is one of the hardest things in software development.](https://martinfowler.com/bliki/TwoHardThings.html)

#Purpose intended naming

From experience, writing expressive function names, variables, file names or CSS class names to its purpose can take up quite some characters. Often, developers want to write short names. But is writing long names so bad? I don’t believe it is, and here is why.

**A brief history;** Where we came from, the project was built upon having class names based on styling (not to be mistaken for utility classes). The class names look something like this: *“rounded_corners”* or *“gray_borderbox”*. As software changes, so often does styling. Because the rounded_corners class name was used all over the place, it was hard to change everything when it had to be styled differently. We wanted to move to sharp borders instead of rounded corners on mobile.

This is how it ended up:

```$xslt
.rounded_corners {
   border-radius: 0;
}
@media only screen and (min-width: 321px) {
   .rounded_corners {
      border-radius: 5px;
   }
}

```
This is obviously not how the class was expected to work when you read its name. It was too hard to use this approach when styling is changing often.

##How should we name instead?

We decided to approach the styling from the perspective of design. Design style guides also often describe the styling on its purpose. We chose to follow that approach. So instead of “rounded_corners”, we called it: “notification-box”. If we want to change how a notification looks, it is now a lot easier to read, the naming can be identified for every colleague out of the development department (like branding, online marketing etc). If you have a style guide, you can most likely implement it directly. The basic structure on how to name things can be defined as the following method.

`A set of css and html that can stand by itself; containing the bare minimum to be able to represent its function and semantics.
`

With something like articles, it becomes a bit larger. It has substyling that may or may not be reused.
Example:

```$xslt
<section class="user-article">
  <h1 class="user-article--header">
     My amazing headline
  </h1>
  <p class="user-article--credit-line">
    <span class="user-article--credit-line--author">Jordy</span>
    <time class="user-article--credit-line--published-on"
          datetime="2018-06-0T19:00"
    >
       published: 2 weeks ago
    </time>
  </p>
  <p class="user-article--introduction">
     Some introduction about this subject
  </p>
  <blockquote class="user-article--quote">
     Some famous quote
  </blockquote>
  <p class="user-article--content">
     A large text that is displayed in three columns.
  </p>
</section>

```

The above code example shows the intention of each element within the scope of main purpose of the code; an article. The single dashes represent a space between words. A double dash represents the separation of its parent.

#Hierarchy & GZIP/deflate

As you see in the above examples, writing class names this way makes you repeat yourself quite a lot. Because of the hierarchy in the naming, you in general not need to nest anything and can still keep the style sheet readable.
A style sheet for the previous example could look something like this.

```$xslt
/* This describes the gist of the style */
.user-article {
   padding: 10px;
}
/* The double-dash describes the second level*/
.user-article--credit-line {
   margin-top: 20px;
   color: #999;
   font-size: 11px;
}
/* In this case, a separation of another set of dashes can separate the third level */
.user-article--credit-line--author {
   display: inline-block;
   font-weight: 100;
}

```

Repetition of code in the output in a HTML-document is not a bad thing, because a web server with GZIP/deflate can compress this really well.

##So what is GZIP/deflate actually?

GZIP is based on the powerful deflate algorithm, which is a combination of the *LZ77* and *Huffman* coding. What helps us reduce our file size here is mostly due to the *LZ77* compression. It groups symbols together. This is perfect, since the larger amount of grouped symbols we have, the better the compression algorithm works. [You can learn more about LZ77 compression, in this video of Colt McAnlis here.](https://www.youtube.com/watch?v=Jqc418tQDkg)


#Time for a benchmark

GZIP/deflate will compress a bit like following:

```$xslt
We now have the following class names to compress (these names are also used for the benchmark data)
- user-article
- user-article--credit-line
- user-article--credit-line--author
Example 1: Let's tag the names
user-article = [1]
-- = [2]
[1][2]credit-line = [3]
[1][3][2]author
So as you can see, the author now exists of 3 tags. The author itself is unrepeated, so remains uncompressed.
Example 2: Now when you are adding another class, it can look like this
user-article = [1]
-- = [2]
[1][2]credit-line = [3]
[1][2]credit-line[2] = [4]
[4]publish-date = publish-date
[4]author = author

```
*Note: this is pseudo code to demonstrate how roughly works, just to keep it short.*

The compression ratio of `example 1` is 157%. Resulting from 73 bytes to 47 bytes. In `example 2`, the compression ratio is even higher with 187%, compressing from 112 to 60 bytes.

So we learn that in order to achieve a high compression ratio, you need to repeat as long names as you can. Being concise is key.

#“Can’t you just use a minifier?” or “Write shorter class names?”

As in the examples before, referencing unique classnames like *“fxd12–34”* is harder for the compressor. Working on larger documents, the benefit quickly fades and you actually end up with a larger document size.

The same counts for a CSS selector minifier. Yes, they are shorter, and when using more of the same types, you might end up with a slightly smaller amount initially, but you need to reference the minified css selectors also in your HTML-document. That can result in a more complex workflow, while all browsers support GZIP/deflate already. That is a choice you need to make for yourself.


#So do you need to do anything else than writing hierarchical correct CSS class names?

Yes, you rely on GZIP/deflate compression, that usually needs to be configured on the web server. In most cases, it is already on.

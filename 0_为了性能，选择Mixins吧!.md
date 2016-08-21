/**
  * author : Harry Roberts,
  * url    : http://csswizardry.com/2016/02/mixins-better-for-performance/,
  * 
  * tanslator : AllenYang,
  * contact   : { 
  *     github : github.com/allenyangfly,
  *      email : allenyang1995@outlook.com,
  *        url : allenyang.cn,
  *      phone : 15145093565,
  *         QQ : 827883530
  * } 
  */


## Mixins Better for Performance
## 为了性能，选择Mixins吧！

<<<<<<<<
When it comes to preprocessors, one of the most frequent questions I’m asked is "Mixins or @extend"? I’ve always been quite vocal about this topic, and I firmly believe you should avoid @extend for a number of reasons:

当我们提及预编译的时候，我经常会被问到的一个问题是 " Mixins 还是 @extend "? 关于这个话题我经常直言不讳，而且鉴于以下的这几条原因，我坚定的认为你应该避免使用@extend :
->>>>>>


<<<<<<<<
1. It alters your source order, which is always risky in CSS.
2. It creates awkward groupings in your code, putting unrelated selectors together.
3. It is very greedy, @extending every instance of a given subject, not just the one you actually wanted..
4. It can get really out of control, really fast.

1. @extend 会改变了你的源命令，这在CSS中是相当危险的。
2. @extend 会破坏代码结构合理性，把不相关的选择器串联到一起。
3. @extend 是非常贪婪的，它会包含一切的代码，而不仅仅是你想要的那一个。
4. @extend 可能会很快就脱离我们控制。
->>>>>>>


<<<<<<<
@extend is now widely considered an anti-pattern, so its usage is thankfully fading out, but we’re not quite there yet.

I was workshopping with a client yesterday and was asked about the mixin vs. @extend situation, to which I gave my usual answer of Don’t use @extend, ever!, and in return I was asked But isn’t @extend better for performance? It generates less code.

It is true that @extend (when used correctly) will produce less CSS, but my answer was a firm no: mixins are better for performance.


@extend 现在被广泛的认为是一种反面模式, 所以它的使用慢慢淡出了人们的视野，但是我们还没有完全弃用它。

我昨天和一个客户聊天的的时候被问到mixin和@extend哪个更好，我告诉他了我通常会使用的答案：”永远不要使用@extend “，但却被反问了到：“@extend 不是性能更好吗？它生成了更少的代码”

认为使用@extend (当被正确的使用)生成更少的CSS是正确的，但是对于性能而言我的回答是错：mixins的性能更好。
->>>>>>>


<<<<<<<<
I answered the question with quite some confidence, despite having never actually done any tests. The reason for my confidence was a pretty solid theory that I had:

Because gzip favours repetition, surely we’ll get a better compression ratio if we share the exact same declarations, say, 1000 times, than if we shared 1000 unique classes twice.

Y’see, when people talk about the performance of mixins, they’re usually thinking about filesize on the filesystem. But because we have gzip enabled (you do have gzip enabled, right?), we should be thinking about filesize over the network.

My thinking was that once we’ve gzipped our CSS, files with greater repetition of identical strings will end up being smaller than files whose repetition is less frequent, regardless of the size of the files on the filesystem. I posited that a bigger file would end up smaller after gzip if that extra filesize was comprised of repeated string.

I got back to my hotel room and decided to put my theory to the test.


我相当有自信的回答了这个的问题，尽管我从没做过任何的测试。我很有自信的原因是我有一套独门秘笈：

由于gzip是有利于重复代码压缩的，很显然如果我们能明确的告知压缩程序我们重复的声明，我们就能到一个更好的压缩比例。

你看，当人们谈论 mixins 的性能的时候，他们通常考虑的是文件在服务器上的大小。但是由于有gzip的存在（你也使用gzip的对吧？），我们应该思考网络传输过程中的文件大小。

我的想法是一旦我们使用gzip压缩我们的CSS，包含重复字符串次数较多的文件会变得比重复字符串次数小的文件更小，所以不要考虑文件在服务器的文件系统中的大小。所以我假设如果一个文件包含重复的字符串，经过gzip压缩会变得更小。

我决定回到我的酒店把我的理论验证一下。
->>>>>>>>>



## The Experiment
## 实验

<<<<<
Here’s what I did.

1. I created two CSS files.
2. Each file had 1000 unique classes, generated using Sass


```
@for $i from 1 through 1000 {
  .#{unique-id()}-#{$i} {
     ...
  }
}
```

3. I gave each class a unique declaration, simply reusing the same random string that formed the name itself by using the parent selector, and I put some nonsense strings either side of that:

```
@for $i from 1 through 1000 {
  .#{unique-id()}-#{$i} {
    content: "ibf#{&}jaslbw";
  }
}
```

4. I then chose three simple declarations that would remain the same across all 1000 classes:

```
color: red;
font-weight: bold;
line-height: 2;
```

5. In one file, I shared these declarations via a mixin:

```
@mixin foo {
  color: red;
  font-weight: bold;
  line-height: 2;
}
   
.#{unique-id()}-#{$i} {
  @include foo;
  content: "ibf#{&}jaslbw";
}
```

6. And in the other I shared them via @extend:

```
%foo {
  color: red;
  font-weight: bold;
  line-height: 2;
}
   
.#{unique-id()}-#{$i} {
  @extend %foo;
  content: "ibf#{&}jaslbw";
}
```



这就是我实验的：

1. 我新建了两个CSS文件
2. 每个文件文件有1000个用SASS生成的独一无二的类

```
@for $i from 1 through 1000 {
  .#{unique-id()}-#{$i} {
     ...
  }
}
```

3. 我给每个类声明了独一无二的内容，通过重复使用父级的选择器生成每项自身的类名，我还在里面生成了一些没有意义的字符串:

```
@for $i from 1 through 1000 {
  .#{unique-id()}-#{$i} {
    content: "ibf#{&}jaslbw";
  }
}
```

4. 我选择了三个简单的声明应用于所有的个类:

```
color: red;
font-weight: bold;
line-height: 2;
```

5. 在一个文件中，我使用minxin来进行声明:
```
@mixin foo {
  color: red;
  font-weight: bold;
  line-height: 2;
}
   
.#{unique-id()}-#{$i} {
  @include foo;
  content: "ibf#{&}jaslbw";
}
```

6. 在另一个文件中，我使用@extend来进行声明:
```
%foo {
  color: red;
  font-weight: bold;
  line-height: 2;
}
   
.#{unique-id()}-#{$i} {
  @extend %foo;
  content: "ibf#{&}jaslbw";
}
```
所有的例子都可以在[github](https://github.com/csswizardry/extend-vs-mixin)上看到
->>>>



<<<<<
This left me with two files made up of totally unique classes and 1000 unique declarations, and with three identical declarations shared in two different ways.

The filesizes of these should not surprise you in the slightest:

* mixin.css came in at 108K.
* extend.css came in at 72K.
* This gives a difference in filesize of 36K.
* Using mixins was 150% larger than using @extend.

This is exactly what I expected—mixins do produce more CSS than @extend does.


在这个测试中，这两个文件分别用两种方式各自生成了1000个独一无二的声明和三个相同的属性语句。

他们的大小可能你并不吃惊:

* mixin.css 文件的大小 108K.
* extend.css 文件的大小是 72K.
* 两个文件之间相差了 36K.
* 使用mixins比使用@extend大了 150%.

这恰好就是我所预料的那样 — mixins 会比@extend 产生更多的CSS代码。
->>>>



<<<<<
But! We have to remember that we should not be worried about filesize on the filesystem—we only care about the sizes of our gzipped files.

I minified and gzipped the two files and got the results I expected:

* mixin.css came in at 12K.
* extend.css came in at 18K.
* This gives a difference in filesize of 6K.
* Using mixins was 33.333% smaller than using @extend.

Amazing! We’ve gone from mixins being 1.5x larger than using @extend, to mixins being 0.3x smaller than using @extend. My theory seems correct!


但是！我们必须瑾记我们不用担心文件在服务器文件系统中大小 - 我们仅仅需要关心gzip压缩后的文件大小。

* mixin.css 经过gzip后得到的大小是 12K.
* extend.css 经过gzip后得到的大小是 18K.
* 两个文件之间相差了 6K.
* 使用mixins比使用@extend小了 33.333%.

太令人吃惊了! 在我们经过SASS编译得到的文件中，使用 mixins 的文件比使用 @extend 的文件大了150%，但是经过gzip压缩后，使用 mixins 的文件反而比使用 @extend 的文件小了 33.333% 。我的理论是正确的！
->>>>



## The Experiment
## 实验

<<<<<
I do feel that the test files were pretty fair—creating unique strings for class names was designed to hinder compression, so that we could more accurately test the effects of gzip on our actual subject: the shared declarations.

That said, the test files were pretty unrealistic, so I decided to make things a little more reasonable.

I grabbed the compiled CSS from an existing project, made two copies of it, and I @imported each of my test files into each project respectively. This meant that my test files were surrounded by some 1794 lines of actual, realistic CSS.

我个人觉得上面的测试非常的公正 - 创建独一无二的类名是为了阻碍压缩，所以我们可以很精确的测试gzip在我们现实项目中的作用：压缩共享的声明。

但有人说上面的测试文件非常的不贴近现实，所以我决定直接拿一个已有的项目测试。

我用了一个现有项目当中的CSS文件，并且把CSS文件做了一份拷贝，然后我分别用@import导入我的两个测试文件到各自的项目中。这意味着我的测试声明被 1794 行现有项目的中的代码包围着。
->>>>>



<<<<<<
I compiled each new test file and these were the results:

* mixin.css came in at 16K.
* extend.css came in at 22K.
* This gives a difference in filesize of 6K.
* Using mixins was 27% smaller than using @extend.

The absolute numbers seem trivial (a mere 6K), but in relative terms, we can achieve a 27% saving over the wire simply by opting to use mixins to repeat declarations over and over, as opposed to using @extend to repeat a handful of selectors.


我编译并且压缩了两个测试的文件，这是他们的结果:

* mixin.css 的结果是 16K.
* extend.css 的结果是 22K.
* 两个文件之间相差了 6K.
* 使用mixins比使用@extend小了 27%.

绝对意义上来考虑结果似乎相差不大（仅仅6K）,但是相对而言，我们仅通过选择使用mixins来声明重复的代码就能节省27%的流量!
->>>>>



## Mixins Are Better for Performance
## Mixins 的性能更好

<<<<<<
My tests showed pretty conclusively that mixins end up being better for network performance than using @extend. The way gzip works means that we get better savings even when our uncompresssed files are substantially larger.

This means that the performance argument for @extend is non-existent. As well as being bad for your CSS, @extend is bad for performance. Please stop using it.



我的测试得出了一个非常可靠的结论：minxins在网络传输中比 @extend 拥有更好的性能.尽管有些文件未压缩时更大，但使用gzip压缩后，依然可以保证我们拥有更好的性能。

关于 @extend 的性能之争从此不复存在，@extend 除了会对你的CSS结构产生破坏之外同样在性能上也同样落后，所以请停止使用它，一起拥抱mixins吧!
->>>>>




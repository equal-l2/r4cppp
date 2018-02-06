<!--
# Rust For Systems Programmers
-->
# システムプログラマのためのRust
(訳注:[nrc/r4cpp](https://github.com/r4cppp)の日本語訳です。md33f226を元にして
翻訳しています。専門用語の訳は[TRPR-ja](https://github.com/rust-lang-ja/the-rust-programming-language-ja/blob/master/TranslationTable.md)
に従います)

<!--
A Rust tutorial for experienced C and C++ programmers.
-->
C/C++に熟練したプログラマのためのRustチュートリアルです。

<!--
Jump to [contents](#contents).
Jump to [contributing](#contributing).
-->
[目次](#目次)にジャンプ。
[貢献](#貢献)にジャンプ。

<!--
This tutorial is intended for programmers who already know how pointers and
references work and are used to systems programming concepts such as integer
widths and memory management. We intend to cover, primarily, the differences
between Rust and C++ to get you writing Rust programs quickly without lots of
fluff you probably already know.
-->
このチュートリアルは、ポインタや参照がどのように機能するか、またそれらがどの
ようにシステムプログラミングにおける概念(整数の大きさ、メモリ管理など)に関わる
のかを理解しているプログラマが対象です。Rustプログラムをすぐに書けるようにする
ため、主にRustとC++の差異について扱うこととし、すでに知っているであろうことは
取り扱いません。

<!--
Hopefully, Rust is a pretty intuitive language for C++ programmers. Most of the
syntax is pretty similar. The big difference (in my experience) is that the
sometimes vague concepts of good systems programming are strictly enforced by
the compiler. This can be infuriating at first - there are things you want to
do, but the compiler won't let you (at least in safe code), and sometimes these
things *are* safe, but you can't convince the compiler of that. However, you'll
quickly develop a good intuition for what is allowed. Communicating your own
notions of memory safety to the compiler requires some new and sometimes
complicated type annotations. But if you have a strong idea of lifetimes for
your objects and experience with generic programming, they shouldn't be too
tough to learn.
-->
RustとC++の文法はかなりの部分で共通しているので、おそらくRustはC++プログラマに
とってはかなり分かりやすい言語でしょう。私の経験した大きな違いの一つは、
コンパイラが「良いシステムプログラミング」の（ときに漠然とした）概念を強制する
ことです。これは最初のうちは腹立たしいものです。やりたいことをコンパイラが
（少なくとも「安全」なコード中では）やらせてくれないのです。*実際には*安全だっ
たとしても、コンパイラを説得することはできません。しかし、すぐに何が許される
のか直感的に分かるようになります。あなたの考えるメモリ安全性をコンパイラに
伝えるには、新たな（ときに複雑な）型注釈が必要になります。しかし、オブジェクト
のライフタイムをきちんと理解し、ジェネリックプログラミングを経験すれば、
それほど難しいものではありません。

<!--
This tutorial started as a [series of blog posts](http://featherweightmusings.blogspot.co.nz/search/label/rust-for-c).
Partly as an aid for me (@nrc) learning Rust (there is no better way to
check that you have learnt something than to try and explain it to somebody
else) and partly because I found the existing resources for learning Rust
unsatisfactory - they spent too much time on the basics that I already knew and
used higher level intuitions to describe concepts that could better be explained
to me using lower level intuitions. Since then, the documentation for Rust has
got *much* better, but I still think that existing C++ programmers are an
audience who are a natural target for Rust, but are not particularly well
catered for.
-->
このチュートリアルは[一連のブログ記事](
http://featherweightmusings.blogspot.co.nz/search/label/rust-for-c)から始まり
ました。これを書いたのは、私（@nrc）がRustを勉強するため（何かを習得したことを
確かめるには、それを人に説明してみるのが一番です）であり、また現存するRustの
資料に満足できなかったからでもあります。それらは私がもう知っているような基本に
時間を掛けすぎていましたし、
<!--
and
used higher level intuitions to describe concepts that could better be explained
to me using lower level intuitions.
-->
その時と比べると、Rustのドキュメントは*かなり*良くなりましたが、
<!--
but I still think that existing C++ programmers are an
audience who are a natural target for Rust, but are not particularly well
catered for.
-->


<!--
## Contents
-->
## 目次

<!--
1. [Introduction - Hello world!](hello%20world.md)
1. [Control flow](control%20flow.md)
1. [Primitive types and operators](primitives.md)
1. [Unique pointers](unique.md)
1. [Borrowed pointers](borrowed.md)
1. [Rc and raw pointers](rc%20raw.md)
1. [Data types](data%20types.md)
1. [Destructuring pt 1](destructuring.md)
1. [Destructuring pt 2](destructuring%202.md)
1. [Arrays and vecs](arrays.md)
1. [Graphs and arena allocation](graphs/README.md)
1. [Closures and first-class functions](closures.md)
-->
1. [はじめに - Hello world!](hello%20world.md)
1. [制御構文](control%20flow.md)
1. [プリミティブ型と演算子](primitives.md)
1. [ユニークポインタ](unique.md)
1. [借用されたポインタ](borrowed.md)
1. [Rcと生ポインタ](rc%20raw.md)
1. [データ型](data%20types.md)
1. [分配　その1](destructuring.md)
1. [分配　その2](destructuring%202.md)
1. [配列とvec](arrays.md)
1. [グラフとアリーナ・アロケーション](graphs/README.md)


<!--
## Other resources
-->
## 他の資料

* [The Rust book/guide](http://doc.rust-lang.org/book/) - the best place for
  learning Rust in general and probably the best place to go for a second opinion
  on stuff here or for stuff not covered.
* [Rust API documentation](http://doc.rust-lang.org/std/index.html) - detailed
  documentation for the Rust libraries.
* [The Rust reference manual](https://doc.rust-lang.org/reference/) - a little
  out of date in places, but thorough; good for looking up details.
* [Discuss forum](http://users.rust-lang.org/) - general forum for discussion or
  questions about using and learning Rust.
* [#rust irc channel](https://chat.mibbit.com/?server=irc.mozilla.org&channel=%23rust) - probably
  the best place to get quick answers to your Rust questions if you like irc.
* [StackOverflow Rust questions](https://stackoverflow.com/questions/tagged/rust) - answers
  to many beginner and advanced questions about Rust, but be careful though - Rust
  has changed *a lot* over the years and some of the answers might be very out of date.


<!--
## Contributing
-->
## 貢献
(訳注：日本語翻訳に関する貢献以外はフォーク元にお願いします。以下のリンクは
フォーク元のIssueやPRへのリンクです）

<!--
Yes please!
-->
ぜひお願いします！

<!--
If you spot a typo or mistake, please submit a PR, don't be shy! Please feel
free to file [an issue](https://github.com/nrc/r4cppp/issues/new) for
larger changes or for new chapters you'd like to see. I'd also be happy to see
re-organisation of existing work or expanded examples, if you feel the tutorial
could be improved in those ways.
-->
誤字や誤りを見つけたら、PRを作成してください。大きな変更や新たな章の要望に
ついては[Issue](https://github.com/nrc/r4cppp/issues/new)を作成してください。
また、このチュートリアルをより良くするような構成の見直しや例の拡充も歓迎します。

<!--
If you'd like to contribute a paragraph, section, or chapter please do! If you
want ideas for things to cover, see the [list of issues](https://github.com/nrc/r4cppp/issues),
in particular those tagged [new material](https://github.com/nrc/r4cppp/labels/new%20material).
If you're not sure of something, please get in touch by pinging me here
(@nrc) or on irc (nrc, on #rust or #rust-internals).
-->
新たな文、節、あるいは章の寄稿も受け付けています！新たな題材を得るには
[Issue一覧](https://github.com/nrc/r4cppp/issues)の[new material](
https://github.com/nrc/r4cppp/labels/new%20material)タグの付いたIssueを見て
ください。不明な点があれば、GitHub（@nrc）またはIRC（#rust または
 #rust-internals で nrc）へ連絡してください。


<!--
### Style
-->
### スタイル
（訳注：フォーク元のポリシーです。フォーク元への貢献の際の参考にどうぞ）

<!--
Obviously, the intended audience is C++ programmers. The tutorial should
concentrate on things that will be new to experienced C++ programmers, rather
than a general audience (although, I don't assume the audience is familiar with
the most recent versions of C++). I'd like to avoid too much basic material and
definitely avoid too much overlap with other resources, in particular the Rust
guide/book.
-->
明らかなことですが、対象とする読者はC++プログラマです。 したがって、内容は
熟練したC++プログラマにとって新たな知識になることを中心にすべきです(
ただし、読者が最新版のC++規格を熟知していることは想定しません)。また、あまりに
基礎的な事や、他の資料、とりわけ the Rust guide/book と重複するような内容は避け
ます。

<!--
Work on edge case use cases (e.g., using a different build system from Cargo, or
writing syntax extensions, using unstable APIs) is definitely welcome, as is
in-depth work on topics already covered at a high level.
-->

<!--
I'd like to avoid recipe-style examples for converting C++ code to Rust code,
but small examples of this kind are OK.
-->

<!--
Use of different formats (e.g., question and answer/FAQs, or larger worked
examples) are welcome.
-->

<!--
I don't plan on adding exercises or suggestions for mini-projects, but if you're
interested in that, let me know.
-->

<!--
I'm aiming for a fairly academic tone, but not too dry. All writing should be in
English (British English, not American English; although I would be very happy
to have localisations/translations into any language, including American
English) and be valid GitHub markdown. For advice on writing style, grammar,
punctuation, etc. see the Oxford Style Manual
or [The Economist Style Guide](http://www.economist.com/styleguide/introduction).
Please limit width to 80 columns. I am a fan of the Oxford comma.
-->

<!--
Don't feel like work has to be perfect to be submitted, I'm happy to edit and
I'm sure other people will be in the future.
-->

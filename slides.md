---
theme: default
class: text-center
download: true
highlighter: shiki
lineNumbers: true
colorSchema: light
aspectRatio: 16/9
drawings:
  persist: false
fonts:
  sans: Noto Sans JP
title: '[''a'', ''b'', ''c''].map(&:upcase) is 何？'
---

<div class="text-gray text-3xl">
  
# ['a', 'b', 'c'].map(&:upcase) is 何？

</div>
<span class="text-2xl text-orange-400">初学者が調べるRubyのしくみ</span>

<div class="absolute bottom-10 left-16">
  <span class="text-xl">
    2022-02-22 UV Study: Ruby LT会 Vol.3
  </span>
</div>

<div class="absolute bottom-10 right-16">
  <span class="font-700 text-xl">
    @haruguchi-yuma
  </span>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# 目次

1. 自己紹介
1. 概要説明
1. `['a', 'b', 'c'].map(&:upcase)`のコードの説明
1. ちょっと遊んでみた
1. 感想

---

# 自己紹介

[FJORD BOOT CAMP](https://bootcamp.fjord.jp/)で学習しています。
外部のLT会は初参加です！よろしくお願いします😁

<div class="grid grid-cols-2 gap-4">

- 名前：haruguchi-yuma

- <a href="https://github.com/haruguchi-yuma" target="_blank"><img alt="Github" src="https://img.shields.io/badge/HaruguchiYuma-%2312100E.svg?&style=flat-square&logo=Github&logoColor=white" class="inline" /></a>
<a href="https://twitter.com/haruguchiyuma" target="_blank"><img alt="Twitter" src="https://img.shields.io/badge/@haruguchiyuma-%231DA1F2.svg?&style=flat-square&logo=twitter&logoColor=white" class="mx-1 inline" /></a>
<a href="https://haruguchi-yuma.hatenablog.com/" target="_blank"><img alt="Hatena" src="https://img.shields.io/badge/Blog-haruguchi-orange?&style=flat-square&logo=hatena&logoColor=orange" class="mx-1 inline" /></a>

- 趣味：バンド、数学、読書、モブプロ
- 住んでいるところ：滋賀県
- 前職：公立中学校の教員


<div class="flex flex-col">

<div class="flex flex-row justify-around">

<img src="/turtle.png" class="w-30 rounded-full"/>
<img src="/piyorudo.png" class="w-30 rounded-full" />
<img src="/lake_biwa.png" class="w-30 h-40" />

</div>


</div>

</div>


---
class: 'text-center'
---

<div class="absolute top-60 right-105">

<h1 class="text-8xl font-black">概要説明</h1>

</div>

---

# 何を発表するのか？


<v-clicks>

<div class="grid grid-cols-2 gap-4">


<div>

### 1. どういう仕組みで動いているのか

```ruby
[ 'a' , 'b' , 'c' ].map(&:upcase)
=> [ 'A', 'B', 'C' ]
```
- 引数に&をつけるのはなぜ？
- ただのシンボル`:upcase`がなぜメソッドっぽい振る舞いをするのか？

</div>


<div>

### 2. この仕組みを使って遊んでみた

```ruby
[ 'a', 'b', 'c' ].map(&'upcase')
=> ['A', 'B', 'C']
```

- 文字列を渡しても動くようにしてみた！
</div>

</div>

</v-clicks>

<style>
code {
  font-size: 1.1rem;
}
</style>
---

<div class="absolute top-60 right-60">

<h1 class="text-8xl font-black">['a', 'b', 'c'].map(&:upase)の説明</h1>

</div>

---

# 理解するために必要な知識

<div class="my-4">

- ブロック付きメソッド
- Procオブジェクト
- Symbol#to_procメソッドの挙動

</div>
---

# ブロック付きのメソッド
### 定義方法と呼び出し

<div>

<v-clicks>

<div class="my-4">



</div>

```ruby {1-|9-13|1-7|1,2|5,6,10-12|3-4|1-13}
# メソッド定義
def foo(&block)
  p block # => #<Proc:0x0000000104a79338>
  p block.class # => Proc
  # callメソッドでブロックの処理を実行
  block.call('Alice') # 必要に応じて引数を渡すことができる
end

# 呼び出し
foo do |name|
  puts "#{name}さん、ごきげんよう!"
end
=> Aliceさん、ごきげんよう!
```

<div>

### ☆Point
<div class="border-solid border-2 border-orange-400 p-1">

- (&...)とすることでブロックはProcオブジェクトに変換される
- ブロック（Procオブジェクト)の処理は`call`メソッドで実行

</div>

</div>

</v-clicks>

</div>

<style>
code {
  font-size: 1.1rem;
}
</style>

---


<v-clicks>



<p class="text-center　text-2xl py-20"><span class="border-b-2 border-orange-500">ブロック→Procオブジェクト</span>→.callで実行</p>


<h3 class="text-center font-black"> メソッドに直接Procオブジェクトを渡すこともできるのでは？🤔</h3>



<div>

<p class="text-center text-4xl text-orange-500">=> できた！！！</p>

</div>



</v-clicks>

---

# Procオブジェクトをメソッドに渡す

<v-clicks>

```ruby{1-9|1-6|6-9|1,8-9|1-9}
def foo(&block)
  block.call('Alice')
end

# Procオブジェクトを生成
greeting_proc = Proc.new { |name| puts "#{name}さん、ごきげんよう!" }

foo(&greeting_proc) #&が必要
=> Aliceさん、ごきげんよう!
```

<div>

### ☆Point
<div class="border-solid border-2 border-orange-400 p-1">

- ブロックの代わりに最初からProcオブジェクトを引数として渡すことも可能
- メソッド定義で(&...)としているときはメソッド呼び出しも引数の先頭に&をつけて呼び出す

</div>

もっとよく調べてみると、、、

</div>

</v-clicks>

<style>
code {
  font-size: 1.1rem;
}
</style>
---

# 実はprocオブジェクト以外も渡せる

```ruby
foo(&greeting_proc)
=> Aliceさん、ごきげんよう!
```

引数の先頭に＆をつけて呼び出すとき、オブジェクトに対して暗黙的に`to_proc`メソッドを呼び出している！！(るりま参照)

<v-clicks>

<p class="text-xl text-orange-500">=> `to_proc`メソッドを定義しているオブジェクトであれば引数として渡すことができる！！</p>

<div class="my-4">

<hr>

### to_procメソッドを定義しているクラス

- Proc
- Method
- <span class="border-solid border-2 border-orange-500">Symbol</span>

</div>

<div>

<p>なのでこのようなコードが動くということになります。</p>

```ruby
['a', 'b', 'c'].map(&:upcase)
```

</div>

</v-clicks>

<style>
code {
  font-size: 1.1rem;
}
</style>

---

# Symbol#to_procの挙動

### callするとselfをメソッドとして呼び出す

<v-clicks>

```ruby
:upcase.to_proc.call
=> no receiver given (ArgumentError)
```

```ruby
# 'a'.upcaseと同じ
:upcase.to_proc.call('a')
=> "A"
```

<div>

### ☆Point
<div class="border-solid border-2 border-orange-400 p-1">

- :symbol.to_procを実行（call)するとselfをメソッドとして呼び出す
- ただし、`call`メソッドの第一引数でメソッドのレシーバを指定する


</div>

</div>

</v-clicks>

<style>
code {
  font-size: 1.1rem;
}
</style>

---

# まとめ

```ruby
['a', 'b', 'c'].map(&:upcase)
=> ['A', 'B', 'C' ]

# イメージ
  :upcase.to_proc.call('a') => 'A'
  :upcase.to_proc.call('b') => 'B'
  :upcase.to_proc.call('c') => 'C'
```

1. ただのシンボル:upcaseは&によってProcオブジェクト化する
1. mapメソッドは＃イメージのように配列の要素を一つずつ渡していくのでそれぞれ大文字に変換する

<style>
code {
  font-size: 1.1rem;
}
</style>

---

<div class="absolute top-60 right-80">

<h1 class="text-8xl font-black">ちょっと遊んでみた🎤</h1>

</div>

---

# 文字列を渡しても動くようにしたい！

<v-clicks>

```ruby{7-8|1-5|1-}
class String
  def to_proc
    Proc.new { |obj| eval "obj.#{self}" }
  end
end

['a', 'b', 'c'].map(&'upcase') # 文字列のupcaseを渡す
=> ['A', 'B', 'C']
```
<div class="mt-20">

<p class="text-black text-2xl text-center"><span class="text-orange-500">動きました！！</span></p>

</div>

</v-clicks>

<style>
code {
  font-size: 1.1rem;
}
</style>

---

# 感想：技術ネタを初めて発表してみて

<div class="my-4">

### 適当なことは言えないので、ドキュメントをくまなくみる癖がついた

</div>

<v-clicks>

- 意外と調査するのに時間がかかる
- この表現は正しいのか？伝わるのか？
- 改めてRubyの柔軟性に気がついた
- Rubyで色々試すのが楽しかった

</v-clicks>

---
<div class="grid grid-cols-2 gap-4">

<div class="my-4">

# 参考資料
- [るりま - ブロック付きメソッド呼び出し](https://docs.ruby-lang.org/ja/latest/doc/spec=2fcall.html#block)
- [るりま - class Proc](https://docs.ruby-lang.org/ja/latest/class/Proc.html)
- [るりま - class Symbol#to_proc](https://docs.ruby-lang.org/ja/latest/class/Symbol.html#I_TO_PROC)

</div>

<div>

<v-clicks>

<img src="/arigatou.png" class="rounded-full"/>

</v-clicks>

</div>

</div>

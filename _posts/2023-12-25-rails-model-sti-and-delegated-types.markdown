---
layout: post
title:  '我認知中真正的 Rails Model “多型" 是什麼？'
date:   2023-12-25 15:00:00 +0800
categories: Rails
---

由於我學習的第一個物件導向(OOP)語言是 C++，大家都知道，在 OOP 領域，多型（Polymorphism）是很基礎且重要的觀念（本文就不多做解釋）。但是在 Rails 中，Polymorphism 這個名詞，指的是資料庫表格關係中的其中一種形式 Polymorphic Associations，而對於 Rails Model 在 Ruby 這個 OOP 語言層面上的多型，卻是 Rails ORM 的 STI 與 Delegated Type。這也導致在與同事討論 OOP 多型時，常常一開始彼此認知就有差異。

筆者在工作經歷中，鮮少見到有人真正的在 Rails 中對 ORM 進行抽象化的設計，這可能會導致，其實是可以用一套 CRUD 邏輯就能在某個功能層面操作應付數種 model，但是卻硬生生要重複寫兩三套一樣的 Controller，只因為 model name 不一樣。

STI 與 Delegated Type 同樣都能達到 Rails model 的抽象化，兩者的差別在於，STI 讓每個 model 都共用同一個 table，而 Delegated Type 則是每個 model 擁有各自的 table。

以下進行介紹：

# [Single Table Inheritance (STI)](https://edgeguides.rubyonrails.org/association_basics.html#single-table-inheritance-sti)

建立 Vehicle、Car、Motorcycle 三個 Model，其中 Car 與 Motorcycle 繼承自 Vehicle。
```shell
$ bin/rails generate model vehicle type:string color:string price:decimal{10.2}
$ bin/rails generate model car --parent=Vehicle
$ bin/rails generate model motorcycle --parent=Vehicle
```

```ruby
class Vehicle < ApplicationRecord
  def say
    'hello'
  end
end
```

```ruby
class Motorcycle < Vehicle
  def sound
    'Bom Bom'
  end
end

class Car < Vehicle
  def sound
    'Bu bu'
  end
end
```

```ruby
irb(main):001> Vehicle.pluck :id, :type, :color
=> [[1, "Car", "Red"], [2, "Motorcycle", "White"]]

irb(main):002> Vehicle.all.map &:sound
=> ["Bu bu", "Bom Bom"]

irb(main):003> Vehicle.all.map &:say
=>  ["hello", "hello"]

irb(main):009> Car.take.say
=> "hello"
```
#### 缺點
由於各個 subclass 間的微小差異，需要的欄位不同。這些欄位一律加在 vehicle 表格上，導致某些欄位不會被使用到的狀況。

# [Delegated Types](https://edgeguides.rubyonrails.org/association_basics.html#delegated-types)

#### 特點
class 間不同的欄位會在 class 自己的 table上，共用的欄位則集中於一個 table。可解決 STI 在 subclass 發生欄位多餘的狀況。

```shell
$ rails g model entry entryable_type:string entryable_id:integer
$ rails g model message subject:string body:string
$ rails g model comment content:string
```

```ruby
class Entry < ApplicationRecord
  delegated_type :entryable, types: %w[ Message Comment ], dependent: :destroy

  def say
    'hello'
  end
end
```

```ruby
module Entryable
  extend ActiveSupport::Concern

  included do
    has_one :entry, as: :entryable, touch: true
    delegate :say, to: :entry
  end
end
```

```ruby
class Message < ApplicationRecord
  include Entryable

  def title
    subject
  end
end

class Comment < ApplicationRecord
  include Entryable

  def title
	content.trucate 20
  end
end
```

```ruby
irb(main):001> Entry.create! entryable: Message.new(subject: 'hello')
irb(main):002> Entry.create! entryable: Comment.new(content: 'this is a good post!')

irb(main):003> Entry.all.map &:title
=> ["hello", "this is a good post!"]

irb(main):010> Comment.take.say
=> "hello"
```

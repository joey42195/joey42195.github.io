---
layout: post
title:  "我用 R 語言爬蟲分析二手車售價"
date:   2023-11-04 13:00:03 +0800
categories: Rails
tags: Rails Widget
---

之前買了一台中古車(solio)，就用程式爬了一下二手車網上的車價、領牌年份、里程數，真的還滿方便的，而且成就感滿點。

 一開始還遭遇反爬蟲技術，後來開大絕，發現 R 語言有 RSelenium 套件，可直接假裝一個真人把瀏覽器開起來，然後把顯示完畢的網頁內容直接拿來爬，然後打上散點圖，看到幾個現象：


### 1. 市場上大宗以2004至2005年份領牌車為主
這張圖將不同的領牌年份，分成數個小圖來觀察。黑點的數量表示在該年份在網站上有幾台正在出售，Y軸表示待售車的里程數。

![領牌年份-里程數-售價 散點圖](/assets/images/mil-price-year.jpg)

不知道是不是因為 2004~2005 那時候車子很紅，賣得很好的關係，所以二手車市場上以這段時間的車為大宗。


### 2. 領牌年份與售價有明顯相關性
y軸是售價，x軸為年份，直棒中的黑點是平均值，最上端為市場上最高售價，底端為最低售價，意義類似股市的K線圖。

![領牌年份-售價 誤差棒](/assets/images/price-year.jpg)

看圖可以發現，2009的車均價落在 15 萬左右，2006~2009年的車價整體落在 10~15 萬左右。
2005的車，多數在 7.5 ~ 10萬之間即可買到，2004年車則在 7.5萬元上下。
至於 2003 年以下的車，售價跟 2004 相差不遠，有兩輛低於 5萬元，但一分錢一分貨，如果不是真的很會看車，最好是別冒風險去買。
2002 年有兩輛，售價在 7.5 - 10 萬之間，不知道為啥賣這麼貴，一樣，不是很懂的話，就別冒險買。


### 3. 行駛里程與領牌年份的相關性令人不解

y軸為里程數milage，x軸為售價price，不同圖為不同年份。
![里程數-年份 誤差棒](/assets/images/mil-year.jpg)

里程數的數據相當詭異，我們可以看到 2009 年車的里程數大概都落在 10~16萬公里左右，還算滿符合經驗法則，不是營業車、業務車的話，一年約跑1.5萬。
詭異就在這裡，爬來的資料裡，有一半的 2004-2005 年車，里程數也落在 10~15萬。這會讓我懷疑，中古車市場裡面是不是有一半的車子都有調過表？

### 4. 結論
預算 10 萬以上的人，可能就直接去找 2006 年以上的車來看，會比較划算。
預算 10 萬以內的人，選擇可以很多，市場上大宗都在 2004-2005年份，低於 2004 年的車，可以不用看花心力去看，因為售價沒有便宜太多。
我預算大概 8-10萬，預計是年底要買。經過以上的分析，大概就有個底，到時候出去看車，我應該就鎖定 2005 年車，看能否尋到售價 8萬左右，車況也還 ok 的。至於里程數就別太相信，說不定，里程高的車，還會擁有比較誠實不作假的車況。

關於車況的鑑定，為此，我還報名了中古車鑑定的課程，還會有實車教學。哪天我又決定不寫程式了的話，乾脆去學中古車買賣好了 XD。

### 參考
- [RSelenium：R 使用 Selenium 操控瀏覽器下載網頁資料](https://blog.gtwang.org/r/rselenium-r-selenium-browser-web-scraping-tutorial/)
- [在 Mac 安裝 Chrome Driver](https://medium.com/@brettlin_78528/%E7%94%A8-python-%E5%8C%AF%E5%85%A5-selenium-%E7%9A%84%E6%96%B9%E5%BC%8F-%E4%BB%A5%E5%8F%8A%E5%A6%82%E4%BD%95%E7%94%A8mac-%E5%AE%89%E8%A3%9D-chromedriver-5d92121c02d7)

### 後記：如何在 Mac 安裝 selenium-server

```shell
$ brew install selenium-server-standalone
$ selenium-server -port 4444
$ brew services start selenium-server-standalone
```

```ruby
    # 使用偽瀏覽器拜訪某個 url。
    def use_web_browser_to_visit(url)
      if url.blank?
        raise "#{@game.name} url is nil!(Strategy::CommonMethod::#{__method__})"
      end

      options = Selenium::WebDriver::Chrome::Options.new
      options.add_argument('--headless')
      driver = Selenium::WebDriver.for(:chrome, options: options)

      driver.navigate.to(url) if !Rails.env.test? # 測試環境下就不打了。

      result = true
    rescue StandardError => e
      # 若有連不上的狀況，Selenium::WebDriver 都會 raise exceptions。
      debug_logger(e.to_s)

      if Rails.env.production?
        ExceptionNotifier.notify_exception("[#{@game.name}] #{e}")
      end

      result = false
    ensure
      # 關掉瀏覽器，以免浪費記憶體。
      driver&.quit
      result
    end
```

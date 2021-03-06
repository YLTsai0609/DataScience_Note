# Reference

* [2018 - 05 - 11 資訊品質 (InfoQ) 系列 1：系統性思考分析專案的資訊品質](https://taweihuang.hpd.io/2018/05/11/infoqframework/)
* [論文 Kenett, R. S., & Shmueli, G. (2014). On information quality](https://www.google.com/search?q=Kenett%2C+R.+S.%2C+%26+Shmueli%2C+G.+(2014).+On+information+quality&oq=Kenett%2C+R.+S.%2C+%26+Shmueli%2C+G.+(2014).+On+information+quality&aqs=chrome..69i57.715j0j1&sourceid=chrome&ie=UTF-8)

# 情境

* 在線下建立了一個點擊預測模型，經過嚴密的分析後，在訓練集合與測試集合都表現得很好，結果上線之後的表現卻差強人意....
* 過去建立了一個迴歸分析模型，用以分析效費者購買幾個與幾個重要變數的關係，過去兩年的解釋能力都非常強，但這幾個月不知道為什麼解釋能力都不太好。
* 做完線上 A/B 測驗後，發現實驗組顯著優於對照組，但實際上線到所有用戶時，發現成效反而變差了！

如果遇到上面這些情境，你會怎麼樣找出問題的根源呢？許多朋友可能會認為自己過度配適 (overfit) 原先的資料集合，因而開始著手設計新的演算法，試圖找出表現更優異的模型。然而，我們設計出的新模型會不會又陷入上述的情境呢？如果又發生了上述情況，問題又在哪裡呢？我們很有可能遇到了 **「資訊品質」(Information Quality，InfoQ)** 的問題。

# 什麼是資訊品質(InfoQ)

Kenett與Shmueli對於資訊品質(infoQ)的定義非常簡單，一個**資料集合**透過**某個給定的分析方法**能夠**達成分析目標**的潛力。

因此，從上面這個定義，我們可以知道infoQ有四個重要的決定因素

# 1. 分析目標(Analysis Goal, $g$):

   一個資料專案的分析目標其實可以拆成兩個面向，一是**商業/科學上的目標**，二是**資料建模上的目標**，在商業上與科學上的目標，我們必須有清楚的問題，通常要定義出量化的**衡量標準(metrics)**，才有辦法與資料建模上的目標對接，常見的資料建模目標有4種，分別是
   1. 描述性分析(descriptive analytics)，用於回答發生什麼事?
   2. 診斷性/解釋性分析(diagonostic / explanatory analytics)，用於回答為什麼發生這樣的事情?
   3. 預測性分析(predictive analytics)，用於回答新的資料將會發生什麼事?
   4. 建議性分析(prescriptive analytics)，用於回答我應該要怎麼做?
   5. [check here 4 types of analytics goal](https://insights.principa.co.za/4-types-of-data-analytics-descriptive-diagnostic-predictive-prescriptive)

舉例來說，進行使用者研究時，一定會先從大的商業戰略開始：新的 feature 要增加營收呢？還是應該要改善使用者體驗呢？還是要能夠從競爭者那裡吸引更多的使用者呢？清楚定義商業問題後，我們可以透過不同的方法想出好的量化指標。比如說，大鼻想要改善部落格讀者的使用者體驗，因此我可能會透過使用者訪談了解「當使用者體驗不好時，他們會有什麼樣的反應？」

 

結果訪談結果顯示，大部分的使用者會傾向「提早離開閱讀頁面」以及「減少閱讀時間」，因此我接下來的量化衡量標準便會針對跳出率(Bounce Rate)、離開率(Exit Rate)與平均網頁停留時間 (Time on Page)、平均網頁停留時間 (Time on Site) 做研究主題。為了設計這些指標的改善策略，此時我便會將資料建模目標設定成「診斷／解釋性分析」，找出影響上述指標的重要因素（如：網站設計、寫作風格、寫作主題等等）。

# 2. 資料集合(Data $D$)

「資料集合」當然就是指我們要用來進行分析的資料。常見的資料型態有：

  1. 橫斷面資料 (cross-sectional data)、
  2. 時間序列 (time series)、
  3. 長期資料 (longitudinal data)、
  4. 網絡型資料 (network data)、
  5. 函數型資料 (functional data) 
  6. 可以參考 Quora [文章 1](https://www.quora.com/What-is-the-difference-between-panel-data-time-serial-data-and-cross-sectional-data)、[文章2](https://www.quora.com/What-are-new-ideas-on-social-network-data-analysis-for-research)、[康乃爾大學課程簡報](http://faculty.bscb.cornell.edu/~hooker/ShortCourseHandout.pdf)等。「資料集合」的品質是資料分析專案的成敗，因此在這篇系列後續的文章，將會特別著重在衡量與提升「資料集合」的品質，在這裡就不贅述了！

# 3. 分析方法(Empirical Method $f$)

對於 InfoQ 而言，分析方法的諸多議題中，最重要的就是要能選對符合「分析目標」的模型，以及設計相對應的「測試機制」。比如說，如果我們希望做 **「診斷/解釋性分析」，比較簡單的參數化線性模型，可能會比複雜的深度神經網絡好**；如果想要做 **「建議性分析」，實驗設計與測試會比線性模型更能夠衡量策略的影響與衝擊。**

# 4. 效用指標(Utility $U$)

「效用」指的是「給定資料集合與分析方法，得出的分析結果能否達成分析目標」，也就是在分析完成後衡量「是否達成資料分析目標」以及「是否達成商業/科學目標」。「是否達成資料分析目標」比較簡單，比如說：解釋性建模常用的就是 p-value、檢定力等，在預測性分析可能就是準確度、精確度與召回率等常見的指標。「是否達成商業/科學目標」可能要進行一系列的質化與量化分析，確認資料分析的結果與商業/科學目標的期待是一致的。

# 個案研究：改善 APP 的使用者參與度 (User Engagement)

(以下為虛構個案) 假設你是一個戲劇串流 app 的資料科學家，他們的企業願景有一部分是：

著重於戲劇發展，擴張串流內容多元性與提升內容品質，持續優化使用者介面與體驗，並延伸服務至不同的連網裝置。

最近，公司的 PM 發現「使用者的參與度」似乎不如從前，因此希望資料科學家協助，制定改善使用者參予度的重要策略。

## 1. 分析目標 (Analysis Goal, $g$)：

**從戰略層次來看**，由於我們的 APP 以廣告為主要的獲利來源，提升使用者參與度能夠增加廣告曝光次數、廣告點擊率，同時也能夠提高公司業務與廣告商的談判籌碼。因此，此處的商業目標是：「提升用戶參與度進而改善廣告營收」。值得注意的是，在定義商業目標時，要很清楚「為什麼要改善使用者參與度」以及「提升參與度對公司能帶來什麼好處」。

由於 PM 希望協助制定策略，我的思考邏輯通常是：先理解使用者參與度的影響因素，根據這些影響因素設計適合的調整方案，再針對這些調整方案進行測試找出最佳方案。因此，專案初期是屬與「診斷/解釋性分析」，接著則是要設計代表「使用者參與度」的指標。通常我們不會只監測單一指標，而是會監測多個面向的使用者參與度，比如說：

* 人口相關：活躍使用者數 (Active User)、頁面訪問數 (Page Visit)、戲劇總觀看數 (View Count)、觀看總時間 (Total Watching Time) 等
* 活動相關：每人觀看次數 (View Count per User)、每人觀看劇數 (# of Dramas Watched per User)、每人觀看總時數(Total Watching Time per User)、每次觀看時間 (Total Watching Time per View Count) 等
* 忠誠度相關：每人登入APP次數、每人每月登入 APP 天數、每人使用聯網裝置數量等等。

上述指標雖然看起來很清楚易懂，但其實有許多細節尚未定義清楚，如：什麼樣的使用者叫做活躍使用者？時間上要以日、週、還是月等去計算指標等等，這些細節常常要與不同團隊與同事討論，才能確定我們監測的量化指標是有意義的。最後，經過幾番討論，我們訂出的資料分析目標是找出「一個使用者是否活躍」的重要因素。

## 2. 資料集合 (Data, $D$)：

定義清楚之後，我們會根據分析目標蒐集對應的資料。在上述的個案中，我們會蒐集與研究目的相關的資料，除了常見的使用者特徵 (性別、年齡、國籍等) 與瀏覽行為外(瀏覽裝置、點擊次數)，也可以透過使用者研究找出值得蒐集的變數。另外，可能也會透過設計實驗來蒐集數據。

## 3. 分析方法 (Empirical Method, $f$)：

由於目標變數是「一個使用者是否活躍」，因此我們要選擇分類模型，比如說：由於解釋變數較多、可能存在離群值、以及有類別變數，因此我們選擇使用隨機森林 (random forest) 分類一個使用者是否活躍。同時我們可以利用部分相關性 (partial dependence) 以及變數重要性 (variable importance)了解重要的影響因子。

## 效用指標 (Utility, $U$)：

由於屬於分類問題，我們會使用預測準確度 (predictive accuracy)、精確度 (precision)、召回度 (recall)、F1 指標等衡量模型的準確度。

# 小結

相信從上面的解說，大家應該能夠理解影響分析專案資訊品質的四大元素，

$$
InfoQ(f, D, g, U) = U(f(D)|g)
$$

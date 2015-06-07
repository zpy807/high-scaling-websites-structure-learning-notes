# 資料排列組合

在使用 MySQL 或 PostgreSQL 資料庫的時候，我們常常會使用 Auto Increment 的整數去當作我們的鍵值，Int 總共有 `4294967295` 種排列組合方式，而 BigInt 則總共有 `18446744073709551615` 種排列組合方式

使用 Auto Increment 的好處是：

* 可以避免每次的鍵值都是不同的，不需要再做額外的欄位資料檢查是否重複
* 可以依照整數順序去判斷資料新增的順序

但這樣也有壞處：

* 資料被刪除後，空出來的鍵值很難被管理，會造成不必要的浪費
  * e.g. Queue 資料
* 總資料量被限制，當我們預期資料很有可能超過整數欄位的限制時，資料量超過時還需要處理溢位問題
  * e.g. 很多的 Queue 資料

所以若預期資料量很大時，我們會傾向使用字串去當作資料表的鍵值，單一整數的資料排列組合有 10 種 `（0-9）`，而單一字串的資料排列組合有 36 種`（0-9 A-Z）`，雖然整數欄位在資料表搜尋的速度比字串快很多，但是這個是沒辦法的必要之惡。


## 字串排列組合

在資料庫儲存的字串資料是不區分大小寫的，所以若有一個 Primary 或 Unique 的鍵值，裡面是沒辦法同時儲存 `AA`、`Aa`、`aA` 或 `aa` 的，所以我們字串鍵值的排列組合只能用不區分大小寫 36 種 `(0-9 A-Z)` 排列組合去計算。

|字串長度 |  排列組合 (不區分大小寫) |
|---|---|
| 1	 |  36 |
| 2	 |  1,296 |
| 3	 |  46,656 |
| 4	 |  1,679,616 |
| 5	 |  60,466,176 |
| 6	 |  2,176,782,336 |
| 7	 |  78,364,164,096 |
| 8	 |  2,821,109,907,456 |
| 9	 |  101,559,956,668,416 |
| 10 |	3,656,158,440,062,980 |
| 11 |	131,621,703,842,267,000 |
| 12 |	4,738,381,338,321,620,000 |
| 13 |	170,581,728,179,578,000,000 |
| 14 |	6,140,942,214,464,820,000,000 |
| 15 |	221,073,919,720,733,000,000,000 |
| 16 |	7,958,661,109,946,400,000,000,000 |
| 17 |	286,511,799,958,070,000,000,000,000 |
| 18 |	10,314,424,798,490,500,000,000,000,000 |
| 19 |	371,319,292,745,659,000,000,000,000,000 |
| 20 |	13,367,494,538,843,700,000,000,000,000,000 |
| 21 |	481,229,803,398,374,000,000,000,000,000,000 |
| 22 |	17,324,272,922,341,500,000,000,000,000,000,000 |
| 23 |	623,673,825,204,293,000,000,000,000,000,000,000 |
| 24 |	22,452,257,707,354,600,000,000,000,000,000,000,000 |
| 25 |	808,281,277,464,764,000,000,000,000,000,000,000,000 |
| 26 |	29,098,125,988,731,500,000,000,000,000,000,000,000,000 |
| 27 |	1,047,532,535,594,330,000,000,000,000,000,000,000,000,000 |
| 28 |	37,711,171,281,396,000,000,000,000,000,000,000,000,000,000 |
| 29 |	1,357,602,166,130,260,000,000,000,000,000,000,000,000,000,000 |
| 30 |	48,873,677,980,689,300,000,000,000,000,000,000,000,000,000,000 |
| 31 |	1,759,452,407,304,810,000,000,000,000,000,000,000,000,000,000,000 |
| 32 |	63,340,286,662,973,300,000,000,000,000,000,000,000,000,000,000,000 |
| 33 |	2,280,250,319,867,040,000,000,000,000,000,000,000,000,000,000,000,000 |
| 34 |	82,089,011,515,213,400,000,000,000,000,000,000,000,000,000,000,000,000 |
| 35 |	2,955,204,414,547,680,000,000,000,000,000,000,000,000,000,000,000,000,000 |
| 36 |	106,387,358,923,717,000,000,000,000,000,000,000,000,000,000,000,000,000,000 |
| 37 |	3,829,944,921,253,800,000,000,000,000,000,000,000,000,000,000,000,000,000,000 |
| 38 |	137,878,017,165,137,000,000,000,000,000,000,000,000,000,000,000,000,000,000,000 |
| 39 |	4,963,608,617,944,920,000,000,000,000,000,000,000,000,000,000,000,000,000,000,000 |
| 40 |	178,689,910,246,017,000,000,000,000,000,000,000,000,000,000,000,000,000,000,000,000 |

> 只要字串長度大於 13 時，我們擁有的資料排列組合就會超過 BigInt 的 `18446744073709551615` 排列組合

在使用字串當錊鍵值時，在我們每次新增資料手動產生字串鍵值時，我們都要再去資料庫檢查看看此鍵值是否存在，再新增進去，且我們需要額外做新增資料造成 Duplicate Key 的例外狀況處理（如果我們的字串長度很大，雖然發生的機率很低）

## 參考資料
* [MySQL資料型態描述與比較](http://blog.kejyun.com/2012/09/MySQL-Field-Data-Type-Description-And-Compare.html)
* [《江南Style》如何衝破YouTube的計數器](http://www.cw.com.tw/article/article.action?id=5063028)
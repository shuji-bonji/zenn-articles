---
title: "まとめ：S1000D から受け取れるもの"
---

S1000D の価値は、「対話型のマニュアル」という考え方そのものよりも、それを実際に動かすためのデータの決まりにあります。情報をどの単位に分けるか、どの装備に当てはまるかをどう書くか、事業ごとのルールをどう検査するか、質問と分岐をどう記述するか。こうした決まりが、1980 年代から現在まで改訂を重ねながら、防衛や民間航空の現場で使われてきました。

しかも S1000D が決めているのはデータの形だけで、画面を表示するビューアは指定していません。データを S1000D の考え方に沿って持てば、表示は Web 技術など、今の技術で自由に作れます。

古い仕様でありながら S1000D が今注目されているのは、航空機の整備手順だけでなく陸・海の装備や建設などにも使える仕様になったこと、各国の防衛分野や NATO で採用が進んでいること、表示を今の技術で作れること、3D や VR のような新しい表示方法にも対応できることによると考えられます。

この章では、ここまでの章で確認した事実と、新しく確認した事実を整理したうえで、S1000D から何を受け取れるかについての著者の見解を述べます。

## これまでの章で確認した事実

### 長く改訂が続いている

- 公式サイトによると、S1000D は 1980 年代前半に、ASD の前身である AECMA の航空宇宙向けの標準として始まりました（[「主要な標準の概要」](https://zenn.dev/shuji_bonji/books/ietm-overview/viewer/standards)の章）。
- Issue 2 から対象が陸と海の装備にも広がり、公式サイトは現在の用途として、防衛（陸・海・空）、民間航空、建設、船舶を挙げています。
- 2026 年 10 月時点で、公式のダウンロードの一覧には Issue 7 があり、仕様書の表紙の日付は 2026-07-16 です（[「現在の動向と参考情報」](https://zenn.dev/shuji_bonji/books/ietm-overview/viewer/current-trends)の章）。
- 版の改訂は、Steering Committee が変更提案（Change Proposal Form）を審議して行います。変更の提案を審議して改訂する仕組みが、仕様の運営に組み込まれています。

### データの形だけを決めている

- 米国の [MIL-PRF-87268A](https://www.navsea.navy.mil/Portals/103/Documents/NSWC_Carderock/s87268a.pdf) は、IETM の内容と、それを表示する電子表示装置の操作画面の両方に要求を定めていました（[「IETM と IETP の構成要素」](https://zenn.dev/shuji_bonji/books/ietm-overview/viewer/components)の章）。
- これに対して S1000D は、オーサリング環境、CSDB、ビューアの間でやり取りするデータの形を定め、製品は指定していません。公式サイトは CSDB の機能を定めないと明記しており、ビューアのプログラムも仕様の配布物には含まれていません。
- 画面にどの機能を持たせるかは、事業ごとに機能マトリックスで選びます。

### 航空機の整備手順だけの仕様ではなくなった

- [公式の概要ページ](https://s1000d.org/?page_id=101)によると、Issue 2 から陸上と海上の装備が範囲に加わりました。さらに、軍用と民用の装備のほか、建設、方針や手続き、法務文書、組織の文書などにも使えると書いています。
- [ダウンロードサイト](https://users.s1000d.org/)の案内は、現在の用途として、防衛（陸・海・空）、民間航空、建設、船舶を挙げています。
- 民間航空には [ATA Spec 1000BR](https://publications.airlines.org/pages/spec1000br-civil-aviation-business-rules-for-s1000d)、海運には Shipdex、防衛には DIG のビジネスルールがあり、分野ごとに S1000D を使うためのルールが整えられています。
- AIA は、S シリーズは航空宇宙と防衛のために作られたが、海事、海中、鉄道、建設、農業などにも使えると書いています（[「S シリーズと IPS」](https://zenn.dev/shuji_bonji/books/ietm-overview/viewer/s-series)の章）。

### 採用が国際的に広がっている

- S シリーズの公式サイトは、最近加わった国としてカナダ、オーストラリア、韓国、日本を挙げています。2024 年の出来事としては、NATO の統合ライフサイクル支援の指針（ALP-10）への組み込み、米国防総省による採用の検討の要請、日本の業界団体 SJAC との覚書を挙げています。
- SJAC との覚書は、S1000D について AIA、A4A、ASD、SJAC が結んだものと、S シリーズ全体について AIA、ASD、SJAC が結んだものの二つです。これにより SJAC は、主要な委員会と作業部会に参加できるようになりました。
- 防衛の分野では、米空軍の MIL-STD-3048、ドイツ軍が契約で使える版の指定、オーストラリア国防省の S1000D の IETP の標準 DEF(AUST) IPS-5630 などが整えられています。ただし、確認した範囲では、どの契約でも S1000D を必須にするとまでは書かれていません。

### 3D と VR に対応している

- Issue 7 の仕様書には、対話的な 3D コンテンツ（Interactive 3D content）の規則（第3.9.2.5章）があります。
- 第6.4.1章と第7.3.3章は、3D モデルを使って、仮想的な組み立てと分解、部品の取り外しと取り付けを、アニメーション、シミュレーション、仮想現実（virtual reality）の考え方で示せると書いています。同時に、それらをどこまで使うかで費用が大きく変わるとも注意しています。
- IETP の 3D の部品を、複数の XR ヘッドセットに同時に表示する仕組みについての米国特許（US10891798、2021 年登録）もあります。

## 著者の見解

:::message
ここからは、上の事実をもとにした著者の見解です。公式サイトや規格が述べている内容ではありません。
:::

### 価値の中心は、使われ続けてきたデータの決まり

操作マニュアルを画面で動かそうとすると、次のような問題に必ず突き当たります。

| 作る側が突き当たる問題 | S1000D の答え | 説明している章 |
| --- | --- | --- |
| 情報をどの単位で分けて、更新し、再利用するか | データモジュール（DM）と、DM を一つに特定するデータモジュールコード（DMC） | S1000D の基本 |
| 機種や構成の違いを、どう書き分けて出し分けるか | 適用（applicability）。DM 単位と、段落・ステップ単位の両方で指定できる。条件の値は ACT、CCT、PCT で管理する | Data Module の種類と構造 |
| 質問と分岐、表示の順序をどう記述するか | Process データモジュール（`dialog`、`dmIf`、`dmSeq` など） | Data Module の種類と構造 |
| 書き手ごとのばらつきを、どう検査するか | BREX。事業のルールを、機械で検査できる DM として持つ | Data Module の種類と構造 |
| 一つの情報を、複数の出版物でどう使い回すか | パブリケーションモジュール（PM）が DM を参照して出版物の構成を作る | S1000D の基本 |

どれも、マニュアルを電子化しようとした人が自分で設計し始め、途中で行き詰まる種類の問題です。S1000D には、その答えが、国際的な合意と長年の利用を経た形で、無料の仕様として公開されています。著者は、ここに S1000D の一番の価値があると考えています。

### 表示は今の Web 技術で作れる

S1000D がビューアを指定していないことは、[「導入の利点と留意点」](https://zenn.dev/shuji_bonji/books/ietm-overview/viewer/benefits-and-considerations)の章では、画面を事業ごとに用意しなければならない負担として説明しました。しかし見方を変えると、表示の技術を自由に選べるということでもあります。

```mermaid
flowchart LR
  subgraph data["S1000D の考え方に沿って持つ部分"]
    DM["データモジュール"]
    AP["適用の条件"]
    PR["Process の分岐"]
    BR["BREX の検査ルール"]
  end
  subgraph view["今の技術で自由に作る部分"]
    W["Web のビューア"]
    M["モバイルアプリ"]
    O["その他の表示方法"]
  end
  data --> W
  data --> M
  data --> O
```

1990 年代の IETM は、専用の電子表示装置で動かすことが前提でした。今はブラウザが動く端末であれば、タブレットでも作業用のノートパソコンでも、同じ情報を表示できます。適用による出し分けや Process の分岐は、Web アプリケーションの状態管理の考え方とも相性の良い処理です。

### DX と XR との関係

S1000D は、技術情報を冊子ではなく、識別コードと適用の条件を持った小さなデータの単位として管理します。これは、紙の書類を電子化するだけでなく、業務の仕組みそのものをデータ中心に変える DX（デジタルトランスフォーメーション）の考え方とよく合っています。

また、AR や VR のような XR の技術は、情報をどう見せるかという「表現」の技術で、S1000D は何をどの条件で見せるかを決める「データ」の仕様です。たとえば、作業員が AR ゴーグルで故障した部品を見ると、部品の名前と在庫の状況（S2000M のデータ）と、交換の手順（S1000D のデータ）が視界に重ねて表示される、という整備の環境が考えられます。S1000D と S2000M は、モデル識別コードの付け方に同じ規則を使っており（[「S シリーズと IPS」](https://zenn.dev/shuji_bonji/books/ietm-overview/viewer/s-series)の章で触れた Issue 7 の第2.3章）、二つのデータをつなぐ前提は仕様の側にあります。

さらに進めば、S1000D のデータから XR の表示を自動で作ることも考えられます。ただし、そうした仕組みが一般に使われていると言えるだけの一次資料は確認できていません。仕様書自身も VR をどこまで使うかで費用が大きく変わると注意しているので、現時点では可能性として捉えるのが適切だと考えます。

### 準拠することと、考え方を取り入れることは別

S1000D を活用する方法は、完全に準拠することだけではありません。

- **完全に準拠する**：防衛や航空の事業では、S1000D への準拠が契約の条件になります。この場合は、契約で指定された版、ビジネスルール、機能マトリックスに従います。
- **考え方を取り入れる**：一般の製造業の操作マニュアルなどでは、S1000D の仕様全体に従うのは負担が大きすぎることがあります。その場合でも、データモジュール、適用、Process、BREX といった考え方を、自分たちのデータ設計に取り入れることはできます。将来 S1000D の XML に変換できる形で持っておけば、準拠が必要になったときにも対応しやすくなります。

IETM という語は、1990 年代の米軍の調達用語として、今では古く見えるかもしれません。しかし、その考え方を支えるデータの決まりは、S1000D として現在も改訂が続いています。本書が IETM の定義から始めて S1000D まで説明してきたのは、この 2 つのつながりを示すためです。

## 出典

この章の事実は、次の章で示した出典に基づいています。

- 「主要な標準の概要」：S1000D の経緯と運営、版番号の決まり。https://s1000d.org/?page_id=101 、https://s1000d.org/?page_id=61 、https://users.s1000d.org/
- 「現在の動向と参考情報」：Issue 7 の掲載と、仕様書の表紙の日付。https://s1000d.org/
- 「IETM と IETP の構成要素」：CSDB の機能を定めないこと、ビューアが配布物に含まれないこと、MIL-PRF-87268A が画面にも要求を定めていたこと。https://www.navsea.navy.mil/Portals/103/Documents/NSWC_Carderock/s87268a.pdf
- 「S1000D の基本」「Data Module の種類と構造」：DM、DMC、PM、適用、Process、BREX の説明。

この章で新しく使った出典は、次のとおりです。

- S1000D. *About S1000D*（Issue 2 からの範囲、装備以外の文書への適用）. https://s1000d.org/?page_id=101
- S1000D. *External Links*（ATA Spec 1000BR、DIG、Shipdex）. https://s1000d.org/?page_id=44
- Aerospace Industries Association. *S-Series IPS Specifications*. https://www.aia-aerospace.org/standards/s-series-ips/
- S-Series. *Global adoption*. https://www.s-series.org/our-specifications/global-adoption/
- S-Series. *Aerospace cooperation boosted by new agreements on S1000D and S-Series specifications*. https://www.s-series.org/news-and-events/news/aerospace-cooperation-boosted-by-new-agreements-on-s1000d-and-s-series-specifications/
- Department of Defense. MIL-STD-3048A(USAF). https://everyspec.com/MIL-STD/MIL-STD-3000-9999/download.php?spec=MIL-STD-3048A.054327.pdf
- Bundeswehr. *S1000D International specification for technical publications*. https://www.bundeswehr.de/de/organisation/unterstuetzungsbereich/logistik/logistikkommando-der-bundeswehr-in-erfurt/einheitliche-materialkatalogisierung-im-logkdobw/s-1000d-international-en-specification-en-for-en-technical-en-publications-en-5707894
- Australian Government, Defence. *Developing S1000D Interactive Electronic Technical Publications Standard*. https://defence.gov.au/business-industry/industry-governance/developing-s1000d-interactive-electronic-technical-publications-standard
- 著者が入手した S1000D Issue 7 のパッケージ（第2.3章、第3.9.2.5章、第6.4.1章、第7.3.3章）。入手先は https://users.s1000d.org/
- US10891798. https://patents.google.com/patent/US10891798

## この章のポイント

- S1000D は、1980 年代前半から現在の Issue 7 まで改訂が続き、航空機の整備手順だけでなく、防衛、民間航空、建設、船舶などで使われる仕様になりました。採用は NATO や各国の防衛分野に広がり、2024 年には日本の SJAC も覚書を結びました。
- 著者の見解では、S1000D の一番の価値は、データモジュール、適用、Process、BREX といった、長く使われて固まってきたデータの決まりにあります。
- S1000D はデータの形だけを決めているので、表示は Web 技術や XR など、今の技術で作れます。完全に準拠するか、考え方を取り入れるかは、事業の分野と契約によって選べます。

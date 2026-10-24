---
title: "歴史と維持組織"
---

S1000D は、1980 年代の初めに欧州の航空宇宙の規格として始まり、民間と軍の技術文書の作り方をそろえることを目指してきました。現在は、ASD、AIA、ATA e-Business Program の三つのスポンサー組織のもとで、変更提案（CPF）を審議する仕組みによって維持されています。

この章では、公式サイトが説明している経緯と、現在の維持の仕組みを説明します。版番号の読み方は、[第11章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/issues-and-cautions)で説明します。

## 始まり

[公式の概要ページ](https://s1000d.org/?page_id=101)によると、S1000D の考え方は 1980 年代の初めに、欧州の航空宇宙の規格として始まりました。当時の ASD は、AECMA という名称でした。同じ時期、民間航空の多くのプロジェクトは ATA 100 に従って文書を作り、軍のプロジェクトは各国の規格に従っていました。

統合後方支援（Integrated Logistics Support、ILS）と情報技術が進む中で、AECMA の Customer and Product Support Committee（CPSC）は、Documentation Working Group（DWG）を設けました。DWG に参加した欧州の産業界の代表は、当時の文書の作り方を調べ、航空機のプロジェクトで使える統一した方法を提案する役割を担いました。DWG は、航空宇宙の分野で国際的に受け入れられていた仕様は ATA 100 だけだと判断し、ATA 100 を元にして、民間と軍の文書をそろえる方針をとりました。

DWG は各国に軍の代表の参加を求め、下部組織として Augmented Documentation Working Group（ADWG）を作りました。ADWG の役割は Technical Publications Specification Maintenance Group（TPSMG）に引き継がれ、さらに S1000D Steering Committee に引き継がれました。概要ページによると、現在は Steering Committee が、仕様を維持する責任をすべて持っています。

このような経緯から、S1000D の番号の体系には ATA 100 の影響が見られます。ただし、S1000D は ATA 100 の改訂版ではなく、共通の元から民間と軍の調和を図った、別の仕様です。

Issue 7 の第1.1章も、1980 年代の初めに ASD の航空分野で始まったこと、当時の民間航空の多くが ATA 100 に従っていたこと、Issue 2 から陸と海が加わったことを、概要ページと同じように書いています。

## 範囲が広がった時期

概要ページによると、Issue 2 で陸上と海上の装備が範囲に加わりました。それより前は、航空のための仕様だったことになります。

英語版の Wikipedia には、Issue 2 より前の更新を Change と呼んでいたという説明がありますが、公式の概要ページにはこの呼び方の変化は書かれていないため、本書では扱いません。

2026 年 10 月 23 日時点で、公式のダウンロードの一覧に掲載されているのは、Issue 1.7、1.8、1.9、2.0、2.1、2.2、2.3、3.0、4.0、4.1、4.2、5.0、6、7 です。

## 三つのスポンサー

公式サイトの[組織のページ](https://s1000d.org/?page_id=61)は、S1000D を開発し維持しているのは、民間と防衛の航空、防衛の陸・海の分野の、事業と技術の専門家による国際的な共同体だと説明しています。顧客、供給者、ソリューションの提供者が参加しています。スポンサーは次の三つです。

| 組織 | 公式ページでの説明 |
| --- | --- |
| ASD | 欧州の航空宇宙・安全保障・防衛産業の協会。仕様を最初に開発した |
| AIA | 米国の航空宇宙産業の協会 |
| ATA e-Business Program | 民間航空の情報交換の規格を出してきたプログラム |

公式サイトの[外部リンクのページ](https://s1000d.org/?page_id=44)は、Airlines for America（A4A）を、1936 年に Air Transport Association of America として設立された米国の航空会社の団体として紹介し、ATA e-Business Program を、その情報の枠組みを担うプログラムとして別に紹介しています。本書で「ATA」とだけ書くときは、S1000D のスポンサーである ATA e-Business Program を指します。

Issue 7 の第1.1章は、共同の作成者を Air Transport Association of America（ATA）と書いていますが、仕様書の表紙の発行者（Publishers）は ATA e-Business Program です。公式サイトが二つを別の組織として紹介しているので、本書では仕様本文の「ATA」を、スポンサーの ATA e-Business Program と同じものとしては扱いません。

S シリーズのほかの仕様には、三つのスポンサーのうち ATA e-Business Program は加わっていません。この点は[第9章](https://zenn.dev/shuji_bonji/books/s1000d-intro/viewer/s-series)で説明します。

## Council と Steering Committee

S1000D Council は、ASD、AIA、ATA e-Business Program の間の了解覚書（Memorandum of Understanding）に基づいて作られました。Council の使命は、S1000D の開発全体を管理することです。そのために、将来の方向を示し、全体を管理し、Steering Committee に方針を示し、S1000D の利用を広めます。

S1000D Steering Committee は、仕様に共通の関心を持つ国と組織の代表の集まりです。Council の方針を踏まえて、変更提案（Change Proposal Form、CPF）を審議し、新しい内容や変更を受け入れるかどうかを決め、仕様の新しい Issue として発行します。仕様を最新の状態に保つのは、この Steering Committee です。

## 作業グループ

日常の開発、レビュー、仕様と XML スキーマの変更管理は、常設の作業グループと、一時的なタスクチームが行います。組織のページが挙げている常設の作業グループは、次のとおりです。

- Business Rules Working Group（BRWG）
- Electronic Publications Working Group（EPWG）
- Graphics and Multimedia Working Group（GMWG）
- Production and Publishing Working Group（PPWG）
- Web Site Working Group（WSWG）

このほかに、特定の立場を代表する二つのグループがあります。

- **Civil Aviation Working Group（CAWG）**：世界の民間航空産業を代表する S1000D のグループです。民間航空を S1000D の開発に組み込むために作られ、ATA e-Business Program が運営しています。CAWG のメンバーは関係する作業グループとタスクチームに参加し、ASD、AIA と共同で、民間と防衛の両方の航空産業に合った改訂を進めます。
- **Defense Interest Group（DIG）**：防衛の顧客を代表する独立したグループです。防衛の要求を Steering Committee と Council に伝え、S1000D が防衛の利用者と生産者の必要に合い続けるようにします。DIG はスポンサーではありません。

## 変更の手続き

公式の[変更提案のページ](https://s1000d.org/?page_id=37)によると、仕様の維持の責任は Steering Committee にあり、変更を取り込んで公開する前に、参加国の合意を得ます。

CPF は、仕様に変更を入れるための手続きで、提出者の情報、変更の内容、理由を書きます。CPF は誰でも提出できます。ただし、軍、産業、民間のすべての利用者に影響し得ることを理解したうえで提出する必要があり、国際的で、複数の分野にまたがる合意が得られて初めて受け入れられます。提出の前に、国や事業分野の管理組織の承認が必要になる場合もあります。

XML スキーマを変更する CPF では、CPF White Paper にスキーマの変更内容を書きます。提出と管理には、Specring というオンラインのツールが使われています。

この手続きから分かるのは、現場で便利な運用であっても、それがそのまま次の版の仕様になるわけではない、ということです。仕様に入るのは、CPF を通じて受け入れられた変更だけです。

## 出典

- S1000D. *About S1000D*（History、Issue 2 の範囲）. https://s1000d.org/?page_id=101
- 著者が入手した S1000D Issue 7 のパッケージ（第1.1章。表紙の Publishers は ATA e-Business Program）。入手先は https://users.s1000d.org/
- S1000D. *S1000D Organizational Structure*. https://s1000d.org/?page_id=61
- S1000D. *Change Proposals*. https://s1000d.org/?page_id=37
- S1000D. *External Links*（ASD、AIA、A4A、ATA e-Business Program）. https://s1000d.org/?page_id=44
- S1000D. Product list（2026 年 10 月時点で一覧にある Issue）. https://users.s1000d.org/ProductList.aspx

## この章のポイント

- S1000D は、ATA 100 を元に民間と軍の文書をそろえる取り組みから始まり、Issue 2 で陸と海の装備が範囲に加わりました。
- 現在、仕様を維持する責任は S1000D Steering Committee にあり、ASD、AIA、ATA e-Business Program による Council が全体の方向を決めています。
- 仕様が変わるのは、CPF を通じて受け入れられた変更だけです。プロジェクトの運用を、そのまま仕様として扱うことはできません。

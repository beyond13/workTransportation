■ Salesforce Data Category Group XML の全体構造まとめ

1. ファイル名 (File Name) ＝ カテゴリグループ API 参照名
--------------------------------------------------------------------------------
・物理ファイル名： 例) A.datacategorygroup-meta.xml
・内容： ファイル名の接頭辞（例: 「A」や「Scenes」）が、そのまま Data Category Group の API 参照名（DataCategoryGroupName）となります。
・データ移行時： Data Loader などで Knowledge__DataCategorySelection に登録する際、DataCategoryGroupName 列にはこのファイル名の接頭辞を指定します。


2. XML の標準構造（コードイメージ）
--------------------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<DataCategoryGroup xmlns="http://soap.sforce.com/2006/04/metadata">
    <!-- 1. グループの UI 表示ラベル -->
    <label>シーンから探す</label>
    <active>true</active>

    <!-- 2. ルートノード (Root Node) -->
    <dataCategory>
        <label>保険商品から探す（その他保険）</label>
        <name>All_Scenes</name> <!-- ルートノード API 参照名 (現状維持) -->

        <!-- 3. 第 1 階層ノード (L1) -->
        <dataCategory>
            <label>保険の加入・お見積り</label>
            <name>A01</name> <!-- 2桁採番ルール -->

            <!-- 4. 第 2 階層ノード (L2) -->
            <dataCategory>
                <label>新規加入</label>
                <name>A0101</name>

                <!-- 5. 葉ノード (Leaf Node - 最下層) -->
                <dataCategory>
                    <label>自動車・バイク・自転車・歩行中</label>
                    <name>A010101</name>
                </dataCategory>

            </dataCategory>
        </dataCategory>
    </dataCategory>

    <!-- 6. 関連オブジェクト設定 -->
    <objectUsage>
        <object>KnowledgeArticleVersion</object>
    </objectUsage>
</DataCategoryGroup>


3. 各ノードの役割と「2桁採番ルール」
--------------------------------------------------------------------------------
【ルートノード (Root Node)】
  ・XML位置： 最外層の最初の <dataCategory> （例: All_Scenes）
  ・役割： システム的な「ハンガー（入れ物）」。業務上の分類指定では使用されず、Salesforce のメタデータ文法を満たすために存在します。

【中間ノード (L1 ～ L3)】
  ・XML位置： 内部に子 <dataCategory> を持つノード
  ・役割： 画面上の「フォルダ」。ユーザーを段階的に目的のカテゴリへ誘導するための構造です。

【葉ノード (Leaf Node)】
  ・XML位置： 最内層の <dataCategory>（内部にそれ以上の子タグを持たない）
  ・役割： 最終的な「記事の分類タグ」。ナレッジ記事（FAQ）に紐付ける際に最も頻繁に使用されます。

【2桁採番ルール】
  ・L1 (第1階層): [アルファベット] + [01] ＝ 例) A01 (3桁)
  ・L2 (第2階層): [親 A01] + [01]         ＝ 例) A0101 (5桁)
  ・L3 (第3階層): [親 A0101] + [01]       ＝ 例) A010101 (7桁)
  ・L4 (第4階層): [親 A010101] + [01]     ＝ 例) A01010101 (9桁)


4. DataCategorySelection テーブルとの紐付け関係
--------------------------------------------------------------------------------
Salesforce 上でナレッジ記事とカテゴリを紐付ける Knowledge__DataCategorySelection テーブルの各項目対応：

  ・ParentId               ＝ ナレッジ記事のバージョンID (Knowledge__kav.Id)
  ・DataCategoryGroupName  ＝ XML の物理ファイル名（例: A）
  ・DataCategoryName       ＝ XML 内部の該当ノードの <name>（例: A010101）
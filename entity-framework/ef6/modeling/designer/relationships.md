---
title: リレーションシップ-EF デザイナー-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
ms.openlocfilehash: d429c39dafbf183caabdc85748c188deb8dd6f66
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415077"
---
# <a name="relationships---ef-designer"></a>リレーションシップ-EF デザイナー
> [!NOTE]
> このページでは、EF デザイナーを使用してモデル内のリレーションシップを設定する方法について説明します。 EF のリレーションシップに関する一般的な情報と、リレーションシップを使用してデータにアクセスして操作する方法については、「[リレーションシップ & ナビゲーションプロパティ](~/ef6/fundamentals/relationships.md)」を参照してください。

アソシエーションは、モデル内のエンティティ型間のリレーションシップを定義します。 このトピックでは、Entity Framework Designer (EF デザイナー) との関連付けをマップする方法について説明します。 次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。

![EF デザイナー](~/ef6/media/efdesigner.png)

> [!NOTE]
> 概念モデルをビルドすると、マップされていないエンティティとアソシエーションについての警告が [エラー一覧] に表示される場合があります。 モデルからデータベースを生成することを選択すると、エラーが解消されるため、これらの警告は無視してかまいません。

## <a name="associations-overview"></a>アソシエーションの概要

EF デザイナーを使用してモデルをデザインすると、.edmx ファイルがモデルを表します。 .Edmx ファイルでは、 **Association**要素は2つのエンティティ型間のリレーションシップを定義します。 アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。 アソシエーション end の多重度には、1 (1)、0または 1 (0 ..1)、または多数 (\*) の値を指定できます。 この情報は、2つの子**End**要素で指定されます。

実行時に、アソシエーションの一方の end にあるエンティティ型のインスタンスには、ナビゲーションプロパティまたは外部キーを使用してアクセスできます (エンティティで外部キーを公開する場合)。 外部キーが公開されている場合、エンティティ間のリレーションシップは、オブジェクト ( **Association**要素の子要素 **) を使用**して管理されます。 エンティティ内のリレーションシップに対しては、常に外部キーを公開することをお勧めします。

> [!NOTE]
> 多対多 (\*:\*) では、エンティティに外部キーを追加することはできません。 \*:\* リレーションシップでは、関連付け情報は独立したオブジェクトで管理されます。

CSDL 要素の詳細について**は、「** csdl の[仕様](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md) **」を参照**してください。

## <a name="create-and-delete-associations"></a>関連付けの作成と削除

EF デザイナーとの関連付けを作成すると、.edmx ファイルのモデルコンテンツが更新されます。 アソシエーションを作成した後、アソシエーションのマッピングを作成する必要があります (このトピックで後述します)。

> [!NOTE]
> このセクションでは、モデル間の関連付けを作成するために必要なエンティティを既に追加していることを前提としています。

### <a name="to-create-an-association"></a>アソシエーションを作成するには

1.  デザイン画面の空の領域を右クリックし、[ **新規追加**] をポイントして、[ **関連付け...** ] を選択します。
2.  **[関連付けの追加]** ダイアログボックスで、アソシエーションの設定を入力します。

    ![関連付けの追加](~/ef6/media/addassociation.png)

    > [!NOTE]
    > ナビゲーションプロパティを **クリアし、 **外部キープロパティをエンティティ型のエンティティ **型 &lt;の名前&gt; エンティティチェックボックスに追加 **することによって、ナビゲーションプロパティまたは外部キープロパティをアソシエーションの end のエンティティに追加しないことを選択できます。 ナビゲーション プロパティを 1 つだけ追加する場合、アソシエーションの検査は一方向のみ可能になります。 ナビゲーション プロパティを追加しない場合、アソシエーションの End にあるエンティティにアクセスするには、外部キー プロパティを追加する必要があります。
    
3.   **[OK]** をクリックします。

### <a name="to-delete-an-association"></a>アソシエーションを削除するには

関連付けを削除するには、次のいずれかの操作を行います。

-   EF デザイナー画面でアソシエーションを右クリックし、[ **削除**] を選択します。

- または -

-   1 つまたは複数のアソシエーションを選択し、{1}Del{2} キーを押します。

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>エンティティに外部キーのプロパティを含める (参照に関する制約)

エンティティ内のリレーションシップに対しては、常に外部キーを公開することをお勧めします。 Entity Framework は、参照に関する制約を使用して、プロパティがリレーションシップの外部キーとして機能することを識別します。

リレーションシップの作成時に [***外部キーのプロパティを &lt;エンティティ型の名前&gt; エンティティに追加する***] チェックボックスをオンにした場合は、この参照に関する制約が追加されています。

EF デザイナーを使用して参照に関する制約を追加または編集すると、EF デザイナーによって、.edmx ファイルの CSDL コンテンツ**の 要素**が追加または変更されます。

-   編集するアソシエーションをダブルクリックします。
    [ **参照制約**の ] ダイアログボックスが表示されます。
-   [ **プリンシパル** ] ドロップダウンリストから、参照制約のプリンシパルエンティティを選択します。
    エンティティのキープロパティがダイアログボックスの [ **プリンシパルキー** ] の一覧に追加されます。
-   [ **依存** ] ドロップダウンリストから、参照制約の依存エンティティを選択します。
-   依存キーを持つプリンシパルキーごとに、[ **依存キー** ] 列のドロップダウンリストから対応する依存キーを選択します。

    ![Ref 制約](~/ef6/media/refconstraint.png)

-    **[OK]** をクリックします。

## <a name="create-and-edit-association-mappings"></a>アソシエーションのマッピングを作成および編集する

EF デザイナーの [**マッピングの詳細** ] ウィンドウで、アソシエーションをデータベースにマップする方法を指定できます。

> [!NOTE]
> 参照制約が指定されていないアソシエーションについてのみ、詳細をマップできます。 参照制約が指定されている場合は、外部キープロパティがエンティティに含まれます。また、エンティティのマッピング詳細を使用して、外部キーのマップ先の列を制御できます。

### <a name="create-an-association-mapping"></a>アソシエーションのマッピングを作成する

-   デザイン画面でアソシエーションを右クリックし、[ **テーブルマッピング**] を選択します。
    これにより、[マッピングの **詳細** ] ウィンドウにアソシエーションのマッピングが表示されます。
-   [ **テーブルまたはビューの追加] を**クリックします。
    ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。
-   アソシエーションがマップされるテーブルを選択します。
    [ **マッピングの詳細** ] ウィンドウには、アソシエーションの両端と、各**End**のエンティティ型のキープロパティが表示されます。
-   キープロパティごとに、[ **列** ] フィールドをクリックし、プロパティがマップされる列を選択します。

    ![マッピングの詳細4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>アソシエーションのマッピングを編集する

-   デザイン画面でアソシエーションを右クリックし、[ **テーブルマッピング**] を選択します。
    これにより、[マッピングの **詳細** ] ウィンドウにアソシエーションのマッピングが表示されます。
-   [マップ] をクリックして **、テーブル名&gt;&lt;** します。
    ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。
-   アソシエーションがマップされるテーブルを選択します。
    [ **マッピングの詳細** ] ウィンドウには、アソシエーションの両端と、各 End のエンティティ型のキープロパティが表示されます。
-   キープロパティごとに、[ **列** ] フィールドをクリックし、プロパティがマップされる列を選択します。

## <a name="edit-and-delete-navigation-properties"></a>ナビゲーションプロパティの編集と削除

ナビゲーションプロパティは、モデル内のアソシエーションの end でエンティティを検索するために使用されるショートカットプロパティです。 ナビゲーション プロパティは、2 つのエンティティ型の間のアソシエーションを作成するときに作成できます。

#### <a name="to-edit-navigation-properties"></a>ナビゲーションプロパティを編集するには

-   EF デザイナー画面でナビゲーションプロパティを選択します。
    ナビゲーションプロパティに関する情報は、Visual Studio の [ **プロパティ** ] ウィンドウに表示されます。
-   [ **プロパティ** ] ウィンドウでプロパティの設定を変更します。

#### <a name="to-delete-navigation-properties"></a>ナビゲーションプロパティを削除するには

-   外部キーが概念モデルのエンティティ型で公開されない場合、ナビゲーション プロパティを削除すると、対応するアソシエーションの検査が一方向のみ可能になるか、またはまったく検査できなくなる可能性があります。
-   EF デザイナー画面でナビゲーションプロパティを右クリックし、[ **削除**] を選択します。

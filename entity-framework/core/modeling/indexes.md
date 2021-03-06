---
title: インデックス-EF Core
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 810fccc0c6b035f515107601b245811f7b4118a6
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413937"
---
# <a name="indexes"></a>インデックス

インデックスは、多くのデータストアで共通の概念です。 データストアでの実装は異なる場合がありますが、列または列のセットに基づいて参照を作成するために使用されます。

データ注釈を使用してインデックスを作成することはできません。 Fluent API を使用して、次のように1つの列にインデックスを指定できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

複数の列に対してインデックスを指定することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> 規則により、外部キーとして使用される各プロパティ (またはプロパティのセット) にインデックスが作成されます。
>
> EF Core では、個別のプロパティセットごとに1つのインデックスのみがサポートされます。 Fluent API を使用して、既にインデックスが定義されている一連のプロパティ (規則または以前の構成) のインデックスを構成する場合は、そのインデックスの定義を変更します。 これは、規則によって作成されたインデックスをさらに構成する場合に便利です。

## <a name="index-uniqueness"></a>インデックスの一意性

既定では、インデックスは一意ではありません。複数の行がインデックスの列セットに対して同じ値を持つことができます。 次のように、インデックスを一意にすることができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

インデックスの列セットに同じ値を持つ複数のエンティティを挿入しようとすると、例外がスローされます。

## <a name="index-name"></a>インデックス名

規則により、リレーショナルデータベースに作成されたインデックスの名前は `IX_<type name>_<property name>`になります。 複合インデックスの場合、`<property name>` は、アンダースコアで区切られたプロパティ名のリストになります。

Fluent API を使用して、データベースに作成されたインデックスの名前を設定できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a>インデックスフィルター

一部のリレーショナルデータベースでは、フィルター処理されたインデックスまたは部分的なインデックスを指定できます。 これにより、列の値のサブセットにのみインデックスを作成し、インデックスのサイズを小さくして、パフォーマンスとディスク領域の使用率を向上させることができます。 フィルター選択されたインデックス SQL Server の詳細については、[のドキュメントを参照してください](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes)。

Fluent API を使用して、SQL 式として指定されたインデックスにフィルターを指定できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

SQL Server プロバイダー EF を使用すると、一意のインデックスの一部である null 許容型のすべての列に対して `'IS NOT NULL'` フィルターが追加されます。 この規則をオーバーライドするには、`null` 値を指定します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>[付加列]

一部のリレーショナルデータベースでは、インデックスに含まれるが、"キー" の一部ではない列のセットを構成できます。 これにより、テーブル自体にアクセスする必要がないため、クエリ内のすべての列がキー列または非キー列としてインデックスに含まれる場合、クエリのパフォーマンスが大幅に向上します。 付加列 SQL Server の詳細については、[のドキュメントを参照してください](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)。

次の例では、`Url` 列がインデックスキーの一部であるため、その列に対するクエリフィルター処理でインデックスを使用できます。 さらに、`Title` および `PublishedOn` 列のみにアクセスするクエリは、テーブルにアクセスする必要がなく、より効率的に実行されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]

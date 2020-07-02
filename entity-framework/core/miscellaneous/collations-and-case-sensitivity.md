---
title: 照合順序と大文字小文字の区別-EF Core
description: データベースとクエリで照合順序と大文字と小文字の区別を構成する方法
author: roji
ms.date: 04/27/2020
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/collations-and-case-sensitivity
ms.openlocfilehash: b3874847922cb39aa57d50813e6e50ff7db72eb9
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370566"
---
# <a name="collations-and-case-sensitivity"></a>照合順序と大文字と小文字の区別

> [!NOTE]
> この機能は EF Core 5.0 で導入されています。

データベースでのテキスト処理は複雑になる可能性があり、それには問題があると考えられるより多くのユーザーの注意が必要です。 1つの点として、データベースはテキストの処理方法に大きく異なります。たとえば、一部のデータベースでは既定で大文字と小文字が区別されますが (例: Sqlite, PostgreSQL)、他のデータベースでは大文字と小文字が区別されません (SQL Server、MySQL)。 また、インデックスの使用により、大文字と小文字の区別や類似した側面がクエリのパフォーマンスに大きく影響する可能性があります。これにより、大文字と小文字を区別するデータベースで大文字と小文字を区別し `string.Lower` ない比較を強制的に実行でき、アプリケーションでインデックスを使用できなくなる可能性があります。 このページでは、大文字と小文字の区別を構成する方法、一般的な照合順序、およびクエリのパフォーマンスを低下させずに効率的に行う方法について詳しく説明します。

## <a name="introduction-to-collations"></a>照合順序の概要

テキスト処理の基本的な概念は*照合順序*です。これは、テキスト値の順序と等価性を比較する方法を決定する一連のルールです。 たとえば、大文字と小文字を区別しない照合順序では、等値比較の目的で大文字と小文字の違いが無視されますが、大文字と小文字を区別する照合順序では区別されません。 ただし、大文字と小文字の区別はカルチャに依存しているため (たとえば `i` 、と `I` はトルコ語で異なる文字を表します)、大文字と小文字を区別しない照合順序が複数存在し、それぞれに独自のルールセットがあります。 照合順序のスコープは、文字データのその他の側面にも大文字と小文字の区別を加えたものです。たとえば、ドイツ語では、 `ä` とを `ae` 同一として扱うことが望ましい場合があります (常にではありません)。 最後に、照合順序では、テキスト値の*順序付け*方法も定義されています。の後、ドイツ語の `ä` `a` 場合、スウェーデン語はアルファベットの最後に配置されます。

データベース内のすべてのテキスト操作では、照合順序を使用して、明示的にも暗黙的にも、操作で文字列を比較および順序付けする方法を決定します。 使用可能な照合順序とその名前付けスキームの実際の一覧は、データベースに固有のものです。さまざまなデータベースの関連ドキュメントページへのリンクについて[は、以下のセクション](#database-specific-information)を参照してください。 データベースでは、通常、データベースレベルまたは列レベルで既定の照合順序を定義したり、クエリの特定の操作に使用する照合順序を明示的に指定したりすることができます。

## <a name="database-collation"></a>データベース照合順序

ほとんどのデータベースシステムでは、既定の照合順序はデータベースレベルで定義されています。オーバーライドしない限り、その照合順序は、そのデータベース内で発生するすべてのテキスト操作に暗黙的に適用されます。 データベースの照合順序は、通常、データベースの作成時に (DDL ステートメントを使用して `CREATE DATABASE` ) 設定します。指定しない場合、既定では、セットアップ時に決定されるサーバーレベルの値が既定値に設定されます。 たとえば、SQL Server の既定のサーバーレベルの照合順序は `SQL_Latin1_General_CP1_CI_AS` 、大文字と小文字を区別しない、アクセントを区別する照合順序であるです。 データベースシステムでは、通常、既存のデータベースの照合順序の変更が許可されますが、そのためには複雑になる可能性があります。データベースを作成する前に照合順序を選択することをお勧めします。

EF Core 移行を使用してデータベーススキーマを管理する場合、モデルのメソッドの次の例では、 `OnModelCreating` 大文字と小文字を区別する照合順序を使用するように SQL Server データベースを構成します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=40)]

## <a name="column-collation"></a>列の照合順序

また、テキスト列に対して照合順序を定義して、データベースの既定値を上書きすることもできます。 これは、特定の列で大文字と小文字を区別する必要があり、データベースの残りの部分で大文字と小文字を区別する必要がある場合に便利です。

EF Core 移行を使用してデータベーススキーマを管理する場合、次の例では、 `Name` 大文字と小文字を区別するように構成されているデータベースで、プロパティの列が大文字と小文字を区別しないように構成されています。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=OnModelCreating&highlight=6)]

## <a name="explicit-collation-in-a-query"></a>クエリ内の明示的な照合順序

場合によっては、クエリごとに異なる照合順序を使用して同じ列を照会する必要があります。 たとえば、あるクエリでは、列に対して大文字と小文字を区別した比較を実行する必要がありますが、同じ列で大文字と小文字を区別しない比較を実行することが必要になる場合があります。 これは、クエリ内で照合順序を明示的に指定することで実現できます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

これに `COLLATE` より、列レベルまたはデータベースレベルで定義されている照合順序に関係なく、大文字と小文字を区別する照合順序を適用する句が SQL クエリに生成されます。

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>明示的な照合順序とインデックス

インデックスは、データベースのパフォーマンスにおいて最も重要な要素の1つです。インデックスを使用して効率的に実行されるクエリは、そのインデックスを使用せずに停止をグリッドことがあります。 インデックスは、列の照合順序を暗黙的に継承します。つまり、クエリで異なる照合順序が指定されていない場合、列に対するすべてのクエリが、その列に定義されているインデックスを使用できるようになります。 クエリで明示的な照合順序を指定すると、照合順序が一致しなくなるため、通常はそのクエリでその列に定義されているインデックスを使用できなくなります。このため、この機能を使用する場合は注意が必要です。 常に、列 (またはデータベース) レベルで照合順序を定義することをお勧めします。これにより、すべてのクエリが暗黙的にその照合順序を使用し、任意のインデックスの恩恵を受けることができます。

一部のデータベースでは、インデックスの作成時に照合順序を定義できることに注意してください (PostgreSQL、Sqlite など)。 これにより、複数のインデックスが同じ列に定義され、異なる照合順序で操作が高速化されます (大文字と小文字を区別し、大文字と小文字を区別しない比較など)。 詳細については、データベースプロバイダーのドキュメントを参照してください。

> [!WARNING]
> クエリのクエリプランを常に検査し、大量のデータに対して実行されるパフォーマンスに重要なクエリで適切なインデックスが使用されていることを確認してください。 (またはを呼び出して) クエリの大文字と小文字の区別をオーバーライド `EF.Functions.Collate` `string.ToLower` すると、アプリケーションのパフォーマンスに非常に大きな影響を与える可能性があります。

## <a name="translation-of-built-in-net-string-operations"></a>組み込みの .NET 文字列操作の変換

.NET では、文字列の等価性が既定で大文字と小文字が区別されます。では `s1 == s2` 、文字列が同一であることを必要とする序数の比較が実行されます。 データベースの既定の照合順序は異なるため、インデックスを使用するには単純な等価性が必要であるため、EF Core はデータベースの大文字と小文字を区別する操作に単純な等価性を変換しようとしません。 C# の等値は、使用されている特定のデータベースとその照合順序の構成に応じて、大文字と小

さらに、.NET は [`string.Equals`](https://docs.microsoft.com/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) 列挙型を受け入れるオーバーロードを提供し [`StringComparison`](https://docs.microsoft.com/dotnet/api/system.stringcomparison) ます。これにより、比較に大文字と小文字の区別とカルチャを指定できます。 仕様により、これらのオーバーロードを SQL に変換することによって EF Core し、それらを使用しようとすると、例外が発生します。 1つの点として、EF Core では、大文字と小文字を区別しない照合順序を使用する必要があります。 さらに重要なこととして、照合順序を適用すると、ほとんどの場合、インデックスの使用が妨げられ、非常に基本的で一般的に使用される .NET コンストラクトのパフォーマンスに大きく影響します。 クエリで大文字と小文字を区別するか、大文字と小文字を区別しない比較を使用するには、前述のようにを使用して明示的に照合順序を指定し `EF.Functions.Collate` ます[detailed above](#explicit-collations-and-indexes)

## <a name="database-specific-information"></a>データベース固有の情報

* [照合順序に関するドキュメントを SQL Server](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support)します。
* [照合順序に関する Microsoft Data Sqlite ドキュメント](https://docs.microsoft.com/dotnet/standard/data/sqlite/collation)。
* [照合順序に関する PostgreSQL ドキュメント](https://www.postgresql.org/docs/current/collation.html)。
* [照合順序に関する MySQL ドキュメント](https://dev.mysql.com/doc/refman/en/charset-general.html)。
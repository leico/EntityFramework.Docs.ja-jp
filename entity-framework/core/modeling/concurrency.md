---
title: 同時実行トークン-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: bfeb611f222f7195fe22d920b452b40cc4addf90
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414621"
---
# <a name="concurrency-tokens"></a>コンカレンシー トークン

> [!NOTE]
> このページでは、同時実行トークンの構成方法について説明します。 EF Core での同時実行制御のしくみと、アプリケーションで同時実行の競合を処理する方法の例については、「[同時実行の競合の処理](../saving/concurrency.md)」を参照してください。

同時実行トークンとして構成されたプロパティは、オプティミスティック同時実行制御を実装するために使用されます。

## <a name="configuration"></a>構成

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Timestamp/rowversion

Timestamp/rowversion は、行が挿入または更新されるたびに、データベースによって新しい値が自動的に生成されるプロパティです。 また、プロパティは同時実行トークンとして扱われるので、更新しようとしている行がクエリ後に変更された場合に例外が発生することが保証されます。 正確な詳細は、使用されているデータベースプロバイダーによって異なります。SQL Server では、通常、 *byte []* プロパティを使用します。これは、データベースの*ROWVERSION*列として設定されます。

次のように、プロパティを timestamp/rowversion として構成できます。

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***

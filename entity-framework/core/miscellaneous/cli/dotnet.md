---
title: EF Core ツールリファレンス (.NET CLI)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 8ff2f3481c7f0c255def3272ca53370faba33e95
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238178"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Entity Framework Core ツールリファレンス-.NET Core CLI

Entity Framework Core 用のコマンドラインインターフェイス (CLI) ツールは、デザイン時の開発タスクを実行します。 たとえば、既存のデータベースに基づいて、[移行](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)を作成し、移行を適用し、モデルのコードを生成します。 コマンドは、 [.NET Core SDK](https://www.microsoft.com/net/core)の一部であるクロスプラットフォーム[dotnet](/dotnet/core/tools)コマンドの拡張機能です。 これらのツールは、.NET Core プロジェクトで動作します。

Visual Studio を使用している場合は、代わりに[パッケージマネージャーコンソールツール](powershell.md)を使用することをお勧めします。

* **パッケージマネージャーコンソール**で選択した現在のプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要はありません。
* コマンドが完了すると、コマンドによって生成されたファイルが自動的に開かれます。

## <a name="installing-the-tools"></a>ツールのインストール

インストール手順は、プロジェクトの種類とバージョンによって異なります。

* EF Core 3.x
* ASP.NET Core バージョン2.1 以降
* EF Core 2.x
* EF Core 1.x

### <a name="ef-core-3x"></a>EF Core 3.x

* `dotnet ef`は、グローバルまたはローカルのツールとしてインストールする必要があります。 ほとんどの開発者は `dotnet ef` 、次のコマンドを使用して、をグローバルツールとしてインストールします。

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  を `dotnet ef` ローカルツールとして使用することもできます。 ローカルツールとして使用するには、[ツールマニフェストファイル](https://github.com/dotnet/cli/issues/10288)を使用して、ツールの依存関係として宣言するプロジェクトの依存関係を復元します。

* [.NET Core SDK](https://www.microsoft.com/net/download/core) のインストール。

* 最新のパッケージをインストール `Microsoft.EntityFrameworkCore.Design` します。

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 以降

* 現在の[.NET Core SDK](https://www.microsoft.com/net/download/core)をインストールします。 SDK は、Visual Studio 2017 の最新バージョンがある場合でもインストールする必要があります。

  `Microsoft.EntityFrameworkCore.Design`パッケージは[AspNetCore メタパッケージ](/aspnet/core/fundamentals/metapackage-app)に含まれているため、これは ASP.NET Core 2.1 以降で必要なことです。

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2.x (ASP.NET Core ではありません)

`dotnet ef`コマンドは .NET Core SDK に含まれていますが、パッケージをインストールするために必要なコマンドを有効にし `Microsoft.EntityFrameworkCore.Design` ます。

* 現在の[.NET Core SDK](https://www.microsoft.com/net/download/core)をインストールします。 最新バージョンの Visual Studio がインストールされている場合でも、SDK をインストールする必要があります。

* 最新の安定したパッケージをインストール `Microsoft.EntityFrameworkCore.Design` します。

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1.x

* .NET Core SDK バージョン2.1.200 をインストールします。 以降のバージョンは、EF Core 1.0 および1.1 の CLI ツールと互換性がありません。

* ファイル[のglobal.js](/dotnet/core/tools/global-json)を変更して、2.1.200 SDK バージョンを使用するようにアプリケーションを構成します。 通常、このファイルはソリューションディレクトリ (プロジェクトの1つ上) に含まれています。

* プロジェクトファイルを編集し、 `Microsoft.EntityFrameworkCore.Tools.DotNet` アイテムとしてを追加し `DotNetCliToolReference` ます。 最新の1.x バージョンを指定します (例: 1.1.6)。 このセクションの最後にあるプロジェクトファイルの例を参照してください。

* パッケージの最新バージョン1.x をインストールし `Microsoft.EntityFrameworkCore.Design` ます。次に例を示します。

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  両方のパッケージ参照を追加すると、プロジェクトファイルは次のようになります。

  ``` xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>netcoreapp1.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                        Version="1.1.6"
                         PrivateAssets="All" />
    </ItemGroup>
    <ItemGroup>
       <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                              Version="1.1.6" />
    </ItemGroup>
  </Project>
  ```

  を含むパッケージ参照は、 `PrivateAssets="All"` このプロジェクトを参照するプロジェクトに公開されません。 この制限は、開発時にのみ使用されるパッケージに特に役立ちます。

### <a name="verify-installation"></a>インストールの検証

次のコマンドを実行して EF Core CLI ツールが正しくインストールされていることを確認します。

  ```dotnetcli
  dotnet restore
  dotnet ef
  ```

コマンドからの出力は、使用されているツールのバージョンを識別します。

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="updating-the-tools"></a>ツールの更新

を使用して、 `dotnet tool update --global dotnet-ef` グローバルツールを利用可能な最新バージョンに更新します。プロジェクトにローカルにインストールしたツールを使用する場合は、を使用 `dotnet tool update dotnet-ef` します。 コマンドにを追加して、特定のバージョンをインストールし `--version <VERSION>` ます。 詳細については、dotnet tool のドキュメントの[更新](/dotnet/core/tools/dotnet-tool-update)に関するセクションを参照してください。

## <a name="using-the-tools"></a>ツールの使用

ツールを使用する前に、スタートアッププロジェクトを作成するか、環境を設定することが必要になる場合があります。

### <a name="target-project-and-startup-project"></a>ターゲットプロジェクトとスタートアッププロジェクト

これらのコマンドは、*プロジェクト*と*スタートアッププロジェクト*を参照します。

* *プロジェクト*は*ターゲットプロジェクト*とも呼ばれます。これは、コマンドによってファイルが追加または削除されるためです。 既定では、現在のディレクトリ内のプロジェクトはターゲットプロジェクトです。 オプションを使用して、別のプロジェクトをターゲットプロジェクトとして指定でき <nobr>`--project`</nobr> ます。

* *スタートアッププロジェクト*は、ツールをビルドして実行するためのものです。 このツールでは、デザイン時にアプリケーションコードを実行して、プロジェクトに関する情報 (データベース接続文字列やモデルの構成など) を取得する必要があります。 既定では、現在のディレクトリのプロジェクトはスタートアッププロジェクトです。 オプションを使用して、別のプロジェクトをスタートアッププロジェクトとして指定でき <nobr>`--startup-project`</nobr> ます。

多くの場合、スタートアッププロジェクトとターゲットプロジェクトは同じプロジェクトです。 個別のプロジェクトである一般的なシナリオは、次のような場合です。

* EF Core コンテキストとエンティティクラスは、.NET Core クラスライブラリにあります。
* .NET Core コンソールアプリまたは web アプリはクラスライブラリを参照します。

また、[移行コードを EF Core コンテキストとは別のクラスライブラリに配置](xref:core/managing-schemas/migrations/projects)することもできます。

### <a name="other-target-frameworks"></a>その他のターゲットフレームワーク

CLI ツールは、.NET Core プロジェクトと .NET Framework プロジェクトで使用できます。 .NET Standard クラスライブラリに EF Core モデルがあるアプリには、.NET Core または .NET Framework プロジェクトがない可能性があります。 たとえば、これは Xamarin とユニバーサル Windows プラットフォームアプリに当てはまります。 このような場合は、ツールのスタートアッププロジェクトとして機能することだけを目的とした .NET Core コンソールアプリプロジェクトを作成できます。 プロジェクトは、実際のコードを持たないダミープロジェクトにすることができ &mdash; ます。これは、ツールのターゲットを指定するためにのみ必要です。

ダミープロジェクトが必要な理由 前述のように、ツールはデザイン時にアプリケーションコードを実行する必要があります。 そのためには、.NET Core ランタイムを使用する必要があります。 EF Core モデルが .NET Core または .NET Framework を対象とするプロジェクト内にある場合、EF Core ツールはプロジェクトからランタイムを借用します。 EF Core モデルが .NET Standard クラスライブラリ内にある場合は、これを行うことはできません。 .NET Standard は実際の .NET 実装ではありません。これは、.NET 実装がサポートする必要のある一連の Api の仕様です。 したがって、EF Core ツールでアプリケーションコードを実行するために .NET Standard は十分ではありません。 スタートアッププロジェクトとして使用するために作成するダミープロジェクトは、ツールが .NET Standard クラスライブラリを読み込むことができる具象ターゲットプラットフォームを提供します。

### <a name="aspnet-core-environment"></a>ASP.NET Core 環境

ASP.NET Core プロジェクトの環境を指定するには、コマンドを実行する前に**ASPNETCORE_ENVIRONMENT**環境変数を設定します。

## <a name="common-options"></a>共通オプション

| オプション                                         | Short             | 説明                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | JSON 出力を表示します。                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | 使用する `DbContext` クラス。 クラス名のみ、または名前空間で完全修飾されています。  このオプションを省略した場合、EF Core によってコンテキストクラスが検索されます。 複数のコンテキストクラスがある場合は、このオプションが必要です。                                            |
| `--project <PROJECT>`                          | `-p`              | ターゲットプロジェクトのプロジェクトフォルダーへの相対パス。  既定値は現在のフォルダーです。                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | スタートアッププロジェクトのプロジェクトフォルダーへの相対パス。 既定値は現在のフォルダーです。                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | [ターゲットフレームワーク](/dotnet/standard/frameworks)の[ターゲットフレームワークモニカー](/dotnet/standard/frameworks#supported-target-framework-versions) 。  プロジェクトファイルで複数のターゲットフレームワークを指定し、そのうちの1つを選択する場合は、を使用します。 |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | ビルド構成 (たとえば、 `Debug` または) `Release` 。                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | パッケージを復元する対象のランタイムの識別子。 ランタイム ID (RID) の一覧については、[RID カタログ](/dotnet/core/rid-catalog)に関するページをご覧ください。                                                                                                      |
| `--help`                                       | `-h`              | ヘルプ情報を表示します。                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | 詳細出力を表示します。                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | 出力を色分けしません。                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | レベルのプレフィックス出力。                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>dotnet ef データベースの削除

データベースを削除します。

オプション:

| オプション                   | Short             | 説明                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | 確認しないでください。                                           |
| <nobr>`--dry-run`</nobr> |                   | 削除するデータベースを表示しますが、削除はしません。 |

## <a name="dotnet-ef-database-update"></a>dotnet ef データベースの更新

最後に移行したデータベース、または指定した移行にデータベースを更新します。

引数:

| 引数                   | 説明                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | ターゲットの移行。 移行は、名前または ID で識別できます。 数値0は、*最初の移行の前に*特別なケースであり、すべての移行が元に戻されます。 移行が指定されていない場合、コマンドは既定で最後の移行になります。 |

オプション:

| オプション                                    | 説明                                                                                                                                        |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | データベースへの接続文字列。 既定値は、またはで指定されたもの `AddDbContext` `OnConfiguring` です。 (EF Core 5.0.0 以降から利用できます。) |

次の例では、指定された移行にデータベースを更新します。 最初のは移行名を使用し、2つ目のは移行 ID と指定された接続を使用します。

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>dotnet ef dbcontext 情報

型に関する情報を取得し `DbContext` ます。

## <a name="dotnet-ef-dbcontext-list"></a>dotnet ef dbcontext の一覧

使用可能な種類が一覧表示さ `DbContext` れます。

## <a name="dotnet-ef-dbcontext-scaffold"></a>dotnet ef dbcontext スキャフォールディング

`DbContext`データベースのおよびエンティティ型のコードを生成します。 このコマンドでエンティティ型を生成するには、データベーステーブルに主キーが必要です。

引数:

| 引数                    | 説明                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | データベースへの接続文字列。 ASP.NET Core 2.x プロジェクトの場合、値には*name = \<name of connection string> *を指定できます。 その場合、プロジェクト用に設定されている構成ソースから名前を取得します。 |
| `<PROVIDER>`                | 使用するプロバイダー。 通常、これは NuGet パッケージの名前です (例:) `Microsoft.EntityFrameworkCore.SqlServer` 。                                                                                           |

オプション:

| オプション                                   | Short             | 説明                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | 属性を使用してモデルを構成します (可能な場合)。 このオプションを省略した場合は、fluent API のみが使用されます。                                                                |
| `--context <NAME>`                       | `-c`              | `DbContext`生成するクラスの名前。                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | クラスファイルを格納するディレクトリ `DbContext` 。 パスは、プロジェクトディレクトリに対する相対パスです。 名前空間は、フォルダー名から派生します。                                 |
| `--context-namespace <NAMESPACE>`        |                   | 生成されたクラスに使用する名前空間 `DbContext` 。 注: はオーバーライドさ `--namespace` れます。 (EFCore 5.0.0 以降から利用できます。)                                                |
| `--force`                                | `-f`              | 既存のファイルを上書きします。                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | エンティティクラスファイルを配置するディレクトリ。 パスは、プロジェクトディレクトリに対する相対パスです。                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | 生成されたすべてのクラスに使用する名前空間。 既定値は、ルート名前空間と出力ディレクトリから生成されます。 (EFCore 5.0.0 以降から利用できます。)                 |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | エンティティ型を生成するテーブルのスキーマ。 複数のスキーマを指定するには、それぞれのスキーマを繰り返し `--schema` ます。 このオプションを省略した場合、すべてのスキーマが含まれます。          |
| `--table <TABLE_NAME>`...                | `-t`              | エンティティ型を生成するテーブル。 複数のテーブルを指定するに `-t` は、 `--table` 1 つのテーブルに対してまたはを繰り返します。 このオプションを省略した場合、すべてのテーブルが含まれます。                |
| `--use-database-names`                   |                   | テーブル名と列名は、データベースに表示されるとおりに使用します。 このオプションを省略した場合、データベース名は、C# の名前のスタイル規則により厳密に準拠するように変更されます。 |
| `--no-onconfiguring`                     |                   | 生成されたクラスのメソッドの生成を抑制し `OnConfiguring` `DbContext` ます。 (EFCore 5.0.0 以降から利用できます。)                                                 |

次の例では、すべてのスキーマとテーブルをスキャフォールディングし、新しいファイルを [*モデル*] フォルダーに配置します。

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

次の例では、選択したテーブルのみをスキャフォールディングし、指定された名前と名前空間を持つ別のフォルダーにコンテキストを作成します。

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

## <a name="dotnet-ef-migrations-add"></a>dotnet ef 移行の追加

新しい移行を追加します。

引数:

| 引数              | 説明                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | 移行の名前。 |

オプション:

| オプション                                 | Short             | 説明                                                                                                                             |
|:---------------------------------------|:------------------|:----------------------------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | ファイルの出力に使用するディレクトリ。 パスは、ターゲットプロジェクトディレクトリに対する相対パスです。 既定値は "移行" です。                    |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | 生成されたクラスに使用する名前空間。 既定値は、出力ディレクトリから生成されます。 (EFCore 5.0.0 以降から利用できます。) |

## <a name="dotnet-ef-migrations-list"></a>dotnet ef 移行リスト

使用可能な移行を一覧表示します。

## <a name="dotnet-ef-migrations-remove"></a>dotnet ef 移行の削除

最後の移行を削除します (移行のために実行されたコード変更をロールバックします)。

オプション:

| オプション                 | Short             | 説明                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | 移行を元に戻します (データベースに適用された変更をロールバックします)。 |

## <a name="dotnet-ef-migrations-script"></a>dotnet ef 移行スクリプト

移行から SQL スクリプトを生成します。

引数:

| 引数              | 説明                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | 移行を開始しています。 移行は、名前または ID で識別できます。 数値0は、*最初の移行の前に*特別なケースです。 既定値は 0 です。 |
| `<TO>`                | 移行を終了しています。 既定では、最後の移行になります。                                                                                                         |

オプション:

| オプション                         | Short             | 説明                                                        |
|:-------------------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | スクリプトの書き込み先のファイル。                                   |
| `--idempotent`                 | `-i`              | 任意の移行時にデータベースで使用できるスクリプトを生成します。 |

次の例では、InitialCreate 移行用のスクリプトを作成します。

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

次の例では、InitialCreate 移行後にすべての移行用のスクリプトを作成します。

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>その他のリソース

* [移行](xref:core/managing-schemas/migrations/index)
* [リバースエンジニアリング](xref:core/managing-schemas/scaffolding)

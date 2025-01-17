---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 08/16/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ce29cd03de46e1d93d7f1f28f9f5184cd59a57e7
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "114668242"
---
### <a name="create-a-console-application"></a>コンソール アプリケーションを作成する

リレーの作成時に [クライアント認証が必要] オプションを無効にしていた場合には、ブラウザーを問わずハイブリッド接続 URL に要求を送信できます。 保護されているエンドポイントにアクセスするには、ここに示したように `ServiceBusAuthorization` ヘッダーでトークンを作成して渡す必要があります。

Visual Studio で、新しい **コンソール アプリ (.NET Framework)** プロジェクトを作成します。

### <a name="add-the-relay-nuget-package"></a>Relay NuGet パッケージを追加する

1. 新しく作成したプロジェクトを右クリックしてから、**[NuGet パッケージの管理]** を選択します。
2. **[プレリリースを含める]** オプションを選択します。 
3. **[参照]** を選択し、**Microsoft.Azure.Relay** を検索します。 検索結果から、"**Microsoft Azure Relay**" を選択します。
4. バージョンには、**[2.0.0-preview1-20180523]** を選択します。 
5. **[インストール]** を選択してインストールを完了します。 ダイアログ ボックスを閉じます。

### <a name="write-code-to-send-requests"></a>要求を送信するコードを記述する

1. Program.cs ファイルの先頭にある既存の `using` ステートメントを、次の `using` ステートメントに置き換えます。
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net.Http;
    using Microsoft.Azure.Relay;
    ```
2. ハイブリッド接続の詳細に関する定数を `Program` クラスに追加します。 中かっこ内のプレースホルダーを、ハイブリッド接続の作成時に取得した値に置き換えます。 必ず完全修飾名前空間名を使用してください。
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. 次のメソッドを `Program` クラスに追加します。
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());        Console.ReadLine();
    }
    ```
4. `Program` クラスの `Main` メソッドに次のコード行を追加します。
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Program.cs は次のようになります。
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net.Http;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```


---
title: 要求に対応するアプリ - Azure Active Directory アプリケーション プロキシ
description: AD FS 要求を受け入れてユーザーの安全なリモート アクセスを実現するオンプレミス ASP.NET アプリケーションを発行する方法。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 50f0f4ead9b44da13eb2a45a96db1caed81a5471
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990009"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>アプリケーション プロキシで要求に対応するアプリケーションを利用する
[要求に対応するアプリ](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85))は、セキュリティ トークン サービス (STS) へのリダイレクトを実行します。 STS は、トークンと引き換えにユーザーの資格情報を要求し、アプリケーションにユーザーをリダイレクトします。 アプリケーション プロキシをこれらのリダイレクトに対応させる方法はいくつかあります。 この記事の手順に従って、要求に対応するアプリのデプロイを構成します。 

## <a name="prerequisites"></a>前提条件
要求に対応するアプリのリダイレクト先の STS が、オンプレミス ネットワークの外部でも使用できるようになっていることを確認してください。 そのためには、STS をプロキシを通じて公開するか、外部接続を許可します。 

## <a name="publish-your-application"></a>アプリケーションの発行

1. 「 [アプリケーション プロキシを使用したアプリケーションの発行](../app-proxy/application-proxy-add-on-premises-application.md)」で説明されている手順に従って、アプリケーションを発行します。
2. ポータルのアプリケーション ページに移動し、 **[シングル サインオン]** を選択します。
3. **[事前認証方法]** として **[Azure Active Directory]** を選択した場合は、 **[内部認証方法]** として **[Azure AD シングル サインオンが無効]** を選択してください。 **[事前認証方法]** として **[パススルー]** を選択した場合は、何も変更する必要はありません。

## <a name="configure-adfs"></a>ADFS の構成

要求に対応するアプリの AD FS を構成する方法は 2 つあります。 1 つはカスタム ドメインを使用する方法で、 もう 1 つは WS-Federation を使用する方法です。 

### <a name="option-1-custom-domains"></a>方法 1: カスタム ドメイン

アプリケーションのすべての内部 URL が完全修飾ドメイン名 (FQDN) になっている場合は、アプリケーションの[カスタム ドメイン](application-proxy-configure-custom-domain.md)を構成することができます。 このカスタム ドメインを使用して、内部 URL と同じ外部 URL を作成できます。 外部 URL が内部 URL と一致すると、ユーザーがオンプレミスかリモートかに関わらず、STS リダイレクト機能が作動します。 

### <a name="option-2-ws-federation"></a>方法 2: WS-Federation

1. [ADFS 管理] を開きます。
2. **[証明書利用者信頼]** に移動し、アプリケーション プロキシで発行しているアプリを右クリックして、 **[プロパティ]** を選択します。  

   ![[証明書利用者信頼] でアプリ名を右クリックしているスクリーンショット](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. **[エンドポイント]** タブの **[エンドポイントの種類]** で、 **[WS-Federation]** を選択します。
4. **[信頼された URL]** で、アプリケーション プロキシの **[外部 URL]** で入力した URL を入力し、 **[OK]** をクリックします。  

   ![エンドポイントを追加し、信頼された URL 値を設定しているスクリーン ショット](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>次のステップ
* [ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする](application-proxy-configure-native-client-application.md)
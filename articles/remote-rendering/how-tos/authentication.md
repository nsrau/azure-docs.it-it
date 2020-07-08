---
title: Authentication
description: Illustra il funzionamento dell'autenticazione
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 8f3b144a7790c3122d59d27183b3037998ddadd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565844"
---
# <a name="configure-authentication"></a>Configurare l'autenticazione

Il rendering remoto di Azure usa lo stesso meccanismo di autenticazione di [Azure Spatial Anchor (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Per chiamare correttamente le API REST, i client devono impostare *uno* dei seguenti elementi:

* **AccountKey**: può essere ottenuto nella scheda "chiavi" per l'account di rendering remoto nel portale di Azure. Le chiavi dell'account sono consigliate solo per lo sviluppo e la creazione di prototipi.
    ![ID account](./media/azure-account-primary-key.png)

* **AuthenticationToken**: è un token di Azure ad, che può essere ottenuto tramite la [libreria MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-overview). Sono disponibili più flussi diversi per accettare le credenziali utente e usare tali credenziali per ottenere un token di accesso.

* **MRAccessToken**: è un token Mr, che può essere ottenuto dal servizio token di sicurezza di Azure Mixed Reality (STS). Recuperato dall' `https://sts.mixedreality.azure.com` endpoint usando una chiamata REST simile alla chiamata seguente:

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Dove l'intestazione di autorizzazione è formattata come segue: `Bearer <Azure_AD_token>` o `Bearer <accoundId>:<accountKey>` . Il primo è preferibile per la sicurezza. Il token restituito da questa chiamata REST è il token di accesso.

## <a name="authentication-for-deployed-applications"></a>Autenticazione per le applicazioni distribuite

Le chiavi dell'account sono consigliate per la creazione rapida di prototipi, solo in fase di sviluppo. Si consiglia di non inviare l'applicazione alla produzione usando una chiave dell'account incorporata. L'approccio consigliato prevede l'uso di un approccio basato sull'autenticazione Azure AD basato sull'utente o sul servizio.

 Azure AD autenticazione è descritta nella sezione [autenticazione utente Azure ad](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) del servizio [Azure Spatial Anchors (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

 Per altre informazioni, vedere l' [esercitazione relativa alla protezione del rendering remoto di Azure e dell'archiviazione del modello-Azure Active Directory Authentication](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Per controllare il livello di accesso concesso al servizio, usare i ruoli seguenti quando si concede l'accesso in base al ruolo:

* **Remote rendering Administrator**: fornisce agli utenti le funzionalità di conversione, gestione delle sessioni, rendering e diagnostica per il rendering remoto di Azure.
* **Client di rendering remoto**: consente agli utenti di gestire le funzionalità di sessione, rendering e diagnostica per il rendering remoto di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account](create-an-account.md)
* [Usare le API front-end di Azure per l'autenticazione](frontend-apis.md)
* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)

---
title: Impostazioni dei cookie in Application Proxy - Azure Active Directory  | Microsoft Docs
description: Azure Active Directory (Azure AD) dispone di cookie di sessione e accesso per accedere ad applicazioni locali tramite Application Proxy. In questo articolo vengono fornite informazioni su come usare e configurare le impostazioni dei cookie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca5f1b41e345caafdc465872c948be76c31d55e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72928865"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie settings for accessing on-premises applications in Azure Active Directory (Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory)

Azure Active Directory (Azure AD) dispone di cookie di sessione e accesso per accedere ad applicazioni locali tramite Application Proxy. Informazioni su come usare le impostazioni dei cookie di Application Proxy. 

## <a name="what-are-the-cookie-settings"></a>Quali sono le impostazioni dei cookie?

[Application Proxy](application-proxy.md) utilizza le seguenti impostazioni cookie di sessione e accesso.

| Impostazione dei cookie | Predefinito | Description | Raccomandazioni |
| -------------- | ------- | ----------- | --------------- |
| Usa cookie solo HTTP | **No** | **Sì** consente ad Application Proxy di includere il contrassegno HTTPOnly nelle intestazioni di risposta HTTP. Questo contrassegno offre vantaggi aggiuntivi di sicurezza, ad esempio, impedisce agli scripting del client (CSS) di copiare o modificare i cookie.<br></br><br></br>Prima che fosse supportata l'impostazione solo HTTP, Application Proxy crittografava e trasmetteva i cookie in un canale SSL per prevenire le modifiche. | Usare **Sì** per i vantaggi aggiuntivi di sicurezza.<br></br><br></br>Usare **No** per i client o gli agenti utente che richiedono l'accesso per il cookie di sessione. Ad esempio, usare **No** per un client RDP o MTSC che si connette a un server Gateway Desktop remoto tramite Application Proxy.|
| Usa cookie protetti | **No** | **Sì** consente ad Application Proxy di includere il contrassegno Protetto nelle intestazioni di risposta HTTP. Cookie protetti migliorano la sicurezza mediante la trasmissione di cookie su un canale TLS protetto, ad esempio HTTPS. Ciò impedisce che i cookie vengano esaminati da parti non autorizzate dovuta alla trasmissione di cookie in testo non crittografato. | Usare **Sì** per i vantaggi aggiuntivi di sicurezza.|
| Usa cookie permanenti | **No** | **Sì** consente ad Application Proxy di impostare i cookie di accesso per non scadere quando il Web browser viene chiuso. La permanenza dura fino alla scadenza del token di accesso o fino a quando non si consente di eliminare manualmente i cookie permanenti. | Usare **No** a causa del rischio di sicurezza associato al mantenere gli utenti autenticati.<br></br><br></br>È consigliabile usare solo **Sì** per le applicazioni meno recenti che non possono condividere cookie tra processi. È preferibile aggiornare l'applicazione per gestire la condivisione dei cookie tra processi anziché utilizzare i cookie permanenti. Ad esempio, potrebbero essere necessari cookie permanenti per consentire a un utente di aprire i documenti di Office in visualizzazione Esplora da un sito di SharePoint. Senza i cookie permanenti, questa operazione potrebbe non riuscire se i cookie di accesso non vengono condivisi tra il browser, il processo Esplora e il processo Office. |

## <a name="samesite-cookies"></a>Cookie navigava sullostesso sito
A partire dalla versione [Chrome 80](https://support.google.com/chrome/a/answer/7679408?hl=en) e infine nei browser che usano [Chromium](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html), i cookie che non specificano l'attributo [navigava sullostesso sito](https://web.dev/samesite-cookies-explained) verranno considerati come se fossero impostati su **navigava sullostesso sito = LAX**. L'attributo navigava sullostesso sito dichiara il modo in cui i cookie devono essere limitati a un contesto dello stesso sito. Se impostato su LAX, il cookie viene inviato solo alle richieste dello stesso sito o alla navigazione di primo livello. Tuttavia, il proxy di applicazione richiede che questi cookie vengano conservati nel contesto di terze parti per mantenere gli utenti connessi correttamente durante la sessione. Per questo motivo, vengono apportati aggiornamenti ai cookie di sessione e di accesso del proxy di applicazione per evitare conseguenze negative da questa modifica. Gli aggiornamenti includono:

* Impostazione dell'attributo **navigava sullostesso sito** su **None**: consente l'invio corretto dei cookie delle sessioni e dell'accesso del proxy di applicazione nel contesto di terze parti.
* Impostazione dell'impostazione **usa cookie sicuro** per usare **Sì** come valore predefinito. Chrome richiede anche che i cookie specifichino il flag sicuro o che verrà rifiutato. Questa modifica verrà applicata a tutte le applicazioni esistenti pubblicate tramite il proxy di applicazione. Si noti che i cookie di accesso del proxy di applicazione sono sempre stati impostati in modo da proteggere e trasmessi solo tramite HTTPS. Questa modifica verrà applicata solo ai cookie di sessione.

Queste modifiche ai cookie del proxy di applicazione vengono implementate nel corso delle prossime settimane successive alla data di rilascio di Chrome 80.

Inoltre, se l'applicazione back-end presenta cookie che devono essere disponibili in un contesto di terze parti, è necessario acconsentire esplicitamente modificando l'applicazione per l'uso di navigava sullostesso sito = None per questi cookie. Il proxy di applicazione converte l'intestazione set-cookie nei relativi URL e rispetta le impostazioni per questi cookie impostati dall'applicazione back-end.



## <a name="set-the-cookie-settings---azure-portal"></a>Configurare le impostazioni dei cookie - portale di Azure
Configurare le impostazioni dei cookie con il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**.
3. Selezionare l'applicazione per cui si desidera abilitare l'impostazione dei cookie.
4. Fare clic su **Application Proxy**.
5. All'interno di **Impostazioni aggiuntive**, sregolare l'impostazione dei cookie su **Sì** oppure **No**.
6. Fare clic su **Salva** per applicare le modifiche. 

## <a name="view-current-cookie-settings---powershell"></a>Visualizzare le impostazioni del cookie correnti-PowerShell

Per visualizzare le impostazioni dei cookie correnti per l'applicazione, usare questo comando di PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Impostazione impostazioni cookie-PowerShell

Nei comandi di PowerShell seguenti ```<ObjectId>``` è il valore ObjectId dell'applicazione. 

**Cookie solo http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Proteggi cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookie permanenti**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```

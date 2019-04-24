---
title: Impostazioni dei cookie in Application Proxy - Azure Active Directory  | Microsoft Docs
description: Azure Active Directory (Azure AD) dispone di cookie di sessione e accesso per accedere ad applicazioni locali tramite Application Proxy. In questo articolo vengono fornite informazioni su come usare e configurare le impostazioni dei cookie.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06fd83ee815e9e207c1fa5a1c6767280122c4d0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440531"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookie settings for accessing on-premises applications in Azure Active Directory (Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory)

Azure Active Directory (Azure AD) dispone di cookie di sessione e accesso per accedere ad applicazioni locali tramite Application Proxy. Informazioni su come usare le impostazioni dei cookie di Application Proxy. 

## <a name="what-are-the-cookie-settings"></a>Quali sono le impostazioni dei cookie?

[Application Proxy](application-proxy.md) utilizza le seguenti impostazioni cookie di sessione e accesso.

| Impostazione dei cookie | Predefinito | DESCRIZIONE | Consigli |
| -------------- | ------- | ----------- | --------------- |
| Usa cookie solo HTTP | **No** | **Sì** consente ad Application Proxy di includere il contrassegno HTTPOnly nelle intestazioni di risposta HTTP. Questo contrassegno offre vantaggi aggiuntivi di sicurezza, ad esempio, impedisce agli scripting del client (CSS) di copiare o modificare i cookie.<br></br><br></br>Prima che fosse supportata l'impostazione solo HTTP, Application Proxy crittografava e trasmetteva i cookie in un canale SSL per prevenire le modifiche. | Usare **Sì** per i vantaggi aggiuntivi di sicurezza.<br></br><br></br>Usare **No** per i client o gli agenti utente che richiedono l'accesso per il cookie di sessione. Ad esempio, usare **No** per un client RDP o MTSC che si connette a un server Gateway Desktop remoto tramite Application Proxy.|
| Usa cookie protetti | **No** | **Sì** consente ad Application Proxy di includere il contrassegno Protetto nelle intestazioni di risposta HTTP. Cookie protetti migliorano la sicurezza mediante la trasmissione di cookie su un canale TLS protetto, ad esempio HTTPS. Ciò impedisce che i cookie vengano esaminati da parti non autorizzate dovuta alla trasmissione di cookie in testo non crittografato. | Usare **Sì** per i vantaggi aggiuntivi di sicurezza.|
| Usa cookie permanenti | **No** | **Sì** consente ad Application Proxy di impostare i cookie di accesso per non scadere quando il Web browser viene chiuso. La permanenza dura fino alla scadenza del token di accesso o fino a quando non si consente di eliminare manualmente i cookie permanenti. | Usare **No** a causa del rischio di sicurezza associato al mantenere gli utenti autenticati.<br></br><br></br>È consigliabile usare solo **Sì** per le applicazioni meno recenti che non possono condividere cookie tra processi. È preferibile aggiornare l'applicazione per gestire la condivisione dei cookie tra processi anziché utilizzare i cookie permanenti. Ad esempio, potrebbero essere necessari cookie permanenti per consentire a un utente di aprire i documenti di Office in visualizzazione Esplora da un sito di SharePoint. Senza i cookie permanenti, questa operazione potrebbe non riuscire se i cookie di accesso non vengono condivisi tra il browser, il processo Esplora e il processo Office. |

## <a name="set-the-cookie-settings---azure-portal"></a>Configurare le impostazioni dei cookie - portale di Azure
Configurare le impostazioni dei cookie con il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**.
3. Selezionare l'applicazione per cui si desidera abilitare l'impostazione dei cookie.
4. Fare clic su **Application Proxy**.
5. All'interno di **Impostazioni aggiuntive**, sregolare l'impostazione dei cookie su **Sì** oppure **No**.
6. Fare clic su **Salva** per applicare le modifiche. 

## <a name="view-current-cookie-settings---powershell"></a>Visualizzare le impostazioni correnti cookie - PowerShell

Per visualizzare le impostazioni correnti di cookie per l'applicazione, usare questo comando di PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Impostare le impostazioni dei cookie - PowerShell

Nei comandi di PowerShell seguenti, ```<ObjectId>``` è il valore ObjectId dell'applicazione. 

**Cookie solo per http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Secure Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookie permanenti**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```

---
title: Panoramica del protocollo .NET per Azure AD | Documentazione Microsoft
description: Come usare messaggi HTTP per autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant con Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121523"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrare l'applicazione nel tenant di Active Directory
Prima di tutto è necessario registrare l'applicazione nel tenant di Azure Active Directory (Azure AD). Verrà fornito un ID per l'applicazione, abilitandola per ricevere token.

* Accedere al [portale di Azure](https://portal.azure.com).
* Scegliere il tenant di Azure AD facendo clic sull'account nell'angolo superiore destro della pagina. Fare clic sul menu di spostamento **Cambia Directory** e selezionare il tenant appropriato. 
  * Ignorare questo passaggio se è presente un solo tenant di Azure AD nell'account o se è già stato selezionato il tenant di Azure AD appropriato.
* Nel riquadro di spostamento a sinistra fare clic su **Azure Active Directory**.
* Fare clic su **registrazioni per l'App** e fare clic su **registrazione nuova**.
* Seguire le istruzioni e creare una nuova applicazione. Non è rilevante se si tratta di un'applicazione web o un'applicazione client pubblica (per dispositivi mobili e desktop) per questa esercitazione, ma se si vuole esempi specifici per le applicazioni web o client pubblico, consultare il [guide introduttive](../articles/active-directory/develop/v1-overview.md).
  * **Nome** è il nome dell'applicazione e descrive l'applicazione agli utenti finali.
  * In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
  * Fornire il **URI di reindirizzamento**. Per le applicazioni Web, questo è l'URL di base dell'app in cui gli utenti possono accedere.  Ad esempio: `http://localhost:12345`. Per ottenere un client pubblico (per dispositivi mobili e desktop), Azure AD lo usa per restituire le risposte dei token. Immettere un valore specifico per l'applicazione.  Ad esempio: `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Dopo aver completato la registrazione, AAD assegnerà all'applicazione un identificatore client univoco (i **ID applicazione**). Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.
* Per individuare l'applicazione nel portale di Azure, fare clic su **Registrazioni per l'app**, quindi fare clic su **Visualizza tutte le applicazioni**.

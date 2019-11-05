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
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523108"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrare l'applicazione nel tenant di Active Directory
Per prima cosa, registrare l'applicazione con il tenant di Azure Active Directory (Azure AD). Verrà fornito un ID per l'applicazione, abilitandola per ricevere token.

1. Accedere al [portale di Azure](https://portal.azure.com).
   
1. Scegliere il tenant di Azure AD selezionando l'account nell'angolo in alto a destra della pagina, quindi selezionando la finestra di dialogo **passa alla directory** e quindi selezionando il tenant appropriato. 
   - Ignorare questo passaggio se si dispone di un solo tenant Azure AD nell'account o se è già stato selezionato il tenant di Azure AD appropriato.
   
1. Nella portale di Azure cercare e selezionare **Azure Active Directory**.
   
1. Nel menu a sinistra **Azure Active Directory** selezionare registrazioni per l' **app**e quindi selezionare **nuova registrazione**.
   
1. Seguire le istruzioni e creare una nuova applicazione. Per questa esercitazione non è importante se si tratta di un'applicazione Web o di un'applicazione client pubblica (Mobile & desktop), ma se si desiderano esempi specifici per le applicazioni Web o le applicazioni client pubbliche, consultare le [guide introduttive](../articles/active-directory/develop/v1-overview.md).
   
   - **Nome** è il nome dell'applicazione e descrive l'applicazione agli utenti finali.
   - In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
   - Specificare l' **URI di reindirizzamento**. Per le applicazioni Web, questo è l'URL di base dell'app in cui gli utenti possono accedere.  Ad esempio, `http://localhost:12345`. Per il client pubblico (Mobile & desktop), Azure AD lo usa per restituire le risposte dei token. Immettere un valore specifico per l'applicazione.  Ad esempio, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Una volta completata la registrazione, Azure AD assegnerà all'applicazione un identificatore client univoco ( **ID applicazione**). Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.
   
1. Per trovare l'applicazione nel portale di Azure, selezionare **registrazioni app**, quindi selezionare **Visualizza tutte le applicazioni**.

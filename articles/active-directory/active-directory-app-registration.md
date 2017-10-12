---
title: Registrazione di app di Azure Active Directory | Microsoft Docs
description: Questo articolo illustra come usare il portale di Azure per registrare un'applicazione in Azure Active Directory
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 2f2817688beb2028fd0bba8522827d87a0097f21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Registrare l'applicazione nel tenant di Azure Active Directory

È possibile usare il portale di Azure per registrare l'applicazione nel tenant di Azure Active Directory (Azure AD), in modo da creare un ID per l'applicazione e consentire all'applicazione di ricevere token.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Nel riquadro di spostamento a sinistra scegliere **More Services** (Altri servizi), fare clic su **Registrazioni per l'app** e quindi su **Aggiungi**.
4. Seguire le istruzioni e creare una nuova applicazione. Per ottenere esempi specifici per applicazioni Web o per applicazioni native, vedere le [Guide introduttive](active-directory-developers-guide.md).
  * Per le applicazioni Web fornire l'**URL accesso**, ovvero l'URL di base dell'app con cui gli utenti possono accedere, ad esempio `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Per le applicazioni native fornire un **URI di reindirizzamento**, che Azure AD usa per restituire le risposte dei token. Immettere un valore specifico per l'applicazione in uso, ad esempio `http://MyFirstAADApp`
5. Dopo avere completato la registrazione, Azure AD assegna all'applicazione un identificatore client univoco, l'ID applicazione.

## <a name="update-application-settings-from-the-azure-portal"></a>Aggiornare le impostazioni dell'applicazione dal portale di Azure

È possibile modificare con facilità le impostazioni di un'applicazione usando il portale di Azure. È ad esempio possibile che si voglia configurare un URL di risposta, a cui Azure AD invia le risposte dei token. È anche possibile che si voglia configurare le autorizzazioni per altre applicazioni, ad esempio per consentire all'applicazione di accedere all'API Graph Microsoft. Tutte queste operazioni possono essere eseguite tramite la pagina delle impostazioni dell'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Nel riquadro di spostamento a sinistra scegliere **More Services** (Altri servizi), fare clic su**Registrazioni per l'app** e quindi scegliere l'applicazione dall'elenco.
4. Fare clic su **Impostazioni** per aprire la pagina delle impostazioni per l'applicazione.
  * La pagina **Proprietà** consente di modificare le informazioni generali per l'applicazione, inclusi il nome dell'applicazione, l'URL di accesso e l'URL di disconnessione.
  * La pagina **URL di risposta** consente di aggiungere un URL di risposta, a cui Azure AD invia le risposte dei token.
  * La pagina **Proprietari** consente di aggiungere proprietari dell'applicazione.
  * La pagina **Autorizzazioni** consente di configurare le autorizzazioni per l'app. Per accedere all'API Graph Microsoft, ad esempio, fare clic su **Aggiungi**, selezionare **Microsoft Graph** nel selettore di API e quindi scegliere l'autorizzazione necessaria, ad esempio **Lettura dati directory**.
  * La pagina **Chiavi** consente di aggiungere segreti dell'applicazione. Il segreto verrà visualizzato solo immediatamente dopo la creazione, quindi assicurarsi di copiarlo per un uso futuro.

## <a name="use-the-inline-manifest-editor"></a>Usare l'editor manifesto incorporato

È possibile usare l'editor manifesto incorporato per modificare determinate proprietà dell'applicazione che non vengono esposte direttamente nel portale di Azure. È ad esempio possibile usarlo per modificare il valore URI ID app o per abilitare il flusso implicito OAuth2.0 invece del flusso predefinito di codice per la concessione delle autorizzazioni.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere il tenant di Azure AD selezionando l'account nell'angolo superiore destro della pagina.
3. Nel riquadro di spostamento a sinistra scegliere **More Services** (Altri servizi), fare clic su**Registrazioni per l'app** e quindi scegliere l'applicazione dall'elenco.
4. Fare clic su **Manifesto** nella pagina dell'applicazione per aprire l'editor manifesto incorporato.
5. È possibile modificare direttamente il manifesto e salvarlo al termine delle modifiche. In alternativa è possibile scaricare il manifesto per aprirlo nell'editor preferito e caricare il manifesto aggiornato.

## <a name="next-steps"></a>Passaggi successivi

1. Per procedure dettagliate relative all'autenticazione delle applicazioni tramite Azure AD, vedere le [Guide introduttive](active-directory-developers-guide.md).
2. Per un elenco completo di esempi di codice, vedere [GitHub](https://github.com/azure-samples).

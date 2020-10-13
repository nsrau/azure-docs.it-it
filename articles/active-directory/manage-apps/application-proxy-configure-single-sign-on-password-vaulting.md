---
title: Accedere con Single Sign-On alle app con il proxy dell'applicazione Azure AD | Microsoft Docs
description: Attivare il Single Sign-On per le applicazioni pubblicate locali con il proxy dell'applicazione Azure AD nel portale di Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00b25f1b506228d2d80aecae3845467759a4bc4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165040"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Insieme di credenziali delle password per l'accesso Single Sign-On con il proxy dell'applicazione

Il proxy dell'applicazione Azure Active Directory consente di migliorare la produttività pubblicando le applicazioni locali in modo che possano accedervi anche i dipendenti remoti. Nel portale di Azure è inoltre possibile configurare l'accesso Single Sign-On (SSO) per queste applicazioni. Gli utenti devono solo eseguire l'autenticazione con Azure AD e possono accedere all'applicazione aziendale senza dover eseguire nuovamente l'accesso.

Il proxy dell'applicazione supporta numerose [modalità Single Sign-On](sso-options.md#choosing-a-single-sign-on-method). L'accesso basato su password è progettato per le applicazioni che usano una combinazione di nome utente/password per l'autenticazione. Quando si configura l'accesso basato su password per l'applicazione, gli utenti devono accedere all'applicazione locale una volta. Successivamente, Azure Active Directory archivia le informazioni di accesso e le fornisce automaticamente all'applicazione quando l'utente accede in modalità remota.

Si presuppone che l'utente abbia già pubblicato e testato l'app con il proxy dell'applicazione. In caso contrario, seguire i passaggi in [Pubblicare applicazioni tramite il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md) prima di tornare a questo punto.

## <a name="set-up-password-vaulting-for-your-application"></a>Configurare l'insieme di credenziali delle password per l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
1. Selezionare **Azure Active Directory**  >  **applicazioni aziendali**  >  **tutte le applicazioni**.
1. Nell'elenco a discesa selezionare l'app da configurare con la funzione SSO.  
1. Selezionare **Proxy dell'applicazione**. 
1. Modificare il **tipo di pre-autenticazione** in **PassThrough** e selezionare **Salva**. In seguito è possibile tornare nuovamente al tipo di **Azure Active Directory** . 
1. Selezionare **Single Sign-On**.

   ![Selezionare Single Sign-on dalla pagina Panoramica dell'app](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Per la modalità SSO, scegliere **Accesso basato su password**.
1. Per l'URL di accesso, inserire l'URL della pagina in cui gli utenti immettono nome utente e password per accedere all'app fuori dalla rete aziendale. Potrebbe trattarsi dell'URL esterno creato dopo aver pubblicato l'app tramite il proxy dell'applicazione.

   ![Scegliere l'accesso basato su password e immettere l'URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selezionare **Salva**.
1. Selezionare **Proxy dell'applicazione**. 
1. Modificare il **tipo di pre-autenticazione** in **Azure Active Directory** e selezionare **Salva**. 
1. Selezionare **utenti e gruppi**.
1. Assegnare gli utenti all'applicazione selezionando **Aggiungi utente**. 
1. Se si desidera predefinire le credenziali per un utente, selezionare la casella di controllo prima del nome utente e selezionare **Aggiorna credenziali**.
1. Selezionare **Azure Active Directory**  >  **registrazioni app**  >  **tutte le applicazioni**.
1. Nell'elenco selezionare l'App configurata con la password SSO.
1. Selezionare **Personalizzazione**. 
1. Aggiornare l' **URL della Home page** con l' **URL di accesso** dalla pagina SSO con password e selezionare **Salva**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Test dell'app

Passare al portale app personali. Accedere con le credenziali (o con le credenziali per un account di test configurato con l'accesso). Una volta eseguito l'accesso, fare clic sull'icona dell'app. Questa operazione potrebbe attivare l'installazione dell'estensione del browser per l'accesso sicuro alle app personali. Se l'utente ha credenziali predefinite, l'autenticazione per l'app dovrebbe essere eseguita automaticamente; in caso contrario, è necessario specificare il nome utente o la password per la prima volta. 

## <a name="next-steps"></a>Passaggi successivi

- Altri modi per implementare l'accesso [Single Sign-On](what-is-single-sign-on.md)
- Informazioni sulle [Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD](application-proxy-security.md)

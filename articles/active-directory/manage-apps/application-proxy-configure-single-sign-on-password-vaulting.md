---
title: Accedere con Single Sign-On alle app con il proxy dell'applicazione Azure AD | Microsoft Docs
description: Attivare il Single Sign-On per le applicazioni pubblicate locali con il proxy dell'applicazione Azure AD nel portale di Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025742"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Insieme di credenziali delle password per l'accesso Single Sign-On con il proxy dell'applicazione

Il proxy dell'applicazione Azure Active Directory consente di migliorare la produttività pubblicando le applicazioni locali in modo che possano accedervi anche i dipendenti remoti. Nel portale di Azure è inoltre possibile configurare l'accesso Single Sign-On (SSO) per queste applicazioni. Gli utenti devono solo eseguire l'autenticazione con Azure AD e possono accedere all'applicazione aziendale senza dover eseguire nuovamente l'accesso.

Il proxy dell'applicazione supporta numerose [modalità Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method). L'accesso basato su password è progettato per le applicazioni che usano una combinazione di nome utente/password per l'autenticazione. Quando si configura l'accesso basato su password per l'applicazione, gli utenti devono accedere all'applicazione locale una volta. Successivamente, Azure Active Directory archivia le informazioni di accesso e le fornisce automaticamente all'applicazione quando l'utente accede in modalità remota.

Si presuppone che l'utente abbia già pubblicato e testato l'app con il proxy dell'applicazione. In caso contrario, seguire i passaggi in [Pubblicare applicazioni tramite il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md) prima di tornare a questo punto.

## <a name="set-up-password-vaulting-for-your-application"></a>Configurare l'insieme di credenziali delle password per l'applicazione

1. Accedere al [portale](https://portal.azure.com) di Azure come amministratore.
1. Selezionare **Applicazioni Azure Active Directory** > **Enterprise** > **Tutte le applicazioni**.
1. Nell'elenco a discesa selezionare l'app da configurare con la funzione SSO.  
1. Selezionare **Proxy dell'applicazione**. 
1. Modificare il **tipo di autenticazione** preliminare in **Passthrough** e selezionare **Salva**. Successivamente è possibile tornare al tipo di **Azure Active Directory** di nuovo. 
1. Selezionare **Single Sign-On**.

   ![Selezionare Single Sign-On dalla pagina di panoramica dell'app](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Per la modalità SSO, scegliere **Accesso basato su password**.
1. Per l'URL di accesso, inserire l'URL della pagina in cui gli utenti immettono nome utente e password per accedere all'app fuori dalla rete aziendale. Potrebbe trattarsi dell'URL esterno creato dopo aver pubblicato l'app tramite il proxy dell'applicazione.

   ![Scegliere l'accesso basato su password e immettere l'URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selezionare **Salva**.
1. Selezionare **Proxy dell'applicazione**. 
1. Modificare il tipo di **pre-autenticazione** in **Azure Active Directory** e selezionare **Salva**. 
1. Selezionare **Utenti e gruppi**.
1. Assegnare gli utenti all'applicazione selezionando **Aggiungi utente**. 
1. Se si desidera predefinire le credenziali per un utente, selezionare la casella davanti al nome utente e selezionare **Aggiorna credenziali**.
1. Selezionare**Registrazioni** > app **di Azure Active Directory** > **Tutte le applicazioni**.
1. Nell'elenco selezionare l'app configurata con Password SSO.
1. Selezionare **Personalizzazione**. 
1. Aggiornare **l'URL** della home page con l'URL di **accesso** dalla pagina SSO password e selezionare **Salva**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Test dell'app

Passare al portale App personali. Accedere con le credenziali (o le credenziali per un account di test configurato con accesso). Una volta effettuato l'accesso, fai clic sull'icona dell'app. Ciò potrebbe attivare l'installazione dell'estensione del browser My Apps Secure Sign-in. Se l'utente dispone di credenziali predefinite, l'autenticazione per l'app deve essere eseguita automaticamente, altrimenti è necessario specificare il nome utente o la password per la prima volta. 

## <a name="next-steps"></a>Passaggi successivi

- Altri modi per implementare l'accesso [Single Sign-On](what-is-single-sign-on.md)
- Informazioni sulle [Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD](application-proxy-security.md)

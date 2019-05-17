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
ms.openlocfilehash: c19550adf500ba91462af12b4c5f7f5e38240e67
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783509"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Insieme di credenziali delle password per l'accesso Single Sign-On con il proxy dell'applicazione

Il proxy dell'applicazione Azure Active Directory consente di migliorare la produttività pubblicando le applicazioni locali in modo che possano accedervi anche i dipendenti remoti. Nel portale di Azure è inoltre possibile configurare l'accesso Single Sign-On (SSO) per queste applicazioni. Gli utenti devono solo eseguire l'autenticazione con Azure AD e possono accedere all'applicazione aziendale senza dover eseguire nuovamente l'accesso.

Il proxy dell'applicazione supporta numerose [modalità Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method). L'accesso basato su password è progettato per le applicazioni che usano una combinazione di nome utente/password per l'autenticazione. Quando si configura l'accesso basato su password per l'applicazione, gli utenti devono accedere all'applicazione locale una volta. Successivamente, Azure Active Directory archivia le informazioni di accesso e le fornisce automaticamente all'applicazione quando l'utente accede in modalità remota. 

Si presuppone che l'utente abbia già pubblicato e testato l'app con il proxy dell'applicazione. In caso contrario, seguire i passaggi in [Pubblicare applicazioni tramite il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md) prima di tornare a questo punto. 

## <a name="set-up-password-vaulting-for-your-application"></a>Configurare l'insieme di credenziali delle password per l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**.
3. Nell'elenco a discesa selezionare l'app da configurare con la funzione SSO.  
4. Selezionare **Single Sign-On**.

   ![Selezionare Single Sign-On](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. Per la modalità SSO, scegliere **Accesso basato su password**.
6. Per l'URL di accesso, inserire l'URL della pagina in cui gli utenti immettono nome utente e password per accedere all'app fuori dalla rete aziendale. Potrebbe trattarsi dell'URL esterno creato dopo aver pubblicato l'app tramite il proxy dell'applicazione. 

   ![Scegliere l'accesso basato su password e immettere l'URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Selezionare **Salva**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Test dell'app

Passare all'URL esterno che è stato configurato per l'accesso remoto all'applicazione. Accedere con le credenziali dell'app (o con le credenziali di un account di prova per cui è stato configurato l'accesso). Dopo l'accesso, dovrebbe essere possibile lasciare l'app e tornare senza dover immettere nuovamente le credenziali. 

## <a name="next-steps"></a>Passaggi successivi

- Altri modi per implementare l'accesso [Single Sign-On](what-is-single-sign-on.md)
- Informazioni sulle [Considerazioni relative alla sicurezza quando si accede alle app in remoto usando il proxy applicazione di Azure AD](application-proxy-security.md)

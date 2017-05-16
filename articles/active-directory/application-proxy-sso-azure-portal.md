---
title: Accedere con Single Sign-On alle app con il proxy dell&quot;applicazione Azure AD | Microsoft Docs
description: Attivare il Single Sign-On per le applicazioni pubblicate locali con il proxy dell&quot;applicazione Azure AD nel portale di Azure.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: asteen
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b0454ad51a2d0edecb6f7e7748ec3b74eeefa1da
ms.contentlocale: it-it
ms.lasthandoff: 04/28/2017

---


# <a name="provide-single-sign-on-with-azure-ad-application-proxy---public-preview"></a>Fornire accesso Single Sign-On mediante il proxy dell'applicazione Azure AD: anteprima pubblica

Il proxy dell'applicazione Azure Active Directory consente di migliorare la produttività pubblicando le applicazioni locali in modo che possano accedervi anche i dipendenti remoti. Nel portale di Azure è inoltre possibile configurare l'accesso Single Sign-On (SSO) per queste applicazioni. A questo punto, gli utenti devono solo eseguire l'autenticazione con Azure AD e possono accedere all'applicazione aziendale senza dover effettuare nuovamente l'accesso.

In questo articolo si usa l'esempio di un'app basata su password per illustrare come un insieme di credenziali delle password consenta un uso SSO semplice. 

Si presuppone che l'utente abbia già pubblicato e testato l'app con il proxy dell'applicazione. In caso contrario, seguire i passaggi indicati in [Publish applications using Azure AD Application Proxy - Public Preview](application-proxy-publish-azure-portal.md) (Pubblicare app con il proxy dell'applicazione di Azure AD: anteprima pubblica), quindi leggere questa sezione. 

Se non si ha familiarità con le funzionalità offerte dal proxy dell'applicazione, per altre informazioni vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).

## <a name="set-up-password-vaulting-for-your-application"></a>Configurare l'insieme di credenziali delle password per l'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**.
3. Nell'elenco a discesa selezionare l'app da configurare con la funzione SSO. Se si dispone di molte applicazioni, è possibile usare la casella di ricerca per trovare quella desiderata.  
4. Nella sezione Gestisci selezionare su **Single Sign-On**.

   ![Selezionare Single Sign-On](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Per la modalità SSO, scegliere **Accesso basato su password**.
6. Per l'URL di accesso, indicare l'URL della pagina in cui gli utenti immettono nome utente e password per accedere all'app. Si tratta solitamente dell'URL esterno creato dopo aver pubblicato l'app tramite il proxy dell'applicazione. 

   ![Scegliere l'accesso basato su password e immettere l'URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Selezionare **Salva**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Test dell'app

Accedere al [sito App personali](https://myapps.microsoft.com) e selezionare l'app appena configurata. Accedere con le credenziali dell'app (o con le credenziali dell'account di prova per cui è stato configurato l'accesso). Dopo l'accesso, dovrebbe essere possibile lasciare l'app e tornare senza dover immettere nuovamente le credenziali. 

## <a name="next-steps"></a>Passaggi successivi

Altri modi per implementare l'accesso [Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)


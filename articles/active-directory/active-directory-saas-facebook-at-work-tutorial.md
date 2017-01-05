---
title: 'Esercitazione: Integrazione di Azure Active Directory con Workplace by Facebook | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Workplace by Facebook.
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Esercitazione: Integrazione di Azure Active Directory con Workplace by Facebook
Questa esercitazione descrive l'integrazione di Workplace by Facebook con Azure Active Directory (Azure AD).

L'integrazione di Workplace by Facebook con Azure AD offre i vantaggi seguenti: 

* È possibile controllare in Azure AD chi può accedere a Workplace by Facebook 
* È possibile effettuare automaticamente il provisioning degli account per gli utenti autorizzati ad accedere a Workplace by Facebook
* È possibile abilitare gli utenti per l'accesso automatico a Workplace by Facebook (Single Sign-On) con i propri account di Azure AD
* È possibile gestire gli account da una posizione centralizzata 

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti
Per configurare l'integrazione di Azure AD con CS Stars, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure AD.
* Workplace by Facebook con accesso Single Sign-On abilitato

A questo scopo, è consigliabile seguire le indicazioni seguenti:

* Non usare l'ambiente di produzione, a meno che non sia necessario.
* Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Aggiunta di Workplace by Facebook dalla raccolta
Per configurare l'integrazione di Workplace by Facebook in Azure AD, è necessario aggiungere Workplace by Facebook dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workplace by Facebook dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro. 
   
    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applications][2]
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Applicazioni][3]
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Applicazioni][4]
6. Nella casella di ricerca digitare **Workplace by Facebook**.
7. Nel riquadro dei risultati selezionare **Workplace by Facebook** e quindi fare clic su **Completa** per aggiungere l'applicazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Workplace by Facebook con il proprio account di Azure Active Directory usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On di Azure AD con Workplace by Facebook, seguire questa procedura:**

1. Dopo aver aggiunto Workplace by Facebook nel Portale di Azure classico, fare clic su **Configura accesso Single Sign-On**.
2. Nella schermata **Configura URL app** immettere l'URL che verrà digitato dagli utenti per accedere all'applicazione Workplace by Facebook. Si tratta dell'URL del tenant di Workplace by Facebook (ad esempio: https://example.facebook.com/). Al termine, fare clic su **Avanti**.
3. In un'altra finestra del Web browser accedere al sito aziendale di Workplace by Facebook come amministratore.
4. Seguire le istruzioni disponibili nell'URL seguente per configurare Workplace by Facebook per l'uso di Azure AD come provider di identità: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. Al termine, tornare alle finestre del browser in cui è visualizzato il portale di Azure classico, selezionare la casella di controllo per confermare di aver completato la procedura e quindi fare clic su **Avanti** e **Completa**.


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>Provisioning automatico degli utenti in Workplace by Facebook
Azure AD consente di sincronizzare automaticamente i dettagli dell'account degli utenti assegnati a Workplace by Facebook. La sincronizzazione automatica consente a Workplace by Facebook di ottenere i dati necessari per autorizzare gli utenti all'accesso, prima che questi tentino di eseguire l'accesso per la prima volta. Esegue anche il deprovisioning degli utenti da Workplace by Facebook una volta che l'accesso viene revocato in Azure AD.

Per impostare il provisioning automatico, fare clic su **Configura provisioning account** nella finestra del portale di Azure classico.

Per altre informazioni su come configurare il provisioning automatico, vedere [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-workplace-by-facebook"></a>Assegnazione di utenti a Workplace by Facebook
Per fare in modo che gli utenti di AAD di cui è stato effettuato il provisioning possano visualizzare Workplace by Facebook nel pannello di accesso, è necessario assegnare loro l'accesso nel Portale di Azure classico.

**Assegnazione di utenti a Workplace by Facebook:**

1. Nella pagina iniziale di Workplace by Facebook nel Portale di Azure classico fare clic su **Assegna account**.
2. Nel menu **Mostra** scegliere se assegnare un utente o un gruppo a Workplace by Facebook e quindi fare clic sul pulsante con il segno di spunta.
3. Nell'elenco visualizzato selezionare gli utenti o il gruppo a cui si vuole assegnare Workplace by Facebook.
4. Fare clic sul pulsante **Assegna** nel piè di pagina.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->



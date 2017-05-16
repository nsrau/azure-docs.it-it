---
title: 'Esercitazione: Integrazione di Azure Active Directory con Overdrive Books | Microsoft Docs'
description: Informazioni su come utilizzare Overdrive Books con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5d6f44f868010d50b36a9171b7db63aeee7e8bc0
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Esercitazione: Integrazione di Azure Active Directory con Overdrive Books
In questa esercitazione viene illustrata l'integrazione di Azure e OverDrive  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di OverDrive abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a OverDrive potranno eseguire l'accesso Single Sign-On (SSO) all'applicazione tramite il sito aziendale di OverDrive (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione OverDrive
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenario")

## <a name="enable-the-application-integration-for-overdrive"></a>Abilitare l'integrazione dell'applicazione per OverDrive
Questa sezione descrive come abilitare l'integrazione dell'applicazione per OverDrive.

**Per abilitare l'integrazione dell'applicazione per OverDrive, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **OverDrive**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **OverDrive**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a OverDrive tramite il proprio account in Azure AD utilizzando la federazione basata sul protocollo SAML.

**Per configurare l'accesso SSO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OverDrive** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Abilitare l'accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Abilitare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a OverDrive** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app**, nella casella di testo **OverDrive Sign In URL** (URL di accesso a OverDrive) digitare l'URL usando il modello"*http://mslibrarytest.libraryreserve.com*", quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in OverDrive** scaricare il file di metadati e inviarlo al team di supporto di OverDrive.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configurare l'accesso Single Sign-On")
   
   >[!NOTE]
   >Il team di supporto di OverDrive configura l’accesso Single Sign-On per l’utente e invia una notifica al termine della configurazione.
   > 
   > 
5. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Non è richiesta alcuna operazione per configurare il provisioning degli utenti in OverDrive.  

Quando un utente assegnato tenta di accedere a OverDrive, se necessario viene creato automaticamente un account di OverDrive.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di OverDrive o le API fornite da OverDrive per eseguire il provisioning degli account utente di AAD.
>
>
 
## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a OverDrive, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **OverDrive** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Assegnare utenti")
   
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


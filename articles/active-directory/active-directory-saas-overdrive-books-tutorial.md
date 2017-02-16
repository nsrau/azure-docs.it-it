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
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 66b3bbe84f966c3f8ec782fbf7c3bb20a8a832d0


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Esercitazione: Integrazione di Azure Active Directory con Overdrive Books
In questa esercitazione viene illustrata l'integrazione di Azure e OverDrive  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Overdrive Books abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a OverDrive saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di OverDrive (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione OverDrive
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenario")

## <a name="enabling-the-application-integration-for-overdrive"></a>Abilitazione dell'integrazione dell'applicazione OverDrive
Questa sezione descrive come abilitare l'integrazione dell'applicazione per OverDrive.

### <a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per OverDrive eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applications](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applications")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungi applicazione](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Add application")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Add an application from gallerry")
6. Nella **casella di ricerca** digitare **OverDrive**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Application Gallery")
7. Nel riquadro dei risultati selezionare **OverDrive**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
   ## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a OverDrive tramite il proprio account in Azure AD utilizzando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **OverDrive** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Abilita Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Enable single sign-on")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a OverDrive** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configure single sign-on")
3. Nella pagina **Configura URL app**, nella casella di testo **OverDrive Sign In URL** (URL di accesso a OverDrive) digitare l'URL usando il modello"*http://mslibrarytest.libraryreserve.com*", quindi fare clic su **Avanti**.
   
   ![Configura URL app](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configure App URL")
4. Nella pagina **Configura accesso Single Sign-On in OverDrive** scaricare il file di metadati e inviarlo al team di supporto di OverDrive.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configure single sign-on")
   
   > [!NOTE]
   > Il team di supporto di OverDrive configura l’accesso Single Sign-On per l’utente e invia una notifica al termine della configurazione.
   > 
   > 
5. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Non è richiesta alcuna operazione per configurare il provisioning degli utenti in OverDrive.  
Quando un utente assegnato tenta di accedere a OverDrive, se necessario viene creato automaticamente un account di OverDrive.

> [!NOTE]
> È possibile utilizzare qualsiasi altro strumento di creazione di account utente di OverDrive o le API fornite da OverDrive per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>Per assegnare gli utenti a OverDrive, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **OverDrive** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Assign Users")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Yes")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->



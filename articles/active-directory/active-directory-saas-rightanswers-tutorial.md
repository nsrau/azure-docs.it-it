---
title: 'Esercitazione: Integrazione di Azure Active Directory con RightAnswers | Microsoft Docs'
description: Informazioni su come usare RightAnswers con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f09e25a-a716-41e1-8ca3-fd00e3d1b8cc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 9251f3f9311e1cd4b1d57c611cc1783855d8d2af


---
# <a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>Esercitazione: Integrazione di Azure Active Directory con RightAnswers
In questa esercitazione viene illustrata l'integrazione di Azure e RightAnswers. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di RightAnswers abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a RightAnswers saranno in grado di eseguire l’accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per RightAnswers
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario")

## <a name="enabling-the-application-integration-for-rightanswers"></a>Abilitazione dell'integrazione dell'applicazione per RightAnswers
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per RightAnswers.

### <a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per RightAnswers, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **RightAnswers**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **RightAnswers**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a RightAnswers tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **RightAnswers** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a RightAnswers** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configurare le impostazioni dell'app** digitare nella casella di testo **URL di accesso** l'URL utilizzato dagli utenti per accedere all'applicazione RightAnswers, ad esempio *https://fortify.rightanswers.com/portal/ss/*, quindi fare clic su **Avanti**.
   
    ![Configurare le impostazioni dell'app](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configurare le impostazioni dell'app")

4. Nella pagina **Configura accesso Single Sign-On in RightAnswers** fare clic su **Scarica metadati** per scaricare il file di metadati e salvare il file di metadati localmente nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configurare l'accesso Single Sign-On")

5. Inviare il file di metadati scaricato al team di supporto di RightAnswers.
   
    > [!NOTE]
    > La configurazione effettiva dell'accesso Single Sign-On deve essere eseguita dal team di supporto di RightAnswers.
    > Una volta completata l'abilitazione dell'accesso Single Sign-On per la sottoscrizione, si riceverà una notifica.
    > 
    > 

6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configurare l'accesso Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a RightAnswers, è necessario eseguirne il provisioning in RightAnswers.  
Nel caso di RightAnswers, il provisioning è un'attività automatica.  
Non è necessario eseguire alcuna operazione.

Se necessario, gli utenti vengono creati automaticamente durante il primo tentativo di accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da RightAnswers per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>Per assegnare gli utenti a RightAnswers, eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **RightAnswers** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->



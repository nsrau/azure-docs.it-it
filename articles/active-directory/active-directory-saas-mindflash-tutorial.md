---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mindflash | Microsoft Docs'
description: Informazioni su come utilizzare Mindflash con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 57a6428315a4942a0c6566fef5a4db4ee66cb55a
ms.openlocfilehash: 89bd515fa988e0347508b739dd0676c5eeb1f44d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Esercitazione: Integrazione di Azure Active Directory con Mindflash
In questa esercitazione viene illustrata l'integrazione di Azure e Mindflash. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Mindflash abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Mindflash saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Mindflash (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Mindflash
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Scenario")

## <a name="enabling-the-application-integration-for-mindflash"></a>Abilitazione dell'integrazione dell'applicazione per Mindflash
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Mindflash.

### <a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Mindflash, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Mindflash**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Mindflash**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Mindflash tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Mindflash** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Mindflash** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso** della pagina **Configure App URL** (Configura URL app) digitare l'URL usando il modello "*http://company.mindflash.com*" e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Mindflash** fare clic su **Scarica metadati**, quindi salvare il file dei metadati nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configurare l'accesso Single Sign-On")
5. Inviare il file dei metadati al team di supporto di Mindflash.
   
   > [!NOTE]
   > La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Mindflash. Al termine della configurazione, viene ricevuta una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configurare l'accesso Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Mindflash, è necessario eseguirne il provisioning in Mindflash. Nel caso di Mindflash, il provisioning è un’attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Mindflash** come amministratore.
2. Passare a **Gestisci utenti**.
   
   ![Gestione utenti](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Gestione utenti")
3. Fare clic su **Aggiungi utenti**, quindi fare clic su **Nuovo**.
4. Nella sezione **Aggiungi nuovi utenti** eseguire la procedura seguente:
   
   ![Aggiungi nuovi utenti](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Aggiungi nuovi utenti")
   
   1. Digitare il **nome**, il **cognome** e l'**indirizzo e-mail** di un account AAD valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   2. Fare clic su **Aggiungi**.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mindflash o le API fornite da Mindflash per eseguire il provisioning degli account utente di AAD. 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all’applicazione agli utenti di Azure AD a cui si desidera consentirne l’uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Per assegnare gli utenti a Mindflash eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Mindflash** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



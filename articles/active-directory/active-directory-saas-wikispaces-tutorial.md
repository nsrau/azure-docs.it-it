---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wikispaces | Microsoft Docs'
description: Informazioni su come usare Wikispaces con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 665b95aa-f7f5-4406-9e2a-6fc299a1599c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 6aeeaeef928d483c48f988c71ed8bc8367749229
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Esercitazione: Integrazione di Azure Active Directory con Wikispaces
Questa esercitazione descrive l'integrazione di Azure e Wikispaces.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Wikispaces abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Wikispaces potranno accedere all'applicazione tramite il sito aziendale di Wikispaces (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione Wikispaces
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Sceanrio](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

## <a name="enabling-the-application-integration-for-wikispaces"></a>Abilitazione dell'integrazione dell'applicazione Wikispaces
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Wikispaces.

### <a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Wikispaces, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **Wikispaces**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **Wikispaces** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Wikispaces tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Wikispaces** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Wikispaces** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurare l'accesso Single Sign-On")

3. Nella casella di testo **URL di accesso Wikispaces** della pagina **Configura URL app** digitare l'URL usando il modello "*http://company.wikispaces.net*" e quindi fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurare l'URL dell'app")

4. Nella pagina **Configura accesso Single Sign-On in Wikispaces** fare clic su **Download metadati** e quindi salvare il file dei metadati nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurare l'accesso Single Sign-On")

5. Inviare il file dei metadati al team di supporto di Wikispaces.
   
    > [!NOTE]
    > La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Wikispaces. Al termine della configurazione, viene ricevuta una notifica.
    > 
    > 

6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Wikispaces, è necessario eseguirne il provisioning in Wikispaces.  
Nel caso di Wikispaces, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Wikispaces** come amministratore.

2. Passare a **Membri**.
   
    ![Members](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Members")

3. Fare clic su **Invita persone**.
   
    ![Invitare persone](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invitare persone")

4. Nella sezione **Invita Persone** seguire la procedura seguente:
   
    ![Invitare persone](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invitare persone")
   
    a. Nelle apposite caselle di testo immettere i valori di **Usernames o Email Address** di un account Azure AD valido di cui si vuole eseguire il provisioning.
   
    b. Fare clic su **Send**.  
      
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    > 
    > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Wikispaces per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Per assegnare gli utenti a Wikispaces, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Wikispaces** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



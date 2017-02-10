---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sprinklr | Documentazione Microsoft'
description: Informazioni su come usare Sprinklr con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 451369e21e7471180b6cd8c77d62b157d0bcddff
ms.openlocfilehash: 19bd7971e172f32367ee2dae903389e52709a306


---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Esercitazione: Integrazione di Azure Active Directory con Sprinklr
Questa esercitazione descrive l'integrazione di Azure e Sprinklr.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Un tenant Sprinklr

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Sprinklr potranno accedere all'applicazione tramite il sito aziendale di Sprinklr (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Sprinklr
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenario")

## <a name="enabling-the-application-integration-for-sprinklr"></a>Abilitazione dell'integrazione dell'applicazione per Sprinklr
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Sprinklr.

### <a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Sprinklr, eseguire la procedura seguente:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **Sprinklr**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **Sprinklr**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Sprinklr tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Sprinklr** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Sprinklr** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL accesso Sprinklr** nel formato "*https://\<nome-tenant\>.sprinklr.com*", quindi fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurare l'URL dell'app")

4. Nella pagina **Configura accesso Single Sign-On in Sprinklr** fare clic su **Scarica certificato** per scaricare il file del certificato, quindi salvarlo nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurare l'accesso Single Sign-On")

5. In un'altra finestra del Web browser accedere al sito aziendale di Sprinklr come amministratore.

6. Passare ad **Administration (Amministrazione) \> Settings (Impostazioni)**.
   
    ![Amministrazione](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Amministrazione")

7. Passare a **Manage Partner (Gestisci partner) \> Single Sign (Accesso singolo)** nel riquadro sinistro.
   
    ![Gestione partner](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gestione partner")

8. Fare clic su **+ Add Single Sign Ons**.
   
    ![Accessi Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Accessi Single Sign-On")

9. Nella pagina **Single Sign On** eseguire la procedura seguente:
   
    ![Accessi Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Accessi Single Sign-On")
   
    a. Nella casella di testo **Name** digitare un nome per la configurazione (ad esempio *WAADSSOTest*).
   
    b. Selezionare **Enabled**.
   
    c. Selezionare **Use new SSO Certificate**.

    d. Creare un file **con codifica Base&64;** dal certificato scaricato.  
      
    > [!TIP]
    > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e. Aprire il certificato con codifica base&64; nel blocco note, copiarne il contenuto negli appunti e quindi incollarlo nella casella di testo **Identity Provider Certificate** .
   
    f. Nella finestra di dialogo **Configura accesso Single Sign-On in Sprinklr** del portale di Azure classico copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Entity Id** (ID entità).
   
    g. Nella pagina **Configura accesso Single Sign-On in Sprinklr** del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **Identity Provider Login URL** (URL di accesso provider di identità).
   
    h. Nella pagina **Configura accesso Single Sign-On in Sprinklr** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità).
   
    i. In **SAML User ID Type** (Tipo ID utente SAML) selezionare **Assertion contains User”s sprinklr.com username** (L'asserzione contiene il nome utente sprinklr.com dell'utente).
   
    j. In **SAML User ID Location** (Percorso ID utente SAML) selezionare **User ID is in the Name Identifier element of the Subject statement** (L'ID utente è nell'elemento identificatore nome dell'istruzione Subject).
   
    k. Chiudere **Save**.
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di AAD di accedere, è necessario eseguirne il provisioning nell'applicazione Sprinklr.  
In questa sezione viene descritto come creare gli account utente AAD in Sprinklr.

### <a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente in Sprinklr, eseguire la procedura seguente:
1. Accedere al sito aziendale di Sprinklr come amministratore.

2. Passare ad **Administration (Amministrazione) \> Settings (Impostazioni)**.
   
    ![Amministrazione](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Amministrazione")

3. Passare a **Manage Client (Gestisci client) \> Users (Utenti)** dal riquadro di sinistra.
   
    ![Impostazioni](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Impostazioni")

4. Fare clic su **Aggiungi utente**.
   
    ![Impostazioni](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Impostazioni")

5. Nella finestra di dialogo **Edit User** seguire la procedura seguente:
   
    ![Modifica degli utenti](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Modifica degli utenti")
   
    a. Nelle caselle di testo **Email** (E-mail), **First name** (Nome) e **Last name** (Cognome) digitare i dati di un account utente di Azure AD di cui eseguire il provisioning.
   
    b. Selezionare **Password disabled**.
   
    c. Selezionare una **Language**.
   
    d. Selezionare un **User Type**.
   
    e. Fare clic su **Aggiorna**.
   
    > [!IMPORTANT]
    > **Password disabled** deve essere selezionata per consentire agli utenti di accedere tramite un provider di identità.
    > 
    > 

6. Passare a **Role**ed eseguire la procedura seguente:
   
    ![Ruoli partner](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Ruoli partner")
   
    a. Dall'elenco **Global** (Globale), selezionare **ALL\_Permissions** (Tutte le autorizzazioni).
    
    b. Fare clic su **Aggiorna**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utente di Sprinklr o API fornita da Sprinklr per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Per assegnare gli utenti a Sprinklr,eseguire la procedura seguente:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Sprinklr ** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO2-->



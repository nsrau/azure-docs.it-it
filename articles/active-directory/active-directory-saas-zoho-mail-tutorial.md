---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zoho Mail | Microsoft Docs'
description: Informazioni su come usare Zoho Mail con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 394777b77cbf2fe9fa779c270eec9dded21bdfae
ms.lasthandoff: 02/11/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Esercitazione: Integrazione di Azure Active Directory con Zoho Mail
Questa esercitazione descrive l'integrazione di Azure e Zoho Mail.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant di Zoho Mail

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Zoho Mail potranno accedere all'applicazione tramite il sito aziendale di Zoho Mail (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione Zoho Mail
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scenario")

## <a name="enabling-the-application-integration-for-zoho-mail"></a>Abilitazione dell'integrazione dell'applicazione Zoho Mail
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Zoho Mail.

### <a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per Zoho Mail, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **Zoho Mail**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **Zoho Mail** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Zoho Mail tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;.  
Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **Zoho Mail** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Zoho Mail** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurare l'accesso Single Sign-On")

3. Nella pagina **Configura URL app** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurare l'URL dell'app")
   
    a. Nella casella di testo **URL di accesso Zoho** digitare l'URL usando il modello seguente: `http://<company name>.ZohoMail.com`
   
    b. Fare clic su **Avanti**.

4. Nella pagina **Configura accesso Single Sign-On in Zoho Mail** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurare l'accesso Single Sign-On")

5. In un'altra finestra del Web browser accedere al sito aziendale di Zoho Mail come amministratore.

6. Passare a **Control panel**.
   
    ![Pannello di controllo](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Pannello di controllo")

7. Fare clic sulla scheda **SAML Authentication** .
   
    ![Autenticazione SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Autenticazione SAML")

8. Nella sezione **SAML Authentication Details** seguire questa procedura:
   
    ![Dettagli dell'autenticazione SAML](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Dettagli dell'autenticazione SAML")
   
    a. Nella finestra di dialogo **Configura accesso Single Sign-On in Zoho Mail** del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **URL di accesso**.
   
    b. Nella finestra di dialogo **Configura accesso Single Sign-On in Zoho Mail** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **URL di disconnessione**.
   
    c. Nella finestra di dialogo **Configura accesso Single Sign-On in Zoho Mail** del portale di Azure classico copiare il valore di **Modifica URL password** e quindi incollarlo nella casella di testo **Modifica URL password**.
   
    d. Creare un file **con codifica Base&64;** dal certificato scaricato.  
      
    > [!TIP]
    > Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e. Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PublicKey**
   
    f. In **Algorithm** (Algoritmo) selezionare **RSA**.
   
    g. Fare clic su **OK**.

9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a Zoho Mail, è necessario eseguirne il provisioning in Zoho Mail.  
Nel caso di Zoho Mail, il provisioning è un'attività manuale.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **Zoho Mail** come amministratore.

2. Passare a **Control Panel \> Mail & Docs**.

3. Passare a **User Details \> Add User** (Dettagli utente > Aggiungi utente).
   
    ![Aggiungere un utente](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Aggiungere un utente")

4. Nella finestra di dialogo **Add users** seguire questa procedura:
   
    ![Aggiungere un utente](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Aggiungere un utente")
   
    a. Nelle apposite caselle di testo immettere i valori di **First Name**, **Last Name**, **Email ID** e **Password** di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   
    b. Fare clic su **OK**.  
      
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    > 
    > 

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Zoho Mail per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Per assegnare gli utenti a Zoho Mail, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Zoho Mail** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



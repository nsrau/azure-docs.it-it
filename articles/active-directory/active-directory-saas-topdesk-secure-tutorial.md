---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure | Documentazione Microsoft'
description: Informazioni su come usare TOPdesk - Secure con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 28f0542dbe87bb34c83a7852db7c3a9fef055ce9
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure
Questa esercitazione descrive l'integrazione di Azure e TOPdesk - Secure.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di TOPdesk - Secure abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TOPdesk - Secure potranno accedere all'applicazione tramite il sito aziendale di TOPdesk - Secure (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione TOPdesk - Secure
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>Abilitazione dell'integrazione dell'applicazione TOPdesk - Secure
Questa sezione descrive come abilitare l'integrazione dell'applicazione per TOPdesk - Secure.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per TOPdesk - Secure, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **TOPdesk - Secure**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **TOPdesk - Secure** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a TOPdesk - Secure tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per TOPdesk - Secure richiede il caricamento di un file con l'icona del logo. Per ottenere il file con l'icona, contattare il team di supporto di TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Accedere al sito aziendale di **TOPdesk - Secure** come un amministratore.
2. Nel menu **TOPdesk** fare clic su **Settings** (Impostazioni).
   
    ![Impostazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Impostazioni")

3. Fare clic su **Login Settings**.
   
    ![Login Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")

4. Espandere il menu **Login Settings** (Impostazioni accesso) e quindi fare clic su **General** (Generale).
   
    ![General](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

5. Nell'area **Secure** (Sicura) della sezione di configurazione **SAML login** (Accesso SAML) seguire questa procedura:
   
    ![Technical Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Technical Settings")
   
    a. Fare clic su **Download** per scaricare il file di metadati pubblici e quindi salvarlo in locale nel computer.
   
    b. Aprire il file dei metadati e quindi individuare il nodo **AssertionConsumerService** .
    
    ![Assertion Consumer Service](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Assertion Consumer Service")
   
    c. Copiare il valore di **AssertionConsumerService** .  
      
    > [!NOTE]
    > Questo valore sarà necessario nella sezione **Configure App URL** più avanti in questa esercitazione.
    > 
    > 

6. In un'altra finestra del Web browser accedere al portale di **portale di Azure classico** come amministratore.

7. Nella pagina di integrazione dell'applicazione **TOPdesk - Secure** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurare l'accesso Single Sign-On")

8. Nella pagina **Stabilire come si desidera che gli utenti accedano a TOPdesk - Secure** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurare l'accesso Single Sign-On")

9. Nella pagina **Configura URL app** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurare l'URL dell'app")
   
    a. Nella casella di testo **URL di accesso TOPdesk - Secure** digitare l'URL usato dagli utenti per accedere all'applicazione TOPdesk - Secure, ad esempio "*https://qssolutions.topdesk.net*".
   
    b. Nella casella di testo **URL di risposta TOPdesk - Secure** incollare il valore dell'URL **AssertionConsumerService di TOPdesk - Secure**, ad esempio "*https://qssolutions.topdesk.net/tas/public/login/saml*"
   
    c. Fare clic su **Avanti**.

10. Nella pagina **Configura accesso Single Sign-On in TOPdesk - Secure** fare clic su **Scarica metadati** per scaricare il file dei metadati e quindi salvarlo in locale nel computer.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurare l'accesso Single Sign-On")

11. Per creare un file del certificato, seguire questa procedura:
    
    ![Certificate](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificate")
    
    a. Aprire il file dei metadati scaricato.
    b. Espandere il nodo **RoleDescriptor** con **xsi:type** corrispondente a **fed:ApplicationServiceType**.
    c. Copiare il valore del nodo **X509Certificate** .
    d. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.

12. Nel sito aziendale di TOPdesk - Secure scegliere **Settings** (Impostazioni) dal menu **TOPdesk**.
    
    ![Impostazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Impostazioni")

13. Fare clic su **Login Settings**.
    
    ![Login Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")

14. Espandere il menu **Login Settings** (Impostazioni accesso) e quindi fare clic su **General** (Generale).
    
    ![General](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

15. Nell'area **Public** (Pubblica) fare clic su **Add** (Aggiungi).
    
    ![Add](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Add")

16. Nella pagina **SAML configuration assistant** seguire questa procedura:
    
    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML Configuration Assistant")
    
    a. Per caricare il file di metadati scaricato, in **Federation Metadata** (Metadati configurazione) fare clic su **Browse** (Sfoglia).

    b. Per caricare il file del certificato, in **Certificate (RSA)** (Certificato RSA) fare clic su **Browse** (Sfoglia).

    c. Per caricare il file del logo ottenuto dal team di supporto del team di TOPdesk, in **Logo icon** (Icona logo) fare clic su **Browse** (Sfoglia).

    d. Nella casella di testo **User name attribute** (Attributo nome utente) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. Nella casella di testo **Display name** digitare un nome per la configurazione.

    f. Fare clic su **Save**.

17. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a TOPdesk - Secure, è necessario eseguirne il provisioning in TOPdesk - Secure.  
Nel caso di TOPdesk - Secure, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, seguire questa procedura:
1. Accedere al sito aziendale di **TOPdesk - Secure** come amministratore.
2. Nel menu in alto fare clic su **TOPdesk \> New (Nuovo) \> Support Files (File di supporto) \> Operator (Operatore)**.
   
    ![Operator](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operator")

3. Nella finestra di dialogo **New Operator** seguire questa procedura:
   
    ![New Operator](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "New Operator")
   
    a. Fare clic sulla scheda General.
   
    b. Nella casella di testo **Surname** (Cognome) della sezione **General** (Generale) digitare il cognome di un account di Azure Active Directory valido di cui si vuole eseguire il provisioning.
   
    c. Nella sezione **Location** (Località) selezionare un sito dalla casella **Site** (Sito).
   
    d. Nella casella di testo **Login Name** (Nome di accesso) della sezione **TOPdesk Login** (Accesso TOPdesk) digitare un nome di accesso per l'utente.
   
    e. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TOPdesk - Secure per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Per assegnare gli utenti a TOPdesk - Secure, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **TOPdesk - Secure ** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



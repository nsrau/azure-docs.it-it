---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public |Documentazione Microsoft'
description: Informazioni su come usare TOPdesk - Public con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 54c7da3fc0693ee44358cb77973c8c37e653d3cc
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public
Questa esercitazione descrive l'integrazione di Azure e TOPdesk - Public.  
Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di TOPdesk - Public abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TOPdesk - Public potranno accedere all'applicazione tramite il sito aziendale di TOPdesk - Public (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione TOPdesk - Public
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---public"></a>Abilitazione dell'integrazione dell'applicazione TOPdesk - Public
Questa sezione descrive come abilitare l'integrazione dell'applicazione per TOPdesk - Public.

### <a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Per abilitare l'integrazione dell'applicazione per TOPdesk - Public, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **TOPdesk - Public**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **TOPdesk - Public** e quindi fare clic su **Complete** (Completa) per aggiungere l'applicazione.
   
    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a TOPdesk - Public tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  
La configurazione dell'accesso Single Sign-On per TOPdesk - Public richiede il caricamento di un file con l'icona del logo. Per ottenere il file con l'icona, contattare il team di supporto di TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Accedere al sito aziendale di **TOPdesk - Public** come un amministratore.

2. Nel menu **TOPdesk** fare clic su **Settings** (Impostazioni).
   
    ![Impostazioni](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Impostazioni")

3. Fare clic su **Login Settings**.
   
    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")

4. Espandere il menu **Login Settings** (Impostazioni accesso) e quindi fare clic su **General** (Generale).
   
    ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")

5. Nell'area **Public** (Publico) della sezione di configurazione **SAML login** (Accesso SAML) seguire questa procedura:
   
    ![Technical Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")
   
    a. Fare clic su **Download** per scaricare il file di metadati pubblici e quindi salvarlo in locale nel computer.
   
    b. Aprire il file dei metadati e quindi individuare il nodo **AssertionConsumerService** .
    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
   
    c. Copiare il valore di **AssertionConsumerService** .  
      
    > [!NOTE]
    > Questo valore sarà necessario nella sezione **Configure App URL** più avanti in questa esercitazione.
    > 
    > 

6. In un'altra finestra del Web browser accedere al portale di **portale di Azure classico** come amministratore.

7. Nella pagina di integrazione dell'applicazione **TOPdesk - Public** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configurare l'accesso Single Sign-On")

8. Nella pagina **Stabilire come si desidera che gli utenti accedano a TOPdesk - Public** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configurare l'accesso Single Sign-On")

9. Nella pagina **Configura URL app** seguire questa procedura:
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configurare l'URL dell'app")
   
    a. Nella casella di testo **URL di accesso TOPdesk - Public** digitare l'URL usato dagli utenti per accedere all'applicazione TOPdesk - Public, ad esempio "*https://qssolutions.topdesk.net*".
   
    b. Nella casella di testo **URL di risposta TOPdesk – Public** incollare il valore dell'**URL AssertionConsumerService di TOPdesk - Public**, ad esempio "*https://qssolutions.topdesk.net/tas/public/login/saml*".
   
    c. Fare clic su **Avanti**.

10. Nella pagina **Configura accesso Single Sign-On in TOPdesk - Public** fare clic su **Scarica metadati** per scaricare il file relativo e quindi salvarlo sul computer locale.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configurare l'accesso Single Sign-On")

11. Per creare un file del certificato, seguire questa procedura:
    
    ![Certificate](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificate")
    
    1. Aprire il file dei metadati scaricato.
    2. Espandere il nodo **RoleDescriptor** con **xsi:type** corrispondente a **fed:ApplicationServiceType**.
    3. Copiare il valore del nodo **X509Certificate** .
    4. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.
12. Nel sito della società TOPdesk - Public scegliere **Settings** (Impostazioni) dal menu **TOPdesk**.
    
    ![Impostazioni](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Impostazioni")
13. Fare clic su **Login Settings**.
    
    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")
14. Espandere il menu **Login Settings** (Impostazioni accesso) e quindi fare clic su **General** (Generale).
    
    ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")
15. Nell'area **Public** (Pubblica) fare clic su **Add** (Aggiungi).
    
    ![Login SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "Login SAML")
16. Nella pagina **SAML configuration assistant** seguire questa procedura:
    
    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")
    
    a. Per caricare il file di metadati scaricato, in **Federation Metadata** (Metadati configurazione) fare clic su **Browse** (Sfoglia).

    b. Per caricare il file del certificato, in **Certificate (RSA)** (Certificato RSA) fare clic su **Browse** (Sfoglia).

    c. Per caricare il file del logo ottenuto dal team di supporto del team di TOPdesk, in **Logo icon** (Icona logo) fare clic su **Browse** (Sfoglia).

    d. Nella casella di testo **User name attribute** (Attributo nome utente) digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. Nella casella di testo **Display name** digitare un nome per la configurazione.

    f. Fare clic su **Save**.

17. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configurare l'accesso Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a TOPdesk - Public, è necessario eseguirne il provisioning in TOPdesk - Public.  
Nel caso di TOPdesk - Public, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, seguire questa procedura:
1. Accedere al sito aziendale di **TOPdesk - Public** come amministratore.

2. Nel menu presente sulla parte superiore fare clic su **TOPdesk \> New (Nuovo) \> Support Files (File di supporto) \> Person (Persona)**.
   
    ![Person](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")

3. Nella finestra di dialogo New Person seguire questa procedura:
   
    ![New Person](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "New Person")
   
    a. Fare clic sulla scheda General.

    b. Nella casella di testo Surname digitare il cognome di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
 
    c. Selezionare un **Site** per l'account.
 
    d. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TOPdesk - Public per eseguire il provisioning degli account utente Azure AD.
> 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### <a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Per assegnare gli utenti a TOPdesk - Public, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **TOPdesk - Public **fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



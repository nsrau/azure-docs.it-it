---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure | Microsoft Docs'
description: Informazioni su come usare TOPdesk - Secure con Azure Active Directory per abilitare l'accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes

---
# Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure
Questa esercitazione descrive l'integrazione di Azure e TOPdesk - Secure. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di TOPdesk - Secure abilitata per l'accesso Single Sign-On

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TOPdesk - Secure potranno accedere all'applicazione tramite il sito aziendale di TOPdesk - Secure (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione TOPdesk - Secure
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scenario")

## Abilitazione dell'integrazione dell'applicazione TOPdesk - Secure
Questa sezione descrive come abilitare l'integrazione dell'applicazione per TOPdesk - Secure.

### Per abilitare l'integrazione dell'applicazione per TOPdesk - Secure, seguire questa procedura:
1. Nel portale di Azure classico fare clic su **Active Directory** nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiunta di un'applicazione](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Aggiunta di un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **TOPdesk - Secure**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **TOPdesk - Secure** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

## Configurazione dell'accesso Single Sign-On
Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a TOPdesk - Secure tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML. La configurazione dell'accesso Single Sign-On per TOPdesk - Secure richiede il caricamento di un file con l'icona del logo. Per ottenere il file con l'icona, contattare il team di supporto di TOPdesk.

### Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Accedere al sito aziendale di **TOPdesk - Secure** come un amministratore.
2. Nel menu **TOPdesk** fare clic su **Settings**.
   
   ![Impostazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Impostazioni")
3. Fare clic su **Login Settings**.
   
   ![Login Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")
4. Espandere il menu **Login Settings** e quindi fare clic su **General**.
   
   ![Generale](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")
5. Nell'area **Secure** della sezione di configurazione **SAML login** seguire questa procedura:
   
   ![Technical Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Technical Settings")
   
   1. Fare clic su **Download** per scaricare il file di metadati pubblici e quindi salvarlo in locale nel computer.
   2. Aprire il file dei metadati e quindi individuare il nodo **AssertionConsumerService**. 
      ![AssertionConsumerService](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "AssertionConsumerService")
   3. Copiare il valore di **AssertionConsumerService**.
      
      > [!NOTE]
      > Questo valore sarà necessario nella sezione **Configure App URL** più avanti in questa esercitazione.
      > 
      > 
6. In un'altra finestra del Web browser accedere al portale di **portale di Azure classico** come amministratore.
7. Nella pagina di integrazione dell'applicazione **TOPdesk - Secure** fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configura accesso Single Sign-On")
8. Nella pagina **Stabilire come si desidera che gli utenti accedano a TOPdesk - Secure** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configura accesso Single Sign-On")
9. Nella pagina **Configura URL app** seguire questa procedura:
   
   ![Configura URL app](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configura URL app")
   
   1. Nella casella di testo **URL di accesso TOPdesk - Secure** digitare l'URL usato dagli utenti per accedere all'applicazione TOPdesk - Secure, ad esempio,"*https://qssolutions.topdesk.net*".
   2. Nella casella di testo **URL di risposta TOPdesk - Secure** il valore di **TOPdesk - Public AssertionConsumerService URL** (ad esempio, "*https://qssolutions.topdesk.net/tas/public/login/saml*").
   3. Fare clic su **Next**.
10. Nella pagina **Configura accesso Single Sign-On in TOPdesk - Secure** per scaricare il file dei metadati, fare clic su **Download metadati** e quindi salvarlo in locale nel computer.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configura accesso Single Sign-On")
11. Per creare un file del certificato, seguire questa procedura:
    
    ![Certificate](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificate")
    
    1. Aprire il file dei metadati scaricato.
    2. Espandere il nodo **RoleDescriptor** con **xsi:type** uguale a **fed:ApplicationServiceType**.
    3. Copiare il valore del nodo **X509Certificate**.
    4. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.
12. Nel sito della società TOPdesk - Secure scegliere **Settings** dal menu **TOPdesk**.
    
    ![Impostazioni](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Impostazioni")
13. Fare clic su **Login Settings**.
    
    ![Login Settings](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")
14. Espandere il menu **Login Settings** e quindi fare clic su **General**.
    
    ![Generale](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Generale")
15. Nell'area **Public** fare clic su **Add**.
    
    ![Add](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Add")
16. Nella pagina **SAML configuration assistant** seguire questa procedura:
    
    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML Configuration Assistant")
    
    1. Per caricare il file di metadati scaricato, in **Federation Metadata** fare clic su **Browse**.
    2. Per caricare il file del certificato, in **Certificate (RSA)** fare clic su **Browse**.
    3. Per caricare il file del logo ottenuto dal team di supporto di TOPdesk, in **Logo icon** fare clic su **Browse**.
    4. Nella casella di testo **User name attribute** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5. Nella casella di testo **Display name** digitare un nome per la configurazione.
    6. Fare clic su **Save**.
17. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Completa** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configura accesso Single Sign-On")

## Configurazione del provisioning utente
Per consentire agli utenti di Azure AD di accedere a TOPdesk - Secure, è necessario eseguirne il provisioning in TOPdesk - Secure. 
Nel caso di TOPdesk - Secure, il provisioning è un'attività manuale.

### Per configurare il provisioning utente, seguire questa procedura:
1. Accedere al sito aziendale di **TOPdesk - Secure** come amministratore.
2. Nel menu in alto fare clic su **TOPdesk > New > Support Files > Operator**.
   
   ![Operatore](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operatore")
3. Nella finestra di dialogo **New Operator** seguire questa procedura:
   
   ![New Operator](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "New Operator")
   
   1. Fare clic sulla scheda General.
   2. Nella casella di testo **Surname** della sezione **General** digitare il cognome di un account di Azure Active Directory valido di cui si vuole eseguire il provisioning.
   3. Selezionare un **Site** per l'account nella sezione **Location**.
   4. Nella casella di testo **Login Name** della sezione **TOPdesk Login** digitare un nome di accesso per l'utente.
   5. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TOPdesk - Secure per eseguire il provisioning degli account utente Azure AD.
> 
> 

## Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

### Per assegnare gli utenti a TOPdesk - Secure, seguire questa procedura:
1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **TOPdesk - Secure** fare clic su **Assegna utenti**.
   
   ![Assegna utenti](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Assegna utenti")
3. Selezionare l'utente test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per informazioni dettagliate sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0914_2016-->
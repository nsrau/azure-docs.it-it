---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Kemp LoadMaster Azure AD Integration | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kemp LoadMaster - Azure AD Integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 7ac50acbf9b1b4371ac17f997828f9b8818e53b0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459117"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Kemp LoadMaster - Azure AD Integration

Questa esercitazione descrive come integrare Kemp LoadMaster - Azure AD Integration con Azure Active Directory (Azure AD). L'integrazione di Kemp LoadMaster - Azure AD Integration con Azure AD, consente di:

* Controllare in Azure AD chi può accedere a Kemp LoadMaster - Azure AD Integration.
* Abilitare gli utenti per l'accesso automatico a Kemp LoadMaster - Azure AD Integration con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Kemp LoadMaster - Azure AD Integration abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Kemp LoadMaster - Azure AD Integration supporta l'accesso SSO avviato da **IDP**
* Dopo aver configurato l'integrazione di Azure AD per Kemp LoadMaster, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Aggiunta di Kemp LoadMaster - Azure AD Integration dalla raccolta

Per configurare l'integrazione di Kemp LoadMaster - Azure AD Integration in Azure AD, è necessario aggiungere Kemp LoadMaster - Azure AD Integration dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** .
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni** .
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** .
1. Nella sezione **Aggiungi dalla raccolta** digitare **Kemp LoadMaster - Azure AD Integration** nella casella di ricerca.
1. Selezionare **Kemp LoadMaster - Azure AD Integration** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Configurare e testare l'accesso Single Sign-On (SSO) di Azure AD per Kemp LoadMaster - Azure AD Integration

Configurare e testare l'accesso SSO di Azure AD con Kemp LoadMaster - Azure AD Integration usando un utente di test con il nome **B.Simon** . Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kemp LoadMaster - Azure AD Integration.

Per configurare e testare l'accesso SSO di Azure AD con Kemp LoadMaster - Azure AD Integration, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Kemp LoadMaster - Azure AD Integration](#configure-kemp-loadmaster-azure-ad-integration-sso)** : per configurare le impostazioni dell'accesso Single Sign-On sul lato applicazione.

1. **[Server Web di pubblicazione](#publishing-web-server)**
    1. **[Creare un servizio virtuale](#create-a-virtual-service)**
    1. **[Certificati e sicurezza](#certificates-and-security)**
    1. **[Profilo SAML di Kemp LoadMaster - Azure AD Integration](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Verificare le modifiche](#verify-the-changes)**
1. **[Configurare l'autenticazione basata su Kerberos](#configuring-kerberos-based-authentication)**
    1. **[Creare un account di delega Kerberos per Kemp LoadMaster - Azure AD Integration](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Delega vincolata Kerberos (account di delega Kerberos) per Kemp LoadMaster - Azure AD Integration](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Enterprise Security Package per Kemp LoadMaster - Azure AD Integration](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Creare l'utente di test di Kemp LoadMaster - Azure AD Integration](#create-kemp-loadmaster-azure-ad-integration-test-user)** : per avere una controparte di B.Simon in Kemp LoadMaster - Azure AD Integration collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Kemp LoadMaster - Azure AD Integration** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On** .
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML** .
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. Nella casella di testo **URL di risposta** digitare un URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Kemp LoadMaster - Azure AD Integration](mailto:support@kemp.ax). È anche possibile fare riferimento ai modelli mostrati nella sezione Configurazione SAML di base del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base 64)** e il file **XML dei metadati della federazione** , quindi selezionare **Scarica** per scaricare il file del certificato e il file XML dei metadati della federazione e salvarli nel computer.

    ![Collegamento di download del certificato](./media/kemp-tutorial/certificate-base-64.png)

1. Nella sezione **ConfiguraKemp LoadMaster - Azure AD Integration** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password** .
   1. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kemp LoadMaster - Azure AD Integration.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni** .
1. Nell'elenco delle applicazioni selezionare **Kemp LoadMaster - Azure AD Integration** .
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi** .

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Configurare l'accesso Single Sign-On per Kemp LoadMaster - Azure AD Integration

## <a name="publishing-web-server"></a>Pubblicazione del server Web 

### <a name="create-a-virtual-service"></a>Creare un servizio virtuale 

1. Passare all'interfaccia utente Web LoadMaster di Kemp LoadMaster - Azure AD Integration > Virtual Services > Add New (Servizi virtuali > Aggiungi nuovo).

1. Fare clic su Add New (Aggiungi nuovo).

1. Specificare i parametri per il servizio virtuale.

    ![Screenshot che mostra la pagina "Please Specify the Parameters for the Virtual Service" con i valori di esempio nelle caselle.](./media/kemp-tutorial/kemp-1.png)

    a. Virtual Address (Indirizzo virtuale)
    
    b. Porta
    
    c. Service Name (Optional) (Nome del servizio (facoltativo))
    
    d. Protocollo 

1. Passare alla sezione Real Servers (Server reali).

1. Fare clic su Add New (Aggiungi nuovo).

1. Specificare i parametri per il server reale.
    
    ![Screenshot che mostra la pagina "Please Specify the Parameters for the Virtual Service" con i valori di esempio nelle caselle.](./media/kemp-tutorial/kemp-2.png)

    a. Selezionare Allow Remote Addresses (Consenti indirizzi remoti)
    
    b. Digitare l'indirizzo del server reale
    
    c. Porta
    
    d. Forwarding method (Metodo di inoltro)
    
    e. Peso
    
    f. Connection Limit (Limite connessione)
    
    g. Fare clic su Add This Real Server (Aggiungi questo server reale)

## <a name="certificates-and-security"></a>Certificati e sicurezza
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Importare il certificato in Kemp LoadMaster - Azure AD Integration 
 
1. Passare al portale Web di Kemp LoadMaster - Azure AD Integration > Certificates & Security > SSL Certificates (Certificati e sicurezza > Certificati SSL).

1. Selezionare Manage Certificates > Certificate Configuration (Gestisci certificati > Configurazione certificato).

1. Fare clic su Import Certificate (Importa certificato).

1. Specificare il nome del file contenente il certificato. Il file può anche contenere la chiave privata. Se il file non contiene la chiave privata, è necessario specificare anche il file contenente la chiave privata. Il formato del certificato può essere PEM o PFX (IIS).

1. Fare clic su Choose file (Scegli file) in Certificate file (File del certificato).

1. Fare clic su Key file (optional) (File di chiave (facoltativo)).

1. Fare clic su Salva.

### <a name="ssl-acceleration"></a>Accelerazione SSL
 
1. Passare all'interfaccia utente Web di Kemp Load Master > Virtual Services > View/Modify Services (Servizi virtuali > Visualizza/Modifica servizi).

1. Fare clic su Modify (Modifica) in Operation (Operazione).

1. Fare clic su SSL Properties (Proprietà SSL), che funziona al livello 7.
    
    ![Screenshot che mostra la sezione "S S L Properties" con l'opzione "S S L Acceleration - Enabled" selezionata e un certificato di esempio selezionato.](./media/kemp-tutorial/kemp-3.png)
    
    a. Fare clic su Enabled (Abilitata) in SSL Acceleration (Accelerazione SSL).
    
    b. In Available Certificates (Certificati disponibili) selezionare il certificato importato e fare clic sul simbolo `>`.
    
    c. Quando il certificato SSL desiderato viene visualizzato in Assigned Certificates (Certificati assegnati), fare clic su **Set Certificates** (Imposta certificati).

    > [!NOTE]
    > Assicurarsi di fare clic su **Set Certificates** (Imposta certificati).

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Profilo SAML di Kemp LoadMaster - Azure AD Integration
 
### <a name="import-idp-certificate"></a>Importare il certificato del provider di identità

Passare alla console Web di Kemp LoadMaster - Azure AD Integration 

1. Fare clic su Intermediate Certificates (Certificati intermedi) in Certificates and Authority (Certificati e autorità).

    ![Screenshot che mostra la sezione "Currently installed Intermediate Certificates" con un certificato di esempio selezionato.](./media/kemp-tutorial/kemp-6.png)

    a. Fare clic su Choose file (Scegli file) in Add a new Intermediate Certificate (Aggiungi un nuovo certificato intermedio).
    
    b. Passare al file del certificato scaricato in precedenza dall'applicazione aziendale di Azure AD.
    
    c. Fare clic su Open (Apri).
    
    d. Specificare un nome in Certificate Name (Nome certificato).
    
    e. Fare clic su Add Certificate (Aggiungi certificato).

### <a name="create-authentication-policy"></a>Creare i criteri di autenticazione 
 
Passare a Manage SSO (Gestisci SSO) in Virtual Services (Servizi virtuali).

   ![Screenshot che mostra la pagina "Manage S S O".](./media/kemp-tutorial/kemp-7.png)
   
   a. Fare clic su Add (Aggiungi) in Add new Client Side Configuration (Aggiungi nuova configurazione lato client) dopo averle assegnato un nome.

   b. In Authentication Protocol (Protocollo di autenticazione) selezionare SAML.

   c. Selezionare MetaData File (File di metadati) in IdP Provisioning (Provisioning IdP). 

   d. Fare clic su Choose file (Scegli file).

   e. Passare al file XML scaricato in precedenza dal portale di Azure.

   f. Fare clic su Open (Apri) e quindi su Import IdP MetaData File (Importa file di metadati IdP).

   g. Selezionare il certificato intermedio dal certificato IdP.

   h. In SP Entity ID (ID entità provider di servizi) impostare un valore corrispondente all'identità creata nel portale di Azure 

   i. Fare clic su Set SP Entity ID (Imposta entità provider di servizi).

### <a name="set-authentication"></a>Impostare l'autenticazione  
 
Nella console Web di Kemp LoadMaster - Azure AD Integration

1. Fare clic su Virtual Services (Servizi virtuali).

1. Fare clic su View/Modify Services (Visualizza/Modifica servizi).

1. Fare clic su Modify (Modifica) e passare a ESP Options (Opzioni ESP).
    
    ![Screenshot che mostra la pagina "View/Modify Services" con le sezioni "ESP Options" e "Real Servers" espanse.](./media/kemp-tutorial/kemp-8.png)

    a. Fare clic su Enable ESP (Abilita ESP).
    
    b. Selezionare SAML in Client Authentication Mode (Modalità di autenticazione client).
    
    c. Selezionare l'autenticazione lato client creata in precedenza in SSO Domain (Dominio SSO).
    
    d. Digitare il nome host in Allowed Virtual Hosts (Host virtuali consentiti) e fare clic su Set Allowed Virtual Hosts (Imposta host virtuali consentiti).
    
    e. Digitare /* in Allowed Virtual Directories (Directory virtuali consentite), in base ai requisiti di accesso, e fare clic su Set Allowed Directories (Imposta directory consentite).

### <a name="verify-the-changes"></a>Verificare le modifiche 
 
Passare all'URL dell'applicazione 

Verrà visualizzata la pagina di accesso al tenant invece del messaggio di accesso non autenticato visualizzato in precedenza. 

![Screenshot che mostra la pagina "Sign in" di accesso al tenant.](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Configurare l'autenticazione basata su Kerberos 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Creare un account di delega Kerberos per Kemp LoadMaster - Azure AD Integration 

1. Creare un account utente (in questo esempio AppDelegation).
    
    ![Screenshot che mostra la finestra "kcd user Properties" con la scheda "Account" selezionata.](./media/kemp-tutorial/kemp-10.png)


    a. Selezionare la scheda Editor attributi.

    b. Passare a servicePrincipalName. 

    c. Selezionare servicePrincipalName e fare clic su Modifica.

    d. Digitare http/kcduser nel campo Valore da aggiungere e fare clic su Aggiungi. 

    e. Fare clic su Applica e su OK. È necessario chiudere la finestra prima di riaprirla (per visualizzare la nuova scheda Delega). 

1. Aprire di nuovo la finestra delle proprietà dell'utente e la scheda Delega diventerà disponibile. 

1. Selezionare la scheda Delega.

    ![Screenshot che mostra la finestra "kcd user Properties" con la scheda "Delegation" selezionata.](./media/kemp-tutorial/kemp-11.png)

    a. Selezionare Utente attendibile per la delega solo ai servizi specificati.

    b. Selezionare Usa un qualsiasi protocollo di autenticazione.

    c. Aggiungere i server reali e aggiungere http come servizio. 
    
    d. Selezionare la casella di controllo Espansa. 

    e. È possibile visualizzare tutti i server con il nome host e il nome di dominio completo.
    
    f. Fare clic su OK.

> [!NOTE]
> Impostare il nome dell'entità servizio nell'applicazione o nel sito Web in base alle esigenze. Per accedere all'applicazione quando è stata impostata l'identità del pool di applicazioni. Per accedere all'applicazione IIS utilizzando il nome di dominio completo, passare al prompt dei comandi del server reale e digitare SetSpn con i parametri necessari. Ad esempio: Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Delega vincolata Kerberos (account di delega Kerberos) per Kemp LoadMaster - Azure AD Integration 

Passare alla console Web di Kemp LoadMaster - Azure AD Integration > Virtual Services > Manage SSO (Servizi virtuali > Gestisci SSO).

![Screenshot che mostra la pagina "Manage S S O - Manage Domain".](./media/kemp-tutorial/kemp-12.png)

a. Passare a Server Side Single Sign On Configurations (Configurazioni Single Sign-On lato server).

b. Digitare il nome in Add new Server-Side Configuration (Aggiungi nuova configurazione lato server) e fare clic su Add (Aggiungi).

c. Selezionare Kerberos Constrained Delegation (Delega vincolata Kerberos) in Authentication Protocol (Protocollo di autenticazione).

d. Digitare il nome di dominio in Kerberos Realm (Area autenticazione Kerberos).

e. Fare clic su Set Kerberos realm (Imposta area autenticazione Kerberos).

f. Digitare l'indirizzo IP del controller di dominio in Kerberos Key Distribution Center (Centro distribuzione chiavi Kerberos).

g. Fare clic su Set Kerberos KDC (Imposta delega vincolata Kerberos).

h. Digitare il nome utente della delega vincolata Kerberos in Kerberos Trusted User Name (Nome utente attendibile Kerberos).

i. Fare clic su Set KCD trusted user name (Imposta nome utente attendibile delega vincolata Kerberos).

j. Digitare la password in Kerberos Trusted User Password (Password utente attendibile Kerberos).

k. Fare clic su Set KCD trusted user password (Imposta password attendibile delega vincolata Kerberos).

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Enterprise Security Package per Kemp LoadMaster - Azure AD Integration 

Passare a Virtual Services > View/Modify Services (Servizi virtuali > Visualizza/Modifica servizi).

![Server Web di Kemp LoadMaster - Azure AD Integration](./media/kemp-tutorial/kemp-13.png)

a. Fare clic su Modify (Modifica) sul nome alternativo del servizio virtuale.
    
b. Fare clic su ESP Options (Opzioni ESP).
    
c. In Server Authentication Mode (Modalità di autenticazione server) selezionare KCD (Delega vincolata Kerberos).
        
d. In Server-Side Configuration (Configurazione lato server) selezionare il profilo lato server creato in precedenza.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Creare l'utente di test in Kemp LoadMaster - Azure AD Integration

In questa sezione viene creato un utente di nome B.Simon in Kemp LoadMaster - Azure AD Integration. Collaborare con il [team di supporto tecnico client di Kemp LoadMaster - Azure AD Integration](mailto:support@kemp.ax) per aggiungere gli utenti nella piattaforma di Kemp LoadMaster - Azure AD Integration, Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Kemp LoadMaster - Azure AD Integration nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kemp LoadMaster - Azure AD Integration per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)

- [Provare Kemp LoadMaster - Azure AD Integration con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

- [Come proteggere Kemp LoadMaster - Azure AD Integration con visibilità e controlli avanzati](/cloud-app-security/proxy-intro-aad)
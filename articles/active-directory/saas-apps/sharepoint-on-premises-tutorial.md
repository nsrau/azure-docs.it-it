---
title: 'Esercitazione: Integrazione di Azure Active Directory con SharePoint locale | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SharePoint locale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.openlocfilehash: f30b2356b9d3d8ecf7afcdd8ad039a1f02c47550
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438240"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Esercitazione: Integrazione di Azure Active Directory con SharePoint locale

Questa esercitazione descrive come integrare SharePoint locale con Azure Active Directory (Azure AD).

L'integrazione di SharePoint locale con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SharePoint locale.
- È possibile abilitare gli utenti per l'accesso automatico a SharePoint locale (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SharePoint locale, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SharePoint locale abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SharePoint locale dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Aggiunta di SharePoint locale dalla raccolta
Per configurare l'integrazione di SharePoint locale in Azure AD, è necessario aggiungere SharePoint locale dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SharePoint locale dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **SharePoint locale**, selezionare **SharePoint locale** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SharePoint locale nell'elenco risultati](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SharePoint locale usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di SharePoint locale corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SharePoint locale.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SharePoint locale, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Concedere l'accesso a un utente di test di SharePoint locale](#grant-access-to-sharePoint-on-premises-test-user)**: per avere una controparte di Britta Simon in SharePoint locale collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SharePoint locale.

**Per configurare l'accesso Single Sign-On di Azure AD con SharePoint locale, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SharePoint locale** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

1. Nella sezione **URL e dominio SharePoint locale** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SharePoint locale](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Nella casella di testo **Identificatore** digitare l'URL: `urn:sharepoint:federation`

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di SharePoint locale** fare clic su **Configura SharePoint locale** per aprire la finestra **Configura accesso**. Copiare **URL servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configurazione di SharePoint locale](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > L'applicazione SharePoint locale usa il token SAML 1.1, quindi Azure AD attende la richiesta di WS Fed dal server di SharePoint e, dopo l'autenticazione, emette il token SAML 1.1.

1. In un'altra finestra del Web browser accedere al sito aziendale di SharePoint locale come amministratore.

1. **Configurare un nuovo provider di identità attendibile in SharePoint Server 2016**

    Accedere al server di SharePoint Server 2016 e aprire la shell di gestione SharePoint 2016. Compilare i valori di $realm, $wsfedurl e $filepath dal portale di Azure ed eseguire i comandi seguenti per configurare un nuovo provider di identità attendibile.

    > [!TIP]
    > Se non si ha familiarità con l'uso di PowerShell o per altre informazioni sul funzionamento di PowerShell, vedere [SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Successivamente, eseguire questi passaggi per abilitare il provider di identità attendibili per l'applicazione:

    a. In Amministrazione centrale passare a **Gestione applicazione Web** e selezionare l'applicazione Web che si vuole proteggere con Azure AD.

    b. Nella barra multifunzione fare clic su **Provider di autenticazione** e selezionare la zona che si vuole usare.

    c. Selezionare **Provider di identità attendibili** e selezionare il provider di identità appena registrato denominato *AzureAD*.

    d. Nell'impostazione dell'URL della pagina di accesso scegliere **Personalizza pagina di accesso** e fornire il valore "/_trust/".

    e. Fare clic su **OK**.

    ![Configurare il provider di autenticazione](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Concedere l'accesso a un utente di test di SharePoint locale

Gli utenti che si registreranno ad Azure AD ed effettueranno l'accesso a SharePoint dovranno disporre dell'accesso all'applicazione  Usare i passaggi seguenti per impostare le autorizzazioni per accedere all'applicazione Web.

1. In Amministrazione centrale fare clic su **Gestione applicazioni**.

1. Nella pagina **Gestione applicazioni**, nella sezione **Applicazioni Web** fare clic su **Gestisci applicazioni Web**.

1. Fare clic sull'applicazione Web appropriata e quindi fare clic su **Criteri utente**.

1. In Criteri per applicazioni Web fare clic su **Aggiungi utenti**.

    ![Ricerca di un utente in base all'attestazione del nome](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

1. Nella finestra di dialogo **Aggiungi utenti** fare clic sulla zona appropriata in **Zone**e quindi fare clic su **Avanti**.

1. Nella finestra di dialogo **Criteri per applicazioni Web**, nella sezione **Seleziona utenti** fare clic sull'icona **Sfoglia**.

1. Nella casella di testo **Trova** digitare il valore del **nome dell'entità utente (UPN)** per il quale è stata configurata l'applicazione locale di SharePoint in Azure AD e fare clic su **Ricerca**. </br>Esempio: *brittasimon@contoso.com*.

1. Sotto l'intestazione AzureAD nella visualizzazione elenco selezionare la proprietà relativa al nome e fare clic su **Aggiungi**, quindi fare clic su **OK** per chiudere la finestra di dialogo.

1. In Autorizzazioni fare clic su **Controllo completo**.

    ![Concessione del controllo completo a un utente di attestazioni](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

1. Fare clic su **Avanti**, quindi su **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configurazione di un provider di identità attendibile per più applicazioni Web

La configurazione funziona per una singola applicazione Web, ma necessita di una configurazione aggiuntiva se si prevede di usare lo stesso provider di identità attendibile per più applicazioni Web. Si supponga ad esempio di aver esteso un'applicazione Web per usare l'URL `https://portal.contoso.local` e di voler ora autenticare anche gli utenti `https://sales.contoso.local`. A tale scopo, è necessario aggiornare il provider di identità per rispettare il parametro WReply e aggiornare la registrazione dell'applicazione in Azure AD per aggiungere un URL di risposta.

1. Nel portale di Azure aprire la directory di Azure AD. Fare clic su **Registrazioni per l'app**, quindi su **Visualizza tutte le applicazioni**. Fare clic sull'applicazione creata in precedenza (integrazione SAML di SharePoint).

1. Fare clic su **Impostazioni**.

1. Nel pannello Impostazioni fare clic su **URL di risposta**. 

1. Aggiungere l'URL per l'applicazione Web aggiuntiva con `/_trust/default.aspx` aggiunto all'URL (ad esempio `https://sales.contoso.local/_trust/default.aspx`) e fare clic su **Salva**.

1. Nel server di SharePoint aprire la **shell di gestione SharePoint 2016** ed eseguire i comandi seguenti, usando il nome dell'autorità di certificazione del token di identità attendibili adoperato in precedenza.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. In Amministrazione centrale passare all'applicazione Web e abilitare il provider di identità attendibili esistente. È necessario anche configurare l'URL nella pagina di accesso come una pagina di accesso personalizzata `/_trust/`.

1. In Amministrazione centrale fare clic sull'applicazione Web e scegliere **Criteri utente**. Aggiungere un utente con le autorizzazioni appropriate, come illustrato in precedenza in questo articolo.

### <a name="fixing-people-picker"></a>Correzione della selezione utenti

Gli utenti possono ora accedere a SharePoint 2016 con identità di Azure AD, ma l'esperienza utente può ancora migliorare. Ad esempio, la ricerca di un utente presenta molteplici risultati della ricerca nella selezione utenti. È disponibile un risultato di ricerca per ognuno dei 3 tipi di attestazioni creati nel mapping delle attestazioni. Per scegliere un utente con la selezione utenti, è necessario digitare esattamente il nome utente e scegliere il risultato dell'attestazione **name**.

![Risultati della ricerca di attestazioni](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Non essendo previste convalide per i valori cercati, possono verificarsi errori di ortografia o la scelta accidentale del tipo errato di attestazione da assegnare, ad esempio l'attestazione **SurName**. Questo può impedire agli utenti di accedere correttamente alle risorse.

Per assistenza in questo scenario, esiste una soluzione open source denominata [AzureCP](https://yvand.github.io/AzureCP/) che fornisce un provider di attestazioni personalizzate per SharePoint 2016. Tale soluzione userà Azure AD Graph per risolvere quanto immesso dagli utenti ed eseguirà la convalida. Altre informazioni su [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a SharePoint locale.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a SharePoint locale, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

1. Nell'elenco delle applicazioni selezionare **SharePoint locale**.

    ![Collegamento di SharePoint nell'elenco delle applicazioni](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SharePoint locale nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SharePoint locale.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Uso di Azure AD per l'autenticazione di SharePoint Server](https://docs.microsoft.com/en-us/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media\sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media\sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media\sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media\sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media\sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media\sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media\sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media\sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media\sharepoint-on-premises-tutorial/tutorial_general_203.png


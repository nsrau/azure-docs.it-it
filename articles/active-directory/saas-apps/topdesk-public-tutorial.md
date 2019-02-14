---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TOPdesk - Public.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89412040fdea32746574d8ae5bada9c017617b80
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184993"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Public

Questa esercitazione descrive come integrare TOPdesk - Public con Azure Active Directory (Azure AD).

L'integrazione di TOPdesk - Public con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a TOPdesk - Public.
- È possibile abilitare gli utenti per l'accesso automatico a TOPdesk - Public (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TOPdesk - Public, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di TOPdesk - Public abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di TOPdesk - Public dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-topdesk---public-from-the-gallery"></a>Aggiunta di TOPdesk - Public dalla raccolta
Per configurare l'integrazione di TOPdesk - Public in Azure AD, è necessario aggiungere TOPdesk - Public dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere TOPdesk - Public dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **TOPdesk - Public**, selezionare **TOPdesk - Public** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![TOPdesk - Public nell'elenco dei risultati](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TOPdesk - Public usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di TOPdesk - Public che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TOPdesk - Public.

Per stabilire la relazione di collegamento, in TOPdesk - Public assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con TOPdesk - Public, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test per TOPdesk - Public](#create-a-topdesk---public-test-user)**: per avere una controparte di Britta Simon in TOPdesk - Public collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione OPdesk - Public.

**Per configurare Single Sign-On di Azure AD con TOPdesk - Public, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TOPdesk - Public** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

1. Nella sezione **URL e dominio TOPdesk - Public** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di TOPdesk - Public](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.topdesk.net`
    
    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. L'URL di risposta è descritto più avanti nell'esercitazione. Per ottenere questi valori, contattare il [team di supporto clienti di TOPdesk - Public](https://help.topdesk.com/saas/enterprise/user/).  

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di TOPdesk - Public** fare clic su **Configura TOPdesk - Public** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di TOPdesk - Public](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

1. Accedere al sito aziendale di **TOPdesk - Public** come un amministratore.

1. Nel menu **TOPdesk** fare clic su **Settings** (Impostazioni).
   
    ![Impostazioni](./media/topdesk-public-tutorial/ic790598.png "Impostazioni")

1. Fare clic su **Login Settings**.
   
    ![Login Settings](./media/topdesk-public-tutorial/ic790599.png "Login Settings")

1. Espandere il menu **Login Settings** (Impostazioni accesso) e quindi fare clic su **General** (Generale).
   
    ![General](./media/topdesk-public-tutorial/ic790600.png "General")

1. Nell'area **Public** (Publico) della sezione di configurazione **SAML login** (Accesso SAML) seguire questa procedura:
   
    ![Technical Settings](./media/topdesk-public-tutorial/ic790601.png "Technical Settings")
   
    a. Fare clic su **Download** per scaricare il file di metadati pubblici e quindi salvarlo in locale nel computer.
   
    b. Aprire il file dei metadati scaricato e quindi individuare il nodo **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Copiare il valore **AssertionConsumerService**, incollarlo nella casella di testo **URL di risposta** nella sezione **URL e dominio TOPdesk - Public**.      
   
1. Per creare un file del certificato, seguire questa procedura:
    
    ![Certificate](./media/topdesk-public-tutorial/ic790606.png "Certificate")
    
    a. Aprire il file di metadati scaricato dal portale di Azure.
    
    b. Espandere il nodo **RoleDescriptor** con **xsi:type** corrispondente a **fed:ApplicationServiceType**.
    
    c. Copiare il valore del nodo **X509Certificate** .
    
    d. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.

1. Nell'area **Public** (Pubblica) fare clic su **Add** (Aggiungi).
    
    ![Login SAML](./media/topdesk-public-tutorial/ic790625.png "Login SAML")

1. Nella pagina **SAML configuration assistant** seguire questa procedura:
    
    ![SAML Configuration Assistant](./media/topdesk-public-tutorial/ic790608.png "SAML Configuration Assistant")
    
    a. Per caricare il file di metadati scaricato dal portale di Azure, in **Metadati della federazione** fare clic su **Sfoglia**.

    b. Per caricare il file del certificato, in **Certificate (RSA)** (Certificato RSA) fare clic su **Browse** (Sfoglia).

    c. Per caricare il file del logo ottenuto dal team di supporto del team di TOPdesk, in **Logo icon** (Icona logo) fare clic su **Browse** (Sfoglia).

    d. Nella casella di testo **User name attribute** (Attributo nome utente) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Nella casella di testo **Display name** digitare un nome per la configurazione.

    f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/topdesk-public-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/topdesk-public-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/topdesk-public-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-topdesk---public-test-user"></a>Creare un utente di test di TOPdesk - Public

Per consentire agli utenti di Azure AD di accedere a TOPdesk - Public, è necessario eseguirne il provisioning in TOPdesk - Public.  
Nel caso di TOPdesk - Public, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **TOPdesk - Public** come amministratore.

1. Nel menu presente sulla parte superiore fare clic su **TOPdesk \> New (Nuovo) \> Support Files (File di supporto) \> Person (Persona)**.
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

1. Nella finestra di dialogo New Person seguire questa procedura:
   
    ![New Person](./media/topdesk-public-tutorial/ic790629.png "New Person")
   
    a. Fare clic sulla scheda General.

    b. Digitare il cognome dell'utente, ad esempio Simon, nella casella di testo **Surname** (Cognome)
 
    c. Selezionare un **Site** per l'account.
 
    d. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione account utente fornita da TOPdesk - Public per eseguire il provisioning degli account utente Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a TOPdesk - Public.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a TOPdesk - Public, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **TOPdesk - Public**.

    ![Collegamento TOPdesk - Public nell'elenco delle applicazioni](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TOPdesk - Public nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TOPdesk - Public.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 9919be128ae651b589a37f957cc59ce6d171143f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431733"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile

Questa esercitazione descrive come integrare Citrix ShareFile con Azure Active Directory (Azure AD).

L'integrazione di Citrix ShareFile con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Citrix ShareFile.
- È possibile abilitare gli utenti per l'accesso automatico a Citrix ShareFile (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Citrix ShareFile, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Citrix ShareFile abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiungere Citrix ShareFile dalla raccolta
1. Configurare e testare l'accesso Single Sign-On di Azure AD

## <a name="add-citrix-sharefile-from-the-gallery"></a>Aggiungere Citrix ShareFile dalla raccolta
Per configurare l'integrazione di Citrix ShareFile in Azure AD, è necessario aggiungere Citrix ShareFile dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Citrix ShareFile dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Citrix ShareFile**, selezionare **Citrix ShareFile** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Citrix ShareFile nell'elenco risultati](./media/sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Citrix ShareFile in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Citrix ShareFile che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix ShareFile.

Per stabilire la relazione di collegamento, in Citrix ShareFile assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Citrix ShareFile, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente test di Citrix ShareFile](#create-a-citrix-sharefile-test-user)**: per avere una controparte di Britta Simon in Citrix ShareFile collegata alla relativa rappresentazione in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Citrix ShareFile.

**Per configurare Single Sign-On di Azure AD con Citrix ShareFile, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/sharefile-tutorial/tutorial_sharefile_samlbase.png)

1. Nella sezione **Citrix ShareFile Domain and URLs** (URL e dominio Citrix ShareFile) seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Citrix ShareFile](./media/sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenant-name>.sharefile.com/saml/login`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL usando il criterio seguente:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Citrix ShareFile](https://www.citrix.co.in/products/sharefile/support.html).

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/sharefile-tutorial/tutorial_sharefile_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/sharefile-tutorial/tutorial_general_400.png)

1. Nel **Citrix ShareFile Configuration** (Configurazione di Citrix ShareFile) fare clic su **Configure Citrix ShareFile** (Configura Citrix ShareFile) per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Citrix ShareFile](./media/sharefile-tutorial/tutorial_sharefile_configure.png)

1. In un'altra finestra del Web browser accedere al sito aziendale di **Citrix ShareFile** come amministratore.

1. Nel barra degli strumenti in alto fare clic su **Admin**.

1. Nel riquadro di spostamento sinistro selezionare **Configure Single Sign-On**.
   
    ![Account amministratore](./media/sharefile-tutorial/ic773627.png "Account amministratore")

1. In **Basic Settings** della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** seguire questa procedura:
   
    ![Single Sign-On](./media/sharefile-tutorial/ic773628.png "Single Sign-On")
   
    a. Fare clic su **Enable SAML**.
    
    b. Nella casella di testo **Your IDP Issuer/ Entity ID** (Autorità di certificazione IdP/ID entità) incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure.

    c. Fare clic su **Change** (Cambia) accanto al campo **X.509 Certificate** (Certificato X.509) e quindi caricare il certificato scaricato dal portale di Azure.
    
    d. Nella casella di testo **URL di accesso** incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
    
    e. Nella casella di testo **Logout URL** (URL di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

1. Fare clic su **Save** nel portale di gestione di Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/sharefile-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/sharefile-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/sharefile-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/sharefile-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-citrix-sharefile-test-user"></a>Creare un utente test di Citrix ShareFile

Per consentire agli utenti di Azure AD di accedere a Citrix ShareFile, è necessario eseguirne il provisioning in Citrix ShareFile. Nel caso di Citrix ShareFile, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Citrix ShareFile** .

1. Fare clic su **Manage Users \> Manage Users Home \> + Create Employee** (Gestisci utenti > Gestisci home utenti > + Crea dipendente).
   
   ![Crea dipendente](./media/sharefile-tutorial/IC781050.png "Crea dipendente")

1. Nella sezione **Informazioni di base** seguire questa procedura:
   
   ![Informazioni di base](./media/sharefile-tutorial/IC799951.png "Informazioni di base")
   
   a. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica di Britta Simon come **brittasimon@contoso.com**.
   
   b. Nella casella di testo **First Name** (Nome) digitare il **nome** dell'utente come **Britta**.
   
   c. Nella casella di testo **Last Name** (Cognome) digitare il **cognome** dell'utente come **Simon**.

1. Fare clic su **Add User**.
  
   >[!NOTE]
   >Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica e selezionerà un collegamento per confermare il proprio account prima che venga attivato. È possibile usare qualsiasi altro strumento di creazione di account utente Citrix ShareFile o qualsiasi API fornita da Citrix ShareFile per effettuare il provisioning degli account utente di Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, tramite la concessione dell'accesso a Citrix ShareFile.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Citrix ShareFile, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni, selezionare **Citrix ShareFile**.

    ![Collegamento di Citrix ShareFile nell'elenco delle applicazioni](./media/sharefile-tutorial/tutorial_sharefile_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Citrix ShareFile nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Citrix ShareFile.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharefile-tutorial/tutorial_general_01.png
[2]: ./media/sharefile-tutorial/tutorial_general_02.png
[3]: ./media/sharefile-tutorial/tutorial_general_03.png
[4]: ./media/sharefile-tutorial/tutorial_general_04.png

[100]: ./media/sharefile-tutorial/tutorial_general_100.png

[200]: ./media/sharefile-tutorial/tutorial_general_200.png
[201]: ./media/sharefile-tutorial/tutorial_general_201.png
[202]: ./media/sharefile-tutorial/tutorial_general_202.png
[203]: ./media/sharefile-tutorial/tutorial_general_203.png

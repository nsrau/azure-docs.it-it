---
title: 'Esercitazione: Integrazione di Azure Active Directory con BitaBIZ | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 2a05a4f1b9162a69e074bf6243236df48c8ce536
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429663"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Esercitazione: Integrazione di Azure Active Directory con BitaBIZ

Questa esercitazione descrive come integrare BitaBIZ con Azure Active Directory (Azure AD).

L'integrazione di BitaBIZ con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a BitaBIZ.
- È possibile abilitare gli utenti per l'accesso automatico a BitaBIZ (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con BitaBIZ, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Un sottoscrizione BitaBIZ abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di BitaBIZ dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-bitabiz-from-the-gallery"></a>Aggiunta di BitaBIZ dalla raccolta
Per configurare l'integrazione di BitaBIZ in Azure AD, è necessario aggiungere BitaBIZ dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere BitaBIZ dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **BitaBIZ** selezionare **BitaBIZ** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![BitaBIZ nell'elenco risultati](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BitaBIZ in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di BitaBIZ che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BitaBIZ.

Per stabilire la relazione di collegamento, in BitaBIZ assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con BitaBIZ, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di BitaBIZ](#create-a-bitabiz-test-user)**: per avere una controparte di Britta Simon in BitaBIZ collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione BitaBIZ.

**Per configurare Single Sign-On di Azure AD con BitaBIZ, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **BitaBIZ** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

1. Nella sezione **URL e dominio BitaBIZ** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da IDP:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > Il valore nell'URL precedente è solo a scopo dimostrativo. Aggiornare il valore con l'attuale identificatore, come viene illustrato più avanti nell'esercitazione.

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    Nella casella di testo **URL accesso** digitare l'URL: `https://www.bitabiz.com/dashboard`

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/bitabiz-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di BitaBIZ** fare clic su **Configura BitaBIZ** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

1. In un'altra finestra del browser Web accedere al tenant BitaBIZ come amministratore.

1. Fare clic su **SETUP ADMIN** (CONFIGURA AMMINISTRATORE).

    ![Configurazione di BitaBIZ](./media/bitabiz-tutorial/settings1.png)

1. Fare clic su **Microsoft integrations** (integrazioni Microsoft) nella sezione **Add value** (Aggiungi valore).

    ![Configurazione di BitaBIZ](./media/bitabiz-tutorial/settings2.png)

1. Scorrere fino alla sezione **Microsoft Azure AD (Enable single sign on)** (Microsoft Azure AD (Abilita single sign-on)) ed eseguire la procedura seguente:

    ![Configurazione di BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Copiare il valore dalla casella di testo **ID entità ("Identificatore" in Azure AD)** e incollarlo nella casella di testo **Identificatore** nella sezione **Dominio BitaBIZ e URL** nel portale di Azure. 
    
    b. Nella casella di testo **URL servizio Single Sign-On di Azure AD** incollare **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.
    
    c. Nella casella di testo **Azure AD SAML Entity ID** (ID entità SAML Azure AD) incollare **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure.

    d. Aprire il file del **certificato (Base64)** scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato di firma di Azure AD (con codifica Base64)**.

    e. Aggiungere il nome di dominio della posta elettronica aziendale, ovvero nome azienda.com nella casella di testo **Nome di dominio** per assegnare agli utenti nell'azienda il dominio di posta elettronica SSO (NON OBBLIGATORIO).
    
    f. Contrassegnare l'account BitaBIZ come **SSO enabled** (Abilitato a SSO).
    
    g. Fare clic su **Save Azure AD configuration** (Salva configurazione Azure AD) per salvare e attivare la configurazione di SSO.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/bitabiz-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/bitabiz-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/bitabiz-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-bitabiz-test-user"></a>Creare un utente di test di BitaBIZ

Per consentire agli utenti di Azure AD di accedere a BitaBIZ, è necessario eseguirne il provisioning in BitaBIZ.  
Nel caso di BitaBIZ, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di BitaBIZ come amministratore.

1. Fare clic su **SETUP ADMIN** (CONFIGURA AMMINISTRATORE).

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/settings1.png)

1. Fare clic su **Add users** (Aggiungi utenti) nella sezione **Organization** (Organizzazione).

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/user1.png)

1. Fare clic su **Add new employee** (Aggiungi nuovo dipendente).

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/user2.png)

1. Nella pagina della finestra di dialogo **Add new employee** (Aggiungi nuovo dipendente) eseguire la procedura seguente:

    ![Aggiungi utente BitaBIZ](./media/bitabiz-tutorial/user3.png)

    a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio Britta.

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio Simon.

    c. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    d. Selezionare una data in **Date of employment** (Data di assunzione).

    e. Esistono altri attributi dell'utente non obbligatori che possono essere impostati per l'utente. Consultare il [documento relativo alla configurazione dei dipendenti](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) per altri dettagli.    
    
    f. Fare clic su **Save employee** (Salva dipendente).
    
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    
### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a BitaBIZ.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a BitaBIZ, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **BitaBIZ**.

    ![Collegamento di BitaBIZ nell'elenco delle applicazioni](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro BitaBIZ nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BitaBIZ.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png


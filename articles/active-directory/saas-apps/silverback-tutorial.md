---
title: 'Esercitazione: integrazione di Azure Active Directory con Silverback | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5614c061586c39e44f04f3542285e55e07f14d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172711"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Esercitazione: integrazione di Azure Active Directory con Silverback

Questa esercitazione descrive come integrare Silverback con Azure Active Directory (Azure AD).

L'integrazione di Silverback con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Silverback.
- È possibile abilitare gli utenti per l'accesso automatico a Silverback (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Silverback, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione attiva di Silverback

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Silverback dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-silverback-from-the-gallery"></a>Aggiunta di Silverback dalla raccolta
Per configurare l'integrazione di Silverback in Azure AD, è necessario aggiungere Silverback dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Silverback dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Silverback**, selezionare **Silverback** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Silverback nell'elenco risultati](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Silverback usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Silverback che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Silverback.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Silverback, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Silverback](#create-a-silverback-test-user)**: per avere una controparte di Britta Simon in Silverback collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Silverback.

**Per configurare l'accesso Single Sign-On di Azure AD con Silverback, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Silverback** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. Nella sezione **URL e dominio Silverback** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Silverback](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `<YOURSILVERBACKURL>.com`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di Silverback](mailto:helpdesk@matrix42.com). 

4. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/silverback-tutorial/tutorial_general_400.png)

6.  Accedere al server di Silverback come amministratore e seguire questa procedura:

    a.  Passare a **Admin** (Amministratore)  > **Authentication Provider** (Provider di autenticazione).

    b. Nella pagina **Authentication Provider Settings** (Impostazioni del provider di autenticazione) seguire questa procedura:

    ![L'amministratore ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  Fare clic su **Import from URL** (Importa dall'URL).
    
    d.  Incollare l'URL dei metadati copiato e fare clic su **OK**.
    
    e.  Confermare con **OK** per impostare i valori automaticamente.
    
    f.  Abilitare **Show on Login Page** (Mostra nella pagina di accesso).
    
    g.  Abilitare **Dynamic User Creation** (Creazione utenti dinamica) se si vuole che Azure AD aggiunga automaticamente gli utenti autorizzati. Questa impostazione è facoltativa.
    
    h.  Creare un **titolo** per il pulsante sul portale Self-service.

    i.  Caricare un'**icona** facendo clic su **Choose File** (Scegli file).
    
    j.  Selezionare il **colore** di sfondo per il pulsante.
    
    k.  Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/silverback-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/silverback-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/silverback-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/silverback-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-silverback-test-user"></a>Creare un utente di test di Silverback

Per consentire agli utenti di Azure AD di accedere a Silverback, è necessario effettuarne il provisioning in Silverback. Nel caso di Silverback il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al server di Silverback come amministratore.

2. Passare a **Users** (Utenti) e **aggiungere un nuovo utente del dispositivo**.

3. Nella pagina **Basic** (Informazioni di base) seguire questa procedura:

    ![Utente ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Nella casella di testo **Username** (Nome utente) digitare il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

    d. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon@contoso.com**.

    e. Nella casella di testo **Password** immettere la password.
    
    f. Nella casella di testo **Confirm Password** (Conferma password) immettere nuovamente la password e confermare.

    g. Fare clic su **Save**.

>[!NOTE]
>Se non si vuole creare ogni utente manualmente, abilitare la casella di controllo **Dynamic User Creation** (Creazione utenti dinamica) in **Admin** (Amministratore)  > **Authentication Provider** (Provider di autenticazione).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Silverback.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Silverback, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Silverback**.

    ![Collegamento di Silverback nell'elenco delle applicazioni](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Silverback nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Silverback.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png


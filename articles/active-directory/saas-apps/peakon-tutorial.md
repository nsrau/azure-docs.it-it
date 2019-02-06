---
title: 'Esercitazione: Integrazione di Azure Active Directory con Peakon | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 637988179228fbf0a6000de74a1185af98277e3c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178954"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Esercitazione: Integrazione di Azure Active Directory con Peakon

Questa esercitazione descrive come integrare Peakon con Azure Active Directory (Azure AD).

L'integrazione di Peakon con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Peakon.
- È possibile abilitare gli utenti per l'accesso automatico a Peakon (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Peakon, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Peakon abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Peakon dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-peakon-from-the-gallery"></a>Aggiunta di Peakon dalla raccolta

Per configurare l'integrazione di Peakon in Azure AD, è necessario aggiungere Peakon dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Peakon dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Peakon**, selezionare **Peakon** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Peakon nell'elenco risultati](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Peakon usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Peakon corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Peakon.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Peakon, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Peakon](#creating-a-peakon-test-user)**: per avere una controparte di Britta Simon in Peakon collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Peakon.

**Per configurare l'accesso Single Sign-On di Azure AD con Peakon, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Peakon** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Peakon](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://app.peakon.com/saml/<companyid>/assert`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Peakon](./media/peakon-tutorial/tutorial_peakon_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di risposta effettivi, come illustrato più avanti in questa esercitazione.

6. Nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare il file **Certificato (base)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. Nella sezione **Configura Peakon** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di Peakon](common/configuresection.png)

8. In un'altra finestra del Web browser accedere a Peakon come amministratore.

9. Sulla barra dei menu a sinistra nella pagina fare clic su  **Configuration** (Configurazione) e quindi passare a  **Integrations** (Integrazioni).

    ![Configurazione](./media/peakon-tutorial/tutorial_peakon_config.png)

10. Nella pagina **Integrations** (Integrazioni) fare clic su **Single Sign-On**.

    ![Single Sign-On](./media/peakon-tutorial/tutorial_peakon_single.png)

11. Nella sezione **Single Sign-On** fare clic su **Enable** (Abilita).

    ![Abilitazione](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. Nella sezione **Single sign-on for employees using SAML** (Accesso Single Sign-On per i dipendenti con SAML) seguire questa procedura:

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Nella casella di testo **SSO Login URL** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **SSO Logout URL** (URL di disconnessione SSO) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    c. Fare clic su **Choose file** (Scegli file) per caricare il certificato scaricato dal portale di Azure nella casella relativa al certificato.

    d. Fare clic sull' **icona** per copiare il valore di **Entity ID** (ID entità) e incollarlo nella casella di testo  **Identificatore** nella sezione  **Configurazione SAML di base**  del portale di Azure.

    e. Fare clic sull' **icona** per copiare il valore di **Reply URL (ACS)** (URL di risposta - ACS) e incollarlo nella casella di testo  **URL di risposta** nella sezione  **Configurazione SAML di base**  del portale di Azure.

    f. Fare clic su **Save**

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-a-peakon-test-user"></a>Creazione di un utente di test di Peakon

Per consentire agli utenti di Azure AD di accedere a Peakon, è necessario effettuarne il provisioning in Peakon.  
Nel caso di Peakon, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Peakon come amministratore.

2. Sulla barra dei menu a sinistra nella pagina fare clic su  **Configuration** (Configurazione) e quindi passare a  **Employees** (Dipendenti).

    ![Dipendente](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. In alto a destra nella pagina fare clic su  **Add employee** (Aggiungi dipendente).

      ![Aggiungere un dipendente](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Nella pagina della finestra di dialogo **New employee** (Nuovo dipendente) seguire questa procedura:

     ![Nuovo dipendente](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Nella casella di testo **Name** (Nome) digitare **Britta** come nome e **simon** come cognome.

    b. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica, ad esempio **Brittasimon@contoso.com**.

    c. Fare clic su **Create employee** (Crea dipendente).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Peakon.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Peakon**.

    ![Configure Single Sign-On](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Peakon nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Peakon.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

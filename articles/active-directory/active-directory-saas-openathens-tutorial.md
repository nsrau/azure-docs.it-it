---
title: 'Esercitazione: Integrazione di Azure Active Directory con OpenAthens | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: af26e007c953c4157f5ee7a4251a52e9c45a6eac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Esercitazione: Integrazione di Azure Active Directory con OpenAthens

Questa esercitazione descrive come integrare OpenAthens con Azure Active Directory (Azure AD).

L'integrazione di OpenAthens con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OpenAthens.
- È possibile abilitare gli utenti per l'accesso automatico a OpenAthens (Single Sign-On) con gli account Azure AD personali.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con OpenAthens, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata per l'accesso Single Sign-On a OpenAthens

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di OpenAthens dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-openathens-from-the-gallery"></a>Aggiunta di OpenAthens dalla raccolta
Per configurare l'integrazione di OpenAthens in Azure AD, è necessario aggiungere OpenAthens dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere OpenAthens dalla raccolta**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Sfogliare **Applicazioni aziendali** e quindi passare a **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **OpenAthens** selezionare **OpenAthens** dal pannello dei risultati e quindi selezionare il pulsante **Aggiungi**.

    ![OpenAthens nell'elenco risultati](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OpenAthens in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di OpenAthens che corrisponde all'utente di Azure AD. In altre parole, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpenAthens.

Per stabilire la relazione di collegamento, in OpenAthens assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con OpenAthens, è necessario completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente test OpenAthens](#create-a-openathens-test-user) per avere una controparte di Britta Simon in OpenAthens collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione OpenAthens.

**Per configurare l'accesso a Single Sign-On di Azure AD con OpenAthens**

1. Nella pagina di integrazione dell'applicazione **OpenAthens** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura l'accesso Single Sign-On][4]

2. Per abilitare single sign-on,nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** come **Modalità**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. Nella sezione **URL e dominio OpenAthens** immettere il valore: `https://login.openathens.net/saml/2/metadata-sp` nella casella di testo **Identificatore**.

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. Nella sezione **Certificato di firma SAML** selezionare **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento al download del certificato di firma AMSL](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Fare clic sul pulsante **Salva**.

    ![Pulsante Salva di Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di OpenAthens come amministratore.

7. Selezionare **Connections** (Connessioni) nella scheda **Management** (Gestione). 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Selezionare **SAML 1.1 o 2.0** e quindi selezionare il pulsante **Configura**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Per aggiungere la configurazione, selezionare il pulsante **Sfoglia** per caricare il file di metadati con estensione XML scaricato dal portale di Azure e quindi selezionare **Aggiungi**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Nella scheda **Dettagli** seguire questa procedura.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. In **Display name mapping** (Mapping nome visualizzato) selezionare **Use attribute** (Usa attributo).

    b. Nella casella di testo **Display name attribute** (Attributo nome visualizzato) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. In **Unique user mapping** (Mapping utente univoco) selezionare **Use attribute** (Usa attributo).

    d. Nella casella di testo **Unique user attribute** (Attributo utente univoco) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. In **Status** (Stato) selezionare tutte le tre caselle di controllo.

    f. In **Create local accounts** (Crea account locali) selezionare **automatically** (automaticamente).

    g. Selezionare **Save changes** (Salva modifiche).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare la scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata vedere [Azure AD embedded documentation](https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

L'obiettivo di questa sezione consiste nel creare un utente di test nel portale di Azure con nome "Britta Simon".

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD**

1. Nel portale di Azure fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'indirizzo di posta elettronica per BrittaSimon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella di testo **Password**.

    d. Selezionare **Create**.
  
### <a name="create-an-openathens-test-user"></a>Creare un utente test di OpenAthens

OpenAthens supporta il provisioning JIT e gli utenti vengono creati automaticamente dopo l'autenticazione. In questa sezione non è necessario eseguire alcuna azione.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a OpenAthens.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a OpenAthens**

1. Nel portale di Azure aprire la visualizzazione applicazioni, esplorare la visualizzazione directory e andare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle **Applicazioni** selezionare **OpenAthens**.

    ![Collegamento OpenAthens nell'elenco delle applicazioni](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nell'elenco di **Utenti e gruppi** selezionare **Britta Simon**.

6. Selezionare il pulsante **Seleziona** dall'elenco **Utenti e gruppi**.

7. Selezionare il pulsante **Assegna** nel pannello **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro **OpenAthens** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OpenAthens.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* Per un elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md).
* Per altre informazioni sull'accesso alle applicazioni e single-sign on con Azure Active Directory, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png


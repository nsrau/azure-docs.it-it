---
title: 'Esercitazione: Integrazione di Azure Active Directory con XaitPorter | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: 12fb8e5b2b940c48de766a48f59ed0cc342b5356
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421067"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Esercitazione: Integrazione di Azure Active Directory con XaitPorter

Questa esercitazione descrive come integrare XaitPorter con Azure Active Directory (Azure AD).

L'integrazione di XaitPorter con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a XaitPorter.
- È possibile abilitare gli utenti per l'accesso automatico a XaitPorter (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con XaitPorter, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di XaitPorter abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di XaitPorter dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-xaitporter-from-the-gallery"></a>Aggiunta di XaitPorter dalla raccolta
Per configurare l'integrazione di XaitPorter in Azure AD, è necessario aggiungere XaitPorter dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere XaitPorter dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **XaitPorter**, selezionare **XaitPorter** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![XaitPorter nell'elenco risultati](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con XaitPorter in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di XaitPorter che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in XaitPorter.

Per stabilire la relazione di collegamento, in XaitPorter assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con XaitPorter, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente test di XaitPorter](#create-a-xaitporter-test-user)**: per avere una controparte di Britta Simon in XaitPorter collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione XaitPorter.

**Per configurare Single Sign-On di Azure AD con XaitPorter, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **XaitPorter** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

1. Nella sezione **URL e dominio XaitPorter** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di XaitPorter](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.xaitporter.com/saml/login`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di XaitPorter](https://www.xait.com/support/).
     
1. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note. 

    ![Collegamento di download del certificato](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/xaitporter-tutorial/tutorial_general_400.png)

1. Fornire l'**Indirizzo IP** o l'**URL dei metadati di federazione dell'app** al [team di supporto di SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/), in modo che XaitPorter possa garantire che l'indirizzo IP sia raggiungibile dall'istanza di XaitPorter configurando l'elenco elementi consentiti. 

1. In un'altra finestra del Web browser accedere al sito aziendale di XaitPorter come amministratore.

1. Fare clic sul riquadro **Admin**.

    ![Configure Single Sign-On](./media/xaitporter-tutorial/user1.png)

1. Selezionare **Manage Single Sign-On** dall'elenco a discesa **System Setup**.

    ![Configure Single Sign-On](./media/xaitporter-tutorial/user2.png)

1. Nella sezione **MANAGE SINGLE SIGN-ON** seguire questa procedura:

    ![Configure Single Sign-On](./media/xaitporter-tutorial/user3.png)

    a. Selezionare **Enable Single Sign-On Authentication**.

    b. Nella casella di testo **Identity Provider Settings** (Impostazioni provider di identità) incollare l'**URL dei metadati di federazione dell'app** copiato dal portale di Azure e fare clic su **Fetch** (Recupera).

    c. Selezionare **Enable Autocreation of Users**.

    d. Fare clic su **OK**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/xaitporter-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/xaitporter-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/xaitporter-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-xaitporter-test-user"></a>Creare un utente test di XaitPorter

In questa sezione viene creato un utente di nome Britta Simon in XaitPorter. Collaborare con il [team di supporto clienti di XaitPorter](https://www.xait.com/support/) per aggiungere gli utenti nella piattaforma XaitPorter. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a XaitPorter.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a XaitPorter, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **XaitPorter**.

    ![Collegamento di XaitPorter nell'elenco delle applicazioni](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Facendo clic sul riquadro XaitPorter nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione XaitPorter.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png


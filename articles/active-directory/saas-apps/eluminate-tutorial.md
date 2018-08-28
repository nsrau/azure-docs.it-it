---
title: 'Esercitazione: Integrazione di Azure Active Directory con eLuminate | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed eLuminate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 94c28db4-dbca-446b-8eef-9b728f18ca9a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeedes
ms.openlocfilehash: d2798f3638192604d0912f50a8b1c43f4a1939fb
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40225493"
---
# <a name="tutorial-azure-active-directory-integration-with-eluminate"></a>Esercitazione: Integrazione di Azure Active Directory con eLuminate

Questa esercitazione descrive come integrare eLuminate con Azure Active Directory (Azure AD).

L'integrazione di eLuminate con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a eLuminate.
- È possibile abilitare gli utenti per l'accesso automatico a eLuminate (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con eLuminate, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di eLuminate abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di eLuminate dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-eluminate-from-the-gallery"></a>Aggiunta di eLuminate dalla raccolta

Per configurare l'integrazione di eLuminate in Azure AD, è necessario aggiungere eLuminate dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere eLuminate dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **eLuminate**, selezionare **eLuminate** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![eLuminate nell'elenco dei risultati](./media/eluminate-tutorial/tutorial_eluminate_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con eLuminate usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di eLuminate corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in eLuminate.

Per configurare e testare l'accesso Single Sign-On di Azure AD con eLuminate, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di eLuminate](#create-a-eluminate-test-user)**: per avere una controparte di Britta Simon in eLuminate collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione eLuminate.

**Per configurare Single Sign-On di Azure AD con eLuminate, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **eLuminate** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/eluminate-tutorial/tutorial_eluminate_samlbase.png)

3. Nella sezione **URL e dominio eLuminate** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di eLuminate](./media/eluminate-tutorial/tutorial_eluminate_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://ClientShortName.eluminate.ca/azuresso/account/SignIn`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `Eluminate/ClientShortName`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di eLuminate](mailto:support@intellimedia.ca).

4. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/eluminate-tutorial/tutorial_eluminate_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/eluminate-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On in **eLuminate**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [team di supporto di eLuminate](mailto:support@intellimedia.ca). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/eluminate-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/eluminate-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/eluminate-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/eluminate-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-eluminate-test-user"></a>Creare un utente di test di eLuminate

In questa sezione viene creato un utente chiamato Britta Simon in eLuminate. Collaborare con il [team di supporto di eLuminate](mailto:support@intellimedia.ca) per aggiungere gli utenti alla piattaforma eLuminate. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a eLuminate.

![Assegnare il ruolo utente][200]

**Per assegnare Britta Simon a eLuminate, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **eLuminate**.

    ![Collegamento di eLuminate nell'elenco delle applicazioni](./media/eluminate-tutorial/tutorial_eluminate_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro eLuminate nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione eLuminate.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/eluminate-tutorial/tutorial_general_01.png
[2]: ./media/eluminate-tutorial/tutorial_general_02.png
[3]: ./media/eluminate-tutorial/tutorial_general_03.png
[4]: ./media/eluminate-tutorial/tutorial_general_04.png

[100]: ./media/eluminate-tutorial/tutorial_general_100.png

[200]: ./media/eluminate-tutorial/tutorial_general_200.png
[201]: ./media/eluminate-tutorial/tutorial_general_201.png
[202]: ./media/eluminate-tutorial/tutorial_general_202.png
[203]: ./media/eluminate-tutorial/tutorial_general_203.png
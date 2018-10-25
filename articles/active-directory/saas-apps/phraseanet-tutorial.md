---
title: 'Esercitazione: Integrazione di Azure Active Directory con Phraseanet | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Phraseanet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 078d84ce-e054-4ae1-a52e-46a94a6959a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: cfeadbf1d46d9e36f8619cafe29d9dd69aad6eec
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118186"
---
# <a name="tutorial-azure-active-directory-integration-with-phraseanet"></a>Esercitazione: Integrazione di Azure Active Directory con Phraseanet

Questa esercitazione descrive come integrare Phraseanet con Azure Active Directory (Azure AD).

L'integrazione di Phraseanet con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Phraseanet.
- È possibile abilitare gli utenti per l'accesso automatico a Phraseanet (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Phraseanet, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Phraseanet abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Phraseanet dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-phraseanet-from-the-gallery"></a>Aggiunta di Phraseanet dalla raccolta
Per configurare l'integrazione di Phraseanet in Azure AD, è necessario aggiungere Phraseanet dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Phraseanet dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/phraseanet-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/phraseanet-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/phraseanet-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **Phraseanet**, selezionare **Phraseanet** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/phraseanet-tutorial/tutorial_phraseanet_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Phraseanet in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Phraseanet che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Phraseanet.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Phraseanet, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Phraseanet](#create-a-phraseanet-test-user)**: per avere una controparte di Britta Simon in Phraseanet collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Phraseanet.

**Per configurare l'accesso Single Sign-On di Azure AD con Phraseanet, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione [Phraseanet](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![image](./media/phraseanet-tutorial/b1_b2_select_sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/phraseanet-tutorial/b1_b2_saml_sso.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/phraseanet-tutorial/b1-domains_and_urlsedit.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://<SUBDOMAIN>.alchemyasp.com`

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_url.png)

    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Contattare il [team di supporto di Phraseanet](mailto:support@alchemy.fr) per ottenere il valore.
 
5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML dei metadati federazione** e salvarlo nel computer in uso.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_certificate.png) 

6. Per configurare l'accesso Single Sign-On sul lato **Phraseanet**, è necessario inviare il file **XML dei metadati di federazione** scaricato al [team di supporto di Phraseanet](mailto:support@alchemy.fr). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/phraseanet-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/phraseanet-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/phraseanet-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-a-phraseanet-test-user"></a>Creare un utente di test Phraseanet

In questa sezione viene creato un utente chiamato Britta Simon in Phraseanet. Rivolgersi al [team di supporto di Phraseanet](mailto:support@alchemy.fr) per aggiungere gli utenti nella piattaforma Phraseanet. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Phraseanet.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/phraseanet-tutorial/d_all_applications.png)

2. Nell'elenco di applicazioni selezionare **Phraseanet**.

    ![image](./media/phraseanet-tutorial/tutorial_phraseanet_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/phraseanet-tutorial/d_leftpaneusers.png)

4. Selezionare il pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/phraseanet-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Phraseanet nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Phraseanet.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



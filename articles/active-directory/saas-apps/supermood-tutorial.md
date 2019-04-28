---
title: 'Esercitazione: Integrazione di Azure Active Directory con Supermood | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60efdd5b509045ce2398720d650894983c2cfc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639830"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Esercitazione: Integrazione di Azure Active Directory con Supermood

Questa esercitazione descrive come integrare Supermood con Azure Active Directory (Azure AD).

L'integrazione di Supermood con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Supermood.
- È possibile abilitare gli utenti per l'accesso automatico a Supermood (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Supermood, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione a Supermood abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Supermood dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-supermood-from-the-gallery"></a>Aggiunta di Supermood dalla raccolta
Per configurare l'integrazione di Supermood in Azure AD, è necessario aggiungere Supermood dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Supermood dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Supermood**, selezionare **Supermood** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Supermood nell'elenco risultati](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Supermood in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Supermood che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Supermood.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Supermood, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Supermood](#create-a-supermood-test-user)**: per avere una controparte di Britta Simon in Supermood collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Supermood.

**Per configurare Single Sign-On di Azure AD con Supermood, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Supermood** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

1. Nella sezione **URL e dominio Supermood** seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di Supermood](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. Selezionare **Mostra impostazioni URL avanzate**

    b. Se si vuole configurare l'applicazione in modalità avviata da **IDP** nella sezione **Stato dell'inoltro** digitare un URL: `https://supermood.co/auth/sso/saml20`

    c. Se si vuole configurare l'applicazione in modalità avviata da **SP** nella sezione **URL di accesso** digitare un URL: `https://supermood.co/app/#!/loginv2`

1. L'applicazione Supermood si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configure Single Sign-On](./media/supermood-tutorial/tutorial_supermood_attribute.png)

1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    d. Fare clic su **Ok**

1. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/supermood-tutorial/tutorial_general_400.png)

1. Accedere al pannello dell'amministratore di Supermood.co come amministratore della sicurezza.

1. Fare clic su **Account personale** in basso a sinistra e quindi su **Single Sign-On (SSO)**.

    ![Singolo certificato](./media/supermood-tutorial/tutorial_supermood_single.png)
1. In **Your SAML 2.0 configurations** (Configurazioni personali di SAML 2.0), fare clic su **Add an SAML 2.0 configuration for an email domain** (Aggiungere una configurazione di SAML 2.0 per un dominio di posta elettronica).

    ![Aggiunta del certificato](./media/supermood-tutorial/tutorial_supermood_add.png)

1. Nella sezione **Add an SAML 2.0 configuration for an email domain** (Aggiungere una configurazione di SAML 2.0 per un dominio di posta elettronica), seguire questa procedura:

    ![SAML del certificato](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Nella casella di testo **Email domain for this Identity provider** (Dominio e-mail del provider di identità), digitare il nome di dominio.

    b. Nella casella di testo **Use a metadata URL** (Utilizza un URL dei metadati) incollare l'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare clic su **Aggiungi**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/supermood-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/supermood-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/supermood-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/supermood-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-supermood-test-user"></a>Creare un utente di test di Supermood

In questa sezione viene creato un utente di nome Britta Simon in Supermood. Supermood supporta il provisioning just-in-time, abilitato per impostazione predefinita per gli utenti i cui messaggi di posta elettronica appartengono ai domini aggiunti durante la configurazione finale di Supermood. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Supermood viene creato un nuovo utente, se questo non esiste ancora.

>[!Note]
>Per creare un utente manualmente, contattare il  [team di supporto di Supermood](mailto:hello@supermood.fr).


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Supermood.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Supermood, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Supermood**.

    ![Collegamento a Supermood nell'elenco di applicazioni](./media/supermood-tutorial/tutorial_supermood_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Supermood nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Supermood.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png


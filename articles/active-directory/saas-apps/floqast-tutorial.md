---
title: 'Esercitazione: Integrazione di Azure Active Directory con FloQast | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FloQast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 013cb57d-567c-44d0-a119-e6ba6e607153
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed3d8e687e6364941ac8cc3fb2f08796a6b566d0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199358"
---
# <a name="tutorial-azure-active-directory-integration-with-floqast"></a>Esercitazione: Integrazione di Azure Active Directory con FloQast

Questa esercitazione descrive come integrare FloQast con Azure Active Directory (Azure AD).

L'integrazione di FloQast con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a FloQast.
- È possibile abilitare gli utenti per l'accesso automatico a FloQast (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con FloQast, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di FloQast abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di FloQast dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-floqast-from-the-gallery"></a>Aggiunta di FloQast dalla raccolta
Per configurare l'integrazione di FloQast in Azure AD è necessario aggiungere FloQast dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere FloQast dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **FloQast**, selezionare **FloQast** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![FloQast nell'elenco risultati](./media/floqast-tutorial/tutorial_floqast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con FloQast usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di FloQast che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FloQast.

Per configurare e testare l'accesso Single Sign-On di Azure AD con FloQast, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di FloQast](#create-a-floqast-test-user)**: per avere una controparte di Britta Simon in FloQast collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione FloQast.

**Per configurare l'accesso Single Sign-On di Azure AD con FloQast, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **FloQast** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/floqast-tutorial/tutorial_floqast_samlbase.png)

1. Nella sezione **URL e dominio FloQast** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di FloQast](./media/floqast-tutorial/tutorial_floqast_url.png)

     Nella casella di testo **Identificatore** digitare un URL: `https://go.floqast.com/`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di FloQast](./media/floqast-tutorial/tutorial_floqast_url1.png)

     Nella casella di testo **URL di accesso** digitare l'URL: `https://go.floqast.com/login/sso`
     
1. L'applicazione FloQast prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configurare l'attributo Single Sign-On](./media/floqast-tutorial/tutorial_floqast_attribute.png)
    
1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | FirstName           | user.givenname |
    | LastName        | user.surname |
    | Email       | user.mail    |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/floqast-tutorial/tutorial_attribute_04.png)

    ![Configurare l'aggiunta attributo Single Sign-On](./media/floqast-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer ed eseguire la procedura seguente:

    ![Collegamento di download del certificato](./media/floqast-tutorial/tutorial_floqast_certificate.png)

    a. Selezionare **Mostra impostazioni avanzate per la firma di certificati**.

    ![L'asserzione del certificato](./media/floqast-tutorial/tutorial_floqast_certificateassertion.png)

    b. Selezionare **Opzione di firma** come **Firma asserzione e risposta SAML**.

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/floqast-tutorial/tutorial_general_400.png)
    
1. Per configurare l'accesso Single Sign-On sul lato **FloQast** è necessario inviare il file **XML metadati** scaricato al [team di supporto di FloQast](mailto:support@floqast.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/floqast-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/floqast-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/floqast-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/floqast-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-floqast-test-user"></a>Creare un utente test FloQast

In questa sezione viene creato un utente chiamato Britta Simon in FloQast. Collaborare con il  [team di supporto di FloQast](mailto:support@floqast.com)  per aggiungere gli utenti alla piattaforma FloQast. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a FloQast.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a FloQast, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **FloQast**.

    ![Collegamento di FloQast nell'elenco delle applicazioni](./media/floqast-tutorial/tutorial_floqast_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro FloQast nel pannello di accesso, si accederà automaticamente all'applicazione FloQast.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/floqast-tutorial/tutorial_general_01.png
[2]: ./media/floqast-tutorial/tutorial_general_02.png
[3]: ./media/floqast-tutorial/tutorial_general_03.png
[4]: ./media/floqast-tutorial/tutorial_general_04.png

[100]: ./media/floqast-tutorial/tutorial_general_100.png

[200]: ./media/floqast-tutorial/tutorial_general_200.png
[201]: ./media/floqast-tutorial/tutorial_general_201.png
[202]: ./media/floqast-tutorial/tutorial_general_202.png
[203]: ./media/floqast-tutorial/tutorial_general_203.png


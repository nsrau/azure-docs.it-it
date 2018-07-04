---
title: 'Esercitazione: Integrazione di Azure Active Directory con Screencast-O-Matic | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Screencast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.openlocfilehash: 7212e0b07b525286f0b194a53c6780269630ad9c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2018
ms.locfileid: "36323596"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Esercitazione: Integrazione di Azure Active Directory con Screencast-O-Matic

Questa esercitazione descrive come integrare Screencast-O-Matic con Azure Active Directory (Azure AD).

L'integrazione di Screencast-O-Matic con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Screencast-O-Matic.
- È possibile abilitare gli utenti per l'accesso automatico a Screencast-O-Matic (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Screencast-O-Matic, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Screencast-O-Matic abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Screencast-O-Matic dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Aggiunta di Screencast-O-Matic dalla raccolta
Per configurare l'integrazione di Screencast-O-Matic in Azure AD, è necessario aggiungere Screencast-O-Matic dalla raccolta al proprio elenco delle app SaaS gestite.

**Per aggiungere Screencast-O-Matic dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Screencast-O-Matic**, selezionare **Screencast-O-Matic** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Screencast-O-Matic nell'elenco dei risultati](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Screencast-O-Matic usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Screencast-O-Matic che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Screencast-O-Matic.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Screencast-O-Matic, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Screencast-O-Matic](#create-a-screencast-o-matic-test-user)**: per avere una controparte di Britta Simon in Screencast-O-Matic collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Screencast-O-Matic.

**Per configurare l'accesso Single Sign-On di Azure AD con Screencast-O-Matic, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Screencast-O-Matic** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. Nella sezione **URL e dominio Screencast-O-Matic** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Screencast-O-Matic](./media/screencast-tutorial/tutorial_screencast_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Screencast-O-Matic](mailto:support@screencast-o-matic.com). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/screencast-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere a Screencast-O-Matic come amministratore.

7. Fare clic su **Subscription** (Sottoscrizione).

    ![Sottoscrizione](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. Nella sezione **Access page** (Pagina di accesso) fare clic su **Setup** (Configurazione).

    ![Accesso](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. In **Setup Access Page** (Pagina di accesso di configurazione) seguire questa procedura:

    * Nella sezione **Access URL** (URL di accesso) immettere il nome dell'istanza nella casella di testo specificata.

    ![Accesso](./media/screencast-tutorial/tutorial_screencast_access.png)

    * Selezionare **Require Domain User** (Richiedi utente di dominio) nella sezione **SAML User Restriction (optional)** (Restrizione utente SAML (facoltativa)).

    * In **Upload IDP Metadata XML File** (Carica file XML metadati IDP) fare clic su **Choose File** (Scegli file) per caricare i metadati scaricati dal portale di Azure.

    * Fare clic su **OK**. 

    ![Accesso](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/screencast-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/screencast-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/screencast-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/screencast-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Creare un utente test di Screencast-O-Matic

Questa sezione descrive come creare un utente chiamato Britta Simon in Screencast-O-Matic. Screencast-O-Matic supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, viene creato un nuovo utente durante un tentativo di accesso a Screencast-O-Matic.

>[!Note]
>Per creare un utente manualmente, contattare il [team del supporto clienti di Screencast-O-Matic](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite la concessione dell'accesso a Screencast-O-Matic.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Screencast-O-Matic, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Screencast-O-Matic**.

    ![Collegamento di Screencast-O-Matic nell'elenco delle applicazioni](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Screencast-O-Matic nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Screencast-O-Matic.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png


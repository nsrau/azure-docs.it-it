---
title: 'Esercitazione: Integrazione di Azure Active Directory con E Sales Manager Remix | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 2e2bc0c7e590796a533d9f593eb61e57def91fc8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187999"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrare Azure Active Directory con E Sales Manager Remix

Questa esercitazione descrive come integrare Azure Active Directory (Azure AD) con E Sales Manager Remix.

L'integrazione di Azure AD con E Sales Manager Remix offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a E Sales Manager Remix.
- È possibile abilitare l'accesso automatico degli utenti a E Sales Manager Remix (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con E Sales Manager Remix, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di E Sales Manager Remix abilitata per l'accesso Single Sign-On

> [!NOTE]
> Per testare i passaggi di questa esercitazione, è consigliabile *non* usare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

* Aggiunta di E Sales Manager Remix dalla raccolta
* Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Aggiungere E Sales Manager Remix dalla raccolta
Per configurare l'integrazione di Azure AD con E Sales Manager Remix, aggiungere E Sales Manager Remix dalla raccolta al proprio elenco di app SaaS gestite eseguendo queste operazioni:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Finestra "Applicazioni aziendali"][2]
    
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **E Sales Manager Remix**, selezionare **E Sales Manager Remix** nell'elenco dei risultati e quindi selezionare **Aggiungi**.

    ![E Sales Manager Remix nell'elenco dei risultati](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con E Sales Manager Remix con un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare l'utente di E Sales Manager Remix e la controparte in Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e lo stesso utente in E Sales Manager Remix.

Per configurare e testare l'accesso Single Sign-On di Azure AD con E Sales Manager Remix, completare i blocchi predefiniti nelle cinque sezioni seguenti:

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

Abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure e configurarlo nell'applicazione E Sales Manager Remix eseguendo queste operazioni:

1. Nella pagina di integrazione dell'applicazione **E Sales Manager Remix** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento "Single Sign-On"][4]

1. Nella finestra **Single Sign-On** selezionare **Accesso basato su SAML** nella casella **Modalità Single Sign-On**.
 
    ![Finestra "Single Sign-On"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. In **URL e dominio E Sales Manager Remix Domain** eseguire queste operazioni:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Nella casella **URL accesso** digitare un URL in questo formato: *https://\<URL basato sul server>/\<sottodominio>/esales-pc*.

    b. Nella casella **Identificatore** digitare un URL in questo formato: *https://\<URL basato sul server>/\<sottodominio>/*.

    c. Annotare il valore di **Identificatore**, che verrà usato successivamente in questa esercitazione.
    
    > [!NOTE] 
    > I valori precedenti non sono valori reali. Aggiornarli con l'URL e l'identificatore di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di E Sales Manager Remix](mailto:esupport@softbrain.co.jp).

1. In **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Selezionare la casella di controllo **Visualizza e modifica tutti gli altri attributi utente** e quindi selezionare l'attributo **emailaddress**.
    
    ![Finestra Attributi utente](./media/esalesmanagerremix-tutorial/configure1.png)

    Viene visualizzata la finestra **Modifica attributo**.

1. Copiare i valori di **Spazio dei nomi** e **Nome**. Generare il valore nel formato *\<Spazio dei nomi>/\<Nome>* e salvarlo, perché verrà usato successivamente in questa esercitazione.

    ![Finestra Modifica attributo](./media/esalesmanagerremix-tutorial/configure2.png)

1. In **Configurazione di E Sales Manager Remix** selezionare **Configura E Sales Manager Remix**.

    ![Configurazione di E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Viene visualizzata la finestra **Configura accesso**.

1. Nella sezione **Riferimento rapido** copiare l'URL di accesso e l'URL del servizio Single Sign-On SAML.

1. Selezionare **Salva**.

    ![Pulsante Salva](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Accedere all'applicazione E Sales Manager Remix come amministratore.

1. In alto a destra selezionare **To Administrator Menu** (A menu amministratore).

    ![Comando "To Administrator Menu" (A menu amministratore)](./media/esalesmanagerremix-tutorial/configure4.png)

1. Nel riquadro a sinistra selezionare **System settings** (Impostazioni sistema)  > **Cooperation with external system** (Cooperazione con sistema esterno).

    ![Collegamenti "System settings" (Impostazioni sistema) e "Cooperation with external system" (Cooperazione con sistema esterno)](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Nella finestra **Cooperation with external system** (Cooperazione con sistema esterno) selezionare **SAML**.

    ![Finestra "Cooperation with external system" (Cooperazione con sistema esterno)](./media/esalesmanagerremix-tutorial/configure6.png)

1. In **SAML authentication setting** (Impostazione di autenticazione SAML) eseguire queste operazioni:

    ![Sezione "SAML authentication setting" (Impostazione di autenticazione SAML)](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Selezionare la casella di controllo **PC version** (Versione PC).
    
    b. Nell'elenco a discesa nella sezione **Collaboration item** (Elemento collaborazione) selezionare **email** (e-mail).

    c. Nella casella **Collaboration item** (Elemento collaborazione) incollare il valore di attestazione precedentemente copiato dal portale di Azure, ad esempio **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. Nella casella **Issuer (entity ID)** (Autorità di certificazione - ID entità) incollare il valore dell'identificatore copiato prima dalla sezione **URL e dominio E Sales Manager Remix** del portale di Azure.

    e. Per caricare il certificato scaricato dal portale di Azure, selezionare **File selection** (Selezione file).

    f. Nella casella **ID provider login URL** (URL di accesso provider ID) incollare l'URL del servizio Single Sign-On SAML copiato prima nel portale di Azure.

    g. Nella casella **Identity Provider Logout URL** (URL di disconnessione provider di identità) incollare l'URL del servizio Single Sign-On copiato prima nel portale di Azure.

    h. Selezionare **Setting complete** (Impostazione completata).

> [!TIP]
> Durante la configurazione dell'app, nel [portale di Azure](https://portal.azure.com) è disponibile un riepilogo delle istruzioni precedenti. Dopo aver aggiunto l'app nella sezione **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On** e quindi accedere alla documentazione integrata nella sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzionalità di documentazione integrata vedere [Documentazione integrata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione si crea un utente di test Britta Simon nel portale di Azure seguendo questa procedura:

![Creare un utente test di Azure AD][100]

1. Nel portale di Azure fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Collegamento Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Per visualizzare un elenco degli utenti correnti, selezionare **Utenti e gruppi** > **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Nella parte superiore della finestra **Tutti gli utenti** selezionare **Aggiungi**.

    ![Pulsante Aggiungi](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Viene visualizzata la finestra **Utente**.

1. Nella finestra **Utente** seguire questa procedura:

    ![Finestra Utente](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Creare un utente di test in E Sales Manager Remix

1. Accedere all'applicazione E Sales Manager Remix come amministratore.

1. Selezionare **To Administrator Menu** (A menu amministratore) nel menu in alto a destra.

    ![Configurazione di E Sales Manager Remix](./media/esalesmanagerremix-tutorial/configure4.png)

1. Selezionare **Your company settings** (Impostazioni azienda)  > **Maintenance of departments and employees** (Gestione reparti e dipendenti) e quindi **Employees registered** (Dipendenti registrati).

    ![Scheda "Employees registered" (Dipendenti registrati)](./media/esalesmanagerremix-tutorial/user1.png)

1. Nella sezione **New employee registration** (Registrazione nuovo dipendente) eseguire queste operazioni:
    
    ![Sezione "New employee registration" (Registrazione nuovo dipendente)](./media/esalesmanagerremix-tutorial/user2.png)

    a. Nella casella **Employee Name** (Nome dipendente) digitare il nome dell'utente, ad esempio **Britta**.

    b. Completare i campi obbligatori rimanenti.
    
    c. Se si abilita SAML, l'amministratore non può accedere dalla pagina di accesso. Concedere all'utente privilegi di accesso amministratore selezionando la casella di controllo **Admin Login** (Account di accesso amministratore).

    d. Selezionare **Registration** (Registrazione).

1. In futuro, per accedere come amministratore sarà necessario accedere come utente dotato di autorizzazioni di amministratore e quindi, in alto a destra, selezionare **To Administrator Menu** (A menu amministratore).

    ![Comando "To Administrator Menu" (A menu amministratore)](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a E Sales Manager Remix. A tale scopo, seguire questa procedura: 

![Assegnare il ruolo utente][200] 

1. Nel portale di Azure aprire la visualizzazione **Applicazioni**, passare alla visualizzazione **Directory** e quindi selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Collegamenti "Applicazioni aziendali" e "Tutte le applicazioni"][201] 

1. Nell'elenco **Applicazioni** selezionare **E Sales Manager Remix**.

    ![Collegamento E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic su **Aggiungi** e quindi nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

1. Fare clic sul pulsante **Seleziona**.

1. Nella finestra **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro E Sales Manager Remix nel pannello di accesso, viene automaticamente eseguito l'accesso all'applicazione E Sales Manager Remix.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sull'integrazione di app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png


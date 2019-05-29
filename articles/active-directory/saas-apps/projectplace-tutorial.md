---
title: 'Esercitazione: Integrazione di Azure Active Directory con Projectplace | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560637"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Esercitazione: Integrazione di Azure Active Directory con Projectplace

Questa esercitazione descrive come integrare Projectplace con Azure Active Directory (Azure AD).

Questa integrazione offre i seguenti vantaggi:

* È possibile usare Azure AD per controllare chi può accedere a Projectplace.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Projectplace con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Projectplace, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile iscriversi per ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Projectplace abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Projectplace supporta l'accesso SSO avviato da SP.

## <a name="add-projectplace-from-the-gallery"></a>Aggiungere Projectplace dalla raccolta

Per configurare l'integrazione di Projectplace in Azure AD, è necessario aggiungere Projectplace dalla raccolta all'elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Projectplace**. Selezionare **Projectplace** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con Projectplace usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in Projectplace.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Projectplace, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di Projectplace](#configure-projectplace-single-sign-on)** sul lato dell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di Projectplace](#create-a-projectplace-test-user)** collegato alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Projectplace, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Projectplace** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Immettere un URL nel formato seguente nella casella **URL di accesso** nella finestra di dialogo **Configurazione SAML di base**:

    `https://<company>.projectplace.com`

   ![Finestra di dialogo Configurazione SAML di base](common/sp-signonurl.png)
    > [!NOTE]
    > Questo valore è un segnaposto. Usare l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). È anche possibile fare riferimento ai modelli disponibili nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **XML metadati federazione**, in base ai propri requisiti, quindi salvare il certificato nel computer:

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Projectplace** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-projectplace-single-sign-on"></a>Configurare l'accesso Single Sign-On di Projectplace

Per configurare l'accesso Single Sign-On sul lato **Projectplace**, è necessario inviare il certificato **XML metadati federazione** scaricato e gli URL copiati dal portale di Azure al [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). Il team verificherà che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

>[!NOTE]
>La configurazione dell'accesso Single Sign-On deve essere eseguita dal [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). Al termine della configurazione, viene ricevuta una notifica.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** nella parte superiore della schermata:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon@\<dominioaziendale>.\<estensione>** , ad esempio BrittaSimon@contoso.com.

    1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure AD, concedendole così accesso a Projectplace.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Projectplace**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Projectplace**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-projectplace-test-user"></a>Creare un utente di test di Projectplace

Per consentire agli utenti di Azure AD di accedere ad Projectplace, aggiungerli ad Projectplace. È necessario aggiungerli manualmente.

Per creare un account utente, seguire questi passaggi:

1. Accedere al sito aziendale di **Projectplace** come amministratore.

2. Passare a **Persone**, quindi selezionare **Membri**:
   
    ![Passare a Persone, quindi selezionare Membri](./media/projectplace-tutorial/ic790228.png "Persone")

3. Selezionare **Aggiungi membro**:
   
    ![Selezionare Aggiungi membro](./media/projectplace-tutorial/ic790232.png "Aggiungi membri")

4. Nella sezione **Aggiungi membro** seguire questa procedura.
   
    ![Sezione Aggiungi membro](./media/projectplace-tutorial/ic790233.png "Nuovi membri")
   
    1. Nella casella **Nuovi membri** immettere l'indirizzo e-mail di un account Azure AD valido che si desidera aggiungere.
   
    1. Selezionare **Send** (Invia).

   Il titolare dell'account Azure AD riceve un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account.

>[!NOTE]
>È inoltre possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Projectplace per aggiungere account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il Pannello di accesso.

Quando si seleziona il riquadro di Projectplace nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza Projectplace per cui si è configurato l'accesso SSO. Per altre informazioni, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

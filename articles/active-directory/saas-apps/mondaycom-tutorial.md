---
title: 'Esercitazione: Integrazione di Azure Active Directory con monday.com | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc142bf02a44ea85861f4cc648fd7ee8602c7520
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896886"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Esercitazione: Integrazione di Azure Active Directory con monday.com

Questa esercitazione descrive come integrare monday.com con Azure Active Directory (Azure AD).

L'integrazione di monday.com con Azure AD offre i vantaggi seguenti:

* È possibile usare Azure AD per controllare chi può accedere ad monday.com.
* Gli utenti possono accedere automaticamente a monday.com con i propri account Azure AD (Single Sign-On).
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con monday.com, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione di Azure AD, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione a monday.com con accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato monday.com con Azure AD.

monday.com supporta le funzionalità seguenti:

* **Accesso Single Sign-On avviato da provider di servizi**
* **Accesso Single Sign-On avviato da IDP**
* **Provisioning JIT**

## <a name="add-mondaycom-in-the-azure-portal"></a>Aggiungere monday.com nel portale di Azure

Per integrare monday.com con Azure AD, è necessario aggiungere monday.com all'elenco delle app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca, immettere **monday.com**. Nei risultati della ricerca selezionare **monday.com** e quindi selezionare **Aggiungi**.

    ![monday.com nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con monday.com usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in monday.com.

Per configurare e testare l'accesso Single Sign-On di Azure AD con monday.com, è necessario completare le procedure di base seguenti:

| Attività | DESCRIZIONE |
| --- | --- |
| **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** | Abilitare gli utenti all'uso di questa funzionalità. |
| **[Configurare l'accesso Single Sign-On per monday.com](#configure-mondaycom-single-sign-on)** | Configurare le impostazioni dell'accesso Single Sign-On nell'applicazione. |
| **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** | Testare l'accesso Single Sign-On di Azure AD per un utente di nome Britta Simon. |
| **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** | Abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD. |
| **[Creare un utente di test di monday.com](#create-a-mondaycom-test-user)** | Creare una controparte di Britta Simon in monday.com collegata alla rappresentazione dell'utente in Azure AD. |
| **[Testare l'accesso Single Sign-On](#test-single-sign-on)** | Verifica se la configurazione funziona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione si configura l'accesso Single Sign-On di Azure AD con monday.com nel portale di Azure.

1. Nel riquadro di integrazione dell'applicazione **monday.com** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Configurare l'opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** o **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** seguire questa procedura se è disponibile un file di metadati del provider di servizi e si vuole configurare la *modalità avviata da IDP*:

    1. Selezionare **Carica il file di metadati**.

       ![Opzione Carica il file di metadati](common/upload-metadata.png)

    1. Per selezionare il file di metadati, selezionare l'icona della cartella e quindi selezionare **Carica**.

       ![Selezionare il file di metadati e quindi selezionare il pulsante Carica](common/browse-upload-metadata.png)

    1. Dopo il caricamento del file di metadati, i valori per **Identificatore** e **URL di risposta** vengono inseriti automaticamente nel riquadro **Configurazione SAML di base**:

       ![Valori del provider di identità nel riquadro Configurazione SAML di base](common/idp-intiated.png)

       > [!Note]
       > Se i valori **Identificatore** e **URL di risposta** non vengono popolati automaticamente, immetterli manualmente.

1. Per configurare l'applicazione in *modalità avviata da SP*:

    1. Selezionare **Impostare URL aggiuntivi**.
    
    1. Nella casella **URL di accesso** immettere un URL con il modello seguente: https:\//\<dominio>.monday.com. Contattare il [team di supporto clienti di monday.com](mailto:support@monday.com) per richiedere l'URL di accesso.

        ![Opzione Impostare URL aggiuntivi](common/metadata-upload-additional-signon.png)

1. L'applicazione monday.com prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. Per gestire questi valori di attributo, nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** per aprire il riquadro **Attributi utente**.

    ![Riquadro Attributi utente](common/edit-attribute.png)

1. In **Attestazioni utente** selezionare **Modifica** per modificare le attestazioni. Per aggiungere un'attestazione, selezionare **Aggiungi nuova attestazione** e quindi configurare l'attributo del token SAML come illustrato nell'immagine precedente. Completare quindi i passaggi seguenti: 

    1. Selezionare **Aggiungi nuova attestazione**.

        ![Opzione Aggiungi nuova attestazione nel riquadro Attestazioni utente](common/new-save-attribute.png)

    1. Nel riquadro **Gestisci attestazioni utente** impostare i valori seguenti:
        
       1. Nella casella **Nome** immettere il nome dell'attributo indicato per la riga dell'attestazione utente.

       1. Lasciare vuota la casella **Spazio dei nomi**.

       1. Per **Origine**, selezionare **Attributo**.

       1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo visualizzato per la riga dell'attestazione utente.

       1. Selezionare **OK** e quindi **Salva**.

       ![Gestisci attestazioni utente](common/new-attribute-details.png)

1. In **Certificato di firma SAML** nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Scarica** accanto a **Certificato (Base64)** . Selezionare un'opzione di download in base ai requisiti. Salvare il certificato nel computer.

    ![Opzione di download Certificato (Base64)](common/certificatebase64.png)

1. Nella sezione **Configura monday.com** copiare gli URL seguenti in base ai requisiti:

    * URL di accesso
    * Identificatore di Azure AD
    * URL di chiusura sessione

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Configurare l'accesso Single Sign-On per monday.com

Per configurare l'accesso Single Sign-On sul lato monday.com, inviare il file del certificato (Base64) scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di monday.com](mailto:support@monday.com). Il team di supporto di monday.com usa le informazioni inviate per assicurarsi che la connessione Single Sign-On SAML venga impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test chiamato Britta Simon nel portale di Azure.

1. Nel portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Opzioni Utenti e Tutti gli utenti](common/users.png)

1. Selezionare **Nuovo utente**.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** completare la procedura seguente:

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **brittasimon\@\<dominio-azienda>.\<estensione>** . Ad esempio, **brittasimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password**. Prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

    ![Riquadro Utente](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si concede a Britta Simon l'accesso a monday.com in modo che possa usare l'accesso Single Sign-On di Azure.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **monday.com**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **monday.com**.

    ![monday.com nell'elenco delle applicazioni](common/all-applications.png)

1. Nel menu selezionare **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi** selezionare **Britta Simon** nell'elenco di utenti. Scegliere **Seleziona**.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere **Seleziona**.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-mondaycom-test-user"></a>Creare un utente di test di monday.com

In questa sezione viene creato un utente di nome Britta Simon nell'applicazione monday.com. monday.com supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in monday.com, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Dopo aver configurato l'accesso Single Sign-On, quando si seleziona **monday.com** nel portale App personali, viene eseguito automaticamente l'accesso a monday.com. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere questi articoli:

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

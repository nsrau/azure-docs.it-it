---
title: 'Esercitazione: Integrazione di Azure Active Directory con PageDNA | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11cb309e6c49ed36247398909e5e1b7ad9f7bc42
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891212"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Esercitazione: Integrazione di Azure Active Directory con PageDNA

Questa esercitazione descrive come integrare PageDNA con Azure Active Directory (Azure AD).

L'integrazione di PageDNA con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a PageDNA.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a PageDNA con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per dettagli sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con PageDNA, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Una sottoscrizione a PageDNA con l'accesso Single Sign-On abilitato.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test e viene integrato PageDNA con Azure AD.

PageDNA supporta le funzionalità seguenti:

* Single Sign-On avviato da provider di servizi.

* Provisioning JIT.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Aggiungere PageDNA da Azure Marketplace

Per configurare l'integrazione di PageDNA in Azure AD, è necessario aggiungere PageDNA da Azure Marketplace all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com?azure-portal=true).
1. Nel riquadro sinistro selezionare **Azure Active Directory**.

    ![Opzione Azure Active Directory](common/select-azuread.png)

1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **+ New application** (+Nuova applicazione) nella parte superiore del riquadro.

    ![Opzione Applicazione nuova](common/add-new-app.png)

1. Nella casella di ricerca immettere **PageDNA**. Nei risultati della ricerca selezionare **PageDNA**, quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![PageDNA nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con PageDNA usando un utente di test di nome **Britta Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in PageDNA.

Per configurare e testare l'accesso Single Sign-On di Azure AD con PageDNA, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per consentire agli utenti di usare questa funzionalità.
1. **[Configurare l'accesso Single Sign-On di PageDNA](#configure-pagedna-single-sign-on)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Creare un utente di test di PageDNA](#create-a-pagedna-test-user)** in modo che vi sia un utente di nome Britta Simon in PageDNA collegato a un utente di Azure AD di nome Britta Simon.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con PageDNA, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **PageDNA** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Configurare l'opzione Single Sign-On](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare **Modifica** (l'icona a forma di matita) per aprire il riquadro **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nel riquadro **Configurazione SAML di base** eseguire la procedura seguente:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di PageDNA](common/sp-identifier.png)

    1. Nella casella di testo **URL di accesso** immettere un URL usando uno dei formati seguenti:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Nella casella di testo **Identificatore (ID entità)** immettere un URL usando uno dei formati seguenti:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con URL di accesso e identificatore effettivi. Per ottenere questi valori, contattare il [team di supporto di PageDNA](mailto:success@pagedna.com). È anche possibile fare riferimento ai modelli mostrati nel riquadro **Configurazione SAML di base** del portale di Azure.

1. Nel riquadro **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, selezionare **Scarica** per scaricare il file **Certificato (base)** dalle opzioni specificate e salvarlo nel computer.

    ![Opzione di download Certificato (base)](common/certificateraw.png)

1. Nel riquadro **Configura PageDNA** copiare l'URL o gli URL necessari:

   * **URL di accesso**
   * **Identificatore Azure AD**
   * **URL di disconnessione**

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Configurare l'accesso Single Sign-On di PageDNA

Per configurare l'accesso Single Sign-On sul lato PageDNA, è necessario inviare il certificato (base) scaricato e gli URL appropriati, copiati dal portale di Azure, al [team di supporto di PageDNA](mailto:success@pagedna.com). Il team di PageDNA verificherà che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro di sinistra del portale di Azure selezionare **Azure Active Directory**   > **Utenti** > **Tutti gli utenti**.

    ![Opzioni "Utenti" e "Tutti gli utenti"](common/users.png)

1. Selezionare **+Nuovo utente** in alto nella schermata.

    ![Opzione Nuovo utente](common/new-user.png)

1. Nel riquadro **Utente** eseguire i seguenti passaggi:

    ![Riquadro Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon\@\<dominioazienda>.\<estensione>** . Ad esempio, **BrittaSimon\@contoso.com**.

    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PageDNA.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **PageDNA**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle applicazioni selezionare **PageDNA**.

    ![PageDNA nell'elenco delle applicazioni](common/all-applications.png)

1. Nel riquadro sinistro in **GESTISCI**fare clic su **Utenti e gruppi**.

    ![Opzione Utenti e gruppi](common/users-groups-blade.png)

1. Selezionare **+Aggiungi utente** e quindi **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

1. Nel riquadro **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore del riquadro.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nel riquadro **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere **Seleziona** nella parte inferiore del riquadro.

1. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-pagedna-test-user"></a>Creare l'utente di test di PageDNA

A questo punto in PageDNA viene creato un utente di nome Britta Simon. Non è necessario eseguire alcuna operazione per creare tale utente. PageDNA supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Se in PageDNA non esiste già un utente di nome Britta Simon, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD tramite il portale App personali.

Quando si seleziona **PageDNA** nel portale App personali, si dovrebbe automaticamente accedere alla sottoscrizione di PageDNA per la quale è stato impostato l'accesso Single Sign-On. Per altre informazioni sul portale App personali, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Un elenco di esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
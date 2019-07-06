---
title: 'Esercitazione: Integrazione di Azure Active Directory con RolePoint | Microsoft Docs'
description: Questa esercitazione descrive come configurare l'accesso Single Sign-On tra Azure Active Directory e RolePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fd17d2f8577532778733866260f43e9ac7685
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092726"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Esercitazione: Integrazione di Azure Active Directory con RolePoint

Questa esercitazione descrive come integrare RolePoint con Azure Active Directory (Azure AD).
Questa integrazione offre i seguenti vantaggi:

* È possibile usare Azure AD per controllare chi può accedere a RolePoint.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a RolePoint con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con RolePoint, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di RolePoint abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* RolePoint supporta l'accesso SSO avviato da SP.

## <a name="add-rolepoint-from-the-gallery"></a>Aggiungere RolePoint dalla raccolta

Per configurare l'integrazione di RolePoint in Azure AD, è necessario aggiungere RolePoint dalla raccolta all'elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **RolePoint**. Selezionare **RolePoint** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD con RolePoint usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in RolePoint.

Per configurare e testare l'accesso Single Sign-On di Azure AD con RolePoint, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di RolePoint](#configure-rolepoint-single-sign-on)** sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di RolePoint ](#create-a-rolepoint-test-user)** collegato alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con RolePoint, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione RolePoint nel [portale di Azure](https://portal.azure.com/) selezionare l'accesso **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura.

    ![Finestra di dialogo Configurazione SAML di base](common/sp-identifier.png)

    1. Nella casella **URL di accesso** immettere un URL nel formato seguente:

       `https://<subdomain>.rolepoint.com/login`

    1. Nella casella **Identificatore (ID entità)** immettere un URL nel formato seguente:

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Questi valori sono segnaposto. Usare l'identificatore e l'URL di accesso effettivi. È consigliabile usare un valore di stringa univoco nell'identificatore. Per ottenere questi valori, contattare il [team di supporto di RolePoint](mailto:info@rolepoint.com). È anche possibile fare riferimento ai modelli disponibili nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il collegamento **Scarica** accanto a **XML metadati federazione** in base ai propri requisiti e salvare il file nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura RolePoint**  copiare gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.


### <a name="configure-rolepoint-single-sign-on"></a>Configurare l'accesso Single Sign-On di RolePoint

Per configurare l'accesso Single Sign-On sul lato RolePoint, è necessario collaborare con il [team di supporto di RolePoint](mailto:info@rolepoint.com). Inviare a questo team il file XML dei metadati di federazione e gli URL ottenuti dal portale di Azure. La configurazione di RolePoint verrà eseguita per assicurarsi che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**:

    ![Selezionare Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** in alto nella finestra:

    ![Selezionare Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. Nella casella **Nome** immettere **BrittaSimon**.
  
    1. Nella casella **Nome utente** immettere **BrittaSimon@\<dominioaziendale>.\<estensione>** , ad esempio BrittaSimon@contoso.com.

    1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RolePoint.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **RolePoint**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **RolePoint**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco utenti e quindi fare clic sul pulsante **Seleziona** in basso nella finestra.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** in basso nella finestra.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-rolepoint-test-user"></a>Creare un utente test di RolePoint

A questo punto è necessario creare un utente di nome Britta Simon in RolePoint. Collaborare con il  [team di supporto di RolePoint](mailto:info@rolepoint.com) per aggiungere gli utenti a RolePoint. Gli utenti devono essere creati e attivati prima di poter usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro RolePoint nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di RolePoint per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Accedere e usare le app nel portale di My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

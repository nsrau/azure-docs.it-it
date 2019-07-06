---
title: 'Esercitazione: Integrazione di Azure Active Directory con ITRP | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare l'accesso Single Sign-On tra Azure Active Directory e ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c3be09c998399cdca886f207bf5fc621bf51d8dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099606"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Esercitazione: Integrazione di Azure Active Directory con ITRP

Questa esercitazione descrive come integrare ITRP con Azure Active Directory (Azure AD).
Questa integrazione offre i seguenti vantaggi:

* È possibile usare Azure AD per controllare chi può accedere a ITRP.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a ITRP con gli account Azure AD personali.
* È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ITRP, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ITRP abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ITRP supporta l'accesso SSO avviato da SP.

## <a name="add-itrp-from-the-gallery"></a>Aggiungere ITRP dalla raccolta

Per configurare l'integrazione di ITRP in Azure AD, è necessario aggiungere ITRP dalla raccolta all'elenco di app SaaS gestite.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

    ![Selezionare Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali** > **Tutte le applicazioni**:

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

    ![Selezionare Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **ITRP**. Selezionare **ITRP** nei risultati della ricerca e quindi selezionare **Aggiungi**.

     ![Search Results](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione verrà configurato e testato l'accesso Single Sign-On di Azure AD con ITRP usando un utente di test di nome Britta Simon.
Per abilitare l'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e l'utente corrispondente in ITRP.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ITRP, è necessario completare questi passaggi:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** per abilitare questa funzionalità per gli utenti.
2. **[Configurare l'accesso Single Sign-On di ITRP](#configure-itrp-single-sign-on)** sul lato dell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare l'accesso Single Sign-On di Azure AD per tale utente.
5. **[Creare un utente di test di ITRP](#create-an-itrp-test-user)** collegato alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione verrà abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con ITRP, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione ITRP del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On:

    ![Selezionare un metodo di accesso Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**:

    ![Icona Modifica](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura.

    ![Finestra di dialogo Configurazione SAML di base](common/sp-identifier.png)

    1. Nella casella **URL di accesso** immettere un URL nel formato seguente:
    
       `https://<tenant-name>.itrp.com`

    1. Nella casella **Identificatore (ID entità)** immettere un URL nel formato seguente:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Questi valori sono segnaposto. Usare l'identificatore e l'URL di accesso effettivi. Per ottenere i valori, contattare il [team di supporto di ITRP](https://www.itrp.com/support). È anche possibile fare riferimento ai modelli disponibili nella finestra di dialogo **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML**  selezionare l'icona **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML**:

    ![Icona Modifica](common/edit-certificate.png)

6. Nella finestra di dialogo **Certificato di firma SAML** copiare il valore **Identificazione personale** e salvarlo:

    ![Copiare il valore Identificazione personale](common/copy-thumbprint.png)

7. Nella sezione **Configura ITRP** copiare gli URL appropriati in base alle esigenze:

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    1. **URL di accesso**.

    1. **Identificatore Azure AD**.

    1. **URL di disconnessione**.

### <a name="configure-itrp-single-sign-on"></a>Configurare l'accesso Single Sign-On di ITRP

1. In una nuova finestra del Web browser accedere al sito aziendale di ITRP come amministratore.

1. Nella parte superiore della finestra selezionare l'icona **Settings** (Impostazioni):

    ![Icona Settings (Impostazioni)](./media/itrp-tutorial/ic775570.png "Icona Settings (Impostazioni)")

1. Nel riquadro sinistro selezionare **Single Sign-On**:

    ![Selezionare Single Sign-On](./media/itrp-tutorial/ic775571.png "Selezionare Single Sign-On")

1. Nella sezione di configurazione per **Single Sign-On** seguire questa procedura.

    ![Sezione Single Sign-On](./media/itrp-tutorial/ic775572.png "Single Sign-On")

    ![Sezione Single Sign-On](./media/itrp-tutorial/ic775573.png "Single Sign-On")

    1. Selezionare **Enabled**.

    1. Nella casella  **Remote logout URL** (URL disconnessione remota) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    1. Nella casella  **SAML SSO URL** (URL SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    1. Nella casella **Certificate fingerprint** (Impronta digitale certificato) incollare il valore **Identificazione personale** del certificato copiato dal portale di Azure.

    1. Selezionare **Salva**.

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

    1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon verrà abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ITRP.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **ITRP**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **ITRP**.

    ![Elenco delle applicazioni](common/all-applications.png)

3. Nel riquadro sinistro selezionare **Utenti e gruppi**:

    ![Selezionare Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Selezionare Aggiungi utente](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco utenti e quindi fare clic sul pulsante **Seleziona** in basso nella finestra.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** in basso nella finestra.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-an-itrp-test-user"></a>Creare un utente di test di ITRP

Per consentire agli utenti di Azure AD di accedere a ITRP, aggiungerli a ITRP. È necessario aggiungerli manualmente.

Per creare un account utente, seguire questi passaggi:

1. Accedere al tenant ITRP.

1. Nella parte superiore della finestra selezionare l'icona **Records** (Record):

    ![Icona Records (Record)](./media/itrp-tutorial/ic775575.png "Icona Records (Record)")

1. Nel menu selezionare **People** (Persone):

    ![Selezionare People (Persone)](./media/itrp-tutorial/ic775587.png "Selezionare People (Persone)")

1. Selezionare il segno più ( **+** ) per aggiungere una nuova persona:

    ![Selezionare il segno più](./media/itrp-tutorial/ic775576.png "Selezionare il segno più")

1. Nella finestra di dialogo **Add New Person** (Aggiungi nuova persona) seguire questa procedura.

    ![Finestra di dialogo Add New Person (Aggiungi nuova persona)](./media/itrp-tutorial/ic775577.png "Finestra di dialogo Add New Person (Aggiungi nuova persona)")

    1. Immettere il nome e l'indirizzo di posta elettronica di un account Azure AD valido che si vuole aggiungere.

    1. Selezionare **Salva**.

> [!NOTE]
> È possibile usare qualsiasi strumento o API di creazione di account utente fornita da ITRP per effettuare il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Il passaggio finale consiste nel testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di ITRP nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di ITRP per cui si è configurato l'accesso SSO. Per altre informazioni sul Pannello di accesso, vedere [Accedere e usare le app nel portale App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

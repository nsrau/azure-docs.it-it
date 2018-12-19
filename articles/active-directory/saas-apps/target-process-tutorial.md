---
title: 'Esercitazione: Integrazione di Azure Active Directory con TargetProcess | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.openlocfilehash: 7fb3c1c3e8dbff54802915a69033001cc9378670
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262164"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Esercitazione: Integrazione di Azure Active Directory con TargetProcess

Questa esercitazione descrive come integrare TargetProcess con Azure Active Directory (Azure AD).
L'integrazione di TargetProcess con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a TargetProcess.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a TargetProcess con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TargetProcess, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di TargetProcess abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TargetProcess supporta accesso SSO avviato da **SP**
* TargetProcess supporta il provisioning utenti **Just-in-time**

## <a name="adding-targetprocess-from-the-gallery"></a>Aggiunta di TargetProcess dalla raccolta

Per configurare l'integrazione di TargetProcess in Azure AD, è necessario aggiungere TargetProcess dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere TargetProcess dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **TargetProcess**, selezionare **TargetProcess** dal pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![TargetProcess nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TargetProcess Online usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TargetProcess.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TargetProcess, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test per TargetProcess](#create-targetprocess-test-user)**: per avere una controparte di Britta Simon in TargetProcess collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con TargetProcess, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione [TargetProcess](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di TargetProcess](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.tpondemand.com/`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi.  Per ottenere questi valori, contattare il [team di supporto client di TargetProcess](mailto:support@targetprocess.com).

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura TargetProcess** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

7. Per automatizzare la configurazione all'interno di **TargetProcess**, è necessario installare l'**estensione del browser per l'accesso sicuro alle app personali** facendo clic su **Installare l'estensione**.

    ![image](./media/target-process-tutorial/install_extension.png)

8. Dopo aver aggiunto l'estensione al browser, fare clic su **Setup TargetProcess** (Configura TargetProcess) per passare direttamente all'applicazione TargetProcess. Da qui, fornire le credenziali di amministratore per accedere a TargetProcess. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 9 a 13.

    **Per configurare l'applicazione manualmente, seguire questa procedura:**

9. Accedere all'applicazione TargetProcess come amministratore.

10. Nel menu in alto fare clic su **Impostazione**.

    ![Configurazione](./media/target-process-tutorial/tutorial_target_process_05.png)

11. Fare clic su **Impostazioni**.

    ![Impostazioni](./media/target-process-tutorial/tutorial_target_process_06.png)

12. Fare clic su **Single Sign-On**.

    ![fare clic su Single Sign-On](./media/target-process-tutorial/tutorial_target_process_07.png)

13. Nella finestra di dialogo delle impostazioni Single Sign-On seguire questa procedura:

    ![Configure Single Sign-On](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Fare clic su **Abilita Single Sign-on**.

    b. Nella casella di testo **Sign on URL** (URL di accesso) incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On) copiato dal portale di Azure.

    c. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificato** .

    d. Fare clic su **Abilita Provisioning JIT**.

    e. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a TargetProcess.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **TargetProcess**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **TargetProcess**.

    ![Il collegamento a TargetProcess dall'elenco di applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-targetprocess-test-user"></a>Creare un utente test per TargetProcess

Questa sezione descrive come creare un utente chiamato Britta Simon in TargetProcess. TargetProcess supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a TargetProcess viene creato un nuovo utente, se questo non esiste già.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il  [team di supporto di TargetProcess](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TargetProcess nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TargetProcess per cui è stato configurato l'accesso Single Sign-On. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
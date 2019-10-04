---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rackspace SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Rackspace SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 31826f5d4d88c977f859a009bface2fddf3a1c88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093188"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Esercitazione: Integrazione di Azure Active Directory con Rackspace SSO

Questa esercitazione descrive come integrare Rackspace SSO con Azure Active Directory (Azure AD).
L'integrazione di Rackspace SSO con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Rackspace SSO.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Rackspace SSO con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Rackspace SSO, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Rackspace SSO abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Rackspace SSO supporta l'accesso SSO avviato da **SP**

## <a name="adding-rackspace-sso-from-the-gallery"></a>Aggiungere Rackspace SSO dalla raccolta

Per configurare l'integrazione di Rackspace SSO in Azure AD, è necessario aggiungere Rackspace SSO dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Rackspace SSO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Rackspace SSO**, selezionare **Rackspace SSO** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Rackspace SSO nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Rackspace SSO usando un utente di test di nome **Britta Simon**.
Quando si usa l'accesso Single Sign-On con Rackspace, gli utenti di Rackspace verranno creati automaticamente al primo accesso al portale di Rackspace. 

Per configurare e testare l'accesso Single Sign-On di Azure AD con Rackspace SSO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Rackspace SSO](#configure-rackspace-sso-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare il mapping degli attributi nel pannello di controllo di Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** : per assegnare i ruoli di Rackspace agli utenti di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Rackspace SSO, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Rackspace SSO** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** caricare il **file di metadati del provider di servizi** scaricabile dall'[URL](https://login.rackspace.com/federate/sp.xml) e seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

    ![image](common/upload-metadata.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![image](common/browse-upload-metadata.png)

    c. In seguito al corretto caricamento del file di metadati, gli URL necessari vengono inseriti automaticamente.

    d. Nella casella di testo **URL di accesso** digitare un URL: `https://login.rackspace.com/federate/`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Rackspace SSO](common/sp-signonurl.png)   

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

Questo file verrà caricato in Rackspace per compilare le impostazioni di configurazione richieste per la federazione delle identità.

### <a name="configure-rackspace-sso-single-sign-on"></a>Configurare l'accesso Single Sign-On di Rackspace SSO

Per configurare l'accesso Single Sign-On sul lato **Rackspace SSO**:

1. Vedere l'argomento della documentazione sull'[aggiunta di un provider di identità al Pannello di controllo](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/).
1. Vengono descritte le procedure per:
    1. Creare un nuovo provider di identità
    1. Specificare un dominio di posta elettronica che gli utenti useranno per identificare la società durante l'accesso.
    1. Caricare il file **XML dei metadati della federazione** scaricato in precedenza dal pannello di controllo di Azure.

In questo modo le impostazioni SSO di base necessarie per la connessione di Azure e Rackspace verranno configurate correttamente.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`. Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Rackspace SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Rackspace SSO**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Rackspace SSO**.

    ![Collegamento a Rackspace SSO nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Configurare il mapping degli attributi nel pannello di controllo di Rackspace

Rackspace usa **criteri di mapping degli attributi** per assegnare ruoli e gruppi di Rackspace agli utenti con accesso Single Sign-On. I **criteri di mapping degli attributi** consentono di convertire le attestazioni SAML di Azure AD nei campi di configurazione utente richiesti da Rackspace. Per altre informazioni, vedere la [documentazione di base sul mapping degli attributi](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) di Rackspace. Alcune considerazioni:

* Se si vogliono assegnare vari livelli di accesso a Rackspace usando i gruppi di Azure AD, sarà necessario abilitare l'attestazione gruppi nelle impostazioni dell'accesso Single Sign-On di **Rackspace SSO** in Azure. I **criteri di mapping degli attributi** verranno quindi usati per abbinare gruppi ai ruoli e gruppi desiderati di Rackspace:

    ![Le impostazioni dell'attestazione gruppi](common/sso-groups-claim.png)

* Per impostazione predefinita, Azure AD invia l'UID dei gruppi di Azure AD nell'attestazione di SAML invece del nome del gruppo. Se invece si sta sincronizzando l'istanza locale di Active Directory in Azure AD, è possibile scegliere di inviare i nomi effettivi dei gruppi:

    ![Impostazioni del nome dell'attestazione gruppi](common/sso-groups-claims-names.png)

L'esempio seguente di **criteri di mapping degli attributi** illustra come:
1. Impostare il nome dell'utente di Rackspace nell'attestazione SAML `user.name`. È possibile usare qualsiasi attestazione, ma in genere viene impostato su un campo contenente l'indirizzo di posta elettronica dell'utente.
1. Impostare i ruoli `admin` e `billing:admin` di Rackspace su un utente abbinandoli a un gruppo di Azure AD in base al nome del gruppo o all'UID del gruppo. Viene usata una *sostituzione* di `"{0}"` nel campo `roles` e verrà sostituita dai risultati delle espressioni della regola `remote`.
1. L'uso della `"{D}"`*sostituzione predefinita* per consentire a Rackspace di recuperare campi SAML aggiuntivi cercando attestazioni SAML standard e note nella sostituzione SAML.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Assicurarsi di usare un editor di testo che convalidi la sintassi di YAML durante la modifica del file dei criteri.

Per altri esempi, vedere la [documentazione di base sul mapping degli attributi](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) di Rackspace.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Rackspace SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Rackspace SSO per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

È anche possibile usare il pulsante **Convalida** nelle impostazioni dell'accesso Single Sign-On di **Rackspace SSO**:

   ![Pulsante di convalida SSO](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


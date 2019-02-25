---
title: 'Esercitazione: Integrazione di Azure Active Directory con Fidelity NetBenefits | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Fidelity NetBenefits.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25171a2d455bda3f6a3c0ee85bad47c44820110c
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300755"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Esercitazione: Integrazione di Azure Active Directory con Fidelity NetBenefits

Questa esercitazione descrive come integrare Fidelity NetBenefits con Azure Active Directory (Azure AD).
L'integrazione di Fidelity NetBenefits con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Fidelity NetBenefits.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Fidelity NetBenefits con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Fidelity NetBenefits, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Fidelity NetBenefits abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Fidelity NetBenefits supporta l'accesso SSO avviato da **IDP**

* Fidelity NetBenefits supporta il provisioning utenti **JIT**

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Aggiunta di Fidelity NetBenefits dalla raccolta

Per configurare l'integrazione di Fidelity NetBenefits in Azure AD, è necessario aggiungere Fidelity NetBenefits dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Fidelity NetBenefits dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Fidelity NetBenefits**, selezionare **Fidelity NetBenefits** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Fidelity NetBenefits nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Fidelity NetBenefits usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Fidelity NetBenefits.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Fidelity NetBenefits, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Fidelity NetBenefits](#configure-fidelity-netbenefits-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Fidelity NetBenefits](#create-fidelity-netbenefits-test-user)**: per avere una controparte di Britta Simon in Fidelity NetBenefits collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Fidelity NetBenefits, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Fidelity NetBenefits** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Fidelity NetBenefits](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente:

    Per ambiente di test: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Per ambiente di produzione: `urn:sp:fidelity:geninbndnbparts20`

    b. Nella casella di testo **URL di risposta** digitare un URL che Fidelity deve specificare al momento dell'implementazione oppure contattare il responsabile del servizio clienti Fidelity assegnato.

5. L'applicazione Fidelity NetBenefits prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**. L'applicazione Fidelity NetBenefits prevede che **nameidentifier** sia associato a **employeeid** o a qualsiasi altra attestazione applicabile nell'organizzazione come **nameidentifier**, di conseguenza è necessario modificare il mapping dell'attributo. A questo scopo, fare clic sull'icona **Modifica** e modificare il mapping.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits supporta la federazione statica e dinamica. In quella statica non viene usato il provisioning utenti JIT basato su SAML, mentre quella dinamica supporta il provisioning utenti JIT. Per l'uso di del provisioning basato su JIT, i clienti devono aggiungere altre attestazioni in Azure AD come la data di nascita dell'utente e così via. Tali dettagli vengono forniti dal **manager del servizio clienti Fidelity** assegnato, che deve abilitare questa federazione dinamica per l'istanza.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Fidelity NetBenefits** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Configurare l'accesso Single Sign-On per Fidelity NetBenefits

Per configurare l'accesso Single Sign-On sul lato **Fidelity NetBenefits**, è necessario inviare il file di **XML metadati federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

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

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Fidelity NetBenefits.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Fidelity NetBenefits**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Fidelity NetBenefits**.

    ![Collegamento di Fidelity NetBenefits nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-fidelity-netbenefits-test-user"></a>Creare l'utente di test di Fidelity NetBenefits

In questa sezione viene creato un utente di nome Britta Simon in Fidelity NetBenefits. Se si sta creando una federazione statica, collaborare con il **manager del servizio clienti Fidelity** assegnato per creare gli utenti nella piattaforma Fidelity NetBenefits. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

Per la federazione dinamica, gli utenti vengono creati usando il provisioning utenti JIT. Per l'uso di del provisioning basato su JIT, i clienti devono aggiungere altre attestazioni in Azure AD come la data di nascita dell'utente e così via. Tali dettagli vengono forniti dal **manager del servizio clienti Fidelity** assegnato, che deve abilitare questa federazione dinamica per l'istanza.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Fidelity NetBenefits nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Fidelity NetBenefits per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


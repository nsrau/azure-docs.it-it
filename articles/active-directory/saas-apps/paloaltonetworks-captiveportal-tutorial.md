---
title: 'Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Captive Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks - Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064730"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Captive Portal

Questa esercitazione descrive come integrare Palo Alto Networks - Captive Portal con Azure Active Directory (Azure AD).
L'integrazione di Palo Alto Networks - Captive Portal con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Palo Alto Networks - Captive Portal.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Palo Alto Networks - Captive Portal con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Palo Alto Networks - Captive Portal, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Palo Alto Networks - Captive Portal abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Palo Alto Networks - Captive Portal supporta l'accesso SSO avviato da **IDP**

* Palo Alto Networks - Captive Portal supporta il provisioning utenti **Just-In-Time** (JIT)

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Aggiunta di Palo Alto Networks - Captive Portal dalla raccolta

Per configurare l'integrazione di Palo Alto Networks - Captive Portal in Azure AD, è necessario aggiungere Palo Alto Networks - Captive Portal dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Palo Alto Networks - Captive Portal dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Palo Alto Networks - Captive Portal**, selezionare **Palo Alto Networks - Captive Portal** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Palo Alto Networks - Captive Portal nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Captive Portal usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks - Captive Portal.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Captive Portal, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Palo Alto Networks - Captive Portal](#configure-palo-alto-networks---captive-portal-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test per Palo Alto Networks - Captive Portal](#create-palo-alto-networks---captive-portal-test-user)**: per avere una controparte di Britta Simon in Palo Alto Networks - Captive Portal collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Captive Portal, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Captive Portal** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella finestra di dialogo **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Palo Alto Networks -Captive Portal](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Configurare l'accesso Single Sign-On di Palo Alto Networks - Captive Portal

1. Aprire il sito di Palo Alto come amministratore in un'altra finestra del browser.

2. Fare clic su **Device** (Dispositivo).

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selezionare **SAML Identity Provider** (Provider di identità SAML) nella barra di spostamento a sinistra e fare clic su Import (Importa) per importare il file di metadati.

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Eseguire le operazioni seguenti nella finestra di importazione

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Nella casella di testo **Profile Name** (Nome profilo) specificare un nome, ad esempio Azure AD Admin UI.
    
    b. In **Identity Provider Metadata** (Metadati provider di identità) fare clic su **Browse** (Sfoglia) e selezionare il file metadata.xml scaricato dal portale di Azure.
    
    c. Fare clic su **OK**.

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

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite concessione dell'accesso a Palo Alto Networks - Captive Portal.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Palo Alto Networks - Captive Portal**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Palo Alto Networks - Captive Portal**.

    ![Collegamento di Palo Alto Networks - Captive Portal nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Creare un utente di test di Palo Alto Networks - Captive Portal

In questa sezione viene creato un utente di nome Britta Simon in Palo Alto Networks - Captive Portal. Palo Alto Networks - Captive Portal supporta il **provisioning utenti Just-In-Time** (JIT), che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Palo Alto Networks - Captive Portal, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Per creare un utente manualmente è necessario contattare il [team di supporto clienti di Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

Captive Portal è configurato come protetto da firewall nella macchina virtuale Windows. Per testare la funzionalità Single Sign-On in Captive Portal, accedere alla macchina virtuale Windows tramite il protocollo RDP (Remote Desktop Protocol). All'interno della sessione RDP, aprire un qualsiasi sito Web con il browser. Dovrebbe aprirsi automaticamente l'URL della funzione Single Sign-On con la richiesta di autenticazione. Al termine dell'autenticazione, dovrebbe essere possibile visitare qualsiasi sito Web.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


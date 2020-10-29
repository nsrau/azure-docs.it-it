---
title: 'Esercitazione: Integrazione di Azure Active Directory con ArcGIS Enterprise | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.openlocfilehash: 61920b7c5356b6e1fa5683ac0553060c85e256d3
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457811"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS Enterprise

Questa esercitazione descrive come integrare ArcGIS Enterprise con Azure Active Directory (Azure AD).
L'integrazione di ArcGIS Enterprise con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere ad ArcGIS Enterprise.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) ad ArcGIS Enterprise con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per configurare l'integrazione di Azure AD con ArcGIS Enterprise, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di ArcGIS Enterprise abilitata per l'accesso Single Sign-On

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ArcGIS Enterprise supporta l'accesso SSO avviato da **SP e IDP**
* ArcGIS Enterprise supporta il provisioning utenti **JIT**


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Aggiunta di ArcGIS Enterprise dalla raccolta

Per configurare l'integrazione di ArcGIS Enterprise in Azure AD, è necessario aggiungere ArcGIS Enterprise dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere ArcGIS Enterprise dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni** .

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **ArcGIS Enterprise** selezionare **ArcGIS Enterprise** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![ArcGIS Enterprise nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con [Nome applicazione] usando un utente di test di nome **Britta Simon** .
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in [Nome applicazione].

Per configurare e testare l'accesso Single Sign-On di Azure AD con [Nome applicazione], è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di ArcGIS Enterprise](#configure-arcgis-enterprise-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** : per avere una controparte di Britta Simon in ArcGIS Enterprise collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con [Nome applicazione], seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **ArcGIS Enterprise** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On** .

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base** .

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione nella modalità avviata da **IDP** :

    ![Screenshot che mostra la sezione Configurazione SAML di base in cui è possibile immettere l'identificatore e l'URL di risposta prima di selezionare Salva.](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `<EXTERNAL_DNS_NAME>.portal`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP** :

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di ArcGIS Enterprise](mailto:support@esri.com). Si otterrà il valore dell'identificatore dalla sezione **Set Identity Provider** (Imposta provider di identità), la cui procedura è descritta più avanti in questa esercitazione.

5. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l' **URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Configurare l'accesso Single Sign-On per ArcGIS Enterprise

1. Per automatizzare la configurazione all'interno di ArcGIS Enterprise, è necessario installare l' **estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione** .

    ![Estensione MyApps](common/install-myappssecure-extension.png)

1. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura ArcGIS Enterprise** per passare direttamente all'applicazione ArcGIS Enterprise. Nell'applicazione specificare le credenziali di amministratore per accedere ad ArcGIS Enterprise. L'estensione del browser configurerà automaticamente l'applicazione per l'utente e automatizzerà i passaggi da 3 a 7.

    ![Eseguire la configurazione](common/setup-sso.png)

1. Se si vuole configurare ArcGIS Enterprise manualmente, accedere al sito aziendale di ArcGIS Enterprise come amministratore.


1. Selezionare **Organization >EDIT SETTINGS** (Organizzazione > MODIFICA IMPOSTAZIONI).

    ![Screenshot che mostra la scheda ArcGIS Enterprise Organization con l'opzione Edit settings evidenziata.](./media/arcgisenterprise-tutorial/configure1.png)

1. Selezionare la scheda **Security** (Sicurezza).

    ![Screenshot che mostra la scheda Security selezionata.](./media/arcgisenterprise-tutorial/configure2.png)

1. Scorrere verso il basso alla sezione **Enterprise Logins via SAML** e selezionare **SET ENTERPRISE LOGIN** (IMPOSTA ACCESSO ENTERPRISE).

    ![Screenshot che mostra la sezione Enterprise Logins via SAML in cui è possibile selezionare Set Enterprise Login.](./media/arcgisenterprise-tutorial/configure3.png)

1. Nella sezione **Set Identity Provider** (Imposta provider di identità) eseguire questa procedura:

    ![Screenshot che mostra la sezione Set Identity Provider in cui eseguire i passaggi descritti qui.](./media/arcgisenterprise-tutorial/configure4.png)

    a. Specificare un nome simile a **Test di Azure Active Directory** nella casella di testo **Nome** .

    b. Nella casella di testo **URL** incollare il valore dell' **URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    c. Fare clic su **Mostra impostazioni avanzate** , copiare il valore **ID entità** e incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio ArcGIS Enterprise** nel portale di Azure.
    
    ![Screenshot che mostra dove ottenere l'ID entità e pulsante Aggiorna provider di identità.](./media/arcgisenterprise-tutorial/configure5.png)

    d. Fare clic su **AGGIORNA PROVIDER DI IDENTITÀ** .

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

Questa sezione descrive come creare un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory** , **Utenti** e quindi **Tutti gli utenti** .

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon** .
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio, usare BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Crea** .

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ArcGIS Enterprise.

1. Nel portale di Azure selezionare **Applicazioni aziendali** , quindi **Tutte le applicazioni** e infine **ArcGIS Enterprise** .

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **ArcGIS Enterprise** .

    ![Collegamento di ArcGIS Enterprise nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione** .

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna** .

### <a name="create-arcgis-enterprise-test-user"></a>Creare l'utente di test di ArcGIS Enterprise

In questa sezione viene creato un utente di nome Britta Simon in ArcGIS Enterprise. ArcGIS Enterprise supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in ArcGIS Enterprise, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Per creare un utente manualmente contattare il [team di supporto di ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di ArcGIS Enterprise nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ArcGIS Enterprise per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](./tutorial-list.md)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
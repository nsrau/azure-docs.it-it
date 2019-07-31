---
title: 'Esercitazione: Integrazione di Azure Active Directory con Oracle Cloud Infrastructure Console | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Oracle Cloud Infrastructure Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0725988ff88baea2458f0a5e459440874e66088
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596428"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Esercitazione: Integrare Oracle Cloud Infrastructure Console con Azure Active Directory

Questa esercitazione descrive come integrare Oracle Cloud Infrastructure Console con Azure Active Directory (Azure AD). Integrando Oracle Cloud Infrastructure Console con Azure Active Directory, è possibile:

* Controllare in Azure AD chi può accedere a Oracle Cloud Infrastructure Console.
* Abilitare gli utenti per l'accesso automatico a Oracle Cloud Infrastructure Console con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Oracle Cloud Infrastructure Console abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Oracle Cloud Infrastructure Console supporta l'accesso SSO avviato da **SP**.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Aggiunta di Oracle Cloud Infrastructure Console dalla raccolta

Per configurare l'integrazione di Oracle Cloud Infrastructure Console in Azure AD, è necessario aggiungere Oracle Cloud Infrastructure Console dalla raccolta all'elenco delle app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Oracle Cloud Infrastructure Console** nella casella di ricerca.
1. Selezionare **Oracle Cloud Infrastructure Console** nel riquadro dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Oracle Cloud Infrastructure Console usando un utente di test di nome **B. Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Oracle Cloud Infrastructure Console.

Per configurare e testare l'accesso SSO di Azure AD con Oracle Cloud Infrastructure Console, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
1. **[Configurare Oracle Cloud Infrastructure Console](#configure-oracle-cloud-infrastructure-console)** per configurare le impostazioni dell'accesso Single Sign-On (SSO) sul lato applicazione.
1. **[Creare un utente test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Creare un utente di test di Oracle Cloud Infrastructure Console](#create-oracle-cloud-infrastructure-console-test-user)** per avere una controparte di B. Simon in Oracle Cloud Infrastructure Console collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Oracle Cloud Infrastructure Console** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella pagina **Configurazione SAML di base** immettere i valori per i campi seguenti:

   > [!NOTE]
   > Si otterrà il file di metadati del provider di servizi dalla sezione dell'esercitazione **Configurare l'accesso Single Sign-On per Oracle Cloud Infrastructure Console**.
    
   1. Fare clic su **Carica il file di metadati**.

   1. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

   1. Dopo il caricamento del file di metadati, i valori di **Identificatore** e **URL di risposta** vengono inseriti automaticamente nella casella di testo della sezione **Configurazione SAML di base**.
    
      > [!NOTE]
      > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, inserirli manualmente in base alle proprie esigenze.

      Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Oracle Cloud Infrastructure Console](https://www.oracle.com/support/advanced-customer-support/products/cloud.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML metadati federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

   ![Collegamento di download del certificato](common/metadataxml.png)

1. L'applicazione Oracle Cloud Infrastructure Console prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su  **Modifica** per aprire la finestra di dialogo Attributi utente.

   ![image](common/edit-attribute.png)

1. Oltre a quelli elencati in precedenza, l'applicazione Oracle Cloud Infrastructure Console prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione  **Attributi utente e attestazioni**  della finestra di dialogo  **Attestazioni dei gruppi (anteprima)**   eseguire questa procedura:

   1. Fare clic sulla **penna** accanto a **Valore identificatore nome**.

   1. Selezionare **Persistente** per **Scegliere il formato per l'identificatore del nome**.
 
   1. Fare clic su **Save**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Fare clic sulla **penna** accanto a **Gruppi restituiti nell'attestazione**.

   1. Selezionare **Gruppi di sicurezza** dall'elenco dei pulsanti di opzione.

   1. Per **Attributo di origine** selezionare **ID gruppo**.

   1. Selezionare **Personalizza il nome dell'attestazione basata su gruppo**.

   1. Nella casella di testo **Nome** digitare **groupName**.

   1. Nella casella di testo **Spazio dei nomi (facoltativo)** digitare `https://auth.oraclecloud.com/saml/claims`.

   1. Fare clic su **Save**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Nella sezione **Configura Oracle Cloud Infrastructure Console** copiare gli URL appropriati in base alle esigenze.

   ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Configurare Oracle Cloud Infrastructure Console

1. In un'altra finestra del Web browser accedere a Oracle Cloud Infrastructure Console come amministratore.

1. Fare clic sulla parte sinistra del menu e fare clic su **Identity** (Identità), quindi passare a **Federation** (Federazione).

   ![Configurazione](./media/oracle-cloud-tutorial/config01.png)

1. Salvare il **file di metadati del provider di servizi** facendo clic sul collegamento **Download this document** (Scarica questo documento) e caricarlo nella sezione **Configurazione SAML di base** del portale di Azure e quindi fare clic su **Add Identity Provider** (Aggiungi provider di identità).

   ![Configurazione](./media/oracle-cloud-tutorial/config02.png)

1. Nella finestra popup **Add Identity Provider** (Aggiungi provider di identità) seguire questa procedura:

   ![Configurazione](./media/oracle-cloud-tutorial/config03.png)

   1. Nella casella di testo **NAME** (NOME) immettere il nome.

   1. Nella casella di testo **DESCRIPTION** (DESCRIZIONE) immettere la descrizione.

   1. Selezionare **MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) OR SAML 2.0 COMPLIANT IDENTITY PROVIDER** (MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) O PROVIDER DI IDENTITÀ CONFORME A SAML 2.0) per **TYPE** (TIPO).

   1. Fare clic su **Browse** (Sfoglia) per caricare il file XML dei metadati della federazione scaricato dal portale di Azure.

   1. Fare clic su **Continue** (Continua) e nella sezione **Edit Identity Provider** (Modifica provider di identità) seguire questa procedura:

      ![Configurazione](./media/oracle-cloud-tutorial/config09.png)

   1. Il **** gruppo di provider di identità deve essere selezionato come gruppo personalizzato. L'ID del gruppo deve essere il GUID del gruppo di Azure Active Directory. Il gruppo deve essere mappato al gruppo corrispondente nel campo **OCI GROUP** (GRUPPO OCI).

   1. È possibile eseguire il mapping di più gruppi in base alla configurazione nel portale di Azure e alle esigenze dell'organizzazione. Fare clic su **+ Add mapping** (Aggiungi mapping) per aggiungere tutti i gruppi richiesti.

   1. Fare clic su **Submit**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B. Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B. Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B. Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B. Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Oracle Cloud Infrastructure Console.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Oracle Cloud Infrastructure Console**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

   ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B. Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Creare l'utente di test di Oracle Cloud Infrastructure Console

 Oracle Cloud Infrastructure Console supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso non viene creato un nuovo utente e non è necessario crearne uno.

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Oracle Cloud Infrastructure Console nel Pannello di accesso, si verrà reindirizzati alla pagina di accesso di Oracle Cloud Infrastructure Console. Selezionare il **PROVIDER DI IDENTITÀ** dal menu a discesa e fare clic su **Continue** (Continua) come illustrato di seguito per accedere. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Configurazione](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

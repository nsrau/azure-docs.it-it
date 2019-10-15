---
title: 'Esercitazione: Integrazione di Azure Active Directory con Costpoint | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840055"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Esercitazione: Integrare Costpoint con Azure Active Directory

Questa esercitazione descrive come integrare Costpoint con Azure Active Directory (Azure AD). Integrando Costpoint con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Costpoint.
* Abilitare gli utenti per l'accesso automatico a Costpoint con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Costpoint abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione verranno eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Costpoint supporta l'accesso SSO avviato da **SP e IDP**.

## <a name="generate-costpoint-metadata"></a>Generare i metadati di Costpoint

La configurazione SSO SAML di Costpoint è illustrata nella guida **DeltekCostpoint711Security.pdf**. Scaricare questa guida dal sito di supporto Deltek Costpoint e fare riferimento alla sezione **SAML Single Sign-on Setup** (Configurazione dell'accesso Single Sign-On di SAML) >  **Configure SAML Single Sign-on between Costpoint and Azure AD** (Configura l'accesso Single Sign-On di SAML tra Costpoint e Microsoft Azure). Seguire le istruzioni e generare un file **XML dei metadati di federazione SP di Costpoint**. 

![Utilità di configurazione di Costpoint](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Aggiunta di Costpoint dalla raccolta

Per integrare Costpoint con Azure AD, innanzitutto aggiungere Costpoint all'elenco di app SaaS gestite dalla raccolta nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.

1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.

   ![Pulsante Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

   ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.

   ![Pulsante Nuova applicazione](common/add-new-app.png)

1. Nella sezione **Aggiungi dalla raccolta** digitare **Costpoint** nella casella di ricerca.

   ![Costpoint nell'elenco risultati](common/search-new-app.png)

1. Nell'elenco risultati selezionare **Costpoint** e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso SSO di Azure AD con Costpoint usando un utente di test di nome **B.Simon**. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Costpoint.

Per configurare e testare l'accesso SSO di Azure AD con Costpoint, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
1. **[Configurare Costpoint](#configure-costpoint)** : per configurare le impostazioni dell'accesso Single Sign-On SAML sul lato applicazione.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** per consentire a B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. **[Creare un utente di test di Costpoint](#create-a-costpoint-test-user)** : per avere una controparte di B.Simon in Costpoint collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Costpoint** selezionare **Single Sign-On**.

   ![Collegamento Configura l'accesso Single Sign-On](common/select-sso.png)

1. Nella sezione **Configurazione SAML di base**, se si dispone di un *file di metadati di un provider di servizi*, seguire questa procedura:

   > [!NOTE]
   > Per ottenere il file di metadati del provider di servizi, vedere [Generare i metadati di Costpoint](#generate-costpoint-metadata). La modalità d'uso del file verrà illustrata più avanti nell'esercitazione.
 
   1. Selezionare il pulsante **Carica file di metadati** e quindi selezionare il file **XML dei metadati di federazione SP di Costpoint**, generato in precedenza da Costpoint e quindi selezionare il pulsante **Aggiungi** per caricare il file.

      ![Caricare il file di metadati](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Dopo il caricamento del file di metadati, il valore di **Identificatore** e **URL di risposta** viene inserito automaticamente nella sezione Costpoint.

      > [!NOTE]
      > Se i valori di **Identificatore** e **URL di risposta** non vengono popolati automaticamente, immetterli manualmente in base alle esigenze. Verificare che i valori di **Identificatore (ID entità)** e **URL di risposta (URL del servizio consumer di asserzione)** siano impostati correttamente e che il valore di **URL del servizio consumer di asserzione** corrisponda a un URL di Costpoint valido che termina con **/LoginServlet.cps**.

   1. Selezionare **Imposta URL aggiuntivi**. Per **Stato dell'inoltro** immettere un valore con il formato seguente: `system=[your system]` (ad esempio **system=DELTEKCP**).

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Copia** per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel Blocco note.

   ![Certificato di firma SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Configurare Costpoint

1. Tornare all'utilità di configurazione di Costpoint. Nella casella di testo **IdP Federation Metadata XML** incollare i contenuti del file dell'*URL dei metadati di federazione dell'app*. 

   ![Utilità di configurazione di Costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Continuare a seguire le istruzioni dalla guida **DeltekCostpoint711Security.pdf** per completare la configurazione SAML di Costpoint.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro di sinistra del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

   ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

1. Selezionare **Nuovo utente**.

   ![Pulsante Nuovo utente](common/new-user.png)

1. In **Proprietà utente** completare questa procedura:

   ![Finestra di dialogo Utente](common/user-properties.png)

   1. Nel campo **Nome** immettere **B. Simon**.
   
   1. Nel campo **Nome utente** immettere `b.simon\@yourcompanydomain.extension`, ad esempio B.Simon@contoso.com.
   
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nel campo **Password**.
   
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Costpoint.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

1. Nell'elenco delle applicazioni selezionare **Costpoint**.

1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

   ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti**. Scegliere, quindi, **Seleziona**.

1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi scegliere **Seleziona**.

1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-costpoint-test-user"></a>Creare un utente di test di Costpoint

In questa sezione viene creato un utente in Costpoint. Presupporre che l'ID utente sia **B.SIMON** e che il nome utente sia **B.Simon**. Collaborare con il [team di supporto clienti di Costpoint](https://www.deltek.com/about/contact-us) per aggiungere gli utenti alla piattaforma Costpoint. L'utente deve essere creato e attivato prima di usare l'accesso Single Sign-On.

Dopo aver creato l'utente, l'opzione da selezionare come **Metodo di autenticazione** dell'utente deve essere **Active Directory**, la casella di controllo **Single Sign-on SAML** deve essere selezionata e il nome utente di Azure Active Directory deve essere **Active Directory o ID certificato** (come illustrato nello screenshot seguente).

![Utente di Costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Testare l'accesso SSO

Quando si seleziona il riquadro di Costpoint nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Costpoint per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni per l'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

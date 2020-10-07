---
title: 'Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks Captive Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks Captive Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1096437fc1d77042a9db4dc359d51cd6d9d22960
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304390"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks Captive Portal

Questa esercitazione descrive come integrare Palo Alto Networks Captive Portal con Azure Active Directory (Azure AD).
L'integrazione di Palo Alto Networks Captive Portal con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Palo Alto Networks Captive Portal.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Palo Alto Networks Captive Portal con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per integrare Azure AD con Palo Alto Networks Captive Portal, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure Active Directory. Se Azure AD non è disponibile, è possibile ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Palo Alto Networks Captive Portal abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Palo Alto Networks Captive Portal supporta l'accesso SSO avviato da **IDP**
* Palo Alto Networks Captive Portal supporta il provisioning utenti **JIT**

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Aggiunta di Palo Alto Networks Captive Portal dalla raccolta

Per configurare l'integrazione di Palo Alto Networks Captive Portal in Azure AD, è necessario aggiungere Palo Alto Networks Captive Portal dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Palo Alto Networks Captive Portal** nella casella di ricerca.
1. Selezionare **Palo Alto Networks Captive Portal** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks Captive Portal usando un utente di test di nome **B.Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks Captive Portal.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks Captive Portal, seguire questa procedura:

1. **[Configurare l'accesso SSO di Azure AD](#configure-azure-ad-sso)** : per consentire all'utente di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : configurare B.Simon per l'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso Single Sign-On di Palo Alto Networks Captive Portal](#configure-palo-alto-networks-captive-portal-sso)** : per configurare le impostazioni di Single Sign-On nell'applicazione.
    * **[Creare l'utente di test di a Palo Alto Networks Captive Portal](#create-a-palo-alto-networks-captive-portal-test-user)** : per avere una controparte di B.Simon in Palo Alto Networks Captive Portal collegata alla rappresentazione dell'utente in Azure AD.
3. **[Testare l'accesso SSO](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks Captive Portal** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nel riquadro **Configurazione SAML di base** seguire questa procedura:

   1. In **Identificatore** immettere un URL nel formato seguente: `https://<customer_firewall_host_name>/SAML20/SP`.

   2. In **URL di risposta** immettere un URL nel formato seguente: `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Aggiornare i valori dei segnaposto in questo passaggio con i valori effettivi di identificatore e URL di risposta. Per ottenere i valori effettivi, contattare il [team di supporto clienti di Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. Nella sezione **Certificato di firma SAML** selezionare **Scarica** accanto a **XML metadati federazione**. Salvare il file scaricato nel computer.

    ![Collegamento per il download del file XML dei metadati federazione](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Palo Alto Networks Captive Portal.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Palo Alto Networks Captive Portal**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Configurare l'accesso Single Sign-On di Palo Alto Networks Captive Portal

A questo punto configurare l'accesso Single Sing-On in Palo Alto Networks Captive Portal:

1. In un'altra finestra del Web browser accedere al sito Web di Palo Alto Networks come amministratore.

2. Selezionare la scheda **Device** (Dispositivo).

    ![Scheda Device del sito Web di Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Nel menu selezionare **SAML Identity Provider** (Provider di identità SAML) e quindi selezionare **Import** (Importa).

    ![Pulsante Import](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Nella finestra di dialogo **SAML Identity Provider Server Profile Import** (Importazione profilo server provider di identità SAML) completare la procedura seguente:

    ![Configurare l'accesso Single Sign-On per Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. In **Profile Name** (Nome profilo) immettere un nome, ad esempio **AzureAD-CaptivePortal**.
    
    2. Accanto a **Identity Provider Metadata** (Metadati provider di identità) selezionare **Browse** (Sfoglia). Selezionare il file metadata.xml scaricato nel portale di Azure.
    
    3. Selezionare **OK**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Creare l'utente di test di Palo Alto Networks Captive Portal

A questo punto creare un utente di nome *Britta Simon* in Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. In questa sezione non è necessario completare alcuna attività. Se non esiste già un utente in Palo Alto Networks Captive Portal, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Se si vuole creare un utente manualmente, contattare il [team di supporto clienti di Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

Dopo aver fatto clic su Test this application (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di Palo Alto Networks Captive Portal per cui si è configurato l'accesso SSO

È possibile usare il Pannello di accesso Microsoft. Quando si fa clic sul riquadro Palo Alto Networks Captive Portal nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Palo Alto Networks Captive Portal per cui è stato configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Palo Alto Networks Captive Portal, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

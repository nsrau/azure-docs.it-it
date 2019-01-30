---
title: 'Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks Captive Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 15ddb29f090ca8dc14befc93e3377c181038b554
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816161"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks Captive Portal

Questa esercitazione descrive come integrare Palo Alto Networks Captive Portal con Azure Active Directory (Azure AD).

L'integrazione di Palo Alto Networks Captive Portal con Azure AD offre i vantaggi seguenti:

* In Azure AD è possibile controllare chi può accedere a Palo Alto Networks Captive Portal.
* Per automatizzare l'accesso degli utenti in Palo Alto Networks Captive Portal (Single Sign-On), è possibile usare gli account Azure AD degli utenti.
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD,vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

Per integrare Azure AD con Palo Alto Networks Captive Portal, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure Active Directory. Se Azure AD non è disponibile, è possibile ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Palo Alto Networks Captive Portal abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Palo Alto Networks Captive Portal supporta gli scenari seguenti:

* **Accesso Single Sign-On avviato da IDP**
* **Provisioning JIT**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Aggiungere Palo Alto Networks Captive Portal dalla raccolta

Per iniziare, nella raccolta aggiungere Palo Alto Networks Captive Portal all'elenco di app SaaS gestite:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel menu sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Opzione Applicazioni aziendali nel menu](common/enterprise-applications.png)

3. Selezionare **Nuova applicazione**.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Palo Alto Networks Captive Portal**. Nei risultati della ricerca selezionare **Palo Alto Networks - Captive Portal** e quindi scegliere **Aggiungi**.

     ![Palo Alto Networks - Captive Portal nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks Captive Portal usando un utente di test di nome *Britta Simon*. Per il corretto funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione tra un utente di Azure AD e lo stesso utente in Palo Alto Networks Captive Portal. 

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks Captive Portal, completare le attività seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire all'utente di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On per Palo Alto Networks Captive Portal](#configure-palo-alto-networks-captive-portal-single-sign-on)**: per configurare le impostazioni dell'accesso Single Sign-On nell'applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente *Britta Simon*.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per configurare Britta Simon per l'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Palo Alto Networks Captive Portal](#create-palo-alto-networks-captive-portal-test-user)**: creare un utente *Britta Simon* corrispondente in Palo Alto Networks Captive Portal collegato all'utente di Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In primo luogo, abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure:

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Captive Portal** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare l'icona della matita **Modifica**.

    ![Icona della matita Modifica](common/edit-urls.png)

4. Nel riquadro **Configurazione SAML di base** completare la procedura seguente:

    ![Riquadro Configurazione SAML di base di Palo Alto Networks Captive Portal](common/idp-intiated.png)

    1. In **Identificatore** immettere un URL nel formato seguente: `https://<customer_firewall_host_name>/SAML20/SP`.

    2. In **URL di risposta** immettere un URL nel formato seguente: `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

    > [!NOTE]
    > Aggiornare i valori dei segnaposto in questo passaggio con i valori effettivi di identificatore e URL di risposta. Per ottenere i valori effettivi, contattare il [team di supporto clienti di Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. Nella sezione **Certificato di firma SAML** selezionare **Scarica** accanto a **XML metadati federazione**. Salvare il file scaricato nel computer.

    ![Collegamento per il download del file XML dei metadati federazione](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Configurare l'accesso Single Sign-On per Palo Alto Networks Captive Portal

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

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

A questo punto creare un utente di test di nome *Britta Simon* nel portale di Azure:

1. Nel portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente**.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nel riquadro **Utente** completare la procedura seguente:

    ![Finestra di dialogo Utente](common/user-properties.png)

    1. In **Nome** immettere **BrittaSimon**.
  
    2. In **Nome utente** immettere **BrittaSimon@\<dominio_società\>**. Ad esempio, **BrittaSimon@contoso.com**.

    3. In **Password** immettere una password. È consigliabile prendere nota della password immessa. Per visualizzare la password, è possibile selezionare la casella di controllo **Mostra password**.

    4. Selezionare **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

Concedere quindi l'accesso a Palo Alto Networks Captive Portal in modo che Britta Simon possa usare l'accesso Single Sign-On di Azure:

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni immettere **Palo Alto Networks - Captive Portal** e quindi selezionare l'applicazione.

    ![Collegamento di Palo Alto Networks - Captive Portal nell'elenco delle applicazioni](common/all-applications.png)

3. Nel menu selezionare **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente**. Nel riquadro **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nel riquadro **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**. Scegliere **Seleziona**.

6. Per aggiungere un valore di ruolo per l'asserzione SAML, nel riquadro **Seleziona ruolo** selezionare il ruolo pertinente per l'utente. Scegliere **Seleziona**.

7. Nel riquadro **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Creare un utente di test di Palo Alto Networks Captive Portal

A questo punto creare un utente di nome *Britta Simon* in Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. In questa sezione non è necessario completare alcuna attività. Se non esiste già un utente in Palo Alto Networks Captive Portal, ne viene creato uno nuovo dopo l'autenticazione.

> [!NOTE]
> Se si vuole creare un utente manualmente, contattare il [team di supporto clienti di Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

Palo Alto Networks Captive Portal viene installato in una macchina virtuale Windows VM ed è protetto da firewall. Per testare l'accesso Single Sign-On in Palo Alto Networks Captive Portal, accedere alla macchina virtuale Windows con Remote Desktop Protocol (RDP). Nella sessione RDP aprire un browser e passare a un sito Web qualsiasi. L'URL di SSO viene aperto e viene richiesto di eseguire l'autenticazione. Al termine dell'autenticazione è possibile accedere ai siti Web.

## <a name="additional-resources"></a>Risorse aggiuntive

Per altre informazioni, vedere gli articoli seguenti:

- [Esercitazioni sull'integrazione di app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Accesso condizionale in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


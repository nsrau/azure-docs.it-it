---
title: 'Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Captive Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks - Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: fa47eaea590ecb84386a6e0ce4eff0a6933be554
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444202"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Palo Alto Networks - Captive Portal

Questa esercitazione descrive come integrare Palo Alto Networks - Captive Portal con Azure Active Directory (Azure AD).

L'integrazione di Palo Alto Networks - Captive Portal con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Palo Alto Networks - Captive Portal.
- È possibile abilitare gli utenti per l'accesso automatico a Palo Alto Networks - Captive Portal (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Palo Alto Networks - Captive Portal, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Palo Alto Networks - Captive Portal abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Palo Alto Networks - Captive Portal dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Aggiunta di Palo Alto Networks - Captive Portal dalla raccolta
Per configurare l'integrazione di Palo Alto Networks - Captive Portal in Azure AD, è necessario aggiungere Palo Alto Networks - Captive Portal dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Palo Alto Networks - Captive Portal dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Palo Alto Networks - Captive Portal**, selezionare **Palo Alto Networks - Captive Portal** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Palo Alto Networks - Captive Portal nell'elenco dei risultati](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Captive Portal usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Palo Alto Networks - Captive Portal corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks - Captive Portal.

Per stabilire la relazione di collegamento, in Palo Alto Networks - Captive Portal assegnare il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Captive Portal, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test per Palo Alto Networks - Captive Portal](#create-a-palo-alto-networks---captive-portal-test-user)**: per avere una controparte di Britta Simon in Palo Alto Networks - Captive Portal collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Palo Alto Networks - Captive Portal.

**Per configurare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Captive Portal, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Captive Portal** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

1. Nella sezione **URL e dominio Palo Alto Networks - Captive Portal** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Palo Alto Networks -Captive Portal](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support).

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

1. Aprire il sito di Palo Alto come amministratore in un'altra finestra del browser.

1. Fare clic su **Device** (Dispositivo).

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

1. Selezionare **SAML Identity Provider** (Provider di identità SAML) nella barra di spostamento a sinistra e fare clic su Import (Importa) per importare il file di metadati.

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Eseguire le operazioni seguenti nella finestra di importazione

    ![Configurare l'accesso Single Sign-On per Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Nella casella di testo **Profile Name** (Nome profilo) specificare un nome, ad esempio Azure AD Admin UI.
    
    b. In **Identity Provider Metadata** (Metadati provider di identità) fare clic su **Browse** (Sfoglia) e selezionare il file metadata.xml scaricato dal portale di Azure.
    
    c. Fare clic su **OK**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Creare un utente di test di Palo Alto Networks - Captive Portal

Questa sezione descrive come creare un utente chiamato Britta Simon in Palo Alto Networks - Captive Portal. Palo Alto Networks - Captive Portal supporta il provisioning JIT (just-in-time), abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Palo Alto Networks - Captive Portal viene creato un nuovo utente, se non esiste già. 

> [!NOTE]
> Per creare un utente manualmente è necessario contattare il [team di supporto di Palo Alto Networks - Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite concessione dell'accesso a Palo Alto Networks - Captive Portal.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Palo Alto Networks - Captive Portal, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Palo Alto Networks - Captive Portal**.

    ![Collegamento di Palo Alto Networks - Captive Portal nell'elenco delle applicazioni](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Captive Portal è configurato come protetto da firewall nella macchina virtuale Windows.  Per testare la funzionalità Single Sign-On in Captive Portal, accedere alla macchina virtuale Windows tramite il protocollo RDP (Remote Desktop Protocol). All'interno della sessione RDP, aprire un qualsiasi sito Web con il browser. Dovrebbe aprirsi automaticamente l'URL della funzione Single Sign-On con la richiesta di autenticazione. Al termine dell'autenticazione, dovrebbe essere possibile visitare qualsiasi sito Web. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png


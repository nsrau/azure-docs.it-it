---
title: 'Esercitazione: Integrazione di Azure Active Directory con Oneteam | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Oneteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2e94916c-64ae-4e1a-a8b5-bc6ef7d28c29
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ede20d0fc0afa1e957d5469a097cba91db14f546
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186200"
---
# <a name="tutorial-azure-active-directory-integration-with-oneteam"></a>Esercitazione: Integrazione di Azure Active Directory con Oneteam

Questa esercitazione descrive come integrare Oneteam con Azure Active Directory (Azure AD).

L'integrazione di Oneteam con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Oneteam
- È possibile abilitare gli utenti per l'accesso automatico a Oneteam (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Oneteam, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Oneteam abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Oneteam dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-oneteam-from-the-gallery"></a>Aggiunta di Oneteam dalla raccolta
Per configurare l'integrazione di Oneteam in Azure AD, è necessario aggiungere Oneteam dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Oneteam dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Oneteam**.

    ![Creazione di un utente test di Azure AD](./media/oneteam-tutorial/tutorial_oneteam_search.png)

1. Nel pannello dei risultati selezionare **Oneteam** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/oneteam-tutorial/tutorial_oneteam_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Oneteam in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Oneteam che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Oneteam.

Per stabilire la relazione di collegamento, in Oneteam assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Oneteam, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Oneteam](#creating-a-oneteam-test-user)**: per avere una controparte di Britta Simon in Oneteam collegata alla relativa rappresentazione in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Oneteam.

**Per configurare l'accesso Single Sign-On di Azure AD con Oneteam, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Oneteam** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/oneteam-tutorial/tutorial_oneteam_samlbase.png)

1. Nella sezione **URL e dominio Oneteam** per configurare l'applicazione in modalità avviata da **IDP**:

    ![Configure Single Sign-On](./media/oneteam-tutorial/tutorial_oneteam_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://api.one-team.io/teams/<team name>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://api.one-team.io/teams/<team name>/auth/saml/callback`

1. Selezionare **Mostra impostazioni URL avanzate**, se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Configure Single Sign-On](./media/oneteam-tutorial/tutorial_oneteam_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<team name>.one-team.io/`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Oneteam](https://support.one-team.com/hc/requests/new). 



1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/oneteam-tutorial/tutorial_oneteam_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/oneteam-tutorial/tutorial_general_400.png)
    
1. Per ottenere l'accesso SSO configurato per l'applicazione, è possibile generare il ticket di supporto [con il team di supporto Oneteam](https://support.one-team.com/hc/requests/new) e fornire loro i **metadati** scaricati. 

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/oneteam-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/oneteam-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/oneteam-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/oneteam-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-oneteam-test-user"></a>Creazione di un utente test di Oneteam

Questa sezione descrive come creare un utente di nome Britta Simon in Oneteam. Oneteam supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Oneteam verrà creato un nuovo utente, se non esiste già.

>[!NOTE]
>Se è necessario creare manualmente un utente, è possibile generare il ticket di supporto con il [team di supporto Oneteam](https://support.one-team.com/hc/requests/new).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendo l'accesso a Oneteam.

![Assegna utente][200] 

**Per assegnare Britta Simon a Oneteam seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni, selezionare **Oneteam**.

    ![Configure Single Sign-On](./media/oneteam-tutorial/tutorial_oneteam_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Oneteam nel pannello di accesso, si accederà automaticamente all'applicazione Oneteam.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/oneteam-tutorial/tutorial_general_01.png
[2]: ./media/oneteam-tutorial/tutorial_general_02.png
[3]: ./media/oneteam-tutorial/tutorial_general_03.png
[4]: ./media/oneteam-tutorial/tutorial_general_04.png

[100]: ./media/oneteam-tutorial/tutorial_general_100.png

[200]: ./media/oneteam-tutorial/tutorial_general_200.png
[201]: ./media/oneteam-tutorial/tutorial_general_201.png
[202]: ./media/oneteam-tutorial/tutorial_general_202.png
[203]: ./media/oneteam-tutorial/tutorial_general_203.png


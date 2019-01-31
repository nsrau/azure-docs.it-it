---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wizergos Productivity Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Wizergos Productivity Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: c59bacb9559239c572925ee73303bbb6a0a06353
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188831"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Esercitazione: Integrazione di Azure Active Directory con Wizergos Productivity Software

Questa esercitazione descrive come integrare Wizergos Productivity Software con Azure Active Directory (Azure AD).

L'integrazione di Wizergos Productivity Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Wizergos Productivity Software.
- È possibile abilitare gli utenti per l'accesso automatico a Wizergos Productivity Software (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Wizergos Productivity Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Wizergos Productivity Software abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Wizergos Productivity Software dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Aggiunta di Wizergos Productivity Software dalla raccolta
Per configurare l'integrazione di Wizergos Productivity Software in Azure AD, è necessario aggiungere Wizergos Productivity Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Wizergos Productivity Software dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Wizergos Productivity Software**, selezionare **Wizergos Productivity Software** nel pannello dei risultati e quindi fare clic su **Aggiungi** per aggiungere l'applicazione.

    ![Wizergos Productivity Software nell'elenco risultati](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Wizergos Productivity Software in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Wizergos Productivity Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Wizergos Productivity Software.

Per stabilire la relazione di collegamento, in Wizergos Productivity Software assegnare il valore del **nome utente** di Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Wizergos Productivity Software, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Wizergos Productivity Software](#create-a-wizergos-productivity-software-test-user)**: per avere una controparte di Britta Simon in Wizergos Productivity Software collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione l'accesso Single Sign-On di Azure AD viene abilitato nel portale di Azure e configurato nell'applicazione Wizergos Productivity Software.

**Per configurare Single Sign-On di Azure AD con Wizergos Productivity Software, seguire questa procedura:**

1. Nel portale di Azure, nella pagina relativa all'integrazione dell'applicazione **Wizergos Productivity Software**, fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_samlbase.png)

1. Nella sezione **URL e dominio Wizergos Productivity Software** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On a URL e dominio di Wizergos Productivity Software](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_url.png)

    Nella casella di testo **Identificatore** digitare l'URL: `https://www.wizergos.net`

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/wizergosproductivitysoftware-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Wizergos Productivity Software** fare clic su **Configura Wizergos Productivity Software** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Wizergos Productivity Software](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_configure.png) 

1. In un'altra finestra del browser Web accedere al tenant Wizergos Productivity Software come amministratore.

1. Dal menu hamburger, selezionare **Admin**(Amministratore).

    ![Configurazione accesso Single Sign-On sul lato app](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

1. Nella pagina visualizzata selezionare **AUTHENTICATION** (AUTENTICAZIONE) nel menu a sinistra e fare clic su **Azure AD**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

1. Seguire questa procedura nella sezione **AUTHENTICATION** (AUTENTICAZIONE).

    ![Configurazione accesso Single Sign-On sul lato app](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Fare clic sull'icona **UPLOAD** (CARICA) per caricare il certificato scaricato da Azure AD.
    
    b. Nella casella di testo **Issuer URL** incollare il valore di **ID entità** SAML copiato dal portale di Azure.
    
    c. Nella casella di testo **Single Sign-On URL** incollare il valore di **URL servizio Single Sign-On** SAML copiato dal portale di Azure.
    
    d. Nella casella di testo **Single Sign-Out URL** incollare il valore di **URL disconnessione** copiato dal portale di Azure.
    
    e. Fare clic sul pulsante **Salva** .

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/wizergosproductivitysoftware-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/wizergosproductivitysoftware-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-wizergos-productivity-software-test-user"></a>Creare un utente di test Wizergos Productivity Software

In questa sezione viene creato un utente chiamato Britta Simon in Wizergos Productivity Software. Per aggiungere utenti nella piattaforma Wizergos Productivity Software, contattare il [team di supporto di Wizergos Productivity Software](mailTo:support@wizergos.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata a usare l'accesso Single Sign-On di Azure concedendole l'accesso a Wizergos Productivity Software.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Wizergos Productivity Software, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Wizergos Productivity Software**.

    ![Collegamento di Wizergos Productivity Software nell'elenco delle applicazioni](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Wizergos Productivity Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Wizergos Productivity Software.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[100]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[202]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_202.png
[203]: ./media/wizergosproductivitysoftware-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con IriusRisk | Documenti Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e IriusRisk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d2c854d5-101d-4d67-80e0-87749e1a0352
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: jeedes
ms.openlocfilehash: e33d584b3e7eb939d669253e8c6ef40fd4f963c9
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iriusrisk"></a>Esercitazione: Integrazione di Azure Active Directory con IriusRisk

In questa esercitazione informazioni su come integrare IriusRisk con Azure Active Directory (Azure AD).

Integrazione IriusRisk con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso al IriusRisk.
- È possibile abilitare gli utenti per automaticamente ottenere firmato IriusRisk (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con IriusRisk, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Un IriusRisk single sign-on abilitato sottoscrizione

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di IriusRisk dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-iriusrisk-from-the-gallery"></a>Aggiunta di IriusRisk dalla raccolta
Per configurare l'integrazione di IriusRisk in Azure AD, è necessario aggiungere all'elenco di App SaaS gestite IriusRisk dalla raccolta.

**Per aggiungere IriusRisk dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **IriusRisk**selezionare **IriusRisk** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere l'applicazione.

    ![IriusRisk nell'elenco dei risultati](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con IriusRisk in base a un utente di test denominato "Laura Giussani".

Per single sign-on a funzionare, Azure AD deve conoscere l'utente corrispondente in IriusRisk a un utente in Azure AD. In altre parole, una relazione di collegamento tra un utente AD Azure e l'utente correlato in IriusRisk deve essere stabilito.

In IriusRisk, assegnare il valore della **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare Azure AD single sign-on con IriusRisk, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test IriusRisk](#create-a-iriusrisk-test-user)**  - disporre di un equivalente di Britta Simon in IriusRisk collegato per la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione IriusRisk.

**Per configurare Azure AD single sign-on con IriusRisk, eseguire la procedura seguente:**

1. Nel portale di Azure, sul **IriusRisk** pagina di integrazione dell'applicazione, fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_samlbase.png)

3. Nel **IriusRisk dominio e gli URL** sezione, eseguire la procedura seguente:

    ![IriusRisk informazioni domini e gli URL single sign-on](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.iriusrisk.com/ui#!login`

    b. Nella casella di testo **Identificatore** digitare il valore: `iriusrisk-sp`

    > [!NOTE] 
    > Il valore di URL Sign-on non è di tipo real. è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Contatto [team di supporto IriusRisk Client](mailto:info@continuumsecurity.net) per ottenere questo valore. 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso single sign-on in **IriusRisk** lato, è necessario inviare scaricato **Metadata XML** a [team di supporto IriusRisk](mailto:info@continuumsecurity.net). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-iriusrisk-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-iriusrisk-test-user"></a>Creare un utente test IriusRisk

L'obiettivo di questa sezione consiste nel creare un utente denominato Britta Simon in IriusRisk. IriusRisk supporta il provisioning di just-in-time, che è per impostazione predefinita abilitato. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accedere a IriusRisk se non esiste ancora, viene creato un nuovo utente.

> [!Note]
> Se è necessario creare manualmente un utente, contattare [team di supporto IriusRisk](mailto:info@continuumsecurity.net).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare Britta Simon utilizzare single sign-on Azure concedendo l'accesso a IriusRisk.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon IriusRisk, procedere come segue:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **IriusRisk**.

    ![Il collegamento IriusRisk nell'elenco delle applicazioni](./media/active-directory-saas-iriusrisk-tutorial/tutorial_iriusrisk_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro IriusRisk nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on all'applicazione IriusRisk.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iriusrisk-tutorial/tutorial_general_203.png


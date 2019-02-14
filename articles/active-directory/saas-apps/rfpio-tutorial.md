---
title: 'Esercitazione: Integrazione di Azure Active Directory con RFPIO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af5c3d5d92dea804221b2285d4fa5b9cedae665
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168091"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Esercitazione: Integrazione di Azure Active Directory con RFPIO

Questa esercitazione descrive come integrare RFPIO con Azure Active Directory (Azure AD).

L'integrazione di RFPIO con Azure AD offre i vantaggi seguenti:

- In Azure AD è possibile controllare chi può accedere a RFPIO.
- È possibile abilitare gli utenti per l'accesso automatico a RFPIO (Single Sign-On) con i propri account Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con RFPIO, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD.
- Sottoscrizione RFPIO abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di RFPIO dalla raccolta.
1. Configurazione e test dell'accesso Single Sign-On di Azure AD.

## <a name="add-rfpio-from-the-gallery"></a>Aggiungere RFPIO dalla raccolta
Per configurare l'integrazione di RFPIO in Azure AD, è necessario aggiungere RFPIO dalla raccolta al proprio elenco di app SaaS gestite.

### <a name="to-add-rfpio-from-the-gallery"></a>Per aggiungere RFPIO dalla raccolta

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare l'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Selezionare **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Per aggiungere una nuova applicazione selezionare il pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **RFPIO**.

    ![Creazione di un utente test di Azure AD](./media/rfpio-tutorial/tutorial_rfpio_search.png)

1. Nel pannello dei risultati selezionare **RFPIO** e quindi selezionare il pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RFPIO in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è la relazione esistente fra un utente di Azure AD e l'utente corrispondente in RFPIO. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RFPIO.

Per stabilire la relazione di collegamento, in RFPIO assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con RFPIO, è necessario completare i blocchi predefiniti seguenti:

1. **Configurare l'accesso Single Sign-On di Azure AD**: per consentire agli utenti di usare questa funzionalità.
1. **Creare un utente test di Azure AD**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **Creare un utente di test di RFPIO**: per avere una controparte di Britta Simon in RFPIO collegata alla relativa rappresentazione in Azure AD.
1. **Assegnare l'utente di test di Azure AD**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **Testare l'accesso Single Sign-On**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione RFPIO.

**Per configurare l'accesso Single Sign-On di Azure AD con RFPIO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RFPIO** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

1. Nella sezione **URL e dominio RFPIO**, se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Configure Single Sign-On](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://www.rfpio.com`

    ![Configure Single Sign-On](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Selezionare **Mostra impostazioni URL avanzate**

    c. Nella casella di testo **Stato dell'inoltro** immettere un valore stringa. Per ottenere tale valore, contattare il [team di supporto di RFPIO](https://www.rfpio.com/contact/). 

1. Selezionare **Mostra impostazioni URL avanzate** se si desidera configurare l'applicazione in modalità avviata da **SP**: 

    ![Configure Single Sign-On](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://www.app.rfpio.com`

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/rfpio-tutorial/tutorial_general_400.png)

1. In un'altra finestra del Web browser accedere al sito Web **RFPIO** come amministratore.

1. Fare clic sull'elenco a discesa a sinistra nella parte inferiore.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app1.png)

1. Fare clic su di **Impostazioni organizzazione**. 

    ![Configure Single Sign-On](./media/rfpio-tutorial/app2.png)

1. Fare clic sulla sezione relativa a **FUNZIONALITÀ E INTEGRAZIONE**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app4.png)

1. In **SAML Configurazione SSO** fare clic su **Modifica**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app3.png)

1. In questa sezione eseguire le seguenti operazioni:

    ![Configure Single Sign-On](./media/rfpio-tutorial/app5.png)
    
    a. Copiare il contenuto del **file XML di metadati scaricato** e incollarlo nella casella della **configurazione identità**.

    > [!NOTE]
    >Per copiare il contenuto del file di **metadata XML** scaricato usare **Notepad++** o un **editor XML** appropriato. 

    b. Fare clic su **Convalida**.

    c. Dopo aver fatto clic su **Convalida**, impostare **SAML (Abilitato)** su attivato.

    d. Fare clic su **Submit**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/rfpio-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/rfpio-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/rfpio-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-rfpio-test-user"></a>Creare un utente di test di RFPIO

Per consentire agli utenti di Azure AD di accedere a RFPIO, è necessario eseguirne il provisioning in RFPIO.  
Nel caso di RFPIO, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di RFPIO come amministratore.

1. Fare clic sull'elenco a discesa a sinistra nella parte inferiore.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app1.png)

1. Fare clic su di **Impostazioni organizzazione**. 

    ![Configure Single Sign-On](./media/rfpio-tutorial/app2.png)

1. Fare clic su **MEMBRI DEL TEAM**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app6.png)

1. Fare clic su **AGGIUNGI MEMBRI**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/app7.png)

1. Nella sezione **Aggiungere nuovi membri**. eseguire le seguenti operazioni:

    ![Configure Single Sign-On](./media/rfpio-tutorial/app8.png)

    a. Immettere l'**indirizzo di posta elettronica** nella casella che richiede **un indirizzo di posta elettronica per riga**.

    b. Selezionare il **ruolo** in base alle esigenze.

    c. Fare clic su **AGGIUNGI MEMBRI**.
        
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RFPIO.

![Assegna utente][200] 

**Per assegnare Britta Simon a RFPIO seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **RFPIO**.

    ![Configure Single Sign-On](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro RFPIO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RFPIO.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png


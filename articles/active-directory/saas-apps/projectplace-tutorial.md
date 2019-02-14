---
title: 'Esercitazione: Integrazione di Azure Active Directory con Projectplace | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7476783ec5d58979ec055b4e4d991c69fce59465
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209711"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Esercitazione: Integrazione di Azure Active Directory con Projectplace

Questa esercitazione descrive come integrare Projectplace con Azure Active Directory (Azure AD).

L'integrazione di Projectplace con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Projectplace
- È possibile abilitare gli utenti per l'accesso automatico a Projectplace (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Projectplace, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Projectplace abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Projectplace dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-projectplace-from-the-gallery"></a>Aggiunta di Projectplace dalla raccolta
Per configurare l'integrazione di Projectplace in Azure AD, è necessario aggiungere Projectplace dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Projectplace dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Projectplace**.

    ![Creazione di un utente test di Azure AD](./media/projectplace-tutorial/tutorial_projectplace_search.png)

1. Nel pannello dei risultati selezionare **Projectplace** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/projectplace-tutorial/tutorial_projectplace_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Projectplace usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Projectplace corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Projectplace.

Per stabilire la relazione di collegamento, in Projectplace assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Projectplace, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente di test di Projectplace](#creating-a-projectplace-test-user)**: per avere una controparte di Britta Simon in Projectplace collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Projectplace.

**Per configurare l'accesso Single Sign-On di Azure AD con Projectplace, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Projectplace** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/projectplace-tutorial/tutorial_projectplace_samlbase.png)

1. Nella sezione **URL e dominio Projectplace** seguire questa procedura:

    ![Configure Single Sign-On](./media/projectplace-tutorial/tutorial_projectplace_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company>.projectplace.com`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Per ottenere questo valore, contattare il [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). 
 
1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/projectplace-tutorial/tutorial_projectplace_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/projectplace-tutorial/tutorial_general_400.png)

1. Per configurare l'accesso Single Sign-On sul lato **Projectplace**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

>[!NOTE]
>La configurazione dell'accesso Single Sign-On deve essere eseguita dal [team di supporto di Projectplace](https://success.planview.com/Projectplace/Support). Al termine della configurazione, viene ricevuta una notifica.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/projectplace-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/projectplace-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/projectplace-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/projectplace-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-projectplace-test-user"></a>Creazione di un utente di test di Projectplace

Per consentire agli utenti di Azure AD di accedere a Projectplace, è necessario eseguirne il provisioning in Projectplace. Nel caso di Projectplace, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Projectplace** come amministratore.

1. Passare a **Persone**, quindi fare clic su **Membri**.
   
    ![Persone](./media/projectplace-tutorial/ic790228.png "Persone")

1. Fare clic su **Aggiungi membro**.
   
    ![Aggiungere membri](./media/projectplace-tutorial/ic790232.png "Aggiungere membri")

1. Nella sezione **Aggiungi membro** , eseguire la procedura seguente:
   
    ![Nuovi membri](./media/projectplace-tutorial/ic790233.png "Nuovi membri")
   
    a. Nella casella di testo **Nuovi membri** , digitare l’indirizzo di posta elettronica di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   
    b. Fare clic su **Send**.

   Un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account, viene inviato al titolare dell'account Azure Active Directory.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Projectplace per eseguire il provisioning degli account utente di Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Projectplace.

![Assegna utente][200] 

**Per assegnare Britta Simon a Projectplace, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Projectplace**.

    ![Configure Single Sign-On](./media/projectplace-tutorial/tutorial_projectplace_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Projectplace nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Projectplace.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/projectplace-tutorial/tutorial_general_01.png
[2]: ./media/projectplace-tutorial/tutorial_general_02.png
[3]: ./media/projectplace-tutorial/tutorial_general_03.png
[4]: ./media/projectplace-tutorial/tutorial_general_04.png

[100]: ./media/projectplace-tutorial/tutorial_general_100.png

[200]: ./media/projectplace-tutorial/tutorial_general_200.png
[201]: ./media/projectplace-tutorial/tutorial_general_201.png
[202]: ./media/projectplace-tutorial/tutorial_general_202.png
[203]: ./media/projectplace-tutorial/tutorial_general_203.png


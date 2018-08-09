---
title: 'Esercitazione: Integrazione di Azure Active Directory con IQNavigator VMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: f568f33de348289334c4b4c346e9525e28cce51c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445123"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Esercitazione: Integrazione di Azure Active Directory con IQNavigator VMS

Questa esercitazione descrive come integrare IQNavigator VMS con Azure Active Directory (Azure AD).

L'integrazione di IQNavigator VMS con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a IQNavigator VMS
- È possibile abilitare gli utenti per l'accesso automatico a IQNavigator VMS (Single Sign-On) con gli account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con IQNavigator VMS sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di IQNavigator VMS abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese: [offerta prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di IQNavigator VMS dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Aggiunta di IQNavigator VMS dalla raccolta
Per configurare l'integrazione di IQNavigator VMS in Azure AD, è necessario aggiungere IQNavigator VMS dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere IQNavigator VMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **IQNavigator VMS**.

    ![Creazione di un utente test di Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

1. Nel pannello dei risultati selezionare **IQNavigator VMS** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con IQNavigator VMS con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve individuare l'utente di IQNavigator VMS corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in IQNavigator VMS.

Per stabilire la relazione di collegamento, in IQNavigator VMS assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con IQNavigator VMS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di IQNavigator VMS](#creating-a-iqnavigator-vms-test-user)**: per avere una controparte di Britta Simon in IQNavigator VMS collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione IQNavigator VMS.

**Per configurare l'accesso Single Sign-On di Azure AD con IQNavigator VMS, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **IQNavigator VMS** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

1. Nella sezione **URL e dominio IQNavigator VMS** seguire questa procedura:

    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `iqn.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

1. Selezionare **Mostra URL impostazioni avanzate** ed eseguire l'operazione seguente:

    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    Nella casella di testo **Stato dell'inoltro** digitare un URL usando il modello seguente: `https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e lo stato dell'inoltro effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di IQNavigator VMS](https://www.beeline.com/iqn-product-support/).

1. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.
    
    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

1. L'applicazione IQNavigator prevede il valore di ID utente univoco nell'attestazione dell'identificatore del nome. Il cliente può eseguire il mapping del valore corretto per l'attestazione dell'identificatore del nome. In questo caso è stato eseguito il mapping di user.UserPrincipalName a scopo dimostrativo. Tuttavia, in base alle impostazioni dell'organizzazione è necessario eseguire il mapping del valore corretto.

    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di IQNavigator VMS** fare clic su **Configura IQNavigator VMS** per visualizzare la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

1. Per configurare l'accesso Single Sign-On in **IQNavigator VMS**, è necessario inviare l'**URL dei metadati di federazione dell'app**, l'**URL di disconnessione, l'ID entità SAML e l'URL del servizio Single Sign-On SAML** al [team di supporto di IQNavigator VMS](https://www.beeline.com/iqn-product-support/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.

    ![Creazione di un utente test di Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="creating-a-iqnavigator-vms-test-user"></a>Creazione di un utente test di IQNavigator VMS

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in IQNavigator VMS. Collaborare con il [team di supporto di IQNavigator VMS](https://www.beeline.com/iqn-product-support/) per aggiungere gli utenti nell'account IQNavigator VMS.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a IQNavigator VMS.

![Assegna utente][200]

**Per assegnare Britta Simon a IQNavigator VMS, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

1. Nell'elenco di applicazioni selezionare **IQNavigator VMS**.

    ![Configure Single Sign-On](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro IQNavigator VMS nel pannello di accesso, si accederà automaticamente all'applicazione IQNavigator VMS.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png


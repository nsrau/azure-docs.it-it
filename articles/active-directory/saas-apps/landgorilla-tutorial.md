---
title: 'Esercitazione: Integrazione di Azure Active Directory con Land Gorilla Client| Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Land Gorilla.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.openlocfilehash: 6712ecbc81abdf4756cb72f4949f51d2ffca4142
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435846"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Esercitazione: Integrazione di Azure Active Directory con Land Gorilla Client

Questa esercitazione descrive come integrare Land Gorilla Client con Azure Active Directory (Azure AD).

L'integrazione di Land Gorilla Client con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Land Gorilla Client
- È possibile abilitare gli utenti per l'accesso automatico a Land Gorilla Client (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Land Gorilla Client, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Land Gorilla Client abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Land Gorilla Client dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Aggiungere Land Gorilla Client dalla raccolta
Per configurare l'integrazione di Land Gorilla Client in Azure AD, è necessario aggiungere Land Gorilla Client dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Land Gorilla Client dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

1. Digitare **Land Gorilla Client** nella casella di ricerca.

    ![Creazione di un utente test di Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. Nel pannello dei risultati selezionare **Land Gorilla Client** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Land Gorilla Client in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Land Gorilla Client che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Land Gorilla Client.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente) in Land Gorilla Client.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Land Gorilla Client, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con un gruppo limitato.
1. **[Creazione di un utente test di Land Gorilla](#creating-a-land-gorilla-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Land Gorilla Client.

**Per configurare l'accesso Single Sign-On di Azure AD con Land Gorilla Client, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Land Gorilla Client** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. Nella sezione **URL e dominio Land Gorilla Client** seguire questa procedura:

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. Nella casella di testo **Identificatore** digitare il valore usando uno dei modelli seguenti: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. Nella casella di testo **URL di risposta** digitare un URL corrispondente al modello seguente:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di risposta effettivi. In questo caso, è consigliabile di usare il valore univoco della stringa nell'identificatore. Contattare il [team di Land Gorilla Client](https://www.landgorilla.com/support/) per ottenere questi valori. 

1. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Per completare la configurazione SSO per l'applicazione sul lato Land Gorilla, contattare il [team di supporto di Land Gorilla Client](https://www.landgorilla.com/support/) specificando il file **XML metadati** scaricato.


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea). 

### <a name="creating-a-land-gorilla-test-user"></a>Creare un utente test di Land Gorilla Client

Collaborare con il [team di supporto di Land Gorilla](https://www.landgorilla.com/support/) per aggiungere gli utenti alla piattaforma Land Gorilla.
    
### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso a Land Gorilla Client per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon a Land Gorilla Client, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Land Gorilla Client**.

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Land Gorilla Client nel Pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Land Gorilla Client.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png

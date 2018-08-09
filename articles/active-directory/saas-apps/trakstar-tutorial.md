---
title: 'Esercitazione: Integrazione di Azure Active Directory con Trakstar | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Trakstar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411cb8c3-95c6-4138-acf2-ffc7f663e89a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 24f67ffab016420ca2603c1ae313fc22421be9e4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420787"
---
# <a name="tutorial-azure-active-directory-integration-with-trakstar"></a>Esercitazione: Integrazione di Azure Active Directory con Trakstar

Questa esercitazione descrive come integrare Teamwork con Azure Active Directory (Azure AD).

L'integrazione di Trakstar con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Trakstar
- È possibile abilitare gli utenti per l'accesso automatico a Trakstar (Single Sign-On) con l'account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Trakstar, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Trakstar abilitata per l'accesso Single Sign-On
    - SSO è una funzionalità a pagamento di Trakstar. Per abilitare la funzionalità nell'organizzazione contattare il [team di supporto clienti Trakstar](mailto:support@trakstar.com).

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Trakstar dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-trakstar-from-the-gallery"></a>Aggiunta di Trakstar dalla raccolta
Per configurare l'integrazione di Trakstar in Azure AD, è necessario aggiungere Trakstar dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Trakstar dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Trakstar**.

    ![Creazione di un utente test di Azure AD](./media/trakstar-tutorial/tutorial_trakstar_search.png)

1. Nel pannello dei risultati selezionare **Trakstar** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/trakstar-tutorial/tutorial_trakstar_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Trakstar in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Trakstar che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Trakstar.

Per stabilire la relazione di collegamento, in Trakstar assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Trakstar, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Trakstar](#creating-a-trakstar-test-user)**: per avere una controparte di Britta Simon in Trakstar collegata alla relativa rappresentazione in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Trakstar.

**Per configurare l'accesso Single Sign-On di Azure AD con Trakstar, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Trakstar** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/trakstar-tutorial/tutorial_trakstar_samlbase.png)

1. Nella sezione **URL e dominio Trakstar** seguire questa procedura:

    ![Configure Single Sign-On](./media/trakstar-tutorial/tutorial_trakstar_url.png)

    a. Nella casella di testo **URL accesso** copiare il valore trovato in **ACS (Consumer) URL** (URL ACS - Consumer) in Trakstar (Settings > Authentication & SSO) (Impostazioni > Autenticazione e SSO) nel formato:`https://app.trakstar.com/auth/saml/callback?namespace=<YOUR_NAMESPACE>`

    b. Nella casella di testo **Identificatore** lasciare il valore predefinito: `https://app.trakstar.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Accedere a Trakstar come amministratore per ottenere questi valori.
    > Se non viene visualizza la scheda "Authentication & SSO" (Autenticazione & SSO) nelle impostazioni, è possibile che non si disponga della funzionalità
 
1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/trakstar-tutorial/tutorial_trakstar_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/trakstar-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Trakstar** fare clic su **Configura Trakstar** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configure Single Sign-On](./media/trakstar-tutorial/tutorial_trakstar_configure.png) 

1. Per configurare l'accesso Single Sign-On sul lato **Trakstar**, è necessario accedere come amministratore e inviare il file di **Certificato (Base64)** scaricato, **l'URL di disconnessione, l'ID entità SAML e l'URL del servizio Single Sign-On SAML**. 

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/trakstar-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/trakstar-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/trakstar-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/trakstar-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-trakstar-test-user"></a>Creazione di un utente test di Trakstar

Questa sezione descrive come creare un utente chiamato Britta Simon in Trakstar.


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Trakstar.

![Assegna utente][200] 

**Per assegnare Britta Simon a Trakstar, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Trakstar**.

    ![Configure Single Sign-On](./media/trakstar-tutorial/tutorial_trakstar_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  
Quando si fa clic sul riquadro Trakstar nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Trakstar. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/trakstar-tutorial/tutorial_general_01.png
[2]: ./media/trakstar-tutorial/tutorial_general_02.png
[3]: ./media/trakstar-tutorial/tutorial_general_03.png
[4]: ./media/trakstar-tutorial/tutorial_general_04.png

[100]: ./media/trakstar-tutorial/tutorial_general_100.png

[200]: ./media/trakstar-tutorial/tutorial_general_200.png
[201]: ./media/trakstar-tutorial/tutorial_general_201.png
[202]: ./media/trakstar-tutorial/tutorial_general_202.png
[203]: ./media/trakstar-tutorial/tutorial_general_203.png


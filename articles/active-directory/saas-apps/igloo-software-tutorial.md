---
title: 'Esercitazione: Integrazione di Azure Active Directory con Igloo Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Igloo Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: daa00db39ac55cd14b7720b534313407a98dde2c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817742"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Esercitazione: Integrazione di Azure Active Directory con Igloo Software

Questa esercitazione descrive come integrare Igloo Software con Azure Active Directory (Azure AD).

L'integrazione di Igloo Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Igloo Software
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a Igloo Software con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Igloo Software sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Igloo Software abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Igloo Software dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-igloo-software-from-the-gallery"></a>Aggiunta di Igloo Software dalla raccolta
Per configurare l'integrazione di Igloo Software in Azure AD è necessario aggiungere Igloo Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Igloo Software dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Igloo Software**.

    ![Creazione di un utente test di Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

1. Nel pannello dei risultati selezionare **Igloo Software** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Igloo Software mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Igloo Software corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Igloo Software.

Per stabilire la relazione di collegamento, in Igloo Software assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Igloo Software è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Igloo Software](#creating-an-igloo-software-test-user)**: per avere una controparte di Britta Simon in Igloo Software collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Igloo Software.

**Per configurare l'accesso Single Sign-On di Azure AD con Igloo Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Igloo Software** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

1. Nella sezione **URL e dominio Igloo Software** seguire questa procedura:

    ![Configure Single Sign-On](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company name>.igloocommmunities.com`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Igloo Software](https://www.igloosoftware.com/services/support). 

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/igloo-software-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di Igloo Software** fare clic su **Configura Igloo Software** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configure Single Sign-On](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

1. In un'altra finestra del Web browser accedere al sito aziendale di Igloo Software come amministratore.

1. Passare a **Control panel**.
   
     ![Pannello di controllo](./media/igloo-software-tutorial/ic799949.png "Pannello di controllo")

1. Nella scheda **Appartenenza** fare clic su **Sign In Settings**.
   
    ![Sign in Settings](./media/igloo-software-tutorial/ic783968.png "Sign in Settings")

1. Nella sezione relativa alla configurazione SAML fare clic su **Configure SAML Authentication**.
   
    ![Configurazione SAML](./media/igloo-software-tutorial/ic783969.png "Configurazione SAML")
   
1. Nella sezione **General Configuration** seguire questa procedura:
   
    ![General Configuration](./media/igloo-software-tutorial/ic783970.png "General Configuration")

    a. Nella casella di testo **Connection Name** digitare un nome personalizzato per la configurazione.
   
    b. Nella casella di testo **IdP Login URL** (URL di accesso IdP) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.
   
    c. Nella casella di testo **IdP Logout URL** (URL di disconnessione IdP) incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.
    
    d. Selezionare **Logout Response and Request HTTP Type** (Risposta di disconnessione e tipo di richiesta HTTP) con l'impostazione **POST**.
   
    e. Aprire nel Blocco note il certificato con codifica **Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Public Certificate** (Certificato pubblico).
    
1. In **Configurazione risposta e autenticazione**seguire questa procedura:
    
    ![Response and Authentication Configuration](./media/igloo-software-tutorial/IC783971.png "Response and Authentication Configuration")
  
      a. In **Provider di identità** selezionare **Microsoft ADFS**.
      
      b. In **Tipo di identificatore** selezionare **Indirizzo e-mail**. 

      c. Nella casella di testo **Attributo posta elettronica** digitare **emailaddress**.

      d. Nella casella di testo **Attributo nome** digitare **nomeproprio**.

      e. Nella casella di testo **Attributo cognome** digitare **cognome**.

1. Per completare la configurazione seguire questa procedura:
    
    ![User creation on Sign in](./media/igloo-software-tutorial/IC783972.png "User creation on Sign in") 

     a. In **User creation on Sign in** (Creazione utente all'accesso) selezionare **Create a new user in your site when they sign in** (Creare un nuovo utente all'accesso).

     b. In **Impostazioni di accesso** selezionare **Usa pulsante SAML nella schermata di accesso**.

     c. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/igloo-software-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/igloo-software-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/igloo-software-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-an-igloo-software-test-user"></a>Creazione di un utente test di Igloo Software

Non è richiesto alcun intervento dell'utente per configurare il provisioning degli utenti in Igloo Software.  

Quando un utente assegnato prova ad accedere a Igloo Software usando il pannello di accesso, Igloo Software verifica se l'utente esiste.  Se l'account utente non è presente, Igloo Software lo crea automaticamente.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Igloo Software.

![Assegna utente][200] 

**Per assegnare Britta Simon a Igloo Software, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Igloo Software**.

    ![Configure Single Sign-On](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Igloo Software nel pannello di accesso si accede automaticamente all'applicazione Igloo Software.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png


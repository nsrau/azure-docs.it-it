---
title: 'Esercitazione: Esercitazione: Integrazione di Azure Active Directory con Tableau Online | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 2a1ae1aa6c714007f594d0e37458081fb98d4317
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819938"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Esercitazione: Integrazione di Azure Active Directory con Tableau Online

Questa esercitazione descrive come integrare Tableau Online con Azure Active Directory (Azure AD).

L'integrazione di Tableau Online con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Tableau Online
- È possibile abilitare gli utenti per l'accesso automatico a Tableau Online (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Tableau Online, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Tableau Online abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Tableau Online dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-tableau-online-from-the-gallery"></a>Aggiunta di Tableau Online dalla raccolta
Per configurare l'integrazione di Tableau Online in Azure AD, è necessario aggiungere Tableau Online dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Tableau Online dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Tableau Online**.

    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_search.png)

1. Nel pannello dei risultati selezionare **Tableau Online** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Tableau Online usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Tableau Online che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Tableau Online.

Per stabilire la relazione di collegamento, in Tableau Online assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Tableau Online, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Tableau Online](#creating-a-tableau-online-test-user)** : per avere una controparte di Britta Simon in Tableau Online collegata alla relativa rappresentazione in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Tableau Online.

**Per configurare l'accesso Single Sign-On di Azure AD con Tableau Online, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Tableau Online** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

1. Nella sezione **URL e dominio Tableau Online** eseguire i seguenti passaggi:

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. Nella casella di testo **URL accesso** digitare l'URL: `https://sso.online.tableau.com`

    b. Nella casella di testo **Identificatore** digitare l'URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid> `

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_general_400.png)

1. In una finestra del browser diversa accedere all'applicazione Tableau Online. Fare clic su **Settings** (Impostazioni) e quindi su **Authentication** (Autenticazione).
   
    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)
    
1. Per abilitare SAML, nella sezione **Authentication Types** (Tipi di autenticazione). Selezionare la casella di controllo **Single sign-on with SAML** (Accesso Single Sign-On con SAML).
   
    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

1. Scorrere verso il basso fino alla sezione **Import metadata file into Tableau Online** (Importa file di metadati in Tableau Online).  Fare clic su Browse (Sfoglia) e importare il file di metadati scaricato da Azure AD. Fare quindi clic su **Apply**(Applica).
   
   ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

1. Nella sezione **Match assertions** (Corrispondenza asserzioni) inserire il nome di asserzione del provider di identità corrispondente per l'**indirizzo e-mail**, il **nome** e il **cognome**. Per ottenere queste informazioni da Azure AD: 
  
    a. Nel portale di Azure, aprire la pagina di integrazione dell'applicazione **Tableau Online**.
    
    b. Nella sezione degli attributi, selezionare la casella di controllo **"Visualizza e modifica tutti gli altri attributi utente"**. 
    
   ![Configure Single Sign-On](./media/tableauonline-tutorial/attributesection.png)
      
    c. Copiare il valore dello spazio dei nomi per i seguenti attributi: givenname, e-mail e cognome utilizzando la procedura seguente:

   ![Single Sign-On di Microsoft Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Fare clic sul valore **user.givenname** 
    
    e. Copiare il valore dalla casella di testo **spazio dei nomi**.

   ![Configure Single Sign-On](./media/tableauonline-tutorial/attributesection2.png)

    f. Per copiare i valori dello spazio dei nomi per l'e-mail e il cognome, eseguire i passaggi precedenti.

    g. Tornare all'applicazione Tableau Online e configurare la sezione **Tableau Online Attributes** (Attributi di Tableau Online) come indicato di seguito:
     * Indirizzo di posta elettronica: **mail** o **userprincipalname**
     * Nome: **givenname**
     * Cognome: **surname**
   
   ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-tableau-online-test-user"></a>Creazione di un utente test di Tableau Online

In questa sezione viene creato un utente chiamato Britta Simon in Tableau Online.

1. In **Tableau Online** fare clic su **Settings** (Impostazioni) e quindi sulla sezione **Authentication** (Autenticazione). Scorrere verso il basso fino alla sezione **Select Users** (Selezione utenti). Fare clic su **Add Users** (Aggiungi utenti) e quindi su **Enter Email Addresses** (Immettere indirizzi di posta elettronica).
   
    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)
1. Selezionare **Add users for single sign-on (SSO) authentication**(Aggiungi utenti per l'autenticazione Single Sign-On). Nella casella di testo **Enter Email Addresses** (Immettere indirizzi di posta elettronica) aggiungere britta.simon@contoso.com
   
    ![Creazione di un utente test di Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)
1. Fare clic su **Create**(Crea).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Tableau Online.

![Assegna utente][200] 

**Per assegnare Britta Simon a Tableau Online, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Tableau Online**.

    ![Configure Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Tableau Online nel pannello di accesso, viene effettuato automaticamente l'accesso all'applicazione Tableau Online.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/tableauonline-tutorial/tutorial_general_203.png

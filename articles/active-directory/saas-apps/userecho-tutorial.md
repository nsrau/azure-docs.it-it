---
title: 'Esercitazione: Integrazione di Azure Active Directory con UserEcho | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ecdd37db662c6861e35f80bfbf4ac8ff7e0d08c9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443271"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Esercitazione: Integrazione di Azure Active Directory con UserEcho

Questa esercitazione descrive come integrare UserEcho con Azure Active Directory (Azure AD).

L'integrazione di UserEcho con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a UserEcho.
- È possibile abilitare gli utenti per l'accesso automatico a UserEcho (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con UserEcho, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di UserEcho abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese: [offerta prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di UserEcho dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-userecho-from-the-gallery"></a>Aggiunta di UserEcho dalla raccolta
Per configurare l'integrazione di UserEcho in Azure AD, è necessario aggiungere UserEcho dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere UserEcho dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **UserEcho**.

    ![Creazione di un utente test di Azure AD](./media/userecho-tutorial/tutorial_userecho_search.png)

1. Nel pannello dei risultati selezionare **UserEcho** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con UserEcho con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di UserEcho che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in UserEcho.

Per stabilire la relazione di collegamento, in UserEcho assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con UserEcho, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test per UserEcho](#creating-a-userecho-test-user)**: per avere una controparte di Britta Simon in UserEcho collegata alla relativa rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione UserEcho.

**Per configurare l'accesso Single Sign-On di Azure AD con UserEcho, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **UserEcho** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_samlbase.png)

1. Nella sezione **URL e dominio UserEcho** seguire questa procedura:

    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.userecho.com/`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di UserEcho](https://feedback.userecho.com/). 

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di UserEcho** fare clic su **Configura UserEcho** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_configure.png) 

1. In un'altra finestra del browser accedere al sito aziendale di UserEcho come amministratore.

1. Sulla barra degli strumenti in alto, fare clic sul nome utente per espandere il menu e quindi fare clic su **Setup**.
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_06.png) 

1. Fare clic su **Integrations**.
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_07.png) 

1. Fare clic su **Sito web** e quindi su **Single sign-on (SAML2)**.
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_08.png) 

1. Nella pagina **Single sign-on (SAML)** seguire questa procedura:
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Per **SAML-enabled** (Abilitato per SAML) selezionare **Sì**.
    
    b. Incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **SAML SSO URL**.
    
    c. Incollare l'**URL di disconnessione** copiato dal portale di Azure nella casella di testo **Remote Logout URL** (URL di disconnessione remota).
    
    d. Aprire il certificato scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **X.509 Certificate** .
    
    e. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/userecho-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/userecho-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/userecho-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/userecho-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-userecho-test-user"></a>Creazione di un utente test per UserEcho

Questa sezione descrive come creare un utente chiamato Britta Simon in UserEcho.

**Per creare un utente denominato Britta Simon in UserEcho, seguire questa procedura:**

1. Accedere al sito aziendale di UserEcho come amministratore.

1. Sulla barra degli strumenti in alto, fare clic sul nome utente per espandere il menu e quindi fare clic su **Setup**.
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_06.png)

1. Fare clic su **Users** (Utenti) per espandere la sezione **Users** (Utenti).
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_10.png)

1. Fare clic su **Users**.
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_11.png)

1. Fare clic su **Invite a new user**.
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_12.png)

1. Nella finestra di dialogo **Invita un nuovo utente** seguire questa procedura:
   
    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Nella casella di testo **Name** (Nome) digitare il nome dell'utente, ad esempio Britta Simon.
    
    b.  Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.
    
    c. Fare clic su **Invita**.

A Britta viene inviato un invito che le permette di iniziare a usare UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a UserEcho.

![Assegna utente][200] 

**Per assegnare Britta Simon a UserEcho, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni, selezionare **UserEcho**.

    ![Configure Single Sign-On](./media/userecho-tutorial/tutorial_userecho_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro UserEcho nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione UserEcho.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/userecho-tutorial/tutorial_general_01.png
[2]: ./media/userecho-tutorial/tutorial_general_02.png
[3]: ./media/userecho-tutorial/tutorial_general_03.png
[4]: ./media/userecho-tutorial/tutorial_general_04.png

[100]: ./media/userecho-tutorial/tutorial_general_100.png

[200]: ./media/userecho-tutorial/tutorial_general_200.png
[201]: ./media/userecho-tutorial/tutorial_general_201.png
[202]: ./media/userecho-tutorial/tutorial_general_202.png
[203]: ./media/userecho-tutorial/tutorial_general_203.png


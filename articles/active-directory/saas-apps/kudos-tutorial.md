---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kudos | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Kudos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b1fa6284979a8c0bd0698f85b119117dd27a05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60261840"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Esercitazione: Integrazione di Azure Active Directory con Kudos

Questa esercitazione descrive come integrare Kudos con Azure Active Directory (Azure AD).

L'integrazione di Kudos con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Kudos
- È possibile abilitare gli utenti per l'accesso automatico a Kudos (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Kudos, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Kudos abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Kudos dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-kudos-from-the-gallery"></a>Aggiunta di Kudos dalla raccolta
Per configurare l'integrazione di Kudos in Azure AD, è necessario aggiungere Kudos dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Kudos dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Kudos**.

    ![Creazione di un utente test di Azure AD](./media/kudos-tutorial/tutorial_kudos_search.png)

1. Nel pannello dei risultati selezionare **Kudos** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/kudos-tutorial/tutorial_kudos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Kudos usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Kudos che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Kudos.

Per stabilire la relazione di collegamento, in Kudos assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Kudos, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente test di Kudos](#creating-a-kudos-test-user)**: per avere una controparte di Britta Simon in Kudos collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Kudos.

**Per configurare l'accesso Single Sign-On di Azure AD con Kudos, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Kudos** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/kudos-tutorial/tutorial_kudos_samlbase.png)

1. Nella sezione **URL e dominio Kudos** seguire questa procedura:

    ![Configure Single Sign-On](./media/kudos-tutorial/tutorial_kudos_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company>.kudosnow.com`
    
    > [!NOTE] 
    > Poiché non è reale, aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Kudos](http://success.kudosnow.com/home). 
 
1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/kudos-tutorial/tutorial_kudos_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/kudos-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Kudos** fare clic su **Configura Kudos** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML e l'URL di disconnessione** dalla sezione **Riferimento rapido**.

    ![Configure Single Sign-On](./media/kudos-tutorial/tutorial_kudos_configure.png) 

1. In un'altra finestra del Web browser accedere al sito aziendale di Kudos come amministratore.

1. Nel menu in alto fare clic su **Impostazioni**.
   
    ![Impostazioni](./media/kudos-tutorial/ic787806.png "Impostazioni")

1. Fare clic su **Integrations (Integrazioni) \> SSO**.

1. Nella sezione **SSO** eseguire la procedura seguente:
   
    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")
   
    a. Nella casella di testo **Sign on URL** (URL di accesso) incollare il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On) copiato dal portale di Azure. 

    b. Aprire il certificato con codifica base 64 nel blocco note, copiarne il contenuto negli appunti e incollarlo nella casella di testo **Certificato X.509**
   
    c. In **Logout To URL** (URL di disconnessione) incollare il valore di **Sign-Out URL** (URL di disconnessione) copiato dal portale di Azure.
   
    d. Nella casella di testo **URL di Kudos** digitare il nome della società.
   
    e. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/kudos-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/kudos-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/kudos-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/kudos-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-kudos-test-user"></a>Creazione di un utente test di Kudos

Per consentire agli utenti di Azure AD di accedere a Kudos, è necessario eseguirne il provisioning in Kudos. 

Nel caso di Kudos, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Kudos** come amministratore.

1. Nel menu in alto fare clic su **Impostazioni**.
   
   ![Impostazioni](./media/kudos-tutorial/ic787806.png "Impostazioni")

1. Fare clic su **User Admin**.

1. Fare clic sulla scheda **Users** (Utenti) e quindi su **Add a User** (Aggiungi un utente).
   
   ![Amministratore utenti](./media/kudos-tutorial/ic787809.png "Amministratore utenti")

1. Nella sezione **Aggiungi un utente** eseguire la procedura seguente:
   
    ![Aggiungere un utente](./media/kudos-tutorial/ic787810.png "Aggiungere un utente")
   
    a. Nelle caselle **First Name**, **Last Name**, **Email** e così via immettere il nome, cognome, indirizzo di posta elettronica e altri dettagli di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   
    b. Fare clic su **Create User**.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Kudos o le API fornite da Kudos per eseguire il provisioning degli account utente di AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Kudos.

![Assegna utente][200] 

**Per assegnare Britta Simon a Kudos, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Kudos**.

    ![Configure Single Sign-On](./media/kudos-tutorial/tutorial_kudos_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Kudos nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Kudos. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kudos-tutorial/tutorial_general_01.png
[2]: ./media/kudos-tutorial/tutorial_general_02.png
[3]: ./media/kudos-tutorial/tutorial_general_03.png
[4]: ./media/kudos-tutorial/tutorial_general_04.png

[100]: ./media/kudos-tutorial/tutorial_general_100.png

[200]: ./media/kudos-tutorial/tutorial_general_200.png
[201]: ./media/kudos-tutorial/tutorial_general_201.png
[202]: ./media/kudos-tutorial/tutorial_general_202.png
[203]: ./media/kudos-tutorial/tutorial_general_203.png


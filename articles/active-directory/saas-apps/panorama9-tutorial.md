---
title: 'Esercitazione: Integrazione di Azure Active Directory con Panorama9 | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 94ba73f932cd0eaae463bd08712cfe617be04768
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184394"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Esercitazione: Integrazione di Azure Active Directory con Panorama9

Questa esercitazione descrive come integrare Panorama9 con Azure Active Directory (Azure AD).

L'integrazione di Panorama9 con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Panorama9
- È possibile abilitare gli utenti per l'accesso automatico a Panorama9 (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Panorama9, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Panorama9 abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Panorama9 dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-panorama9-from-the-gallery"></a>Aggiunta di Panorama9 dalla raccolta
Per configurare l'integrazione di Panorama9 in Azure AD, è necessario aggiungere Panorama9 dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Panorama9 dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Panorama9**.

    ![Creazione di un utente test di Azure AD](./media/panorama9-tutorial/tutorial_panorama9_search.png)

1. Nel pannello dei risultati selezionare **Panorama9** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Panorama9 con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Panorama9 che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Panorama9.

Per stabilire la relazione di collegamento, in Panorama9 assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Panorama9, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente di test di Panorama9](#creating-a-panorama9-test-user)**: per avere una controparte di Britta Simon in Panorama9 collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Panorama9.

**Per configurare Single Sign-On di Azure AD con Panorama9, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Panorama9** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

1. Nella sezione **URL e dominio Panorama9** seguire questa procedura:

    ![Configure Single Sign-On](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL come: `https://dashboard.panorama9.com/saml/access/3262`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Panorama9](https://support.panorama9.com). 
 
1. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** del certificato.

    ![Configure Single Sign-On](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/panorama9-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Panorama9** fare clic su **Configura Panorama9** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configure Single Sign-On](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

1. In un'altra finestra del Web browser accedere al sito aziendale di Panorama9 come amministratore.

1. Nella barra degli strumenti in alto, fare clic su **Gestisci** quindi fare clic su **Estensioni**.
   
   ![Estensioni](./media/panorama9-tutorial/ic790023.png "Estensioni")
1. Nella finestra di dialogo **Estensioni**, fare clic su **Single Sign-On**.
   
   ![Single Sign-On](./media/panorama9-tutorial/ic790024.png "Single Sign-On")
1. Nella sezione **Impostazioni** , eseguire la procedura seguente:
   
   ![Impostazioni](./media/panorama9-tutorial/ic790025.png "Impostazioni")
   
    a. Nella casella di testo **Identity provider URL** (URL provider di identità) incollare il valore **URL servizio Single Sign-On** copiato dal portale di Azure.
   
    b. Nella casella di testo **Certificate fingerprint** (Impronta digitale certificato) incollare il valore **Identificazione personale** del certificato copiato dal portale di Azure.    
         
1. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/panorama9-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/panorama9-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/panorama9-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-panorama9-test-user"></a>Creazione di un utente di test di Panorama9

Per consentire agli utenti di Azure AD di accedere a Panorama9, è necessario eseguirne il provisioning in Panorama9.  

Nel caso di Panorama9, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **Panorama9** come amministratore.

1. Nel menu in alto fare clic su **Gestisci**, quindi fare clic su **Utenti**.
   
  ![Utenti](./media/panorama9-tutorial/ic790027.png "Utenti")

1. Nella sezione utenti fare clic su **+** per aggiungere il nuovo utente.

 ![Utenti](./media/panorama9-tutorial/ic790028.png "Utenti")

1. Passare alla sezione Dati utente e digitare l'indirizzo di posta elettronica di un utente valido di Azure Active Directory di cui si vuole eseguire il provisioning nella casella di testo **Posta elettronica**.

1. Tornare alla sezione Utenti e fare clic su **Salva**.
   
> [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione, Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Panorama9.

![Assegna utente][200] 

**Per assegnare Britta Simon a Panorama9, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Panorama9**.

    ![Configure Single Sign-On](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Panorama9 nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Panorama9.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png


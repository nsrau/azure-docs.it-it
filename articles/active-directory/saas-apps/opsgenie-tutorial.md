---
title: 'Esercitazione: Integrazione di Azure Active Directory con OpsGenie | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 52d976761ad5962f09f40565cfa04ae78386d1ca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192389"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Esercitazione: Integrazione di Azure Active Directory con OpsGenie

Questa esercitazione descrive come integrare OpsGenie con Azure Active Directory (Azure AD).

L'integrazione di OpsGenie con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OpsGenie
- È possibile abilitare gli utenti per l'accesso automatico a OpsGenie (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OpsGenie, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di OpsGenie abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di OpsGenie dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-opsgenie-from-the-gallery"></a>Aggiunta di OpsGenie dalla raccolta
Per configurare l'integrazione di OpsGenie in Azure AD, è necessario aggiungere OpsGenie dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OpsGenie dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **OpsGenie**.

    ![Creazione di un utente test di Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_search.png)

1. Nel pannello dei risultati selezionare **OpsGenie** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OpsGenie in base a un utente di prova di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di OpsGenie che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpsGenie.

Per stabilire la relazione di collegamento, in OpsGenie assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con OpsGenie, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente di test di OpsGenie](#creating-a-opsgenie-test-user)** : per avere una controparte di Britta Simon in OpsGenie collegata alla relativa rappresentazione in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione OpsGenie.

**Per configurare Single Sign-On di Azure AD con OpsGenie, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OpsGenie** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

1. Nella sezione **URL e dominio OpsGenie** seguire questa procedura:

    ![Configure Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_url.png)

    Nella casella di testo **URL accesso** digitare l'URL: `https://app.opsgenie.com/auth/login`

1. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/opsgenie-tutorial/tutorial_opsgenie_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/opsgenie-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di OpsGenie** fare clic su **Configura OpsGenie** per aprire la finestra **Configura accesso**. Copiare l'**URL del servizio Single Sign-On SAML** dalla sezione di riferimento rapido.

    ![Configure Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_configure.png)

1. Aprire un'altra istanza del browser e quindi accedere a OpsGenie come amministratore.

1. Fare clic su **Settings** e quindi selezionare la scheda **Single Sign On**.
   
    ![Accesso Single Sign-On di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

1. Per abilitare l'accesso Single Sign-On, selezionare **Enabled**.
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

1. Nella sezione **Provider** fare clic sulla scheda **Azure Active Directory**.
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

1. Nella pagina Azure Active Directory, seguire questa procedura:
   
    ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Nella casella di testo **SAML 2.0 Endpoint** incollare il valore dell'**URL del servizio Single Sign-On** copiato dal portale di Azure.
    
    b. Nella casella di testo **Metadata URL** (URL dei metadati) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.
    
    c. Fare clic su **Salva modifiche**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/opsgenie-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/opsgenie-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/opsgenie-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/opsgenie-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-a-opsgenie-test-user"></a>Creazione di un utente di test di OpsGenie

Questa sezione descrive come creare un utente chiamato Britta Simon in OpsGenie. 

1. In una finestra del Web browser accedere al tenant di OpsGenie come amministratore.

1. Passare all'elenco di utenti facendo clic su **User** nel pannello a sinistra.
   
   ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

1. Fare clic su **Add User**.

1. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:
   
   ![Impostazioni di OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. Nella casella di testo **Email** , digitare l'indirizzo di posta elettronica di Britta in Azure Active Directory.
   
   b. Nella casella di testo **Nome completo** digitare **Britta Simon**.
   
   c. Fare clic su **Save**. 

>[!NOTE]
>Britta riceve un messaggio di posta elettronica con istruzioni per la configurazione del profilo.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OpsGenie.

![Assegna utente][200] 

**Per assegnare Britta Simon a OpsGenie, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **OpsGenie**.

    ![Configure Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro OpsGenie nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OpsGenie.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/opsgenie-tutorial/tutorial_general_203.png


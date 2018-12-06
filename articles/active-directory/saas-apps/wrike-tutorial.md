---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wrike | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Wrike.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 894b7520-5136-4973-a1ba-942a9f7f0a03
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: jeedes
ms.openlocfilehash: d2a8db071152e95b3c70509a1c10f7aa73f02c21
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51978066"
---
# <a name="tutorial-azure-active-directory-integration-with-wrike"></a>Esercitazione: Integrazione di Azure Active Directory con Wrike

Questa esercitazione descrive come integrare Wrike con Azure Active Directory (Azure AD).

L'integrazione di Wrike con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Wrike.
- È possibile abilitare gli utenti per l'accesso automatico a Wrike (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Wrike, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Wrike abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Wrike dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-wrike-from-the-gallery"></a>Aggiunta di Wrike dalla raccolta

Per configurare l'integrazione di Wrike in Azure AD, è necessario aggiungere Wrike dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Wrike dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Wrike**, selezionare **Wrike** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Wrike nell'elenco risultati](./media/wrike-tutorial/tutorial_wrike_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Wrike usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Wrike corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Wrike.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Wrike, è necessario completare le procedure di base seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Wrike](#creating-a-wrike-test-user)**: per avere una controparte di Britta Simon in Wrike collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Wrike.

**Per configurare l'accesso Single Sign-On di Azure AD con Wrike, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Wrike** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Wrike](./media/wrike-tutorial/tutorial_wrike_url.png)
    
    a. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    b. Nella casella di testo **URL di accesso** digitare l'URL: `https://www.wrike.com/login/`

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Wrike](./media/wrike-tutorial/tutorial_wrike_url1.png)

5. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Metadati federativi XML** e salvare il file di metadati nel computer in uso.

    ![Collegamento di download del certificato](./media/wrike-tutorial/tutorial_wrike_certificate.png) 

6. Per configurare l'accesso Single Sign-On in **Wrike**, è necessario inviare il file **XML dei metadati della federazione** scaricato al [team di supporto di Wrike](mailto:support@team.wrike.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-a-wrike-test-user"></a>Creazione di un utente di test di Wrike

Questa sezione descrive come creare un utente di test denominato Britta Simon in Wrike. Wrike supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a Wrike viene creato un nuovo utente, se questo non esiste già.
>[!Note]
>Se è necessario creare un utente manualmente, contattare il  [team di supporto di Wrike](mailto:support@team.wrike.com).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Wrike.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Wrike**.

    ![Configure Single Sign-On](./media/wrike-tutorial/tutorial_wrike_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Wrike nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Wrike.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

---
title: 'Esercitazione: Integrazione di Azure Active Directory con BorrowBox | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BorrowBox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: jeedes
ms.openlocfilehash: 16252f690bfd34e596b2b8db26ab3a2ede0dfdee
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2018
ms.locfileid: "48833460"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>Esercitazione: Integrazione di Azure Active Directory con BorrowBox

Questa esercitazione illustra come integrare BorrowBox con Azure Active Directory (Azure AD).

L'integrazione di BorrowBox con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a BorrowBox.
- È possibile abilitare gli utenti per l'accesso automatico a BorrowBox (Single Sign-On) con il proprio account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con BorrowBox, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata per l'accesso Single Sign-On a BorrowBox

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di BorrowBox dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-borrowbox-from-the-gallery"></a>Aggiunta di BorrowBox dalla raccolta
Per configurare l'integrazione di BorrowBox in Azure AD, è necessario aggiungere BorrowBox dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere BorrowBox dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![image](./media/borrowbox-tutorial/selectazuread.png)

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![image](./media/borrowbox-tutorial/a_select_app.png)
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![image](./media/borrowbox-tutorial/a_new_app.png)

4. Nella casella di ricerca digitare **BorrowBox**, selezionare **BorrowBox** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![image](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BorrowBox in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di BorrowBox che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BorrowBox.

Per configurare e testare l'accesso Single Sign-On di Azure AD con BorrowBox, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di BorrowBox](#create-a-borrowbox-test-user)**: per avere una controparte di Britta Simon in BorrowBox collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione BorrowBox.

**Per configurare l'accesso Single Sign-On di Azure AD con BorrowBox, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **BorrowBox** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![image](./media/borrowbox-tutorial/B1_B2_Select_SSO.png)

2. Fare clic su **Modifica modalità Single Sign-On** nella parte superiore della schermata per selezionare la modalità **SAML**.

      ![image](./media/borrowbox-tutorial/b1_b2_saml_ssso.png)

3. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML** per abilitare il Single Sign-On.

    ![image](./media/borrowbox-tutorial/b1_b2_saml_sso.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![image](./media/borrowbox-tutorial/b1-domains_and_urlsedit.png)

5. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

    a. Fare clic su **Impostare URL aggiuntivi**.

    b. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>`

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di BorrowBox](mailto:borrowbox@bolinda.com). 

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il certificato appropriato in base ai propri requisiti e salvarlo nel computer in uso.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

7. Per configurare l'accesso Single Sign-On sul lato **BorrowBox**, è necessario inviare il certificato/i metadati scaricati dal portale di Azure al [team di supporto di BorrowBox](mailto:borrowbox@bolinda.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel portale di Azure, nel riquadro sinistro, selezionare **Azure Active Directory**, selezionare **Utenti** e quindi selezionare **Tutti gli utenti**.

    ![image](./media/borrowbox-tutorial/d_users_and_groups.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![image](./media/borrowbox-tutorial/d_adduser.png)

3. In Proprietà utente seguire questa procedura.

    ![image](./media/borrowbox-tutorial/d_userproperties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
 
### <a name="create-a-borrowbox-test-user"></a>Creare un utente di test BorrowBox

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in BorrowBox. BorrowBox supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a BorrowBox.
>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di BorrowBox](mailto:borrowbox@bolinda.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BorrowBox.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![image](./media/borrowbox-tutorial/d_all_applications.png)

2. Nell'elenco delle applicazioni selezionare **BorrowBox**.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![image](./media/borrowbox-tutorial/d_leftpaneusers.png)

4. Fare clic sul pulsante **Aggiungi** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![image](./media/borrowbox-tutorial/d_assign_user.png)

4. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

5. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro BorrowBox nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BorrowBox.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

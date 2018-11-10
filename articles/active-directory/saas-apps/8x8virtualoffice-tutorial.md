---
title: 'Esercitazione: Integrazione di Azure Active Directory con 8x8 Virtual Office | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741811"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Esercitazione: Integrazione di Azure Active Directory con 8x8 Virtual Office

Questa esercitazione descrive come integrare 8x8 Virtual Office con Azure Active Directory (Azure AD).

L'integrazione di 8x8 Virtual Office con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a 8x8 Virtual Office.
- È possibile abilitare gli utenti per l'accesso automatico a 8x8 Virtual Office (Single Sign-On) con il proprio account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con 8x8 Virtual Office, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di 8x8 Virtual Office abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di 8x8 Virtual Office dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Aggiunta di 8x8 Virtual Office dalla raccolta

Per configurare l'integrazione di 8x8 Virtual Office in Azure AD, è necessario aggiungere 8x8 Virtual Office dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere 8x8 Virtual Office dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **8x8 Virtual Office**, selezionare **8x8 Virtual Office** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![8x8 Virtual Office nell'elenco dei risultati](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con 8x8 Virtual Office mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di 8x8 Virtual Office corrisponde a un determinato utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in 8x8 Virtual Office.

Per configurare e testare l'accesso Single Sign-On di Azure AD con 8x8 Virtual Office è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)**: per avere una controparte di Britta Simon in 8x8 Virtual Office collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione 8x8 Virtual Office.

**Per configurare l'accesso Single Sign-On di Azure AD con 8x8 Virtual Office, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **8x8 Virtual Office** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di 8x8 Virtual Office](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://sso.8x8.com/saml2`

    b. Nella casella di testo **URL di risposta** digitare un URL: `https://sso.8x8.com/saml2`

5. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (base)** e salvare il file di certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. Nella sezione **Configura 8x8 Virtual Office** copiare l'URL appropriato in base alle proprie esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di 8x8 Virtual Office](common/configuresection.png)

7. Accedere al tenant di 8x8 Virtual Office come amministratore.

8. Selezionare **Virtual Office Account Mgr** (Account manager di Virtual Office) nel pannello dell'applicazione.

    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Selezionare l'account **Business** (Aziendale) da gestire e fare clic sul pulsante **Sign In** (Accedi).

    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Fare clic sulla scheda **ACCOUNTS** (ACCOUNT) nell'elenco di menu.

    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Fare clic su **Single Sign On** nell'elenco di account.
  
    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Selezionare **Single Sign-On** sotto Metodi di autenticazione e fare clic su **SAML**.

    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. Nella sezione **SAML Single Sign-On** seguire questa procedura:

    ![Configurazione sul lato app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Nella casella di testo **URL accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Sign Out URL** (URL disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    c. Nella casella di testo **URL autorità di certificazione** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Fare clic sul pulsante **Sfoglia** per caricare il certificato scaricato dal portale di Azure.

    e. Fare clic sul pulsante **Salva** .

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
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>Creazione di un utente test di 8x8 Virtual Office

Questa sezione descrive come creare un utente chiamato Britta Simon in 8x8 Virtual Office. 8x8 Virtual Office supporta il provisioning just-in-time, che è abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a 8x8 Virtual Office viene creato un nuovo utente, se questo non esiste già.

> [!NOTE]
> Per creare un utente manualmente è necessario contattare il [team di supporto di 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a 8x8 Virtual Office.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni, selezionare **8x8 Virtual Office**.

    ![Configure Single Sign-On](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro 8x8 Virtual Office nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione 8x8 Virtual Office.
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

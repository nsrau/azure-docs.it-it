---
title: 'Esercitazione: integrazione di Azure Active Directory con Way We Do | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc6d8e2cf7ac4786f30484325406a1fe696dff3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165128"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Esercitazione: integrazione di Azure Active Directory con Way We Do

Questa esercitazione descrive come integrare Way We Do con Azure Active Directory (Azure AD).

L'integrazione di Way We Do con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Way We Do.
- È possibile abilitare gli utenti per l'accesso automatico a Way We Do (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Way We Do, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Way We Do abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Way We Do dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-way-we-do-from-the-gallery"></a>Aggiunta di Way We Do dalla raccolta
Per configurare l'integrazione di Way We Do in Azure AD, è necessario aggiungere Way We Do dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Way We Do dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Way We Do**, selezionare **Way We Do** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Way We Do nell'elenco risultati](./media/waywedo-tutorial/tutorial_waywedo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Way We Do usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Way We Do corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Way We Do.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Way We Do, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Way We Do](#create-a-way-we-do-test-user)**: per avere una controparte di Britta Simon in Way We Do collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Way We Do.

**Per configurare l'accesso Single Sign-On di Azure AD con Way We Do, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Way We Do** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/waywedo-tutorial/tutorial_waywedo_samlbase.png)

3. Nella sezione **URL e dominio Way We Do** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Way We Do](./media/waywedo-tutorial/tutorial_waywedo_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Way We Do](mailto:support@waywedo.com). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificate (Raw)** (Certificato (base)) e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/waywedo-tutorial/tutorial_waywedo_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/waywedo-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Way We Do** fare clic su **Configura Way We Do** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Way We Do](./media/waywedo-tutorial/tutorial_waywedo_configure.png) 

7. In un'altra finestra del Web browser accedere a Way We Do come amministratore della sicurezza.

8. Fare clic sull'**icona della persona** nell'angolo in alto a destra di qualsiasi pagina in Way We Do e quindi fare clic su **Account** nel menu a discesa.

    ![Account Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

9. Fare clic sull'**icona del menu** per aprire il menu di navigazione e fare clic su **Single Sign On**.

    ![Accesso Single Sign-On Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

10. Nella pagina **Single sign-on setup** (Configurazione Single Sign-On) seguire questa procedura:

    ![Salvataggio Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Impostare **Turn on single sign-on** (Attiva Single Sign-On) su **Yes** (Sì) per abilitare l'accesso Single Sign-On.

    b. Nella casella di testo **Single sign-on name** (Nome Single Sign-On) immettere il proprio nome.

    c. Nella casella di testo **Entity ID** (ID entità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    d. Nella casella di testo **SAML SSO URL** (URL SSO SAML) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    e. Caricare il certificato facendo clic sul pulsante **Select** (Seleziona) accanto a **Certificate** (Certificato).

    f. **Impostazioni facoltative** -
    
    * Enable Passwords (Abilita le password): quando questa opzione è disabilitata, la normale password per Way We Do consente solo l'accesso Single Sign-on degli utenti.

    * Enable Auto-provisioning (Abilita il provisioning automatico): quando questa opzione è abilitata, l'indirizzo di posta elettronica usato per l'accesso viene confrontato automaticamente con l'elenco degli utenti in Way We Do. Se l'indirizzo di posta elettronica non corrisponde a un utente attivo in Way We Do, aggiunge automaticamente un nuovo account utente per la persona che esegue l'accesso, chiedendo eventuali informazioni mancanti.

      > [!NOTE]
      > Gli utenti aggiunti tramite Single Sign-On vengono aggiunti come utenti generici e non hanno un ruolo assegnato nel sistema. Un amministratore può accedere e modificare il ruolo di sicurezza degli utenti in qualità di editor o amministratore e può anche assegnare uno o più ruoli dell'organigramma. 

    g. Per salvare le impostazioni, fare clic su **Save** (Salva).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/waywedo-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/waywedo-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/waywedo-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/waywedo-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-way-we-do-test-user"></a>Creare un utente di test di Way We Do

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Way We Do. Way We Do supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Way We Do viene creato un nuovo utente, se questo non esiste già.

> [!Note]
> Per creare un utente manualmente, contattare il  [team del supporto clienti di Way We Do](mailto:support@waywedo.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Way We Do.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Way We Do, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Way We Do**.

    ![Collegamento di Way We Do nell'elenco delle applicazioni](./media/waywedo-tutorial/tutorial_waywedo_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Way We Do nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Way We Do.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/waywedo-tutorial/tutorial_general_01.png
[2]: ./media/waywedo-tutorial/tutorial_general_02.png
[3]: ./media/waywedo-tutorial/tutorial_general_03.png
[4]: ./media/waywedo-tutorial/tutorial_general_04.png

[100]: ./media/waywedo-tutorial/tutorial_general_100.png

[200]: ./media/waywedo-tutorial/tutorial_general_200.png
[201]: ./media/waywedo-tutorial/tutorial_general_201.png
[202]: ./media/waywedo-tutorial/tutorial_general_202.png
[203]: ./media/waywedo-tutorial/tutorial_general_203.png


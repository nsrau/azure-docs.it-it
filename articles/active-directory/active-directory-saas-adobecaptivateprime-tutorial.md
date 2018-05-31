---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Captivate Prime | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Captivate Prime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: f61ab05d2c89167eeef0cb930a734bfafeeded1c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206462"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Captivate Prime

Questa esercitazione descrive come integrare Adobe Captivate Prime con Azure Active Directory (Azure AD).

L'integrazione di Adobe Captivate Prime con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Adobe Captivate Prime.
- È possibile abilitare gli utenti per l'accesso automatico ad Adobe Captivate Prime (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Captivate Prime, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Adobe Captivate Prime abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Adobe Captivate Prime dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Aggiunta di Adobe Captivate Prime dalla raccolta
Per configurare l'integrazione di Adobe Captivate Prime in Azure AD, è necessario aggiungere Adobe Captivate Prime dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Adobe Captivate Prime dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Adobe Captivate Prime**, selezionare **Adobe Captivate Prime** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Adobe Captivate Prime nell'elenco risultati](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Captivate Prime con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Adobe Captivate Prime che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Captivate Prime.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Captivate Prime, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)**: per avere una controparte di Britta Simon in Adobe Captivate Prime collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adobe Captivate Prime.

**Per configurare l'accesso Single Sign-On di Azure AD con Adobe Captivate Prime, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Adobe Captivate Prime** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. Nella sezione **URL e dominio Adobe Captivate Prime** seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di Adobe Captivate Prime](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://captivateprime.adobe.com`

    b. Nella casella di testo **URL di risposta** digitare un URL: `https://captivateprime.adobe.com/saml/SSO`

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Andare alla scheda **Proprietà**, copiare l'**URL accesso utente** e incollarlo nel Blocco note.

    ![Collegamento dell'accesso utente](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Per configurare Single Sign-On sul lato **Adobe Captivate Prime**, è necessario inviare l'**XML metadati** scaricato e l'**URL accesso utente** copiato al [team di supporto di Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Creare un utente di test di Adobe Captivate Prime

In questa sezione viene creato un utente di nome Britta Simon in Adobe Captivate Prime. Collaborare con il [team di supporto di Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) per aggiungere gli utenti nella piattaforma Adobe Captivate Prime. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso ad Adobe Captivate Prime per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Adobe Captivate Prime, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Adobe Captivate Prime**.

    ![Collegamento ad Adobe Captivate Prime nell'elenco di applicazioni](./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Adobe Captivate Prime nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Captivate Prime.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobecaptivateprime-tutorial/tutorial_general_203.png


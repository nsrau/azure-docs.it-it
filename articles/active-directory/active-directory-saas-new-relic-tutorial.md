---
title: 'Esercitazione: Integrazione di Azure Active Directory con New Relic | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 45106b499b9a541d5e6120538cfe1715608be118
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Esercitazione: Integrazione di Azure Active Directory con New Relic

Questa esercitazione descrive come integrare New Relic con Azure Active Directory (Azure AD).

L'integrazione di New Relic con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a New Relic.
- È possibile abilitare gli utenti per l'accesso automatico a New Relic (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con New Relic, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di New Relic abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di New Relic dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-new-relic-from-the-gallery"></a>Aggiunta di New Relic dalla raccolta
Per configurare l'integrazione di New Relic in Azure AD, è necessario aggiungere New Relic dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere New Relic dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **New Relic**, nel pannello dei risultati selezionare **New Relic** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![New Relic nell'elenco risultati](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con New Relic usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di New Relic corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in New Relic.

Per stabilire la relazione di collegamento, in New Relic assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con New Relic, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di New Relic](#create-a-new-relic-test-user)**: per avere una controparte di Britta Simon in New Relic collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione New Relic.

**Per configurare l'accesso Single Sign-On di Azure AD con New Relic, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **New Relic** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. Nella sezione **URL e dominio New Relic** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di New Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.newrelic.com`.

    b. Nella casella di testo **Identificatore** digitare il valore: `rpm.newrelic.com`

    > [!NOTE] 
    > Poiché questo non è un valore reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di New Relic](https://support.newrelic.com/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di New Relic** fare clic su **Configura New Relic** per aprire la finestra **Configura accesso**. Copiare i valori **Sign-Out URL (URL di disconnessione) e SAML Single Sign-On Service URL (URL servizio Single Sign-On SAML)** dalla sezione **Quick Reference** (Riferimento rapido).

    ![Configurazione di New Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di **New Relic** come amministratore.

8. Nel menu in alto fare clic su **Impostazioni account**.
   
    ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/ic797036.png "Impostazioni account")

9. Fare clic sulla scheda **Security and authentication** (Sicurezza e autenticazione), quindi fare clic sulla scheda **Single sign on**.
   
    ![Single Sign-On](./media/active-directory-saas-new-relic-tutorial/ic797037.png "Single Sign-On")

10. Nella pagina della finestra di dialogo SAML eseguire la procedura seguente:
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. Fare clic su **Choose File** per caricare il certificato di Azure Active Directory scaricato.

   b. Nella casella di testo **Remote login URL** (URL di accesso remoto) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
   
   c. Nella casella di testo **Logout landing URL** (URL di destinazione di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

   d. Fare clic su **Save my changes**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-new-relic-test-user"></a>Creare un utente di test di New Relic

Per consentire agli utenti di Azure Active Directory di accedere a New Relic, è necessario eseguirne il provisioning in New Relic. Nel caso di New Relic, il provisioning è un’attività manuale.

**Per effettuare il provisioning di un account utente in New Relic, seguire questa procedura:**

1. Accedere al sito aziendale di **New Relic** come amministratore.

2. Nel menu in alto fare clic su **Impostazioni account**.
   
    ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/ic797040.png "Impostazioni account")

3. Nel riquadro **Account** a sinistra fare clic su **Summary** (Riepilogo), quindi su **Add user** (Aggiungi utente).
   
    ![Impostazioni account](./media/active-directory-saas-new-relic-tutorial/ic797041.png "Impostazioni account")

4. Nella finestra di dialogo **Active users** eseguire la procedura seguente:
   
    ![Utenti attivi](./media/active-directory-saas-new-relic-tutorial/ic797042.png "Utenti attivi")
   
    a. Nella casella di testo **Posta elettronica** digitare l'indirizzo di posta elettronica di un utente valido di Azure Active Directory di cui si desidera eseguire il provisioning.

    b. Come **Role** (Ruolo) selezionare **User** (Utente).

    c. Fare clic su **Add this user**.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di New Relic o le API fornite da New Relic per eseguire il provisioning degli account utente di AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a New Relic.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a New Relic, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **New Relic**.

    ![Collegamento di New Relic nell'elenco delle applicazioni](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro New Relic nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione New Relic.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png


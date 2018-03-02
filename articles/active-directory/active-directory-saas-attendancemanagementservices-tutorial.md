---
title: 'Esercitazione: Integrazione di Azure Active Directory con Attendance Management Services | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Attendance Management Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: 1fcbbabe80c3ff4b5a18904637cb227499da6829
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Esercitazione: Integrazione di Azure Active Directory con Attendance Management Services

Questa esercitazione descrive come integrare Attendance Management Services con Azure Active Directory (Azure AD).

L'integrazione di Attendance Management Services con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Attendance Management Services.
- È possibile abilitare gli utenti per l'accesso automatico ad Attendance Management Services (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Attendance Management Services, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Attendance Management Services abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Attendance Management Services dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-attendance-management-services-from-the-gallery"></a>Aggiunta di Attendance Management Services dalla raccolta
Per configurare l'integrazione di Attendance Management Services in Azure AD, è necessario aggiungere Attendance Management Services dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Attendance Management Services dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Attendance Management Services**, selezionare **Attendance Management Services** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Attendance Management Services nell'elenco risultati](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Attendance Management Services usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Attendance Management Services corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Attendance Management Services.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Attendance Management Services, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Attendance Management Services](#create-an-attendance-management-service-test-user)**: per avere una controparte di Britta Simon in Attendance Management Services collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Attendance Management Services.

**Per configurare l'accesso Single Sign-On di Azure AD con Attendance Management Services, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Attendance Management Services** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

3. Nella sezione **URL e dominio Attendance Management Services** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Attendance Management Services](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://id.obc.jp/<tenant information >/`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Attendance Management Services](http://www.obcnet.jp/).

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

5. Fare clic sul pulsante **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Attendance Management Services** fare clic su **Configura Attendance Management Services** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di Attendance Management Services](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

7. In un'altra finestra del browser accedere al sito aziendale di Attendance Management Services come amministratore.

8. Fare clic su **SAML authentication** (Autenticazione SAML) nella sezione **Security management** (Gestione sicurezza).

    ![Configurazione di Attendance Management Services](./media/active-directory-saas-attendancemanagementservices-tutorial/user1.png)

9. Seguire questa procedura:

    ![Configurazione di Attendance Management Services](./media/active-directory-saas-attendancemanagementservices-tutorial/user2.png)

    a. Selezionare **Use SAML authentication** (Usa autenticazione SAML).

    b. Nella casella di testo **Identifier** (Identificatore) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure. 

    c. Nella casella di testo **Authentication endpoint URL** (URL endpoint di autenticazione) incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    d. Fare clic su **Select a file** (Seleziona un file) per caricare il certificato scaricato da Azure AD.

    e. Selezionare **Disable password authentication** (Disabilita autenticazione password).

    f. Fare clic su **Registration** (Registrazione).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

Questa sezione descrive come creare un utente di test denominato Britta Simon nel portale di Azure.

   ![Creare un utente di test di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Creare un utente di test di Attendance Management Services

Per consentire agli utenti di Azure AD di accedere ad Attendance Management Services, è necessario effettuarne il provisioning in Attendance Management Services. Nel caso di Attendance Management Services, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Attendance Management Services come amministratore.

2. Fare clic su **User management** (Gestione utenti) nella sezione **Security management** (Gestione sicurezza).

    ![Aggiungere un dipendente](./media/active-directory-saas-attendancemanagementservices-tutorial/user5.png)

3. Fare clic su **New rules login** (Nuovo accesso con regole).

    ![Aggiungere un dipendente](./media/active-directory-saas-attendancemanagementservices-tutorial/user3.png)

4. Nella sezione **OBCiD information** (Informazioni OBCiD) seguire questa procedura:

    ![Aggiungere un dipendente](./media/active-directory-saas-attendancemanagementservices-tutorial/user4.png)

    a. Nella casella di testo **OBCiD** digitare l'indirizzo di posta elettronica dell'utente, ad esempio **BrittaSimon@contoso.com**.

    b. Nella casella di testo **Password** digitare la password dell'utente.

    c. Fare clic su **Registration** (Registrazione).


### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Attendance Management Services.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Attendance Management Services, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Attendance Management Services**.

    ![Collegamento di Attendance Management Services nell'elenco delle applicazioni](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Attendance Management Services nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Attendance Management Services.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_203.png


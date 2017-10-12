---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: ac73aadcb8205f24f9d2dbce5af76f53bbcb9753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con Mozy Enterprise

Questa esercitazione descrive come integrare Mozy Enterprise con Azure Active Directory (Azure AD).

L'integrazione di Mozy Enterprise con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Mozy Enterprise
- È possibile abilitare gli utenti per l'accesso automatico a Mozy Enterprise (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mozy Enterprise, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Mozy Enterprise abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Mozy Enterprise dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Aggiunta di Mozy Enterprise dalla raccolta
Per configurare l'integrazione di Mozy Enterprise in Azure AD, è necessario aggiungere Mozy Enterprise dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Mozy Enterprise dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Mozy Enterprise**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. Nel pannello dei risultati selezionare **Mozy Enterprise** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mozy Enterprise usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Mozy Enterprise corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mozy Enterprise.

Per stabilire la relazione di collegamento, in Mozy Enterprise assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mozy Enterprise, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**: per avere una controparte di Britta Simon in Mozy Enterprise collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Mozy Enterprise.

**Per configurare l'accesso Single Sign-On di Azure AD con Mozy Enterprise, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Mozy Enterprise** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. Nella sezione **URL e dominio Mozy Enterprise** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto di Mozy Enterprise](http://support.mozy.com/).

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Mozy Enterprise** fare clic su **Configura Mozy Enterprise** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di Mozy Enterprise come amministratore.

8. Nella sezione **Configuration** fare clic su **Authentication Policy**.
   
   ![Criteri di autenticazione](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "Criteri di autenticazione")

9. Nella sezione **Authentication Policy** seguire questa procedura:
   
   ![Criteri di autenticazione](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "Criteri di autenticazione")
   
   a. Selezionare **Directory Service** come **Provider**.
   
   b. Selezionare **Use LDAP Push**.
   
   c. Fare clic sulla scheda **SAML Authentication** .
   
   d. Incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **Authentication URL** (URL di autenticazione).
   
   e. Incollare l'**ID di entità SAML** copiato dal portale di Azure nella casella di testo **SAML Endpoint** (Endpoint SAML).
   
   f. Aprire il certificato con codifica Base 64 scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollare l'intero certificato nella casella di testo **SAML Certificate** (Certificato SAML).
   
   g. Selezionare **Abilita SSO per consentire agli amministratori di accedere con le proprie credenziali di rete**.
   
   h. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Creazione di un utente di test di Mozy Enterprise

Per consentire agli utenti di Azure AD di accedere a Mozy Enterprise, è necessario eseguirne il provisioning in Mozy Enterprise. Nel caso di Mozy Enterprise, il provisioning è un’attività manuale.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Mozy Enterprise o le API fornite da Mozy Enterprise per eseguire il provisioning degli account utente di AAD.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Mozy Enterprise** .

2. Fare clic su **Users** (Utenti) e quindi fare clic su **Add New User** (Aggiungi nuovo utente).
   
   ![Utenti](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "Utenti")
   
   >[!NOTE]
   >L'opzione **Add New User** (Aggiungi nuovo utente) viene visualizzata solo se **Mozy** è selezionato come provider in **Authentication policy** (Criteri di autenticazione). Se è configurata l'autenticazione SAML gli utenti vengono aggiunti automaticamente al primo accesso tramite Single Sign-On.
    
3. Nella finestra di dialogo Nuovo utente eseguire la procedura seguente:
   
   ![Aggiungere utenti](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "Aggiungere utenti")
   
   a. Dall’elenco **Choose a group** selezionare un gruppo.
   
   b. Dall’elenco **What tupe of user** selezionare un tipo.
   
   c. Nella casella di testo **Nome utente** digitare il nome dell'utente di Azure AD.
   
   d. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente di Azure AD.
   
   e. Selezionare **Send user instruction email**.
   
   f. Fare clic su **Add User(s)**.

     >[!NOTE]
     > Dopo aver creato l'utente, verrà inviato un messaggio di posta elettronica all'utente di Azure AD con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Mozy Enterprise.

![Assegna utente][200] 

**Per assegnare Britta Simon a Mozy Enterprise, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Mozy Enterprise**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Mozy Enterprise nel pannello di accesso, dovrebbe essere visualizzata la pagina di accesso dell'applicazione Mozy Enterprise.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con HR2day by Merces | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e HR2day by Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: 77e2fcf9c306259286b15767f3a992510d6d79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Esercitazione: Integrazione di Azure Active Directory con HR2day by Merces

Questa esercitazione descrive come integrare HR2day by Merces con Azure Active Directory (Azure AD).

L'integrazione di HR2day by Merces con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a HR2day by Merces.
- È possibile abilitare gli utenti per l'accesso automatico a HR2day by Merces con gli account Azure AD personali.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con HR2day by Merces, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure AD.
- Sottoscrizione di HR2day by Merces abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Ottenere una [versione di valutazione gratuita di un mese di Azure AD](https://azure.microsoft.com/pricing/free-trial/), se non la si ha già.  

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto qui prevede le due procedure di base seguenti:

1. Aggiunta di HR2day by Merces dalla raccolta.
2. Configurazione e test dell'accesso Single Sign-On di Azure AD.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Aggiunta di HR2day by Merces dalla raccolta
Per configurare l'integrazione di HR2day by Merces in Azure AD, aggiungere HR2day by Merces dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere HR2day by Merces dalla raccolta, seguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca, digitare **HR2day by Merces**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. Nel pannello dei risultati selezionare **HR2day by Merces** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con HR2day by Merces usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di HR2day by Merces corrispondente a un utente di Azure AD. In altre parole, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in HR2day by Merces.

Per stabilire la relazione, in HR2day by Merces assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con HR2day by Merces, è necessario completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on): per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#creating-an-azure-ad-test-user): per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di HR2day by Merces](#creating-an-hr2day-by-merces-test-user): creare una controparte di Britta Simon in HR2day by Merces collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assigning-the-azure-ad-test-user): per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#testing-single-sign-on): per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione HR2day by Merces.

**Per configurare l'accesso Single Sign-On di Azure AD con HR2day by Merces, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **HR2day by Merces** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. Nella sezione **URL e dominio HR2day by Merces** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il modello seguente: `https://<tenantname>.force.com/<instancename>`.

    b. Nella casella di testo **Identificatore** digitare un URL usando il modello seguente: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con URL di accesso e identificatore effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di HR2day by Merces](mailto:servicedesk@merces.nl). 
 


4. Nella sezione **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a HR2day by Merces con il proprio account in Azure AD. A tale scopo, viene usata la federazione basata sul protocollo SAML.

    L'applicazione HR2day by Merces prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati al token SAML. Lo screenshot seguente ne illustra un esempio. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Prima di poter configurare l'asserzione SAML, è necessario contattare il [team di supporto clienti di HR2day by Merces](mailto:servicedesk@merces.nl) e richiedere il valore dell'attributo dell'identificatore univoco per il tenant. Questo valore è necessario per completare i passaggi nella sezione successiva. 

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine seguente. Seguire quindi questa procedura.
    
      | Nome attributo    |   Valore attributo |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | join([mail],"102938475Z","@" |
    
      a. Per aprire la finestra di dialogo **Aggiungi attributo**, selezionare **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare **ATTR_LOGINCLAIM**.

    c. Nell'elenco **Valore** selezionare **Join()**.

    d. Nell'elenco **Stringa1** selezionare **user.mail**.

    e. Per **Stringa2** digitare l'identificatore univoco fornito dal team HR2day.

    f. Nella casella **Separatore** digitare **@**.
    
    g. Selezionare **OK**.

7. Fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. Nella sezione **Configurazione di HR2day by Merces** fare clic su **Configura HR2day by Merces** per aprire la finestra **Configura accesso**. Copiare **URL di accesso**, **ID di entità SAML** e **URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Per configurare SSO per l'applicazione, contattare il [team di supporto clienti di HR2day by Merces](mailTo:servicedesk@merces.nl). Allegare il file del **certificato (Base64)** scaricato al messaggio di posta elettronica. Specificare anche **URL di disconnessione**, **ID di entità SAML** e **URL del servizio Single Sign-On SAML** per consentirne la configurazione per l'integrazione SSO.

    > [!NOTE]
    >Segnalare al team Merces che questa integrazione richiede che l'ID entità sia impostato in base al modello **https://hr2day.force.com/NOMEISTANZA**.

    > [!TIP]
    >Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On**. Accedere quindi alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. Per aprire la finestra di dialogo **Utente**, selezionare **Aggiungi** nella parte superiore della finestra di dialogo.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Nella finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'**indirizzo e-mail** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota della password.

    d. Selezionare **Crea**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Creare un utente di test di HR2day Merces

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in HR2day by Merces. Per aggiungere gli utenti nell'account HR2day, collaborare con il [team di supporto clienti di HR2day by Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Per creare un utente manualmente, contattare il [team di supporto clienti di HR2day by Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a HR2day by Merces.

![Assegnare utenti][200] 

**Per assegnare Britta Simon a HR2day by Merces, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**. Selezionare quindi **Tutte le applicazioni**.

    ![Assegnare utenti][201] 

2. Nell'elenco delle applicazioni, selezionare **HR2day by Merces**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegnare utenti][202] 

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Assegnare utenti][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Fare clic sul pulsante **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro HR2day by Merces nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione HR2day by Merces.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sull'integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png


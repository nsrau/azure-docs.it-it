---
title: 'Esercitazione: Integrazione di Azure Active Directory con PolicyStat | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 704afd5515b02ce2a4fbf35da65fad74dc506271
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Esercitazione: Integrazione di Azure Active Directory con PolicyStat

Questa esercitazione descrive come integrare PolicyStat con Azure Active Directory (Azure AD).

L'integrazione di PolicyStat con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a PolicyStat
- È possibile abilitare gli utenti per l'accesso automatico a PolicyStat (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con PolicyStat, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di PolicyStat abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di PolicyStat dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-policystat-from-the-gallery"></a>Aggiunta di PolicyStat dalla raccolta
Per configurare l'integrazione di PolicyStat in Azure AD, è necessario aggiungere PolicyStat dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere PolicyStat dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **PolicyStat**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_search.png)

5. Nel pannello dei risultati selezionare **PolicyStat** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con PolicyStat con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di PolicyStat che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in PolicyStat.

Per stabilire la relazione di collegamento, in PolicyStat assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con PolicyStat, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di PolicyStat](#creating-a-policystat-test-user)**: per avere una controparte di Britta Simon in PolicyStat collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel nuovo portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione PolicyStat.

**Per configurare Single Sign-On di Azure AD con PolicyStat, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **PolicyStat** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_samlbase.png)

3. Nella sezione **URL e dominio PolicyStat** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.policystat.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto clienti di PolicyStat](http://www.policystat.com/support/). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_certificate.png) 

5. In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a PolicyStat tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

    L'applicazione PolicyStat prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**.  

     Lo screenshot seguente ne illustra un esempio.

     ![Attributi](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_attribute.png "Attributi")

6. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:

    | Nome attributo    |   Valore attributo |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_04.png)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. Nella casella di testo **Nome attributo** digitare **uid**.

    c. Nella casella di testo **Valore attributo** selezionare **ExtractMailPrefix()**.    
   
    d. Nell'elenco **Posta** selezionare **User.mail**.
    
    e. Fare clic su **Ok**

7. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/tutorial_general_400.png)

8. In un'altra finestra del Web browser accedere al sito aziendale di PolicyStat come amministratore.

9. Scegliere la scheda **Admin**, quindi fare clic su **Configurazione di Single Sign-On** nel riquadro di spostamento a sinistra.
   
    ![Menu amministratore](./media/active-directory-saas-policystat-tutorial/ic808633.png "Menu amministratore")

10. Nella sezione **Configurazione** selezionare **Abilitare integrazione di Single Sign-On**.
   
    ![Configurazione Single Sign-on](./media/active-directory-saas-policystat-tutorial/ic808634.png "Configurazione Single Sign-on")

11. Fare clic su **Configura attributi** e nella sezione **Configura attributi** eseguire la procedura seguente:
   
    ![Configurazione Single Sign-on](./media/active-directory-saas-policystat-tutorial/ic808635.png "Configurazione Single Sign-on")
   
    a. Nella casella di testo **Attributo nome utente** digitare **uid**.

    b. Nella casella di testo **First Name Attribute** (Attributo nome) digitare **firstname** dell'utente **Britta**.

    c. Nella casella di testo **Last Name Attribute** (Attributo cognome) digitare **lastname** dell'utente **Simon**.

    d. Nella casella di testo **Email Attribute** (Attributo posta elettronica) digitare **emailaddress** dell'utente **BrittaSimon@contoso.com**.

    e. Fare clic su **Salva modifiche**.

12. Fare clic su **Metadati del provider di identità** quindi, nella sezione **Metadati del provider di identità**, eseguire la procedura seguente:
   
    ![Configurazione Single Sign-on](./media/active-directory-saas-policystat-tutorial/ic808636.png "Configurazione Single Sign-on")
   
    a. Aprire il file di metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Your Identity Provider Metadata** (Metadati provider di identità).

    b. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-policystat-test-user"></a>Creazione di un utente di test di PolicyStat

Per consentire agli utenti di Azure AD di accedere a PolicyStat, è necessario eseguirne il provisioning in PolicyStat.  

PolicyStat supporta solo il provisioning JIT dell'utente. Non è pertanto è necessario aggiungere gli utenti manualmente a PolicyStat. Gli utenti verranno aggiunti automaticamente al primo accesso tramite SSO.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da PolicyStat per eseguire il provisioning degli account utente di Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a PolicyStat.

![Assegna utente][200] 

**Per assegnare Britta Simon a PolicyStat, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **PolicyStat**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro PolicyStat nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione PolicyStat.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_203.png


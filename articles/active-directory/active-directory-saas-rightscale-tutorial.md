---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rightscale | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 222c4414a9f736a3589b4cdd0ed934696f6c31ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Esercitazione: Integrazione di Azure Active Directory con Rightscale

Questa esercitazione descrive come integrare Rightscale con Azure Active Directory (Azure AD).

L'integrazione di Rightscale con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Rightscale
- È possibile abilitare gli utenti per l'accesso automatico a Rightscale (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Rightscale, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Rightscale abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Rightscale dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-rightscale-from-the-gallery"></a>Aggiunta di Rightscale dalla raccolta
Per configurare l'integrazione di Rightscale in Azure AD, è necessario aggiungere Rightscale dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Rightscale dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Rightscale**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_search.png)

5. Nel pannello dei risultati selezionare **Rightscale** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Rightscale in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Rightscale che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Rightscale.

Per stabilire la relazione di collegamento, in Rightscale assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Rightscale, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Rightscale](#creating-a-rightscale-test-user)** : per avere una controparte di Britta Simon in Rightscale collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Rightscale.

**Per configurare l'accesso Single Sign-On di Azure AD con Rightscale, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Rightscale** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_samlbase.png)

3. Nella sezione **URL e dominio Rightscale**, se si vuole configurare l'applicazione in **modalità avviata da IDP**, non è necessario eseguire alcun passaggio, perché l'app è già preintegrata in Azure.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_url.png)

4. Nella sezione **URL e dominio Rightscale**, se si vuole configurare l'applicazione in **modalità avviata da SP**, seguire questa procedura:
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Fare clic su **Mostra impostazioni URL avanzate**.

    b. Nella casella di testo **URL di accesso** digitare l'URL: `https://login.rightscale.com/`

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di Rightscale** fare clic su **Configura Rightscale** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido**.

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
8. Per configurare l'accesso SSO per l'applicazione, è necessario accedere al tenant di RightScale come amministratore.

    a. Nel menu in alto fare clic sulla scheda **Settings** (Impostazioni) e selezionare **Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Fare clic sul pulsante "**new**" (nuovo) per aggiungere i **provider di identità SAML**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. Nella casella **Nome visualizzato**digitare il nome dell'azienda.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Selezionare **Allow RightScale-initiated SSO using a discovery hint** (Consenti accesso SSO avviato da RightScale tramite un hint di individuazione) e digitare il **nome di dominio** nella casella di testo sottostante.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_004.png)

    e. Nella casella di testo **SAML SSO Endpoint** (Endpoint SSO SAML) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_006.png)

    f. Incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure in **SAML Entity ID** (ID entità SAML) in Rightscale.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_008.png)

    g. Fare clic sul pulsante **Browser** per caricare il certificato scaricato dal portale di Azure.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_009.png)

    h. Fare clic su **Salva**.
<CE>
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-rightscale-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-rightscale-test-user"></a>Creazione di un utente di test di Rightscale

In questa sezione viene creato un utente chiamato Britta Simon in RightScale. Collaborare con il [team di supporto clienti di Rightscale](mailto:support@rightscale.com) per aggiungere gli utenti alla piattaforma Rightscale.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Rightscale.

![Assegna utente][200] 

**Per assegnare Britta Simon a Rightscale, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Rightscale**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-rightscale-tutorial/tutorial_rightscale_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.  

Quando si fa clic sul riquadro RightScale nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione RightScale.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rightscale-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con Lucidchart | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 2dea669f03c893632c08d30feeb3173efc2d8243
ms.contentlocale: it-it
ms.lasthandoff: 06/24/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Esercitazione: Integrazione di Azure Active Directory con Lucidchart

Questa esercitazione descrive come integrare Lucidchart con Azure Active Directory (Azure AD).

L'integrazione di Lucidchart con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Lucidchart
- È possibile abilitare gli utenti per l'accesso automatico a Lucidchart (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Lucidchart, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Lucidchart abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Lucidchart dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-lucidchart-from-the-gallery"></a>Aggiunta di Lucidchart dalla raccolta
Per configurare l'integrazione di Lucidchart in Azure AD, è necessario aggiungere Lucidchart dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Lucidchart dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Lucidchart**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lucidchart-tutorial/tutorial_lucidchart_search.png)

5. Nel pannello dei risultati selezionare **Lucidchart** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lucidchart-tutorial/tutorial_lucidchart_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Lucidchart usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di Lucidchart che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Lucidchart.

Per stabilire la relazione di collegamento, in Lucidchart assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Lucidchart, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Lucidchart](#creating-a-lucidchart-test-user)**: per avere una controparte di Britta Simon in Lucidchart collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Lucidchart.

**Per configurare l'accesso Single Sign-On di Azure AD con Lucidchart, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Lucidchart** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/tutorial_lucidchart_samlbase.png)

3. Nella sezione **URL e dominio Lucidchart** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/tutorial_lucidchart_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://chart2.office.lucidchart.com/saml/sso/azure`

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/tutorial_lucidchart_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di Lucidchart come amministratore.

7. Nel menu in alto fare clic su **Team**.
   
    ![Team](./media/active-directory-saas-lucidchart-tutorial/ic791190.png "Team")

8. Fare clic su **Applications (Applicazioni) \> Manage SAML (Gestisci SAML)**.
   
    ![Gestire SAML](./media/active-directory-saas-lucidchart-tutorial/ic791191.png "Gestire SAML")

9. Nella pagina **Impostazioni di autenticazione SAML** eseguire la procedura seguente:
   
    a. Selezionare **Enable SAML Authentication** (Abilita autenticazione SAML) e quindi fare clic su **Optional** (Facoltativo).

    ![Dettagli dell'autenticazione SAML](./media/active-directory-saas-lucidchart-tutorial/ic791192.png "Dettagli dell'autenticazione SAML")
 
    b. Nella casella di testo **Domain** (Dominio) digitare il dominio e quindi fare clic su **Change Certificate** (Cambia certificato).

    ![Cambiare il certificato](./media/active-directory-saas-lucidchart-tutorial/ic791193.png "Cambiare il certificato")
 
    c. Aprire il file di metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Upload Metadata**.

    ![Caricare i metadati](./media/active-directory-saas-lucidchart-tutorial/ic791194.png "Caricare i metadati")
 
    d. Selezionare **Automatically Add new users to the team** (Aggiungi automaticamente nuovi utenti al team) e quindi fare clic su **Save changes** (Salva modifiche).

    ![Salvare le modifiche](./media/active-directory-saas-lucidchart-tutorial/ic791195.png "Salvare le modifiche")

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lucidchart-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lucidchart-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lucidchart-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-lucidchart-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-lucidchart-test-user"></a>Creazione di un utente test di Lucidchart

Non è richiesta alcuna operazione per configurare il provisioning degli utenti in Lucidchart.  Quando un utente assegnato tenta di accedere a Lucidchart utilizzando il pannello di accesso, Lucidchart controlla se l'utente esiste.  

Se l’account utente non è ancora disponibile, viene creato automaticamente da Lucidchart.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Lucidchart.

![Assegna utente][200] 

**Per assegnare Britta Simon a Lucidchart, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Lucidchart**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-lucidchart-tutorial/tutorial_lucidchart_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Lucidchart nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Lucidchart.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lucidchart-tutorial/tutorial_general_203.png



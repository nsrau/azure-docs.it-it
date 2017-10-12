---
title: 'Esercitazione: integrazione di Azure Active Directory con Moxtra | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: db2f041a44b6771b0a4f734e58d899298ef0847b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Esercitazione: integrazione di Azure Active Directory con Moxtra

Questa esercitazione descrive come integrare Moxtra con Azure Active Directory (Azure AD).

L'integrazione di Moxtra con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Moxtra
- È possibile abilitare gli utenti per l'accesso automatico a Moxtra (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Moxtra, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Moxtra abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Moxtra dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-moxtra-from-the-gallery"></a>Aggiunta di Moxtra dalla raccolta
Per configurare l'integrazione di Moxtra in Azure AD, è necessario aggiungere Moxtra dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Moxtra dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca, digitare **Moxtra**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. Nel pannello dei risultati selezionare **Moxtra** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Moxtra usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Moxtra corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Moxtra.

Per stabilire la relazione di collegamento, in Moxtra assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Moxtra, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Moxtra](#creating-a-moxtra-test-user)**: per avere una controparte di Britta Simon in Moxtra collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Moxtra.

**Per configurare l'accesso Single Sign-On di Azure AD con Moxtra, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Moxtra** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. Nella sezione **URL e dominio Moxtra** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://www.moxtra.com/service/#login`

4. L'applicazione Moxtra prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. Lo screenshot seguente illustra un esempio di questa configurazione. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < ID entità SAML > 

    > [!Note]
    > Il valore dell'attributo **idpid** non è reale. È possibile ottenere il valore effettivo dalla **sezione di riferimento rapido** in **Configurazione di Moxtra**.
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Fare clic su **OK**.
    
5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di Moxtra** fare clic su **Configura Moxtra** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. In un'altra finestra del browser, accedere al sito aziendale di Moxtra come amministratore.

9. Nella barra degli strumenti a sinistra, fare clic su**Admin Console > SAML Single Sign-On** (Console di amministrazione > Single Sign-On SAML), quindi su **New** (Nuovo).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. Nella pagina **SAML** , eseguire i passaggi seguenti:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Nella casella di testo **Nome** , digitare un nome per la configurazione (ad esempio *SAML*). 
  
    b. Nella casella di testo **IdP Entity ID** (ID entità IdP) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure. 
 
    c. Nella casella di testo **Login URL** (URL di accesso) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure. 
 
    d. Nella casella di testo **AuthnContextClassRef** digitare **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Nella casella di testo **NameID Format** (Formato NameID) digitare **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Aprire il certificato scaricato dal portale di Azure nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo **Certificate** (Certificato).    
 
    g. Nella casella di testo dominio email SAML, digitare il dominio email SAML.    
  
    >[!NOTE]
    >Per verificare il dominio, fare clic su “**i**” di seguito.

    h. Fare clic su **Update**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-moxtra-test-user"></a>Creare un utente test Moxtra

Questa sezione descrive come creare un utente chiamato Britta Simon in Moxtra.

**Per creare un utente denominato Britta Simon in Moxtra, seguire questa procedura:**

1. Accedere al sito aziendale di Moxtra come amministratore.

2. Nella barra degli strumenti a sinistra, fare clic su**Admin Console > Gestione utente** e quindi su **Aggiungi utente**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. Nella finestra di dialogo **Aggiungi utente** seguire questa procedura:
  
    a. Nella casella di testo **Nome** digitare **Britta**.
  
    b. Nella casella di testo **Cognome** digitare **Simon**.
  
    c. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di Britta come nel portale di Azure.
  
    d. Nella casella di testo **Divisione**, digitare **Dev**.
  
    e. Nella casella di testo **Reparto**, digitare **IT**.
  
    f. Selezionare **Administrator**.
  
    g. Fare clic su **Aggiungi**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Moxtra.

![Assegna utente][200] 

**Per assegnare Britta Simon a Moxtra, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni, selezionare **Moxtra**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Moxtra nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Moxtra.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png


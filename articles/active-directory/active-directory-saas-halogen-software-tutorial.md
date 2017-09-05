---
title: 'Esercitazione: Integrazione di Azure Active Directory con Halogen Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Halogen Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: e09fa93038965e4880a23002bac6917ad2a077f7
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017


---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Esercitazione: Integrazione di Azure Active Directory con Halogen Software

Questa esercitazione descrive come integrare Halogen Software con Azure Active Directory (Azure AD).

L'integrazione di Halogen Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Halogen Software.
- È possibile abilitare gli utenti per l'accesso automatico a Halogen Software (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Halogen Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Halogen Software abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Halogen Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-halogen-software-from-the-gallery"></a>Aggiunta di Halogen Software dalla raccolta

Per configurare l'integrazione di Halogen Software in Azure AD, è necessario aggiungere Halogen Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Halogen Software dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Halogen Software**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_search.png)

5. Nel pannello dei risultati selezionare **Halogen Software** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Halogen Software mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Halogen Software corrisponde a un determinato utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Halogen Software.

Per stabilire la relazione di collegamento, in Halogen Software assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Halogen Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Halogen Software](#creating-a-halogen-software-test-user)**: per avere una controparte di Britta Simon in Halogen Software collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Halogen Software.

**Per configurare Single Sign-On di Azure AD con Halogen Software, eseguire la procedura seguente:**

1. Nella pagina di integrazione dell'applicazione **Halogen Software** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

3. Nella sezione **URL e dominio Halogen Software** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://global.hgncloud.com/<companyname>`.

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Halogen Software](https://support.halogensoftware.com/). 
 


4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-halogen-software-tutorial/tutorial_general_400.png)

6. In una finestra del browser diversa accedere all'applicazione **Software alogeno** come amministratore.

7. Scegliere la scheda **Options (Opzioni)** . 
   
    ![Cos'è Azure AD Connect][12]

8. Nel riquadro di spostamento a sinistra fare clic su **SAML Configuration**. 
   
    ![Cos'è Azure AD Connect][13]

9. Nella pagina **Configurazione SAML** seguire la procedura seguente: 

    ![Cos'è Azure AD Connect][14]

     a. Come **Identificatore univoco**, selezionare **NameID**.

     b. Come **Unique Identifier Maps To** (L'identificatore univoco mappa verso) selezionare **Username** (Nome utente).
  
     c. Per caricare il file di metadati fare clic su **Browse** (Sfoglia) per selezionare il file e quindi fare clic su **Upload File** (Carica file).
 
     d. Per testare la configurazione, fare clic su **Run Test**. 
    
    >[!NOTE]
    >È necessario attendere la visualizzazione del messaggio"*The SAML test is complete. Please close this window*". chiudere quindi la ginestra del browser aperta. La casella di controllo **Enable SAML** (Abilita SAML) è selezionata solo se il test è stato completato. 
     
     e. Selezionare **Enable SAML**.
    
     f. Fare clic su **Salva modifiche**. 

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-halogen-software-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-halogen-software-test-user"></a>Creazione di un utente test di Halogen Software

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Halogen Software.

**Per creare un utente test denominato Britta Simon in Halogen Software, eseguire la procedura seguente:**

1. Accedere all'applicazione **Halogen Software** come amministratore.

2. Fare clic sulla scheda **User Center** (Centro utenti) e quindi su **Create User** (Crea utente).
   
    ![Cos'è Azure AD Connect][300]  

3. Nella pagina **Nuovo utente** eseguire la procedura seguente:
   
    ![Cos'è Azure AD Connect][301]

    a. Digitare il nome dell'utente, ad esempio **Britta**, nella casella di testo **First Name** (Nome).
    
    b. Digitare il cognome dell'utente, ad esempio **Simon**, nella casella di testo **Last Name** (Cognome). 

    c. Nella casella di testo **Username** (Nome utente) digitare **Britta Simon**, il nome utente che appare nel portale di Azure.

    d. Nella casella di testo **Password** digitare una password per Britta.
    
    e. Fare clic su **Save**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Halogen Software.

![Assegna utente][200] 

**Per assegnare Britta Simon a Halogen Software, eseguire la procedura seguente:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Halogen Software**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-halogen-software-tutorial/tutorial_halogensoftware_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Halogen Software nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Halogen Software.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png


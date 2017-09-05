---
title: 'Esercitazione: Integrazione di Azure Active Directory con SmarterU | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 129d08c8a7b4228d4d5f1a3b7938ab649b2747a7
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Esercitazione: Integrazione di Azure Active Directory con SmarterU

Questa esercitazione descrive come integrare SmarterU con Azure Active Directory (Azure AD).

L'integrazione di SmarterU con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SmarterU
- È possibile abilitare gli utenti per l'accesso automatico a SmarterU (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SmarterU, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SmarterU abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SmarterU dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-smarteru-from-the-gallery"></a>Aggiunta di SmarterU dalla raccolta
Per configurare l'integrazione di SmarterU in Azure AD, è necessario aggiungere SmarterU dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere SmarterU dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **SmarterU**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_search.png)

5. Nel pannello dei risultati selezionare **SmarterU** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SmarterU usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di SmarterU corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SmarterU.

Per stabilire la relazione di collegamento, in SmarterU assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SmarterU, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di SmarterU](#creating-a-smarteru-test-user)**: per avere una controparte di Britta Simon in SmarterU collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SmarterU.

**Per configurare l'accesso Single Sign-On di Azure AD con SmarterU, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SmarterU** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_samlbase.png)

3. Nella sezione **URL e dominio SmarterU** seguire questa procedura: 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_url.png)

    Nella casella di testo **Identificatore** digitare l'URL: `https://www.smarteru.com/`

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di SmarterU come amministratore.

7. Nella barra degli strumenti in alto fare clic su **Impostazioni account**.
   
    ![Impostazioni account](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Impostazioni account")

8. Nella pagina di configurazione dell’account, eseguire la procedura seguente:
   
    ![Autorizzazione esterna](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorizzazione esterna") 
 
      a. Selezionare **Attiva autorizzazione esterna**.
  
      b. Nella sezione **Controllo accesso master** selezionare la scheda **SmarterU**.
  
      c. Nella sezione **Accesso predefinito utente** selezionare la scheda **SmarterU**.
  
      d. Selezionare **Attiva Okta**.
  
      e. Copiare il contenuto del file dei metadati scaricato e incollarlo nella casella di testo **Metadati Okta** .
  
      f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-smarteru-test-user"></a>Creazione di un utente di test di SmarterU

Per consentire agli utenti di Azure AD di accedere a SmarterU, è necessario effettuarne il provisioning in SmarterU.

Nel caso di SmarterU, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **SmarterU** .

2. Andare a **Utenti**.

3. Nella sezione degli utenti, eseguire la procedura seguente:
   
    ![Nuovo utente](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nuovo utente")  

    a. Fare clic su **+Utente**.
    
    b. Digitare i valori degli attributi correlati dell'account utente Azure AD nelle caselle di testo seguenti: **Posta elettronica principale**, **ID dipendente**, **Password**, **Verifica password**, **Nome**, **Cognome**.
    
    c. Fare clic su **Attivo**. 
    
    d. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione di account utentedi SmarterU o API fornita da SmarterU per eseguire il provisioning degli account utente di Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SmarterU.

![Assegna utente][200] 

**Per assegnare Britta Simon a SmarterU, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SmarterU**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
 
Quando si fa clic sul riquadro SmarterU nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione SmarterU.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_203.png



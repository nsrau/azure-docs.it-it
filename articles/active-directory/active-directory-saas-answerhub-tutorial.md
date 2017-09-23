---
title: 'Esercitazione: Integrazione di Azure Active Directory con AnswerHub | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 3a1c9cc5d7a2ebe28e9fb7e0e6ed8e3d393873ae
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Esercitazione: Integrazione di Azure Active Directory con AnswerHub

Questa esercitazione descrive come integrare AnswerHub con Azure Active Directory (Azure AD).

L'integrazione di AnswerHub con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad AnswerHub
- È possibile abilitare gli utenti per l'accesso automatico ad AnswerHub (Single Sign-On) con gli Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con AnswerHub, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di AnswerHub abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di AnswerHub dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-answerhub-from-the-gallery"></a>Aggiunta di AnswerHub dalla raccolta
Per configurare l'integrazione di AnswerHub in Azure AD, è necessario aggiungere AnswerHub dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere AnswerHub dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **AnswerHub**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_search.png)

5. Nel pannello dei risultati selezionare **AnswerHub** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con AnswerHub usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di AnswerHub corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in AnswerHub.

Per stabilire la relazione di collegamento, in AnswerHub assegnare il valore del **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con AnswerHub, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di AnswerHub](#creating-an-answerhub-test-user)**: per avere una controparte di Britta Simon in AnswerHub collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione AnswerHub.

**Per configurare l'accesso Single Sign-On di Azure AD con AnswerHub, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **AnswerHub** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. Nella sezione **URL e dominio AnswerHub** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<company>.answerhub.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di AnswerHub](mailto:success@answerhub.com). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di AnswerHub** fare clic su **Configura AnswerHub** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di AnswerHub come amministratore.
   
    >[!NOTE]
    >Per ottenere assistenza nella configurazione di AnswerHub, contattare il [team di supporto di AnswerHub](mailto:success@answerhub.com.).
   
8. Passare a **Administration**.

9. Fare clic sulla scheda **User and Group** .

10. Nel riquadro di spostamento a sinistra passare alla sezione **Social Settings** e fare clic su **SAML Setup**.

11. Fare clic sulla scheda **IDP Config** .

12. Nella scheda **IDP Config** seguire questa procedura:

     ![Configurazione SAML](./media/active-directory-saas-answerhub-tutorial/ic785172.png "Configurazione SAML")  
  
     a. Nella casella di testo **IDP Login URL** (URL di accesso IdP) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
  
     b. Nella casella di testo **IDP Logout URL** (URL di disconnessione IdP) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
     
     c. Nella casella di testo **IDP Name Identifier Format** (Formato identificatore nome IdP) immettere lo stesso valore di ID utente selezionato nella sezione **Attributi utente** del portale di Azure.
  
     d. Fare clic su **Keys and Certificates**.

13. Nella scheda Keys and Certificates seguire questa procedura:
    
     ![Chiavi e certificati](./media/active-directory-saas-answerhub-tutorial/ic785173.png "Chiavi e certificati")  
 
     a. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **IDP Public Key (x509 Format)** (Chiave pubblica IdP (formato x509)).
  
     b. Fare clic su **Salva**.

14. Nella scheda **IDP Config** fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-answerhub-test-user"></a>Creazione di un utente di test di AnswerHub

Per consentire agli utenti di Azure AD di accedere ad AnswerHub, è necessario effettuarne il provisioning in AnswerHub.  
Nel caso di AnswerHub, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **AnswerHub** come amministratore.

2. Passare a **Administration**.

3. Fare clic sulla scheda **Users & Groups**.

4. Nel riquadro di spostamento a sinistra fare clic su **Create or import users** nella sezione **Manage users**.
   
   ![Utenti e gruppi](./media/active-directory-saas-answerhub-tutorial/ic785175.png "Utenti e gruppi")

5. Nelle caselle di testo **Email address**, **Username** e **Password** digitare l'indirizzo di posta elettronica, il nome utente e la password di un account utente Azure Active Directory valido di cui si vuole eseguire il provisioning e quindi fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da AnswerHub per eseguire il provisioning degli account utente di Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad AnswerHub.

![Assegna utente][200] 

**Per assegnare Britta Simon ad AnswerHub, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Autotask**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro AnswerHub nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione AnswerHub.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_203.png



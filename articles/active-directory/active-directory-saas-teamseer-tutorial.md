---
title: 'Esercitazione: Integrazione di Azure Active Directory con TeamSeer | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 2a5e8f6d1443681c43db95da5cef0b7f2ef92291
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Esercitazione: Integrazione di Azure Active Directory con TeamSeer

Questa esercitazione descrive come integrare TeamSeer con Azure Active Directory (Azure AD).

L'integrazione di TeamSeer con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a TeamSeer
- È possibile abilitare gli utenti per l'accesso automatico a TeamSeer (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TeamSeer, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di TeamSeer abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di TeamSeer dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-teamseer-from-the-gallery"></a>Aggiunta di TeamSeer dalla raccolta
Per configurare l'integrazione di TeamSeer in Azure AD, è necessario aggiungere TeamSeer dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere TeamSeer dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **TeamSeer**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. Nel pannello dei risultati selezionare **TeamSeer** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TeamSeer usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di TeamSeer corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TeamSeer.

Per stabilire la relazione di collegamento, in TeamSeer assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con TeamSeer, è necessario completare le procedure di base seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di TeamSeer](#creating-a-teamseer-test-user)**: per avere una controparte di Britta Simon in TeamSeer collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione TeamSeer.

**Per configurare l'accesso Single Sign-On di Azure AD con TeamSeer, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TeamSeer** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. Nella sezione **URL e dominio TeamSeer** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di TeamSeer](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di TeamSeer** fare clic su **Configura TeamSeer** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. In un'altra finestra del Web browser accedere al sito aziendale di TeamSeer come amministratore.

8. Andare ad **Amministratore risorse umane**.
   
    ![Amministratore risorse umane](./media/active-directory-saas-teamseer-tutorial/ic789634.png "Amministratore risorse umane")

9. Fare clic su **Configura**.
   
    ![Installazione](./media/active-directory-saas-teamseer-tutorial/ic789635.png "Installazione")

10. Fare clic su **Configura dettagli del provider SAML**.
   
    ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/ic789636.png "Impostazioni SAML")

11. Nella sezione dei dettagli del provider SAML, eseguire la procedura seguente:
   
    ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/ic789637.png "Impostazioni SAML")   

    a. Incollare il valore dell'**URL del servizio Single Sign-On** nella casella di testo **URL**.
          
    b. Aprire il certificato con codifica Base 64 in Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **IdP Public Certificate** (Certificato pubblico IdP).

12. Per completare la configurazione del provider SAML eseguire la procedura seguente:
    
    ![Impostazioni SAML](./media/active-directory-saas-teamseer-tutorial/ic789638.png "Impostazioni SAML") 

    a. In **Test indirizzi e-mail**, digitare l’indirizzo e-mail dell’utente test. 
  
    b. Nella casella di testo **Autorità di certificazione** digitare l'URL del provider di servizi. 
  
    c. Fare clic su **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-teamseer-test-user"></a>Creazione di un utente di test di TeamSeer

Per consentire agli utenti di Azure AD di accedere a TeamSeer, è necessario effettuarne il provisioning in TeamSeer. Nel caso di TeamSeer, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **TeamSeer** come amministratore.

2. Eseguire la procedura seguente:
   
    ![Amministratore risorse umane](./media/active-directory-saas-teamseer-tutorial/ic789640.png "Amministratore risorse umane")  
 
    a. Accedere a **Amministratore risorse umane \> Utenti**.
  
    b. Fare clic su **Esegui procedura guidata nuovo utente**.

3. Nella sezione **Dettagli utente** eseguire la procedura seguente:
   
    ![Dettagli utente](./media/active-directory-saas-teamseer-tutorial/ic789641.png "Dettagli utente")

    a. Digitare **Nome**, **Cognome** e **Nome utente (indirizzo di posta elettronica)** di un account AAD valido di cui si vuole effettuare il provisioning nelle caselle di testo correlate.
  
    b. Fare clic su **Avanti**.

4. Seguire le istruzioni visualizzate per l'aggiunta di un nuovo utente e fare clic su **Fine**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TeamSeer per eseguire il provisioning degli account utente di Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TeamSeer.

![Assegna utente][200] 

**Per assegnare Britta Simon a TeamSeer, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **TeamSeer**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png



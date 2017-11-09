---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wdesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 37660b80cfb01d6a3105aea5ce248f1e03c46695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Esercitazione: Integrazione di Azure Active Directory con Wdesk

Questa esercitazione descrive come integrare Wdesk con Azure Active Directory (Azure AD).

L'integrazione di Wdesk con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Wdesk
- È possibile abilitare gli utenti per l'accesso automatico a Wdesk (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure Active Directory, vedere: [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Wdesk, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Wdesk abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Wdesk dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-wdesk-from-the-gallery"></a>Aggiunta di Wdesk dalla raccolta
Per configurare l'integrazione di Wdesk in Azure AD, è necessario aggiungere Wdesk dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Wdesk dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Wdesk**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. Nel pannello dei risultati selezionare **Wdesk** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Wdesk con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Wdesk che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Wdesk.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Wdesk.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Wdesk, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Wdesk](#creating-a-wdesk-test-user)**: per avere una controparte di Britta Simon in Wdesk collegata alla relativa rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Wdesk.

**Per configurare l'accesso Single Sign-On di Azure AD con Wdesk, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Wdesk** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. Per configurare l'applicazione in modalità avviata da **IDP**, nella sezione **URL e dominio Wdesk** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

4. Selezionare **Mostra impostazioni URL avanzate** Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si ottengono questi valori dal portale di WDesk quando si configura l'accesso Single Sign-On. 
  
5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. In un'altra finestra del Web browser accedere a Wdesk come amministratore della sicurezza.

8. In basso a sinistra fare clic su **Amministratore** e scegliere **Amministratore dell'account**:
 
     ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. In Wdesk Admin passare a **Security** (Sicurezza) e quindi a **SAML** > **SAML Settings** (Impostazioni SAML):

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. In **General Settings** (Impostazioni generali) selezionare **Enable SAML Single Sign On** (Abilita SAML Single Sign On):

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. In **Service Provider Details** (Dettagli del provider di servizi) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copiare l'**URL di accesso** e incollarlo nella casella di testo **URL di accesso** nel portale di Azure.
   
      b. Copiare l'**URL dei metadati** e incollarlo nella casella di testo **Identificatore** nel portale di Azure.
       
      c. Copiare l'**URL del servizio consumer** e incollarlo nella casella di testo **URL di risposta** nel portale di Azure.
   
      d. Fare clic su **Salva** nel portale di Azure per salvare le modifiche.      

12. Fare clic su **Configure IdP Settings** (Configura impostazioni IdP) per aprire la finestra di dialogo **Edit IdP Settings** (Modifica impostazioni IdP). Fare clic su **Choose File** (Scegli file) per trovare il file **Metadata.xml** salvato dal portale di Azure, quindi caricarlo.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Fare clic su **Salva modifiche**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-wdesk-test-user"></a>Creazione di un utente test di Wdesk

Per consentire agli utenti di Azure AD di accedere a Wdesk, è necessario effettuarne il provisioning in Wdesk. Nel caso di Wdesk il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Wdesk come amministratore della sicurezza.
2. Passare ad **Admin** (Amministratore) > **Account Admin** (Amministratore dell'account).

     ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Fare clic su **Members** (Membri) in **People** (Persone).

4. Fare clic su **Add Member** (Aggiungi membro) per aprire la finestra di dialogo **Add Member** (Aggiungi membro). 
   
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. Nella casella di testo **User** (Utente) immettere il relativo nome utente come **brittasimon@contoso.com** e fare clic sul pulsante **Continue** (Continua).

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Immettere i dettagli come mostrato di seguito:
  
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    b. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    c. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

7. Fare clic sul pulsante **Save Member** (Salva membro).  

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Wdesk.

![Assegna utente][200] 

**Per assegnare Britta Simon a Wdesk, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Wdesk**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Wdesk nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Wdesk.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png


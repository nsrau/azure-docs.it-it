---
title: 'Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 801088bd63f025ca7cb1f9e4fe66b9c6f7f93453
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox

Questa esercitazione descrive come integrare Salesforce Sandbox con Azure Active Directory (Azure AD).

L'integrazione di Salesforce Sandbox con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Salesforce Sandbox
- È possibile abilitare gli utenti per l'accesso automatico a Salesforce Sandbox (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Salesforce Sandbox, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Salesforce Sandbox abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Salesforce Sandbox dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Aggiunta di Salesforce Sandbox dalla raccolta
Per configurare l'integrazione di Salesforce Sandbox in Azure AD, è necessario aggiungere Salesforce Sandbox dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Salesforce Sandbox dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Salesforce Sandbox**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. Nel pannello dei risultati selezionare **Salesforce Sandbox** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione si configura e si testa l'accesso Single Sign-On di Azure AD con Salesforce Sandbox in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Salesforce Sandbox che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Salesforce Sandbox.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Nome utente** in Salesforce Sandbox.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Salesforce Sandbox, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Salesforce Sandbox](#creating-a-salesforce-sandbox-test-user)**: per avere una controparte di Britta Simon in Salesforce Sandbox collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure e si configura l'accesso Single Sign-On nell'applicazione Salesforce Sandbox.

**Per configurare Single Sign-On di Azure AD con Salesforce Sandbox, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Salesforce Sandbox** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Nella sezione **URL e dominio Salesforce Sandbox** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: `https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > Poiché non è reale, Aggiornare questo valore con l'URL di accesso effettivo. Contattare il [team di supporto clienti di Sandbox Salesforce](https://help.salesforce.com/support) per ottenere questo valore.


4. Se l'accesso Single Sign-On è già stato configurato per un'altra istanza di Salesforce Sandbox nella directory, sarà necessario configurare anche l'**identificatore** in modo che abbia lo stesso valore di **URL di accesso**. 
    
    >[!Note]
    >Per trovare il campo **Identificatore**, selezionare la casella di controllo **Impostazioni avanzate** nella pagina **Configura URL app** della finestra di dialogo 


5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di Salesforce Sandbox** fare clic su **Configura Salesforce Sandbox** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Aprire una nuova scheda del browser e accedere all'account di amministratore di Salesforce Sandbox.

9. Nel menu in alto fare clic su **Impostazione**.

    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. Nel riquadro di spostamento a sinistra, fare clic su **Security Controls** (Controlli di sicurezza) e quindi fare clic su **Single Sign-On Settings** (Impostazioni Single Sign-On).

    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. Nella sezione Impostazioni Single Sign-On eseguire la procedura seguente: ![Configurazione dell'accesso Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Selezionare **Abilitato SAML**. 

     b.  Fare clic su **Nuovo**.

12. Nella sezione Impostazioni SAML Single Sign-On, eseguire la procedura seguente:

    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a.Nella casella di testo Nome digitare il nome della configurazione, ad esempio *SPSSOWAAD\_Test*. 

    b. Incollare il valore **SMAL Entity ID** (ID entità SMAL) nella casella di testo **Issuer** (Autorità di certificazione).

    c. Nella casella di testo **ID entità** digitare **https://test.salesforce.com** se è la prima istanza di Salesforce Sandbox aggiunta alla directory. Se esiste già un'istanza di Salesforce Sandbox, in **ID entità** digitare l'**URL di accesso**, che deve avere questo formato: `http://company.my.salesforce.com`  
 
    d. Per caricare il certificato scaricato, fare clic su **Sfoglia** .  

    e. In **SAML Identity Type** (Tipo di identità SAML) selezionare **Assertion contains the Federation ID from the User object** (L'asserzione contiene l'ID federazione dell'oggetto utente).
 
    f. In **SAML Identity Location**(Percorso identità SAML) selezionare **Identity is in the NameIdentifier element of the Subject statement** (L'identità è nell'elemento NameIdentifier dell'istruzione Subject).

    g. Incollare **URL servizio Single Sign-On** nella casella di testo **URL di accesso provider di identità**. 

    h. SFDC non supporta la disconnessione SAML.  Come soluzione alternativa, incollare "https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0" nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità).

    i. In **Service Provider Initiated Request Binding** (Binding richiesta avviato dal provider di servizi) selezionare **HTTP POST**. 

    j. Fare clic su **Save**.

### <a name="enable-your-domain"></a>Abilitare il dominio
In questa sezione si presuppone che sia già stato creato un dominio.  Per altre informazioni, vedere [Definizione del nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Per abilitare il dominio, eseguire la procedura seguente:**

1. Nel riquadro di spostamento a sinistra fare clic su **Domain Management** (Gestione dominio) e quindi su **My Domain** (Dominio personale).
   
     ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Assicurarsi che il dominio sia stato configurato correttamente. 

2. Nella sezione **Login Page Settings** (Impostazioni pagina di accesso) fare clic su **Edit** (Modifica), quindi per **Authentication Service** (Servizio di autenticazione) selezionare il nome dell'impostazione Single Sign-On SAML definita nella sezione precedente e fare clic su **Save** (Salva).
   
   ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

Non appena si dispone di un dominio configurato, gli utenti devono utilizzare l'URL del dominio per l'accesso a Salesforce Sandbox.  

Per ottenere il valore dell'URL, fare clic sul profilo SSO creato nella sezione precedente.    

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Creazione di un utente test di Salesforce Sandbox

In questa sezione si crea un utente di nome Britta Simon in Salesforce Sandbox. Salesforce Sandbox supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita.
Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Salesforce Sandbox, ne viene creato uno nuovo quando si tenta di accedere a Salesforce Sandbox.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Salesforce Sandbox.

![Assegna utente][200] 

**Per assegnare Britta Simon a Salesforce Sandbox, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Salesforce Sandbox**.

    ![Configure Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configura provisioning utenti](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png


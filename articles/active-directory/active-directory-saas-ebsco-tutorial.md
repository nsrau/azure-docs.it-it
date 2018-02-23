---
title: 'Esercitazione: Integrazione di Azure Active Directory con EBSCO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory ed EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: ea7fe09c31d88cf2095b3a3777b6b1f9feb8df46
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Esercitazione: Integrazione di Azure Active Directory con EBSCO

Questa esercitazione descrive come integrare EBSCO con Azure Active Directory (Azure AD).

L'integrazione di EBSCO con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a EBSCO.
- È possibile abilitare gli utenti per l'accesso automatico a EBSCO (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con EBSCO, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di EBSCO abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di EBSCO dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-ebsco-from-the-gallery"></a>Aggiunta di EBSCO dalla raccolta
Per configurare l'integrazione di EBSCO in Azure AD, è necessario aggiungere EBSCO dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere EBSCO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **EBSCO**, selezionare **EBSCO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![EBSCO nell'elenco risultati](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con EBSCO con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è la controparte in EBSCO di un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in EBSCO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con EBSCO, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di EBSCO](#create-an-ebsco-test-user)**: per automatizzare la personalizzazione o il provisioning utenti EBSCOhost. EBSCO supporta il provisioning utenti JIT (Just-In-Time).
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione EBSCO.

**Per configurare l'accesso Single Sign-On di Azure AD con EBSCO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **EBSCO** nel portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. Nella sezione **URL e dominio EBSCO** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni Single Sign-On per URL e dominio di EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url.png)

    Nella casella di testo **Identificatore** digitare un URL: `pingsso.ebscohost.com`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni Single Sign-On per URL e dominio di EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > Poiché il valore dell'URL di accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti EBSCO](mailto:sso@ebsco.com). 

    o   **Elementi univoci:**  

    o   **Custid** = Immettere l'ID cliente EBSCO univoco 

    o   **Profile** = I clienti possono personalizzare il collegamento in modo da indirizzare gli utenti a uno profilo specifico, in base al prodotto acquistato da EBSCO. È possibile immettere un ID di profilo specifico. I principali ID sono eds (EBSCO Discovery Service) ed ehost (database EBSCOhost). Istruzioni specifiche sull'argomento sono disponibili in [questa pagina](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

5. L'applicazione EBSCO prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configure Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > L'attributo **name** è obbligatorio ed è mappato a **User Identifier** (Identificatore utente) nell'applicazione EBSCO. Viene aggiunto per impostazione predefinita e non è quindi necessario inserirlo manualmente.
    
6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **Ok**

7. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-ebsco-tutorial/tutorial_general_400.png)
    
9. Per configurare l'accesso Single Sign-On sul lato **EBSCO**, è necessario inviare il file **XML metadati** scaricato al [team di supporto EBSCO](mailto:sso@ebsco.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-ebsco-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-ebsco-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-ebsco-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-ebsco-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-ebsco-test-user"></a>Creare un utente di test di EBSCO

Nel caso di EBSCO, il provisioning utenti viene eseguito automaticamente.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

Azure AD passa i dati necessari all'applicazione EBSCO. Il provisioning utenti di EBSCO può essere eseguito automaticamente o richiedere la compilazione di un modulo unico, a seconda che il cliente abbia o meno molti account EBSCOhost preesistenti con impostazioni personali salvate. Questo aspetto può essere esaminato con il [team di supporto EBSCO](mailto:sso@ebsco.com) durante l'implementazione. In entrambi i casi, il cliente non deve creare alcun account EBSCOhost prima del test.

   >[!Note]
   >È possibile automatizzare la personalizzazione o il provisioning utenti EBSCOhost. Per informazioni sul provisioning utenti JIT (Just-In-Time), contattare il [team di supporto EBSCO](mailto:sso@ebsco.com). 
 
### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a EBSCO.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a EBSCO, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **EBSCO**.

    ![Collegamento di EBSCO nell'elenco delle applicazioni](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

1. Quando si fa clic sul riquadro EBSCO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione EBSCO.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

2. Dopo aver eseguito l'accesso all'applicazione, fare clic sul pulsante **Sign In** (Accedi) nell'angolo in alto a destra.

    ![Accesso a EBSCO nell'elenco delle applicazioni](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Si riceverà un messaggio unico in cui viene richiesto di associare i dati di accesso istituzionali/SAML con l'opzione **Link your existing MyEBSCOhost account to your institution account now** (Collega l'account MyEBSCOhost esistente all'account dell'istituto ora) oppure **Create a new MyEBSCOhost account and link it to your institution account** (Crea un nuovo account MyEBSCOhost e collegalo all'account dell'istituto). L'account viene usato per la personalizzazione nell'applicazione EBSCOhost. Selezionare l'opzione per **creare un nuovo account** e si noterà che il modulo per la personalizzazione è precompilato con i valori della risposta SAML, come illustrato nella schermata seguente. Fare clic su "**Continue**" (Continua) per salvare la selezione.
    
     ![Utente EBSCO nell'elenco delle applicazioni](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_user.png)

4. Dopo aver completato la configurazione precedente, cancellare nuovamente i cookie, la cache e i dati di accesso. Le impostazioni di personalizzazione vengono memorizzate e non sarà necessario eseguire nuovamente l'accesso manuale.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_203.png


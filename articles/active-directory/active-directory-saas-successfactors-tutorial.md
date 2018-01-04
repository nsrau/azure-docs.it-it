---
title: 'Esercitazione: Integrazione di Azure Active Directory con SuccessFactors | Microsoft Docs'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: b9545599b4ac02927c38931777a3cee623a4e940
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Esercitazione: Integrazione di Azure Active Directory con SuccessFactors

In questa esercitazione informazioni su come integrare SuccessFactors con Azure Active Directory (Azure AD).

L'integrazione di SuccessFactors con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso a SuccessFactors.
- È possibile consentire agli utenti di automaticamente ottenere firmato a SuccessFactors (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SuccessFactors, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Un SuccessFactors single sign-sottoscrizione abilitata

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SuccessFactors dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-successfactors-from-the-gallery"></a>Aggiunta di SuccessFactors dalla raccolta
Per configurare l'integrazione di SuccessFactors in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SuccessFactors dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **SuccessFactors**selezionare **SuccessFactors** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere l'applicazione.

    ![Nell'elenco dei risultati SuccessFactors](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con SuccessFactors in base a un utente di test denominato "Laura Giussani".

Per single sign-on a funzionare, Azure AD deve conoscere l'utente corrispondente in SuccessFactors a un utente in Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SuccessFactors.

In SuccessFactors, assegnare il valore della **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SuccessFactors, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test SuccessFactors](#create-a-successfactors-test-user)**  - disporre di un equivalente di Britta Simon in SuccessFactors collegato per la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione SuccessFactors.

**Per configurare l'accesso Single Sign-On di Azure AD con SuccessFactors, seguire questa procedura:**

1. Nel portale di Azure, sul **SuccessFactors** pagina di integrazione dell'applicazione, fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. Nel **SuccessFactors dominio e gli URL** sezione, eseguire la procedura seguente:

    ![Dominio di SuccessFactors e gli URL single sign-on, informazioni](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Contatto [team di supporto di SuccessFactors Client](https://www.successfactors.com/en_us/support.html) per ottenere questi valori. 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. Nel **SuccessFactors configurazione** fare clic su **configurare SuccessFactors** per aprire **Configura sign-on** finestra. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configure Single Sign-On](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. In una finestra del web browser, accedere al **il portale di amministrazione di SuccessFactors** come amministratore.
    
8. Vedere **Sicurezza applicazione** e **Single Sign On Feature** (Funzionalità di accesso Single Sign-On). 

9. Inserire un valore qualsiasi in **Reset Token** (Reimposta token) e fare clic su **Save Token** (Salva token) per abilitare SSO SAML.
   
    ![Configurazione dell'accesso Single Sign-On sul lato app][11]

    > [!NOTE] 
    > Questo valore viene utilizzato come l'opzione di attivazione/disattivazione. Se viene salvato un valore, SSO SAML è ON. Se viene salvato un valore vuoto, SSO SAML è OFF.

10. Modalità nativa alla seguente schermata ed eseguire le azioni seguenti:
   
    ![Configurazione dell'accesso Single Sign-On sul lato app][12]
   
    a. Selezionare il pulsante di opzione **SAML v2 SSO** (SSO SAML v2)
   
    b. Impostare il **nome dell'entità oggetto dell'asserzione SAML**(ad esempio, autorità di certificazione SAML + nome società).
   
    c. Nel **URL autorità di certificazione** casella di testo, incollare il **ID entità SAML** valore a cui è stato copiato dal portale di Azure.
   
    d. Selezionare **Response(Customer Generated/IdP/AP)** (Risposta - Generata da cliente/IdP/AP) per **Require Mandatory Signature** (Richiedi firma obbligatoria).
   
    e. Selezionare **Abilitato** per **Enable SAML Flag** (Abilita flag SAML).
   
    f. Selezionare **No** per **Login Request Signature(SF Generated/SP/RP)** (Firma richiesta di accesso - Generata da SF/SP/RP).
   
    g. Selezionare **Browser/Post Profile** (Profilo browser/post) per **SAML Profile** (Profilo SAML).
   
    h. Selezionare **No** per **Enforce Certificate Valid Period** (Applicare periodo di validità certificato).
   
    i. Copiare il contenuto del file di certificato scaricato dal portale di Azure e quindi incollarlo nella **certificato di verifica SAML** casella di testo.

    > [!NOTE] 
    > Il contenuto del certificato deve avere i tag di inizio e fine certificato.

11. Passare a SAML V2 e seguire questa procedura:
   
    ![Configurazione dell'accesso Single Sign-On sul lato app][13]
   
    a. Selezionare **Sì** per **Support SP-initiated Global Logout** (Supporto disconnessione globale avviata da SP).
   
    b. Nel **globale Logout URL del servizio (destinazione LogoutRequest)** casella di testo, incollare il **Sign-Out URL** valore a cui è stato copiato formano il portale di Azure.
   
    c. Selezionare **No** per **Require sp must encrypt all NameID element** (Richiesta a sp di crittografare tutti gli elementi NameID).
   
    d. Selezionare **non specificato** per **NameID Format** (Formato NameID).
   
    e. Selezionare **Sì** per **Enable sp initiated login (AuthnRequest)** (Abilita accesso avviato da sp - AuthnRequest).
   
    f. Nel **richiesta di invio da autorità di certificazione aziendale** casella di testo, incollare **SAML Single Sign-On Service URL** valore a cui è stato copiato dal portale di Azure.

12. Eseguire questi passaggi se si desidera rendere i nomi di account di accesso utente tra maiuscole e minuscole.
   
    ![Configure Single Sign-On][29]
    
    a. Vedere **Impostazioni società** in basso.
   
    b. Selezionare la casella di controllo accanto alla **Enable Non-Case-Sensitive Username**(Abilita nome utente senza distinzione maiuscole/minuscole).
   
    c.Fare clic su **Save**(Salva).
   
    > [!NOTE] 
    > Se si tenta di abilitare questa opzione, il sistema verifica se viene creato un nome di account di accesso SAML duplicato. Ad esempio, se il cliente ha nomi utente Utente1 e utente1. L'annullamento della distinzione maiuscole/minuscole crea questi duplicati. Il sistema consente un messaggio di errore e non abilitare la funzionalità. Il cliente deve modificare uno dei nomi utente in modo che sia stato digitato diverso.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-successfactors-test-user"></a>Creare un utente test SuccessFactors

Per consentire agli utenti di Azure AD di accedere a SuccessFactors, è necessario eseguirne il provisioning in SuccessFactors.  
Nel caso di SuccessFactors, il provisioning è un'attività manuale.

Per creare utenti in SuccessFactors, è necessario contattare il [team di supporto di SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare Britta Simon utilizzare single sign-on Azure concedendo l'accesso a SuccessFactors.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SuccessFactors, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni, selezionare **SuccessFactors**.

    ![Il collegamento di SuccessFactors nell'elenco delle applicazioni](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SuccessFactors nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione SuccessFactors.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png


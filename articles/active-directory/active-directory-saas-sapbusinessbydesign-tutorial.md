---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ab76a0ac1ef954efd3c66e6f565514b889ed9444
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign

Questa esercitazione descrive come integrare SAP Business ByDesign con Azure Active Directory (Azure AD).

L'integrazione di SAP Business ByDesign con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SAP Business ByDesign.
- È possibile abilitare gli utenti per l'accesso automatico a SAP Business ByDesign (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Business ByDesign, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di SAP Business ByDesign abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP Business ByDesign dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Aggiunta di SAP Business ByDesign dalla raccolta
Per configurare l'integrazione di SAP Business ByDesign in Azure AD, è necessario aggiungere SAP Business ByDesign dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP Business ByDesign dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SAP Business ByDesign**, selezionare **SAP Business ByDesign** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SAP Business ByDesign nell'elenco risultati](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Business ByDesign con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP Business ByDesign che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Business ByDesign.

Per stabilire la relazione di collegamento, in SAP Business ByDesign assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Business ByDesign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**: per avere una controparte di Britta Simon in SAP Business ByDesign collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP Business ByDesign.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP Business ByDesign, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP Business ByDesign** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. Nella sezione **URL e dominio SAP Business ByDesign** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<servername>.sapbydesign.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html).

4. Nella sezione **Attributi utente** seguire questa procedura:

    ![Sezione degli attributi di SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. Nell'elenco **Identificatore utente** selezionare la funzione **ExtractMailPrefix()**.
    
    b. Nell'elenco **Posta** selezionare l'attributo utente da usare per l'implementazione. Ad esempio, se si vuole usare il valore EmployeeID come identificatore utente univoco e il valore dell'attributo è stato archiviato in ExtensionAttribute2, selezionare user.extensionattribute2.     

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di SAP Business ByDesign** fare clic su **Configura SAP Business ByDesign** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione di SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Per ottenere l'accesso SSO configurato per l'applicazione, seguire questa procedura:
   
    a. Accedere al portale di SAP Business ByDesign con diritti di amministratore.
   
    b. Passare a **Application and User Management Common Task** (Attività comuni di gestione utenti e applicazioni) e fare clic sulla scheda **Identity Provider** (Provider di identità).
   
    c. Fare clic su **New Identity Provider** (Nuovo provider di identità) e selezionare il file XML metadati scaricato dal portale di Azure. Quando si importano i metadati, il sistema carica automaticamente il certificato di firma e il certificato di crittografia necessari.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Per includere l'**URL del servizio consumer di asserzione** nella richiesta SAML, selezionare **Include Assertion Consumer Service URL** (Includi URL del servizio Consumer di asserzione).
   
    e. Fare clic su **Activate Single Sign-On**(Attiva Single Sign-On).
   
    f. Salvare le modifiche.
   
    g. Fare clic sulla scheda **My System** (Sistema locale).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Incollare l'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure nella casella di testo **Single Sign On URL** (URL di accesso Azure AD).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Specificare se il dipendente può scegliere manualmente tra l'accesso con ID utente e password o SSO selezionando **Manual Identity Provider Selection**(Selezione manuale provider di identità).
   
    j. Nella sezione **SSO URL** (URL SSO), specificare l'URL che deve essere usato dai dipendenti per l'accesso al sistema. 
    Nell'elenco a discesa URL Sent to Employee (URL inviato al dipendente) è possibile scegliere tra le opzioni seguenti:
   
    **Non-SSO URL**
   
    Il sistema invia al dipendente solo il normale URL di sistema. Il dipendente non può accedere con SSO e deve usare invece la password o il certificato.
   
    **SSO URL** 
   
    Il sistema invia al dipendente solo l'URL SSO. Il dipendente può accedere con SSO. La richiesta di autenticazione viene reindirizzata tramite IdP.
   
    **Automatic Selection**
   
    Se SSO non è attivo, il sistema invia al dipendente il normale URL di sistema. Se SSO è attivo, il sistema verifica se il dipendente ha una password. Se è disponibile una password, vengono inviati al dipendente sia l'URL SSO che l'URL non SSO. Tuttavia, se il dipendente non ha una password, riceverà solo l'URL SSO.
   
    k. Salvare le modifiche.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Creare un utente di test di SAP Business ByDesign

In questa sezione viene creato un utente di nome Britta Simon in SAP Business ByDesign. Collaborare con il [team di supporto clienti di SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) per aggiungere gli utenti nella piattaforma SAP Business ByDesign. 

> [!NOTE]
> Assicurarsi che il valore di NameID corrisponda al campo username nella piattaforma SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SAP Business ByDesign.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SAP Business ByDesign, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **SAP Business ByDesign**.

    ![Collegamento di SAP Business ByDesign nell'elenco delle applicazioni](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Business ByDesign nel pannello di accesso, si accederà automaticamente all'applicazione SAP Business ByDesign.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png



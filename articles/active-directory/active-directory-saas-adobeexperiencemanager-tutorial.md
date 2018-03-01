---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: c366e314b77cd3344a90826b22b96a45e35b0b4e
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager

Questa esercitazione descrive come integrare Adobe Experience Manager con Azure Active Directory (Azure AD).

L'integrazione di Adobe Experience Manager con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Adobe Experience Manager.
- È possibile abilitare gli utenti per l'accesso automatico ad Adobe Experience Manager con i rispettivi account di Azure AD.
- Gli account possono essere gestiti da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Experience Manager, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Adobe Experience Manager abilitata per Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Adobe Experience Manager dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Aggiungere Adobe Experience Manager dalla raccolta
Per configurare l'integrazione di Adobe Experience Manager in Azure AD, è necessario aggiungere Adobe Experience Manager dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Adobe Experience Manager dalla raccolta, eseguire questa procedura:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'icona **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Adobe Experience Manager**. Selezionare **Adobe Experience Manager** nel pannello dei risultati e quindi scegliere **Aggiungi** per aggiungere l'applicazione.

    ![Adobe Experience Manager nell'elenco dei risultati](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Experience Manager usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On per un utente di Azure AD, Azure AD deve conoscere l'utente corrispondente in Adobe Experience Manager. In altre parole, deve essere stabilito un collegamento tra un utente di Azure AD e l'utente correlato in Adobe Experience Manager.

In Adobe Experience Manager, assegnare al valore del **nome utente** lo stesso valore del **nome utente** in Azure AD. Questo passaggio stabilisce il collegamento tra i due utenti. 

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Experience Manager, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di Adobe Experience Manager](#create-an-adobe-experience-manager-test-user) per avere una controparte di Britta Simon in Adobe Experience Manager collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adobe Experience Manager.

**Per configurare l'accesso Single Sign-On di Azure AD con Adobe Experience Manager, eseguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Adobe Experience Manager** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Per abilitare l'accesso Single Sign-On, nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** nel menu a discesa **Modalità**.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Nella sezione **URL e dominio Adobe Experience Manager**, se si vuole configurare l'app in modalità **IDP** eseguire la procedura seguente:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Nella casella **Identificatore** digitare un valore univoco definito anche nel server Adobe Experience Manager. 

    b. Nella casella **URL di risposta** digitare un URL nel formato seguente: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html).
 
4. Selezionare **Mostra impostazioni URL avanzate** Se si vuole configurare l'applicazione in modalità avviata da **SP**, eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Nella casella **URL di accesso** digitare l'URL del server Adobe Experience Manager. 

5. Nella sezione **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Per aprire la finestra di configurazione dell'accesso nella sezione Configurazione di Adobe Experience Manager selezionare **Configura Adobe Experience Manager**. Copiare i valori di **URL servizio Single Sign-On**, **ID entità** e **URL di disconnessione** SAML dalla sezione di riferimento rapido.

    ![Collegamento alla sezione di configurazione](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Selezionare **Salva**.

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. In un'altra finestra del browser, aprire il portale di amministrazione di **Adobe Experience Manager**.

9. Selezionare **Settings** (Impostazioni) > **Security** (Sicurezza) > **Users** (Utenti).

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Selezionare **Administrator** (Amministratore) o un altro utente rilevante.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Selezionare **Account Settings** (Impostazioni account) > **Manage TrustStore** (Gestisci archivio trust).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. In **Add Certificate from CER file** (Aggiungi certificato da file CER), fare clic su **Select Certificate File** (Seleziona file certificato). Individuare e selezionare il file di certificato, scaricato in precedenza dal portale di Azure.

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Il certificato viene aggiunto all'archivio trust. Notare l'alias del certificato.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Nella pagina **Users** (Utenti) selezionare **authentication-service**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Selezionare **Account Settings** (Impostazioni account) > **Create/Manage KeyStore** (Crea/Gestisci archivio chiavi). Creare l'archivio chiavi specificando una password.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Tornare alla schermata di amministrazione e quindi selezionare **Settings** (Impostazioni) > **Operations** (Operazioni) > **Web Console** (Console Web).

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Viene aperta la pagina di configurazione.

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Cercare **Adobe Granite SAML 2.0 Authentication Handler** e quindi selezionare l'icona **Aggiungi**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. In questa pagina eseguire le azioni seguenti.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Nella casella **Path** (Percorso) immettere  **/** .

    b. Nella casella **IDP URL** (URL IDP) incollare il valore **URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Nella casella **IDP Certificate Alias** (Alias certificato IDP) immettere il valore di **Certificate Alias** (Alias certificato) aggiunto nell'archivio trust.

    d. Nella casella **Security Provided Entity ID** (ID entità provider di servizi) immettere il valore **ID entità SAML** univoco configurato nel portale di Azure.

    e. Nella casella **Assertion Consumer Service URL** (URL del servizio consumer di asserzione) immettere il valore di **URL di risposta** configurato nel portale di Azure.

    f. Nella casella **Password of Key Store** (Password archivio chiavi) immettere la **Password** impostata nell'archivio chiavi.

    g. Nella casella **User Attribute ID** (ID attributo utente) immettere l'**ID nome** o un altro ID utente pertinente.

    h. Selezionare **Autocreate CRX Users** (Creazione automatica utenti CRX).

    i. Nella casella **Logout URL** (URL di disconnessione) incollare il valore **URL di disconnessione** ottenuto dal portale di Azure.

    j. Selezionare **Salva**.

> [!TIP]
> Un riepilogo di queste istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali**, selezionare la scheda **Single Sign-On**. Accedere quindi alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni sulla funzione di documentazione incorporata, vedere la [documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD, seguire questa procedura:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password**. Prendere quindi nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Creare un utente di test di Adobe Experience Manager

In questa sezione viene creato un utente di nome Britta Simon in Adobe Experience Manager. Se è stata selezionata l'opzione **Autocreate CRX Users** (Creazione automatica utenti CRX), gli utenti vengono creati automaticamente dopo l'autenticazione. 

Se si vogliono creare utenti manualmente, collaborare con il [team di supporto di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) per aggiungere gli utenti nella piattaforma Adobe Experience Manager. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso ad Adobe Experience Manager tramite concessione dell'accesso Single Sign-On di Azure.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Adobe Experience Manager, eseguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione applicazioni. Passare quindi alla visualizzazione directory, accedere ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Adobe Experience Manager**.

    ![Collegamento di Adobe Experience Manager nell'elenco delle applicazioni](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Users and groups** (Utenti e gruppi) selezionare **Britta Simon** nell'elenco di utenti.

6. Nella finestra di dialogo **Utenti e gruppi** fare clic sul pulsante **Seleziona**.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro Adobe Experience Manager nel pannello di accesso, dovrebbe essere possibile accedere automaticamente all'applicazione Adobe Experience Manager.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png


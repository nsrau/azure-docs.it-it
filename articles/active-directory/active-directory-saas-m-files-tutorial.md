---
title: 'Esercitazione: Integrazione di Azure Active Directory con M-Files | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e M-Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 0f2682cf7cd3e11a5a7156938fbe9d4c7f541312
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Esercitazione: Integrazione di Azure Active Directory con M-Files

Questa esercitazione descrive come integrare M-Files con Azure Active Directory (Azure AD).

L'integrazione di M-Files con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a M-Files
- È possibile abilitare gli utenti per l'accesso automatico a M-Files (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con M-Files, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di M-Files abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di M-Files dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-m-files-from-the-gallery"></a>Aggiunta di M-Files dalla raccolta
Per configurare l'integrazione di M-Files in Azure AD, è necessario aggiungere M-Files dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere M-Files dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **M-Files**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_search.png)

5. Nel pannello dei risultati selezionare **M-Files** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con M-Files usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente controparte di M-Files che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in M-Files.

Per stabilire la relazione di collegamento, in M-Files assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con M-Files, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di M-Files](#creating-a-m-files-test-user)**: per avere una controparte di Britta Simon in M-Files collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione M-Files.

**Per configurare l'accesso Single Sign-On di Azure AD con M-Files, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **M-Files** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_samlbase.png)

3. Nella sezione **URL e dominio M-Files** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto client di M-Files](mailto:support@m-files.com). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_general_400.png)

6. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il [team di supporto di M-Files](mailto:support@m-files.com) e fornire i metadati scaricati.
   
    >[!NOTE]
    >Se si desidera configurare l'accesso Single Sign-On per l'applicazione desktop M-Files, attenersi alla procedura seguente. Non sono necessarie operazioni aggiuntive se si desidera configurare l'accesso Single Sign-On per la versione Web di M-Files.  

7. Per configurare l'applicazione desktop M-Files in modo da abilitare l'accesso Single Sign-On con Azure AD attenersi alla procedura seguente. Per scaricare M-Files, passare alla pagina di [download di M-Files](https://www.m-files.com/en/download-latest-version).

8. Aprire la finestra **M-Files Desktop Settings** (Impostazioni M-Files Desktop). Fare quindi clic su **Aggiungi**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)

9. Nella finestra **Document Vault Connection Properties** (Proprietà connessione insieme di credenziali del documento) eseguire le seguenti operazioni:
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  

    Nella sezione Server digitare i valori seguenti:  

    a. Per **Nome** digitare `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Per **Numero porta** digitare **4466**. 

    c. Per **Protocollo** selezionare **HTTPS**. 

    d. Nel campo **Autenticazione** selezionare l'opzione relativa **Specific Windows user** (Utente specifico di Windows). Verrà visualizzata una pagina di accesso. Inserire le credenziali di Azure AD. 

    e. Per **Vault on Server** (Insieme di credenziali nel server) selezionare l'insieme di credenziali corrispondente nel server.
 
    f. Fare clic su **OK**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-m-files-test-user"></a>Creazione di un utente di test per M-Files

Questa sezione descrive come creare un utente chiamato Britta Simon in M-Files. Collaborare con il [team di supporto M-Files](mailto:support@m-files.com) per aggiungere gli utenti in M-Files.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a M-Files.

![Assegna utente][200] 

**Per assegnare Britta Simon a M-Files, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **M-Files**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro M-Files nel pannello di accesso, si accederà automaticamente all'applicazione M-Files.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png



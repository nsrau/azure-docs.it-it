---
title: 'Esercitazione: Integrazione di Azure Active Directory con QPrism | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e QPrism.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: 1f697b95074e0fc9dbb3e8c7800e69f8ece9e0b3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Esercitazione: Integrazione di Azure Active Directory con QPrism

Questa esercitazione descrive come integrare QPrism con Azure Active Directory (Azure AD).

L'integrazione di QPrism con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a QPrism.
- È possibile abilitare gli utenti per l'accesso automatico a QPrism (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre dettagli sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con QPrism, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per l'accesso Single-Sign On a QPrism

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di QPrism dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-qprism-from-the-gallery"></a>Aggiungere QPrism dalla raccolta
Per configurare l'integrazione di QPrism in Azure AD, è necessario aggiungere QPrism dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere QPrism dalla raccolta:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **QPrism** e selezionare **QPrism** nel pannello dei risultati. Fare quindi clic su **Aggiungi** per aggiungere l'applicazione.

    ![QPrism nell'elenco risultati](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con QPrism con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di QPrism che corrisponde a un utente di Azure AD. In altre parole, deve esistere una relazione di collegamento tra un utente di Azure AD e l'utente correlato in QPrism.

Per stabilire questa relazione, in QPrism, assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con QPrism, completare i blocchi predefiniti seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
2. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. [Creare un utente di test di QPrism](#create-a-qprism-test-user) per avere una controparte di Britta Simon in QPrism collegata alla rappresentazione dell'utente in Azure AD.
4. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione QPrism.

1. Nella pagina di integrazione dell'applicazione **QPrism** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. Nella sezione **URL e dominio QPrism** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. Nella casella di testo **URL accesso** digitare un URL che usa il modello seguente: `https://<customer domain>.qmyzone.com/login`

    b. Nella casella di testo **Identificatore** digitare l'URL che usa il modello seguente:`https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto di QPrism](mailto:qsupport-ce@quatrro.com). 

4. Per generare l'**URL dei metadati**, seguire questa procedura:

    a. Selezionare **Registrazioni per l'app**.
    
    ![Configurare le registrazioni per l'app per Single Sign-On](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. Selezionare **Endpoint** per aprire la finestra di dialogo **Endpoint**.  
    
    ![Configurare l'endpoint per l'accesso Single Sign-On](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. Selezionare il pulsante Copia per copiare l'URL del **DOCUMENTO METADATI FEDERAZIONE** e incollarlo nel Blocco note.
    
    ![Configurare l'endpoint per l'accesso Single Sign-On](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. Passare ora alla pagina delle proprietà di **QPrism** e copiare l'**ID applicazione** usando **Copia**. Incollarlo quindi nel Blocco note.
 
    ![Configurare l'ID applicazione per l'accesso Single Sign-On](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. Generare l'**URL dei metadati** usando il modello seguente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. Selezionare **Salva**.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Per configurare l'accesso Single Sign-On sul lato **QPrism**, inviare l'**URL dei metadati** al [team di supporto di QPrism](mailto:qsupport-ce@quatrro.com), che garantisce che la connessione Single Sign-On SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente di test in Azure AD:**

1. Nel portale di Azure fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** selezionare **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-qprism-test-user"></a>Creare un utente test QPrism

In questa sezione viene creato un utente di nome Britta Simon in QPrism. Collaborare con il [team di supporto di QPrism](mailto:qsupport-ce@quatrro.com) per aggiungere gli utenti alla piattaforma QPrism. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a QPrism.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a QPrism:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi passare alla visualizzazione delle directory. Passare ad **Applicazioni aziendali** e selezionare **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **QPrism**.

    ![Collegamento QPrism nell'elenco delle applicazioni](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Selezionare **Aggiungi**. In **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Nella finestra di dialogo **Utenti e gruppi** selezionare **Seleziona**.

7. In **Aggiungi assegnazione** selezionare **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro QPrism nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione QPrism.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png


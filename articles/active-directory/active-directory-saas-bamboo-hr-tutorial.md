---
title: 'Esercitazione: Integrazione di Azure Active Directory con BambooHR | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 081144a645683d4d00ed0d464e23558378dc1b38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Esercitazione: Integrazione di Azure Active Directory con BambooHR

Questa esercitazione descrive come integrare BambooHR con Azure Active Directory (Azure AD).

L'integrazione di BambooHR con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a BambooHR.
- È possibile abilitare gli utenti per l'accesso Single Sign-On automatico a BambooHR con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con BambooHR, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di BambooHR abilitata per l'accesso Single Sign-On

> [!NOTE]
> Per testare i passaggi di questa esercitazione, non è consigliabile usare un ambiente di produzione.

A questo scopo, seguire queste indicazioni:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

Lo scenario descritto in questa esercitazione prevede le due procedure di base seguenti:

1. Aggiunta di BambooHR dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="add-bamboohr-from-the-gallery"></a>Aggiunta di BambooHR dalla raccolta
Per configurare l'integrazione di BambooHR in Azure AD, è necessario aggiungere BambooHR dalla raccolta all'elenco di app SaaS gestite seguendo questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali][2]
    
3. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.

    ![Pulsante "Nuova applicazione"][3]

4. Nella casella di ricerca digitare **BambooHR**. Nell'elenco dei risultati selezionare **BambooHR** e quindi fare clic su **Aggiungi**.

    ![BambooHR nell'elenco risultati](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con BambooHR usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte in BambooHR. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BambooHR.

Per stabilire la relazione di collegamento in BambooHR, assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente) di BambooHR.

Per configurare e testare l'accesso Single Sign-On di Azure AD con BambooHR, completare le procedure di base nelle cinque sezioni successive.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione BambooHR seguendo questa procedura:

1. Nella pagina di integrazione dell'applicazione **BambooHR** del portale di Azure selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nell'elenco a discesa **Modalità** della finestra **Single Sign-On** selezionare **Accesso basato su SAML**.
 
    ![Finestra Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. In **URL e dominio BambooHR** seguire questa procedura:

    ![Sezione URL e dominio BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. Nella casella **URL accesso** digitare un URL nel formato seguente: `https://<company>.bamboohr.com`.

    b. Nella casella **Identificatore** digitare un valore `BambooHR-SAML`.

    > [!NOTE] 
    > Poiché il valore **URL accesso** non è reale, aggiornarlo con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di BambooHR](https://www.bamboohr.com/contact.php). 
 
4. In **Certificato di firma SAML** selezionare **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Selezionare **Salva**.

    ![Pulsante Salva](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. In **Configurazione di BambooHR** selezionare **Configura BambooHR** per aprire la finestra **Configura accesso**. Nella sezione **Riferimento rapido** copiare l'**URL del servizio Single Sign-On SAML** per usarlo in seguito.

    ![Configurazione di BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. In una nuova finestra accedere al sito aziendale di BambooHR come amministratore.

8. Nella home page seguire questa procedura:
   
    ![Pagina Single Sign-On di BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Single Sign-On")   

    a. Selezionare **Apps** (App).
   
    b. Nel riquadro **Apps** (App) selezionare **Single Sign-On**.
   
    c. Selezionare **SAML Single Sign-On** (Single Sign-On SAML).

9. Nel riquadro **SAML Single Sign-On** (Single Sign-On SAML) seguire questa procedura:
   
    ![Riquadro SAML Single Sign-On (Single Sign-On SAML)](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On (Single Sign-On SAML)")
   
    a. Nella casella **SSO Login URL** (URL di accesso SSO) incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure nel passaggio 6.
      
    b. Nel Blocco note aprire il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **X.509 Certificate** (Certificato X.509).
   
    c. Selezionare **Salva**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile nel [portale di Azure](https://portal.azure.com) durante la configurazione dell'app. Dopo aver aggiunto l'app dalla sezione **Active Directory** > **Applicazioni aziendali** è sufficiente selezionare la scheda **Single Sign-On** e quindi accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Per altre informazioni, vedere la [documentazione incorporata su Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

L'obiettivo di questa sezione consiste nel creare l'utente di test "Britta Simon" nel portale di Azure.

   ![Creare l'utente di test di Azure AD Britta Simon][100]

Per creare un utente di test in Azure AD, seguire questa procedura:

1. Nel portale di Azure fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. Nella parte superiore del riquadro **Tutti gli utenti** selezionare **Aggiungi**.

    ![Pulsante Aggiungi](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. Nella finestra **Utente** seguire questa procedura:

    ![Finestra Utente](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.
 
### <a name="create-a-bamboohr-test-user"></a>Creare un utente di test di BambooHR

Per consentire agli utenti di Azure AD di accedere a BambooHR, impostarli manualmente in BambooHR seguendo questa procedura:

1. Accedere al sito di **BambooHR** come amministratore.

2. Nella barra degli strumenti superiore selezionare **Impostazioni**.
   
    ![Pulsante Impostazioni](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Impostazioni")

3. Selezionare **Panoramica**.

4. Nel riquadro di sinistra selezionare **Sicurezza** > **Utenti**.

5. Digitare il nome utente, la password e l'indirizzo di posta elettronica dell'account di Azure AD valido che si vuole configurare.

6. Selezionare **Salva**.
        
>[!NOTE]
>Per configurare account utente di Azure AD è possibile anche usare le API o gli strumenti di creazione di account utente di BambooHR.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

Abilitare l'utente Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BambooHR.

![Assegnare il ruolo utente][200] 

Per assegnare Britta Simon a BambooHR, seguire questa procedura:

1. Nel portale di Azure aprire la visualizzazione dell'applicazione, passare alla visualizzazione della directory, selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco **Applicazioni aziendali** selezionare **BambooHR**.

    ![Collegamento di BambooHR nell'elenco Applicazioni aziendali](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. Nel riquadro sinistro fare clic su **Utenti e gruppi**.

    ![Collegamento "Utenti e gruppi"][202]

4. Selezionare il pulsante **Aggiungi** e quindi, nel riquadro **Aggiungi assegnazione**, selezionare **Utenti e gruppi**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti**.

6. Fare clic sul pulsante **Seleziona**.

7. Nella finestra **Aggiungi assegnazione** selezionare il pulsante **Assegna**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro **BambooHR** nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione BambooHR.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sull'integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png


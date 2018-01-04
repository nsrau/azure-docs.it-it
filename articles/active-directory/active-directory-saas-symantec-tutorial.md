---
title: 'Esercitazione: Integrazione di Azure Active Directory con Symantec Web Security Service (WSS) | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Symantec Web Security Service (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 35427941a084e6750d66ccd3e135d7eef1767c6c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Esercitazione: Integrazione di Azure Active Directory con Symantec Web Security Service (WSS)

Questa esercitazione descrive come integrare l'account Symantec Web Security Service con l'account Azure Active Directory (Azure AD) in modo che WSS possa autenticare un utente finale di cui è stato effettuato il provisioning in Azure AD usando l'autenticazione SAML e applicare regole dei criteri a livello di utente o gruppo.

L'integrazione di Symantec Web Security Service (WSS) con Azure AD offre i vantaggi seguenti:

- Gestire tutti gli utenti finali e i gruppi usati dall'account WSS dal portale di Azure AD. 

- Consentire agli utenti finali di autenticarsi in WSS usando le credenziali di Azure AD.

- Abilitare l'applicazione delle regole dei criteri a livello di utente e gruppo definite nell'account WSS.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Symantec Web Security Service (WSS), sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Account Symantec Web Security Service (WSS)

> [!NOTE]
> Non è consigliabile usare un account WSS attualmente in uso nell'ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare per i test un account WSS attualmente in uso nell'ambiente di produzione, a meno che ciò non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si configurerà Azure AD per abilitare l'accesso Single Sign-On a WSS usando le credenziali dell'utente finale definite nell'account Azure AD.
Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta dell'app Symantec Web Security Service (WSS) dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Aggiunta di Symantec Web Security Service (WSS) dalla raccolta
Per configurare l'integrazione di Symantec Web Security Service (WSS) in Azure AD, è necessario aggiungere Symantec Web Security Service (WSS) dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Symantec Web Security Service (WSS) dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Symantec Web Security Service (WSS)**, selezionare **Symantec Web Security Service (WSS)** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Symantec Web Security Service (WSS) nell'elenco risultati](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Symantec Web Security Service (WSS) usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Symantec Web Security Service (WSS) corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Symantec Web Security Service (WSS).

Per stabilire la relazione di collegamento, in Symantec Web Security Service (WSS) assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Symantec Web Security Service (WSS), è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Symantec Web Security Service (WSS)](#create-a-symantec-web-security-service-wss-test-user)**: per avere una controparte di Britta Simon in Symantec Web Security Service (WSS) collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Symantec Web Security Service (WSS).

**Per configurare l'accesso Single Sign-On di Azure AD con Symantec Web Security Service (WSS), seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Symantec Web Security Service (WSS)** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. Nella sezione **URL e dominio Symantec Web Security Service (WSS)** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Symantec Web Security Service (WSS)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Nella casella di testo **URL di risposta** digitare l'URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contattare il [team di supporto clienti di Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se per qualche motivo i valori **Identificatore** e **URL di risposta** non funzionano.

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Per configurare l'accesso Single Sign-On sul lato Symantec Web Security Service (WSS), fare riferimento alla documentazione online di WSS. Il file **XML metadati** scaricato dovrà essere importato nel portale di WSS. Per assistenza per la configurazione nel portale di WSS, contattare il [team di supporto di Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Creare un utente di test di Symantec Web Security Service (WSS)

In questa sezione viene creato un utente di nome Britta Simon in Symantec Web Security Service (WSS). Il nome utente corrispondente può essere creato manualmente nel portale di WSS oppure è possibile attendere alcuni minuti (circa 15) affinché venga eseguita la sincronizzazione con il portale di WSS degli utenti/gruppi di cui è stato effettuato il provisioning in Azure AD. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. È anche necessario effettuare il provisioning nel portale di Symantec Web Security Service (WSS) dell'indirizzo IP pubblico del computer dell'utente finale che verrà usato per esplorare i siti Web.

> [!NOTE]
> [Fare clic qui](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) per ottenere l'indirizzo IP pubblico del computer.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Symantec Web Security Service (WSS).

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Symantec Web Security Service (WSS), seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Symantec Web Security Service (WSS)**.

    ![Collegamento di Symantec Web Security Service (WSS) nell'elenco delle applicazioni](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione verrà testata la funzionalità Single Sign-On dopo aver configurato l'account WSS per l'uso di Azure AD per l'autenticazione SAML.

Dopo aver configurato il Web browser per inoltrare il traffico a WSS, quando si apre il Web browser e si prova a esplorare un sito, si viene reindirizzati alla pagina di accesso di Azure. Immettere le credenziali dell'utente finale di test di cui è stato effettuato il provisioning in Azure AD (ovvero BrittaSimon) e la password associata. Una volta eseguita l'autenticazione, sarà possibile esplorare il sito Web scelto. Se si crea una regola dei criteri sul lato WSS per impedire a BrittaSimon l'esplorazione di un sito specifico, quando si prova a esplorare tale sito come utente BrittaSimon viene visualizzata la pagina di blocco di WSS.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png


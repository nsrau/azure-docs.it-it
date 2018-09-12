---
title: 'Esercitazione: Integrazione di Azure Active Directory con Snowflake | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Snowflake.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: jeedes
ms.openlocfilehash: 3ad3f42563878d829f900d5cddb0c6866d2deab5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43310822"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Esercitazione: Integrazione di Azure Active Directory con Snowflake

Questa esercitazione descrive come integrare Snowflake con Azure Active Directory (Azure AD).

L'integrazione di Snowflake con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Snowflake.
- È possibile abilitare gli utenti per l'accesso automatico a Snowflake (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Snowflake, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata per l'accesso Single Sign-On a Snowflake

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Snowflake dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-snowflake-from-the-gallery"></a>Aggiunta di Snowflake dalla raccolta
Per configurare l'integrazione di Snowflake in Azure AD, è necessario aggiungere Snowflake dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Snowflake dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Snowflake** selezionare **Snowflake** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Snowflake nell'elenco risultati](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Snowflake in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Snowflake che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Snowflake.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Snowflake, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Snowflake](#create-a-snowflake-test-user)**: per avere una controparte di Britta Simon in Snowflake collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Snowflake.

**Per configurare Single Sign-On di Azure AD con Snowflake, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Snowflake** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/snowflake-tutorial/tutorial_snowflake_samlbase.png)

3. Nella sezione **URL e dominio Snowflake** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<SNOWFLAKE-URL>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<SNOWFLAKE-URL>/fed/login`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Snowflake](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<SNOWFLAKE-URL>`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Snowflake](https://support.snowflake.net/s/snowflake-support). 

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/snowflake-tutorial/tutorial_general_400.png)
    
7. Nella sezione **Configurazione di Snowflake** fare clic su **Configura Snowflake** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione di Snowflake](./media/snowflake-tutorial/tutorial_snowflake_configure.png) 

8. Per configurare l'accesso Single Sign-On sul lato **Snowflake**, è necessario inviare il **certificato (Base64)** scaricato e l'**URL del servizio Single Sign-On SAML** al [team di supporto di Snowflake](https://support.snowflake.net/s/snowflake-support). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/snowflake-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/snowflake-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/snowflake-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/snowflake-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-snowflake-test-user"></a>Creare un utente di test di Snowflake

In questa sezione viene creato un utente di nome Britta Simon in Snowflake. Collaborare con il [team di supporto di Snowflake](https://support.snowflake.net/s/snowflake-support) per aggiungere gli utenti alla piattaforma Snowflake. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Snowflake.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Snowflake, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco applicazioni selezionare **Snowflake**.

    ![Collegamento di Snowflake nell'elenco delle applicazioni](./media/snowflake-tutorial/tutorial_snowflake_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Snowflake nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione Snowflake.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/snowflake-tutorial/tutorial_general_01.png
[2]: ./media/snowflake-tutorial/tutorial_general_02.png
[3]: ./media/snowflake-tutorial/tutorial_general_03.png
[4]: ./media/snowflake-tutorial/tutorial_general_04.png

[100]: ./media/snowflake-tutorial/tutorial_general_100.png

[200]: ./media/snowflake-tutorial/tutorial_general_200.png
[201]: ./media/snowflake-tutorial/tutorial_general_201.png
[202]: ./media/snowflake-tutorial/tutorial_general_202.png
[203]: ./media/snowflake-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cerner Central | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cerner Central.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d2bc549d-d286-4679-854e-bb67c62b0475
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: e14a9fc822fa42ec12390d1ed4540b246802a990
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226375"
---
# <a name="tutorial-azure-active-directory-integration-with-cerner-central"></a>Esercitazione: Integrazione di Azure Active Directory con Central Desktop

Questa esercitazione descrive come integrare Cerner Central con Azure Active Directory (Azure AD).

L'integrazione di Cerner Central con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Cerner Central.
- È possibile abilitare gli utenti per l'accesso automatico a Cerner Central (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Cerner Central, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Account di sistema Cerner Central approvato

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Cerner Central dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-cerner-central-from-the-gallery"></a>Aggiunta di Cerner Central dalla raccolta
Per configurare l'integrazione di Cerner Central in Azure AD, è necessario aggiungere Cerner Central dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Cerner Central dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]

3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Cerner Central**.

    ![Creazione di un utente test di Azure AD](./media/cernercentral-tutorial/tutorial_cernercentral_search.png)

5. Nel pannello dei risultati selezionare **Cerner Central** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/cernercentral-tutorial/tutorial_cernercentral_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione si configura e si testa l'accesso Single Sign-On di Azure AD con Cerner Central in base a un utente test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Cerner Central che corrisponde a un utente di Azure AD. Deve essere stabilita, in altre parole, una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cerner Central.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cerner Central, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test Cerner Central](#creating-a-cerner-central-test-user)**: per avere una controparte di Britta Simon in Cerner Central collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure e si configura l'accesso Single Sign-On nell'applicazione Cerner Central.

**Per configurare l'accesso Single Sign-On di Azure AD con Cerner Central, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cerner Central** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](./media/cernercentral-tutorial/tutorial_cernercentral_samlbase.png)

3. Nella sezione **URL e dominio Cerner Central** seguire questa procedura:

    ![Configure Single Sign-On](./media/cernercentral-tutorial/tutorial_cernercentral_url.png)

    a. Nella casella di testo **Identificatore** digitare il valore adottando il modello seguente:

    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    
    b. Nella casella di testo **URL di risposta** digitare l'URL usando i modelli seguenti:
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/sso` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/sso` |

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto di Cerner Central](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations).

4. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Configure Single Sign-On](./media/cernercentral-tutorial/tutorial_metadataurl.png)

5. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/cernercentral-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On sul lato **Cerner Central**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [supporto di Cerner Central](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations). Il supporto configura l'accesso SSO sul lato applicazione per completare l'integrazione.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/cernercentral-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.

    ![Creazione di un utente test di Azure AD](./media/cernercentral-tutorial/create_aaduser_02.png) 

3. Per aprire la finestra di dialogo **Utente**, fare clic su **Aggiungi**.

    ![Creazione di un utente test di Azure AD](./media/cernercentral-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/cernercentral-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di Britta Simon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.

### <a name="creating-a-cerner-central-test-user"></a>Creazione di un utente test di Cerner Central

L'applicazione **Cerner Central** consente l'autenticazione da qualsiasi provider di identità federato. Se un utente è in grado di accedere alla home page dell'applicazione, viene federato e non è più necessario il provisioning manuale. È possibile scoprire più dettagli [qui](cernercentral-provisioning-tutorial.md) su come configurare il provisioning utenti automatico.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a Cerner Central.

![Assegna utente][200]

**Per assegnare Britta Simon a Cerner Central, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **Cerner Central**.

    ![Configure Single Sign-On](./media/cernercentral-tutorial/tutorial_cernercentral_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cerner Central nel pannello di accesso, si accede automaticamente all'applicazione Cerner Central. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](cernercentral-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cernercentral-tutorial/tutorial_general_01.png
[2]: ./media/cernercentral-tutorial/tutorial_general_02.png
[3]: ./media/cernercentral-tutorial/tutorial_general_03.png
[4]: ./media/cernercentral-tutorial/tutorial_general_04.png

[100]: ./media/cernercentral-tutorial/tutorial_general_100.png

[200]: ./media/cernercentral-tutorial/tutorial_general_200.png
[201]: ./media/cernercentral-tutorial/tutorial_general_201.png
[202]: ./media/cernercentral-tutorial/tutorial_general_202.png
[203]: ./media/cernercentral-tutorial/tutorial_general_203.png
---
title: 'Esercitazione: Integrazione di Azure Active Directory con Infinite Campus | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 303d794e558a5e85b4dd2bca3d9bbb4c2a1ff5dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151805"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Esercitazione: Integrazione di Azure Active Directory con Infinite Campus

Questa esercitazione descrive come integrare Infinite Campus con Azure Active Directory (Azure AD).

L'integrazione di Infinite Campus con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Infinite Campus.
- È possibile abilitare gli utenti per l'accesso automatico a Infinite Campus (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Infinite Campus, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Infinite Campus abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
- Per completare la configurazione è necessario avere almeno il ruolo di amministratore di Azure Active Directory.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Infinite Campus dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-infinite-campus-from-the-gallery"></a>Aggiunta di Infinite Campus dalla raccolta

Per configurare l'integrazione di Infinite Campus in Azure AD, è necessario aggiungere Infinite Campus dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Infinite Campus dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Infinite Campus**, selezionare **Infinite Campus** dal pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Infinite Campus nell'elenco risultati](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Infinite Campus usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Infinite Campus che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Infinite Campus.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Infinite Campus, è necessario completare i passaggi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Infinite Campus](#creating-a-infinite-campus-test-user)**: per avere una controparte di Britta Simon in Infinite Campus collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Infinite Campus.

**Per configurare l'accesso Single Sign-On di Azure AD con Infinite Campus, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Infinite Campus** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

5. Nella sezione **Configurazione SAML di base**, se è disponibile un **file di metadati di un provider di servizi** (passaggio **11.c**), seguire questa procedura:

    a. Fare clic su **Carica il file di metadati**.

        ![image](common/b9_saml.png)

    b. Fare clic su **logo cartella** per selezionare il file di metadati e fare quindi clic su **Upload**.

    ![image](common/b9(1)_saml.png)

    c. Dopo che è stato caricato il file di metadati, i valori **Identificatore** e **URL di risposta** vengono popolati automaticamente nella casella di testo della sezione **Configurazione SAML di base**, come illustrato di seguito:

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. Nella casella di testo **URL di accesso** digitare l'URL usando il criterio seguente (il dominio varia in funzione del modello di hosting): `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > Il **file di metadati del provider di servizi** sarà disponibile nella pagina di configurazione del provider di servizi SSO di Infinite Campus, come spiegato più avanti nell'esercitazione. Se si inizia con una nuova configurazione di provider di servizi SAML in Infinite Campus, andare al **passaggio 11** per completare l'esportazione del file di metadati del provider di servizi.

6. Se non è disponibile un **file di metadati del provider di servizi**, seguire questa procedura (si noti che il dominio varierà con il modello di hosting):

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Infinite Campus](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML**, fare clic sull'**icona** Copia per copiare l' **URL dei metadati di federazione dell'app**  e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Nella sezione **Configura Infinite Campus** usare i valori seguenti per la convalida durante il caricamento o l'uso del file/URL dei metadati di Azure.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di Infinite Campus](common/configuresection.png)

8. In un'altra finestra del Web browser accedere a Infinite Campus come amministratore della sicurezza.

9. Dal menu a sinistra scegliere **System Administration** (Amministrazione sistema).

    ![Amministrazione](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Passare a **User Security** > **SAML Management** > **SSO Service Provider Configuration** (Sicurezza utente > Gestione SAML > Configurazione del provider di servizi SAML).

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. Nella pagina **SSO Service Provider Configuration** (Configurazione del provider di servizi SAML) seguire questa procedura:

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selezionare **Enable SAML Single Sign On** (Abilita SAML Single Sign On).
    
    b. Nella sezione **Select an option to retrieve Identity Provider (IDP) server data** (Selezionare un'opzione per recuperare i dati del server del provider di identità) selezionare **Metadata URL** (URL dei metadati), incollare l'**URL dei metadati di federazione dell'app** nella casella e quindi fare clic su **Sync** (Sincronizza).

    c. Fare clic sul collegamento **Service Provider Metadata** (Metadati del provider di servizi) per salvare il **file di metadati del provider di servizi** nel computer e caricarlo nella sezione **Configurazione SAML di base** per popolare automaticamente i valori **Identificatore** e **URL di risposta** nel portale di Azure (fare riferimento al passaggio 4 per il caricamento e la compilazione automatica dei valori e al passaggio 5 per l'immissione manuale).

    d. Dopo aver fatto clic su **Sync** (Sincronizza), i valori vengono automaticamente popolati nella pagina **SSO Service Provider Configuration** (Configurazione del provider di servizi SAML).

    e. Fare clic su **Save**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un _singolo_ utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.

### <a name="creating-a-infinite-campus-test-user"></a>Creazione di un utente di test di Infinite Campus

Infinite Campus ha un'architettura basata sui dati demografici. Contattare il [team di supporto di Infinite Campus](mailto:sales@infinitecampus.com) per aggiungere gli utenti nella piattaforma Infinite Campus.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Infinite Campus.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **Infinite Campus**.

    ![Configure Single Sign-On](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Infinite Campus nel pannello di accesso, si accederà automaticamente all'applicazione Infinite Campus. Se si esegue l'accesso all'applicazione Infinite Campus nello stesso browser con cui si amministra Azure Active Directory, assicurarsi di aver fatto accesso ad Azure Active Directory come utente di test. Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

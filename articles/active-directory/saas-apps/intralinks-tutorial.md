---
title: 'Esercitazione: Integrazione di Azure Active Directory con Intralinks | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Intralinks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41aac9c64922a3d551ae685bb5b642bf72dd52b2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166224"
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Esercitazione: Integrazione di Azure Active Directory con Intralinks

Questa esercitazione descrive come integrare Intralinks con Azure Active Directory (Azure AD).

L'integrazione di Intralinks con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Intralinks
- È possibile abilitare gli utenti per l'accesso automatico a Intralinks (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Intralinks, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione Intralinks abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Intralinks dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-intralinks-from-the-gallery"></a>Aggiunta di Intralinks dalla raccolta
Per configurare l'integrazione di Intralinks in Azure AD, è necessario aggiungere Intralinks dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Intralinks dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Intralinks**.

    ![Creazione di un utente test di Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. Nel pannello dei risultati selezionare **Intralinks** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Intralinks con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Intralinks che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Intralinks.

Per stabilire la relazione di collegamento, in Intralinks assegnare il valore di **nome utente** in Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Intralinks, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente di test di Intralinks](#creating-an-intralinks-test-user)**: per avere una controparte di Britta Simon in Intralinks collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Intralinks.

**Per configurare l'accesso Single Sign-On di Azure AD con Intralinks, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Intralinks** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_intralinks_samlbase.png)

1. Nella sezione **URL e dominio Intralinks** seguire questa procedura:

    ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_intralinks_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il criterio seguente: `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Poiché non è reale, aggiornarlo con l'URL di accesso effettivo. Per ottenere questo valore, contattare il [team di supporto client di Intralinks](https://www.intralinks.com/contact). 
 
1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_intralinks_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_general_400.png)

1. Per configurare l'accesso Single Sign-On sul lato **Intralinks**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di Intralinks](https://www.intralinks.com/contact). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/intralinks-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/intralinks-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/intralinks-tutorial/create_aaduser_03.png) 

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/intralinks-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="creating-an-intralinks-test-user"></a>Creazione di un utente test di Intralinks

In questa sezione viene creato un utente chiamato Britta Simon in Intralinks. Collaborare con il [team di supporto di Intralinks](https://www.intralinks.com/contact) per aggiungere gli utenti nella piattaforma Intralinks.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Intralinks.

![Assegna utente][200] 

**Per assegnare Britta Simon a Intralinks, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco di applicazioni selezionare **Intralinks**.

    ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_intralinks_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="add-intralinks-via-or-elite-application"></a>Aggiungere un'applicazione Intralinks VIA o Elite

Intralinks usa la stessa piattaforma di identità SSO per tutte le altre applicazioni Intralinks esclusa applicazione Deal Nexus. Se si prevede di usare qualsiasi altra applicazione Intralinks, è necessario configurare prima di tutto l'accesso SSO per un'applicazione di Intralinks primaria usando la procedura descritta sopra.

Si potrà quindi usare la procedura seguente per aggiungere un'altra applicazione Intralinks nel tenant che può sfruttare l'applicazione primaria per SSO. 

>[!NOTE]
>Questa funzionalità è disponibile solo per i clienti degli SKU di Azure AD Premium e non per i clienti di SKU Basic o Gratuito.

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]


1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Intralinks**.

    ![Creazione di un utente test di Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

1. In **Intralinks Aggiungi app** eseguire la procedura seguente:

    ![Aggiunta di un'applicazione Intralinks VIA o Elite](./media/intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. Nella casella di testo **Nome** immettere un nome appropriato per l'applicazione, ad esempio **Intralinks Elite**.

    b. Fare clic sul pulsante **Aggiungi**.

1.  Nella pagina di integrazione dell'applicazione **Intralinks** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Modalità** come **Accesso collegato**.
 
    ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

1. Ottenere l'URL SSO avviato dal provider di servizi dal [team Intralinks](https://www.intralinks.com/contact) per l'altra applicazione Intralinks e immetterlo in **Configurare l'URL di accesso** come illustrato di seguito. 
    
     ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     Nella casella di testo URL accesso digitare l'URL utilizzato dagli utenti per accedere all'applicazione Intralinks adottando il criterio seguente:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/intralinks-tutorial/tutorial_general_400.png)

1. Assegnare l'applicazione a un utente o a gruppi, come illustrato nella sezione **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Intralinks nel pannello di accesso, si accede automaticamente all'applicazione Intralinks.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/intralinks-tutorial/tutorial_general_01.png
[2]: ./media/intralinks-tutorial/tutorial_general_02.png
[3]: ./media/intralinks-tutorial/tutorial_general_03.png
[4]: ./media/intralinks-tutorial/tutorial_general_04.png

[100]: ./media/intralinks-tutorial/tutorial_general_100.png

[200]: ./media/intralinks-tutorial/tutorial_general_200.png
[201]: ./media/intralinks-tutorial/tutorial_general_201.png
[202]: ./media/intralinks-tutorial/tutorial_general_202.png
[203]: ./media/intralinks-tutorial/tutorial_general_203.png


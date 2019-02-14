---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rally Software | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba4f1d41497af8f57a343eacc92c5da7d2488d07
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163700"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Esercitazione: Integrazione di Azure Active Directory con Rally Software

Questa esercitazione descrive come integrare Rally Software con Azure Active Directory (Azure AD).

L'integrazione di Rally Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Rally Software.
- È possibile abilitare gli utenti per l'accesso automatico Single Sign-On a Rally Software con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Rally Software sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Rally Software abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Rally Software dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-rally-software-from-the-gallery"></a>Aggiunta di Rally Software dalla raccolta
Per configurare l'integrazione di Rally Software in Azure AD è necessario aggiungere Rally Software dalla raccolta al proprio elenco delle app SaaS gestite.

**Per aggiungere Rally Software dalla raccolta seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Rally Software**, selezionare **Rally Software** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Rally Software nell'elenco risultati](./media/rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Rally Software mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Rally Software corrisponde a un determinato utente di Azure AD. In altre parole è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Rally Software.

Per stabilire la relazione di collegamento, in Rally Software assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Rally Software è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente test di Rally Software](#create-a-rally-software-test-user)**: per avere una controparte di Britta Simon in Rally Software collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Rally Software.

**Per configurare l'accesso Single Sign-On di Azure AD con Rally Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Rally Software** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

1. Nella sezione **URL e dominio Rally Software** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Rally Software](./media/rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenant-name>.rally.com`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Rally Software](https://help.rallydev.com/). 
 


1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/rally-software-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Rally Software** fare clic su **Configura Rally Software** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione e l'ID di entità SAML** dalla sezione **Riferimento rapido**.

    ![Configurazione di Rally Software](./media/rally-software-tutorial/tutorial_rallysoftware_configure.png) 

1. Accedere al tenant **Rally Software** .

1. Nella barra degli strumenti in alto fare clic su **Setup** (Configurazione), quindi selezionare **Subscription** (Sottoscrizione).
   
    ![Sottoscrizione](./media/rally-software-tutorial/ic769531.png "Sottoscrizione")

1. Fare clic sul pulsante **Action** (Azione). Selezionare **Edit Subscription** (Modifica sottoscrizione) in alto a destra sulla barra degli strumenti.

1. Nella finestra di dialogo **Subscription** (Sottoscrizione) eseguire la procedura seguente, quindi fare clic su **Save & Close** (Salva e chiudi):
   
    ![Autenticazione](./media/rally-software-tutorial/ic769542.png "Autenticazione")
   
    a. Selezionare **Rally or SSO authentication** (Autenticazione Rally o SSO) dall'elenco a discesa Authentication (Autenticazione).

    b. Nella casella di testo **Identity Provider URL** (URL provider di identità) incollare il valore di **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure. 

    c. Nella casella di testo **SSO Logout** (Disconnessione SSO) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/rally-software-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/rally-software-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/rally-software-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/rally-software-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-rally-software-test-user"></a>Creare un utente test di Rally Software

Per consentire agli utenti di Azure AD di accedere, è necessario effettuarne il provisioning nell'applicazione Rally Software usando i relativi nomi utente di Azure Active Directory.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al tenant Rally Software.

1. Passare a **Impostazione \> UTENTI** e quindi fare clic su **+ Aggiungi nuovo**.
   
    ![Utenti](./media/rally-software-tutorial/ic781039.png "Utenti")

1. Digitare il nome nella casella di testo Nuovo utente, quindi fare clic su **Aggiungi con dettagli**.

1. Nella sezione **Crea utente** , eseguire la procedura seguente:
   
    ![Crea utente](./media/rally-software-tutorial/ic781040.png "Crea utente")

    a. Nella casella di testo **Name** (Nome) digitare il nome dell'utente, ad esempio **Britta Simon**.
   
    b. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon@contoso.com**.

    c. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio **Britta**.

    d. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

    e. Fare clic su **Salva e chiudi**.

   >[!NOTE]
   >È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Rally Software per effettuare il provisioning degli account utente di Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite la concessione dell'accesso a Rally Software.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Rally Software, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Rally Software**.

    ![Collegamento di Rally Software nell'elenco delle applicazioni](./media/rally-software-tutorial/tutorial_rallysoftware_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Rally Software nel pannello di accesso si accede automaticamente all'applicazione Rally Software.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rally-software-tutorial/tutorial_general_01.png
[2]: ./media/rally-software-tutorial/tutorial_general_02.png
[3]: ./media/rally-software-tutorial/tutorial_general_03.png
[4]: ./media/rally-software-tutorial/tutorial_general_04.png

[100]: ./media/rally-software-tutorial/tutorial_general_100.png

[200]: ./media/rally-software-tutorial/tutorial_general_200.png
[201]: ./media/rally-software-tutorial/tutorial_general_201.png
[202]: ./media/rally-software-tutorial/tutorial_general_202.png
[203]: ./media/rally-software-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con SignalFx | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 6f2d869f345aeb8f50d42de6b1533b849ffb2182
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197569"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Esercitazione: Integrazione di Azure Active Directory con SignalFx

Questa esercitazione descrive come integrare SignalFx con Azure Active Directory (Azure AD).

L'integrazione di SignalFx con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SignalFx.
- È possibile abilitare l'accesso automatico degli utenti a SignalFx (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SignalFx, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di SignalFx abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SignalFx dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-signalfx-from-the-gallery"></a>Aggiunta di SignalFx dalla raccolta
Per configurare l'integrazione di SignalFx in Azure AD, è necessario aggiungere SignalFx dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SignalFx dalla raccolta, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **SignalFx**, selezionare **SignalFx** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SignalFx nell'elenco dei risultati](./media/signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SignalFx usando un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in SignalFx. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SignalFx.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SignalFx, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di SignalFx](#create-a-signalfx-test-user)**: per definire una controparte di Britta Simon in SignalFx collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e l'accesso viene quindi configurato nell'applicazione SignalFx.

**Per configurare l'accesso Single Sign-On di Azure AD con SignalFx, eseguire queste operazioni:**

1. Nella pagina di integrazione dell'applicazione **SignalFx** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/signalfx-tutorial/tutorial_signalfx_samlbase.png)

1. Nella sezione **URL e dominio SignalFx** eseguire queste operazioni:

    ![Informazioni su URL e dominio di SignalFx per l'accesso Single Sign-On](./media/signalfx-tutorial/tutorial_signalfx_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://api.signalfx.com/v1/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Il valore precedente non è un valore reale. È necessario aggiornarlo con l'URL di risposta effettivo, come descritto più avanti nell'esercitazione.

1. L'applicazione SignalFx si aspetta un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.   

    ![Configure Single Sign-On](./media/signalfx-tutorial/tutorial_signalfx_attribute.png)

1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/signalfx-tutorial/tutorial_attribute_04.png)

    ![Configurare l'aggiunta attributo Single Sign-On](./media/signalfx-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.
 
1. Nella sezione **Certificato di firma SAML** seguire questa procedura: 

    ![Collegamento di download del certificato](./media/signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    b. Fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/signalfx-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di SignalFx** fare clic su **Configura SignalFx** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di SignalFx](./media/signalfx-tutorial/tutorial_signalfx_configure.png) 

1. Accedere al sito aziendale di SignalFx come amministratore.

1. In SignalFx fare clic su **Integrations** (Integrazioni) nella parte superiore per aprire la pagina Integrations (Integrazioni).

    ![Integrazione di SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Fare clic sul riquadro **Azure Active Directory** nella sezione **Login Services** (Servizi di accesso).
 
    ![SAML per SignalFx](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Fare clic su **NUOVA INTEGRAZIONE** e nella scheda **INSTALL** (INSTALLA) eseguire queste operazioni:
 
    ![Pagina di integrazione SAML di SignalFx](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Nella casella di testo **Name** (Nome) digitare un nuovo nome di integrazione, ad esempio **SSO SAML NomeAzienda**.

    b. Copiare il valore di **Integration ID** (ID integrazione) e aggiungerlo a **Reply URL** (URL di risposta) come `https://api.signalfx.com/v1/saml/acs/<integration ID>` nella casella di testo **URL di risposta** della sezione **URL e dominio SignalFx** nel portale di Azure.

    c. Fare clic su **Upload File** (Carica file) per caricare il **certificato con codifica Base64** scaricato dal portale di Azure nella casella di testo **Certificate** (Certificato).

    d. Nella casella di testo **Issuer URL** (URL autorità di certificazione) incollare il valore di **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure.

    e. Nella casella di testo **Metadata URL** (URL dei metadati) incollare l'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/signalfx-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/signalfx-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/signalfx-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/signalfx-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
  
### <a name="create-a-signalfx-test-user"></a>Creare un utente di test di SignalFx

Questa sezione descrive come creare un utente di nome Britta Simon in SignalFx. SignalFx supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a SignalFx.

Quando un utente accede a SignalFx dal servizio Single Sign-On SAML per la prima volta, il [team di supporto di SignalFx](mailto:kmazzola@signalfx.com) invia un messaggio di posta elettronica che contiene un collegamento su cui l'utente deve fare clic per autenticarsi. Questo avviene solo al primo accesso dell'utente. Nei tentativi di accesso successivi non verrà più richiesta la convalida tramite posta elettronica.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di SignalFx](mailto:kmazzola@signalfx.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SignalFx.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SignalFx, eseguire queste operazioni:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **SignalFx**.

    ![Collegamento SignalFx nell'elenco delle applicazioni](./media/signalfx-tutorial/tutorial_signalfx_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SignalFx nel pannello di accesso, viene automaticamente eseguito l'accesso all'applicazione SignalFx.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/signalfx-tutorial/tutorial_general_01.png
[2]: ./media/signalfx-tutorial/tutorial_general_02.png
[3]: ./media/signalfx-tutorial/tutorial_general_03.png
[4]: ./media/signalfx-tutorial/tutorial_general_04.png

[100]: ./media/signalfx-tutorial/tutorial_general_100.png

[200]: ./media/signalfx-tutorial/tutorial_general_200.png
[201]: ./media/signalfx-tutorial/tutorial_general_201.png
[202]: ./media/signalfx-tutorial/tutorial_general_202.png
[203]: ./media/signalfx-tutorial/tutorial_general_203.png


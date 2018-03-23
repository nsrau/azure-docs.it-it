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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: jeedes
ms.openlocfilehash: 50a86a01c22450ae2d92e6743fb6de7e652d4017
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Esercitazione: Integrazione di Azure Active Directory con SignalFx

Questa esercitazione descrive come integrare SignalFx con Azure Active Directory (Azure AD).

L'integrazione di SignalFx con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SignalFx.
- È possibile abilitare l'accesso automatico degli utenti a SignalFx (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

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
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-signalfx-from-the-gallery"></a>Aggiunta di SignalFx dalla raccolta
Per configurare l'integrazione di SignalFx in Azure AD, è necessario aggiungere SignalFx dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SignalFx dalla raccolta, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **SignalFx**, selezionare **SignalFx** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SignalFx nell'elenco dei risultati](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SignalFx usando un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in SignalFx. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SignalFx.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SignalFx, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di SignalFx](#create-a-signalfx-test-user)**: per definire una controparte di Britta Simon in SignalFx collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e l'accesso viene quindi configurato nell'applicazione SignalFx.

**Per configurare l'accesso Single Sign-On di Azure AD con SignalFx, eseguire queste operazioni:**

1. Nella pagina di integrazione dell'applicazione **SignalFx** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. Nella sezione **URL e dominio SignalFx** eseguire queste operazioni:

    ![Informazioni su URL e dominio di SignalFx per l'accesso Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://api.signalfx.com/v1/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Il valore precedente non è un valore reale. È necessario aggiornarlo con l'URL di risposta effettivo, come descritto più avanti nell'esercitazione.

4. L'applicazione SignalFx si aspetta un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.   

    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Configurare l'aggiunta attributo Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.
 
6. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. Per generare l'**URL dei metadati**, eseguire queste operazioni:

    a. Fare clic su **Registrazioni per l'app**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_appregistrations.png)
   
    b. Fare clic su **Endpoint** per aprire la finestra di dialogo **Endpoint**.  
    
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_endpointicon.png)

    c. Fare clic sul pulsante Copia per copiare l'URL del **DOCUMENTO METADATI FEDERAZIONE** e incollarlo nel Blocco note.
    
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_endpoint.png)
     
    d. Passare ora alla pagina delle proprietà di **SignalFx**, copiare il valore di **ID applicazione** usando il pulsante **Copia** e incollarlo nel Blocco note.
 
    ![Configure Single Sign-On](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_appid.png)

    e. Generare l'**URL dei metadati** usando il modello seguente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

9. Nella sezione **Configurazione di SignalFx** fare clic su **Configura SignalFx** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

10. Accedere al sito aziendale di SignalFx come amministratore.

11. In SignalFx fare clic su **Integrations** (Integrazioni) nella parte superiore per aprire la pagina Integrations (Integrazioni).

    ![Integrazione di SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

12. Fare clic sul riquadro **Azure Active Directory** nella sezione **Login Services** (Servizi di accesso).
 
    ![SAML per SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

13. Fare clic su **NUOVA INTEGRAZIONE** e nella scheda **INSTALL** (INSTALLA) eseguire queste operazioni:
 
    ![Pagina di integrazione SAML di SignalFx](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Nella casella di testo **Name** (Nome) digitare un nuovo nome di integrazione, ad esempio **SSO SAML NomeAzienda**.

    b. Copiare il valore di **Integration ID** (ID integrazione) e aggiungerlo a **Reply URL** (URL di risposta) come `https://api.signalfx.com/v1/saml/acs/<integration ID>` nella casella di testo **URL di risposta** della sezione **URL e dominio SignalFx** nel portale di Azure.

    c. Fare clic su **Upload File** (Carica file) per caricare il **certificato con codifica Base64** scaricato dal portale di Azure nella casella di testo **Certificate** (Certificato).

    d. Nella casella di testo **Issuer URL** (URL autorità di certificazione) incollare il valore di **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure.

    e. Nella casella di testo **Metadata URL** (URL metadati) incollare il valore di **SAML Entity ID** (ID entità SAML) generato dal portale di Azure.

    f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-signalfx-test-user"></a>Creare un utente di test di SignalFx

Questa sezione descrive come creare un utente di nome Britta Simon in SignalFx. SignalFx supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a SignalFx.

Quando un utente accede a SignalFx dal servizio Single Sign-On SAML per la prima volta, il [team di supporto di SignalFx](mailto:kmazzola@signalfx.com) invia un messaggio di posta elettronica che contiene un collegamento su cui l'utente deve fare clic per autenticarsi. Questo avviene solo al primo accesso dell'utente. Nei tentativi di accesso successivi non verrà più richiesta la convalida tramite posta elettronica.

>[!Note]
>Se è necessario creare manualmente un utente, contattare il [team di supporto di SignalFx](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a SignalFx.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SignalFx, eseguire queste operazioni:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SignalFx**.

    ![Collegamento SignalFx nell'elenco delle applicazioni](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SignalFx nel pannello di accesso, viene automaticamente eseguito l'accesso all'applicazione SignalFx.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png


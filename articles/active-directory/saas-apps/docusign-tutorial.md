---
title: 'Esercitazione: Integrazione di Azure Active Directory con DocuSign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 6b2b5f72c9520498d4834bbbfaf6c56656a807e7
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015219"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Esercitazione: Integrazione di Azure Active Directory con DocuSign

Questa esercitazione descrive come integrare DocuSign con Azure Active Directory (Azure AD).

L'integrazione di DocuSign con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a DocuSign.
- È possibile abilitare gli utenti per l'accesso automatico a DocuSign (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con DocuSign, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di DocuSign abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di DocuSign dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-docusign-from-the-gallery"></a>Aggiunta di DocuSign dalla raccolta

Per configurare l'integrazione di DocuSign in Azure AD, è necessario aggiungere DocuSign dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere DocuSign dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **DocuSign**, selezionare **DocuSign** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![DocuSign nell'elenco dei risultati](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con DocuSign in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di DocuSign che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in DocuSign.

Per configurare e testare l'accesso Single Sign-On di Azure AD con DocuSign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di DocuSign](#creating-a-docusign-test-user)**: per avere una controparte di Britta Simon in DocuSign collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione DocuSign.

**Per configurare Single Sign-On di Azure AD con DocuSign, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **DocuSign** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di DocuSign](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. La procedura a questo scopo viene descritta più avanti nella sezione **Visualizzare endpoint SAML 2.0** dell'esercitazione.

5. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (Base64)** e salvare il file di certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. Nella sezione **Configura DocuSign** copiare l'URL appropriato in base alle esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di DocuSign](common/configuresection.png)

7. In un'altra finestra del Web browser accedere al **portale di amministrazione di DocuSign** come amministratore.

8. Nell'angolo in alto a destra della pagina fare clic sul **logo** del profilo e quindi su **Go to Admin** (Vai ad amministrazione).
  
    ![Configurazione dell'accesso Single Sign-On][51]

9. Nella pagina delle soluzioni di dominio fare clic su **Domini**

    ![Configurazione dell'accesso Single Sign-On][50]

10. Nella sezione **Domini** fare clic su **CLAIM DOMAIN** (RICHIEDI DOMINIO).

    ![Configurazione dell'accesso Single Sign-On][52]

11. Nella finestra di dialogo **Claim a domain** (Richiedi un dominio) digitare il dominio aziendale nella casella di testo **Nome dominio** e quindi fare clic su **CLAIM** (RICHIEDI). Verificare il dominio e che lo stato sia attivo.

    ![Configurazione dell'accesso Single Sign-On][53]

12. Nella pagina delle soluzioni di dominio fare clic su **Provider di identità**.
  
    ![Configurazione dell'accesso Single Sign-On][54]

13. Nella sezione **Provider di identità** fare clic su **AGGIUNGI PROVIDER DI IDENTITÀ**. 

    ![Configurazione dell'accesso Single Sign-On][55]

14. Nella pagina **Identity Provider Settings** (Impostazioni provider di identità) eseguire questa procedura:

    ![Configurazione dell'accesso Single Sign-On][56]

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione. Non usare spazi.

    b. Nella casella di testo **Autorità di certificazione del provider di identità** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **URL di accesso provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Nella casella di testo **URL di disconnessione provider di identità** incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Selezionare **Sign AuthN Request** (Firma richiesta di autenticazione).

    f. Per **Invia richiesta di autenticazione da** selezionare **POST**.

    g. Per **Invia richiesta di disconnessione da** selezionare **GET**.

    h. Nella sezione **Custom Attribute Mapping** (Mapping attributi personalizzati) fare clic su **AGGIUNGI NUOVO MAPPING**.

    ![Configurazione dell'accesso Single Sign-On][62]

    i. Scegliere il campo di cui si vuole eseguire il mapping all'attestazione di Azure AD. In questo esempio, viene eseguito il mapping dell'attestazione **emailaddress** con il valore di **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Si tratta del nome di attestazione predefinito di Azure AD per l'attestazione basata su posta elettronica. Fare clic su **SALVA**.

    ![Configurazione dell'accesso Single Sign-On][57]

    > [!NOTE]
    > Usare l' **Identificatore utente** appropriato per associare l'utente da Azure AD al mapping degli utenti di DocuSign. Selezionare il campo corretto e immettere il valore appropriato in base alle impostazioni dell'organizzazione.

    j. Nella sezione **Certificato provider di identità** fare clic su **AGGIUNGI CERTIFICATO**, caricare il certificato scaricato dal portale di Azure AD e quindi fare clic su **SALVA**.

    ![Configurazione dell'accesso Single Sign-On][58]

    k. Nella sezione **Provider di identità** fare clic su **AZIONI** e quindi su **Endpoint**.

    ![Configurazione dell'accesso Single Sign-On][59]

    l. Nella sezione **View SAML 2.0 Endpoints** (Visualizza endpoint SAML 2.0) del **portale di amministrazione di DocuSign** seguire questa procedura:

    ![Configurazione dell'accesso Single Sign-On][60]

    * Copiare il valore di **Service Provider Issuer URL** (URL autorità di certificazione provider di servizi) e quindi incollarlo nella casella di testo **Identificatore** nella sezione **URL e dominio DocuSign** nel portale di Azure.

    * Copiare il valore di **Service Provider Login URL** (URL di accesso provider di servizi) e incollarlo nella casella di testo **URL di accesso** in **URL e dominio DocuSign** nel portale di Azure.

    * Fare clic su **Close**

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

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

### <a name="creating-a-docusign-test-user"></a>Creazione di un utente test di DocuSign

Questa sezione descrive come creare un utente di nome Britta Simon in DocuSign. DocuSign supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a DocuSign.
>[!Note]
>Se è necessario creare un utente manualmente, contattare il  [team di supporto di DocuSign](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a DocuSign.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco di applicazioni selezionare **DocuSign**.

    ![Configure Single Sign-On](./media/docusign-tutorial/tutorial_docusign_app.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro DocuSign nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione DocuSign.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png

---
title: 'Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer - Azure AD integration | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MOVEit Transfer - Azure AD integration.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 5d9955655119a51656d6b17ba1cecf624c416874
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Esercitazione: Integrazione di Azure Active Directory con MOVEit Transfer - Azure AD integration

Questa esercitazione descrive come integrare MOVEit Transfer - Azure AD integration con Azure Active Directory (Azure AD).

L'integrazione di MOVEit Transfer - Azure AD integration con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a MOVEit Transfer - Azure AD integration.
- È possibile abilitare gli utenti per l'accesso automatico a MOVEit Transfer - Azure AD integration (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con MOVEit Transfer - Azure AD integration, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di MOVEit Transfer - Azure AD integration abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di MOVEit Transfer - Azure AD integration dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Aggiunta di MOVEit Transfer - Azure AD integration dalla raccolta
Per configurare l'integrazione di MOVEit Transfer - Azure AD integration in Azure AD, è necessario aggiungere MOVEit Transfer - Azure AD integration dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere MOVEit Transfer - Azure AD integration dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **MOVEit Transfer - Azure AD integration**, selezionare **MOVEit Transfer - Azure AD integration** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![MOVEit Transfer - Azure AD integration nell'elenco risultati](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con MOVEit Transfer - Azure AD integration usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di MOVEit Transfer - Azure AD integration corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in MOVEit Transfer - Azure AD integration.

Per stabilire la relazione di collegamento, in MOVEit Transfer - Azure AD integration assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con MOVEit Transfer - Azure AD integration, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di MOVEit Transfer - Azure AD integration](#create-a-moveit-transfer---azure-ad-integration-test-user)**: per avere una controparte di Britta Simon in MOVEit Transfer - Azure AD integration collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione MOVEit Transfer - Azure AD integration.

**Per configurare l'accesso Single Sign-On di Azure AD con MOVEit Transfer - Azure AD integration, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **MOVEit Transfer - Azure AD integration** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. Nella sezione **URL e dominio MOVEit Transfer - Azure AD integration** seguire questa procedura:

    ![Configure Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://contoso.com`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://contoso.com/<tenatid>`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. È possibile fare riferimento a questi valori più avanti nella sezione **URL dei metadati del provider di servizi** oppure contattare il [team di supporto clienti di MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support) per ottenere questi valori.

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Accedere al tenant di MOVEit Transfer come amministratore.

7. Nella barra di spostamento a sinistra fare clic su **Settings**(Impostazioni).

    ![Sezione delle impostazioni sul lato dell'app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Fare clic sul collegamento **Single Signon** (Single Sign-On) in **Security Policies -> User Auth** (Criteri di sicurezza -> Autenticazione utente).

    ![Criteri di sicurezza sul lato dell'app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Fare clic sul collegamento con l'URL dei metadati per scaricare il documento di metadati.

    ![URL dei metadati del provider di servizi](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Verificare che il valore di **entityID** corrisponda a quello di **Identificatore** nella sezione **URL e dominio MOVEit Transfer - Azure AD integration**.
    * Verificare che l'URL della posizione **AssertionConsumerService** corrisponda all'**URL di risposta** nella sezione **URL e dominio MOVEit Transfer - Azure AD integration**.
    
    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Fare clic sul pulsante **Add Identity Provider** (Aggiungi provider di identità) per aggiungere un nuovo provider di identità federato.

    ![Aggiungi provider di identità](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Fare clic su **Browse** (Sfoglia) per selezionare il file di metadati scaricato dal portale di Azure e quindi fare clic su **Add Identity Provider** (Aggiungi provider di identità) per caricare il file scaricato.

    ![Provider di identità SAML](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Selezionare "**Yes**" (Sì) per **Enabled** (Abilitato) nella pagina **Edit Federated Identity Provider Settings** (Modifica impostazioni provider di identità federato) e fare clic su **Save** (Salva).

    ![Impostazioni provider di identità federato](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. Nella pagina **Edit Federated Identity Provider User Settings** (Modifica impostazioni utente del provider di identità federato) eseguire queste operazioni:
    
    ![Modifica delle impostazioni del provider di identità federato](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selezionare **SAML NameID** (ID nome SAML) per **Login name** (Nome di accesso).
    
    b. Selezionare **Other** (Altro) per **Full name** (Nome completo) e nella casella di testo **Attribute name** (Nome attributo) immettere il valore: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selezionare **Other** (Altro) per **Email** (Posta elettronica) e nella casella di testo **Attribute name** (Nome attributo) immettere il valore: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selezionare **Yes** (Sì) per **Auto-create account on signon** (Crea automaticamente account all'accesso).
    
    e. Fare clic sul pulsante **Salva** .

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Creare un utente di test di MOVEit Transfer - Azure AD integration

Questa sezione descrive come creare un utente di test di nome Britta Simon in MOVEit Transfer - Azure AD integration. MOVEit Transfer - Azure AD integration supporta il provisioning JIT, che è stato abilitato. Non è necessario alcun intervento dell'utente in questa sezione. Durante il tentativo di accesso a MOVEit Transfer - Azure AD integration viene creato un nuovo utente, se questo non esiste già.

>[!NOTE]
>Per creare un utente manualmente, contattare il [team di supporto clienti di MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a MOVEit Transfer - Azure AD integration.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a MOVEit Transfer - Azure AD integration, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **MOVEit Transfer - Azure AD integration**.

    ![Collegamento di MOVEit Transfer - Azure AD integration nell'elenco delle applicazioni](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro MOVEit Transfer - Azure AD integration nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione MOVEit Transfer - Azure AD integration. 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png


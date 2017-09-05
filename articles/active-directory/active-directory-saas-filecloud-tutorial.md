---
title: 'Esercitazione: Integrazione di Azure Active Directory con FileCloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e FileCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f39f0ddd-b504-4562-971f-77b88d1e75fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ad03516f684acc59912ffc57f6e0712828bd03f2
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Esercitazione: Integrazione di Azure Active Directory con FileCloud

Questa esercitazione descrive come integrare FileCloud con Azure Active Directory (Azure AD).

L'integrazione di FileCloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a FileCloud.
- È possibile abilitare gli utenti per l'accesso automatico a FileCloud (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con FileCloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di FileCloud abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di FileCloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-filecloud-from-the-gallery"></a>Aggiunta di FileCloud dalla raccolta
Per configurare l'integrazione di FileCloud in Azure AD, è necessario aggiungere FileCloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere FileCloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **FileCloud**, selezionare **FileCloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![FileCloud nell'elenco dei risultati](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con FileCloud con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di FileCloud che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in FileCloud.

Per stabilire la relazione di collegamento, in FileCloud assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con FileCloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di FileCloud](#create-a-filecloud-test-user)**: per avere una controparte di Britta Simon in FileCloud collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione FileCloud.

**Per configurare l'accesso Single Sign-On di Azure AD con FileCloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **FileCloud** del portale di Azure fare clic su **Single Sign-On**.

    ![Configurare il collegamento Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_samlbase.png)

3. Nella sezione **URL e dominio FileCloud** seguire questa procedura:

    ![Informazioni su URL e dominio per Single Sign-On di FileCloud](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.filecloudhosted.com`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di FileCloud](mailto:support@codelathe.com).

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-filecloud-tutorial/tutorial_general_400.png)

6. Nella sezione **FileCloud Configuration** (Configurazione di FileCloud) fare clic su **Configure FileCloud** (Configura FileCloud) per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla sezione **Riferimento rapido**.

    ![Configurazione di FileCloud](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_configure.png) 

7. In un'altra finestra del browser Web accedere al tenant FileCloud come amministratore.

8. Nella barra di spostamento a sinistra fare clic su **Settings** (Impostazioni). 
   
    ![Sezione Settings (Impostazioni) sul lato dell'app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

9. Fare clic sulla scheda **SSO** nella sezione Settings (Impostazioni). 
   
    ![Scheda SSO sul lato dell'app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

10. Impostare **SAML** come **Default SSO Type** (Tipo SSO predefinito) nel riquadro **Single Sign On (SSO) Settings** (Impostazioni Single Sign-On - SSO).
   
    ![Pannello Single Sign-On Settings (Impostazioni Single Sign-On - SSO) sul lato dell'app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

11. Incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure nella casella di testo **IdP End Point URL** (URL entità IdP).

    ![Casella di testo IdP End Point URL (URL entità IdP)](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

12. Aprire il file di metadati scaricato in Blocco note, copiare i contenuti negli Appunti, quindi incollarli nella casella di testo **IdP Meta Data** (Metadati IdP) nel riquadro **SAML Settings** (Impostazioni SAML).

    ![Sezione IdP Meta Data (Metadati IdP) sul lato dell'app](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

13. Fare clic sul pulsante **Salva** .

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-filecloud-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-filecloud-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-filecloud-test-user"></a>Creare un utente test di FileCloud

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in FileCloud. FileCloud supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a FileCloud viene creato un nuovo utente, se questo non esiste già.

>[!NOTE]
>Per creare un utente manualmente, è necessario contattare il [team di supporto clienti di FileCloud](mailto:support@codelathe.com). 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a FileCloud.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a FileCloud, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **FileCloud**.

    ![Collegamento FileCloud nell'elenco Applicazioni](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro FileCloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione FileCloud.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png



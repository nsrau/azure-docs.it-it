---
title: 'Esercitazione: integrazione di Azure Active Directory con JDA Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e JDA Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 06af825f-79ec-4de9-8851-c79d65d1e586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f2dfaf281130115ff04ff84b413e224f54cfcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60268383"
---
# <a name="tutorial-azure-active-directory-integration-with-jda-cloud"></a>Esercitazione: integrazione di Azure Active Directory con JDA Cloud

Questa esercitazione descrive come integrare JDA Cloud con Azure Active Directory (Azure AD).

L'integrazione di JDA Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a JDA Cloud.
- È possibile abilitare l'accesso automatico degli utenti a JDA Cloud (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con JDA Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di JDA Cloud abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di JDA Cloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-jda-cloud-from-the-gallery"></a>Aggiunta di JDA Cloud dalla raccolta

Per configurare l'integrazione di JDA Cloud in Azure AD, è necessario aggiungere JDA Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere JDA Cloud dalla raccolta, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **JDA Cloud**, selezionare **JDA Cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![JDA Cloud nell'elenco dei risultati](./media/jdacloud-tutorial/tutorial_jdacloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con JDA Cloud con un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in JDA Cloud. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in JDA Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con JDA Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test per JDA Cloud](#create-a-jda-cloud-test-user)**: per avere una controparte di Britta Simon in JDA Cloud collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e l'accesso viene quindi configurato nell'applicazione JDA Cloud.

**Per configurare l'accesso Single Sign-On di Azure AD con JDA Cloud, eseguire queste operazioni:**

1. Nella pagina di integrazione dell'applicazione **JDA Cloud** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/jdacloud-tutorial/tutorial_jdacloud_samlbase.png)

3. Nella sezione **URL e dominio JDA Cloud**, eseguire le operazioni seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio di JDA Cloud per l'accesso Single Sign-On](./media/jdacloud-tutorial/tutorial_jdacloud_url1.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<SUBDOMAIN>.jdadelivers.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<SUBDOMAIN>.jdadelivers.com/sp/ACS.saml2`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio di JDA Cloud per l'accesso Single Sign-On](./media/jdacloud-tutorial/tutorial_jdacloud_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://ssonp-dl2.jdadelivers.com/sp/startSSO.ping?PartnerIdpId=<SAML Entity ID>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterrà il valore **ID entità SAML** dalla **sezione riferimento rapido** nella sezione **configurazione JDA Cloud**. Per ottenere questi valori, contattare il [team del supporto clienti di JDA Cloud](https://support.jda.com/).

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/jdacloud-tutorial/tutorial_jdacloud_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/jdacloud-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di JDA Cloud** fare clic su **Configura JDA Cloud** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido**.

    ![Configure Single Sign-On](./media/jdacloud-tutorial/tutorial_jdacloud_configure.png)

8. Per configurare l'accesso Single Sign-On sul lato **JDA Cloud**, è necessario inviare il file **XML dei metadati** scaricato al [team di supporto di JDA Cloud](https://support.jda.com/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/jdacloud-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/jdacloud-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/jdacloud-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/jdacloud-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="create-a-jda-cloud-test-user"></a>Creare un utente di test per JDA Cloud

In questa sezione viene creato un utente di nome Britta Simon in JDA Cloud. Per aggiungere gli utenti nella piattaforma JDA Cloud, collaborare con il  [team di supporto di JDA Cloud](https://support.jda.com/) . Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a JDA Cloud.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a JDA Cloud, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **JDA Cloud**.

    ![Collegamento JDA Cloud nell'elenco delle applicazioni](./media/jdacloud-tutorial/tutorial_jdacloud_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro JDA Cloud nel pannello di accesso, viene automaticamente eseguito l'accesso all'applicazione JDA Cloud.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jdacloud-tutorial/tutorial_general_01.png
[2]: ./media/jdacloud-tutorial/tutorial_general_02.png
[3]: ./media/jdacloud-tutorial/tutorial_general_03.png
[4]: ./media/jdacloud-tutorial/tutorial_general_04.png

[100]: ./media/jdacloud-tutorial/tutorial_general_100.png

[200]: ./media/jdacloud-tutorial/tutorial_general_200.png
[201]: ./media/jdacloud-tutorial/tutorial_general_201.png
[202]: ./media/jdacloud-tutorial/tutorial_general_202.png
[203]: ./media/jdacloud-tutorial/tutorial_general_203.png

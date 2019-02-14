---
title: 'Esercitazione: Integrazione di Azure Active Directory con Cisco Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Cisco Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db1cea1d-ff0a-4f0d-b5fd-50ca32702d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f4d379f01d072bbfdcd95cd5bb19ccc0f9546d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166369"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Cloud

Questa esercitazione descrive come integrare Cisco Cloud con Azure Active Directory (Azure AD).

L'integrazione di Cisco Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Cisco Cloud.
- È possibile abilitare gli utenti per l'accesso automatico a Cisco Cloud (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Cisco Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Cisco Cloud abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Cisco Cloud dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-cisco-cloud-from-the-gallery"></a>Aggiunta di Cisco Cloud dalla raccolta
Per configurare l'integrazione di Cisco Cloud in Azure AD, è necessario aggiungere Cisco Cloud dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Cisco Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Cisco Cloud**, selezionare **Cisco Cloud** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Cisco Cloud nell'elenco dei risultati](./media/ciscocloud-tutorial/tutorial_ciscocloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Cisco Cloud usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Cisco Cloud corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cisco Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Cisco Cloud, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Cisco Cloud](#create-a-cisco-cloud-test-user)**: per avere una controparte di Britta Simon in Cisco Cloud collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Cisco Cloud.

**Per configurare l'accesso Single Sign-On di Azure AD con Cisco Cloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Cisco Cloud** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.

    ![Finestra di dialogo Single Sign-On](./media/ciscocloud-tutorial/tutorial_ciscocloud_samlbase.png)

1. Nella sezione **URL e dominio Cisco Cloud** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio di Cisco Cloud per l'accesso Single Sign-On](./media/ciscocloud-tutorial/tutorial_ciscocloud_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `<subdomain>.cisco.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.cisco.com/sp/ACS.saml2`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio di Cisco Cloud per l'accesso Single Sign-On](./media/ciscocloud-tutorial/tutorial_ciscocloud_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://<subdomain>.cloudapps.cisco.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Cisco Cloud](mailto:cpr-ops@cisco.com).

1. L'applicazione Cisco Cloud si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione.
 La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configure Single Sign-On](./media/ciscocloud-tutorial/attribute.png)

1. Fare clic sulla casella di controllo **Visualizza e modifica tutti gli altri attributi utente** nella sezione **Attributi utente** per espandere gli attributi. Seguire questa procedura per ogni attributo visualizzato:

    | Nome attributo | Valore attributo |
    | ---------------| ----------------|
    | country      |user.country |
    | company      |user.companyname |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/ciscocloud-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/ciscocloud-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuoto il valore riferito allo **spazio dei nomi**.

    e. Fare clic su **OK**.

1. Nella sezione  **Certificato di firma SAML**  fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note.

    ![Collegamento di download del certificato](./media/ciscocloud-tutorial/tutorial_ciscocloud_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/ciscocloud-tutorial/tutorial_general_400.png)

1. Per configurare l'accesso Single Sign-On in **Cisco Cloud**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [team di supporto di Cisco Cloud](mailto:cpr-ops@cisco.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/ciscocloud-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/ciscocloud-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/ciscocloud-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/ciscocloud-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-cisco-cloud-test-user"></a>Creare un utente di test di Cisco Cloud

In questa sezione viene creato un utente di nome Britta Simon in Cisco Cloud. Collaborare con il  [team di supporto di Cisco Cloud](mailto:cpr-ops@cisco.com) per aggiungere gli utenti alla piattaforma Cisco Cloud. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cisco Cloud.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Cisco Cloud, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Cisco Cloud**.

    ![Collegamento a Cisco Cloud nell'elenco delle applicazioni](./media/ciscocloud-tutorial/tutorial_ciscocloud_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Cisco Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Cisco Cloud.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ciscocloud-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloud-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloud-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloud-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloud-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloud-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloud-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloud-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloud-tutorial/tutorial_general_203.png


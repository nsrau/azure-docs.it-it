---
title: 'Esercitazione: integrazione di Azure Active Directory con SecureW2 JoinNow Connector | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SecureW2 JoinNow Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b367befb90ec28ece963d67b479749e1c8ad363
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60340008"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>Esercitazione: integrazione di Azure Active Directory con SecureW2 JoinNow Connector

Questa esercitazione descrive come integrare SecureW2 JoinNow Connector con Azure Active Directory (Azure AD).

L'integrazione di SecureW2 JoinNow Connector con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SecureW2 JoinNow Connector
- È possibile abilitare gli utenti per l'accesso automatico a SecureW2 JoinNow Connector (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con SecureW2 JoinNow Connector, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata all'accesso Single Sign-On SecureW2 JoinNow Connector

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di SecureW2 JoinNow Connector dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Aggiunta di SecureW2 JoinNow Connector dalla raccolta
Per configurare l'integrazione di SecureW2 JoinNow Connector in Azure AD, è necessario aggiungere SecureW2 JoinNow Connector dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SecureW2 JoinNow Connector dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **SecureW2 JoinNow Connector**, selezionare **SecureW2 JoinNow Connector** nel pannello dei risultati quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SecureW2 JoinNow Connector nell'elenco dei risultati](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SecureW2 JoinNow Connector con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SecureW2 JoinNow Connector che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SecureW2 JoinNow Connector.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SecureW2 JoinNow Connector, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SecureW2 JoinNow Connector](#create-a-securew2-joinnow-connector-test-user)** : per avere una controparte di Britta Simon in SecureW2 JoinNow Connector collegata alla relativa rappresentazione in Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SecureW2 JoinNow Connector.

**Per configurare l'accesso Single Sign-On di Azure AD con SecureW2 JoinNow Connector, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SecureW2 JoinNow Connector** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. Nella sezione **URL e dominio SecureW2 JoinNow Connector** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di SecureW2 JoinNow Connector](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di SecureW2 JoinNow Connector](mailto:support@securew2.com). 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. Per configurare l'accesso Single Sign-On nel lato **SecureW2 JoinNow Connector**, è necessario inviare il file scaricato **XML Metadati** al [team di supporto di SecureW2 JoinNow Connector](mailto:support@securew2.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>Creare un utente di test SecureW2 JoinNow Connector

In questa sezione viene creato un utente chiamato Britta Simon in SecureW2 JoinNow Connector. Rivolgersi al  [team di supporto clienti di withSecureW2 JoinNow Connector](mailto:support@securew2.com) per aggiungere gli utenti alla piattaforma SecureW2 JoinNow Connector. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a SecureW2 JoinNow Connector.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a SecureW2 JoinNow Connector, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **SecureW2 JoinNow Connector**.

    ![Il collegamento di SecureW2 JoinNow Connector nell'elenco delle applicazioni](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

**Per procedere con l'applicazione, eseguire la procedura seguente:** 

a. Aprire il client di SecureW2 JoinNow Connector, selezionare il dispositivo appropriato dall'elenco e fare clic sul pulsante **Accedi**.

b. Il browser predefinito deve aprirsi e l'utente dovrebbe essere reindirizzato al portale di Azure per l'autenticazione.

c. Quando l'autenticazione è riuscita, deve restituire la pagina iniziale di SecureW2 JoinNow Connector.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png


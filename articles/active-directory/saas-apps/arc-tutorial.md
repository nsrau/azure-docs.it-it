---
title: 'Esercitazione: Integrazione di Azure Active Directory con Arc Publishing - SSO | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Arc Publishing - SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: c1bf5ba68d8242a0ef0831987ac6fd041c62ace9
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969368"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Esercitazione: Integrazione di Azure Active Directory con Arc Publishing - SSO

Questa esercitazione descrive come integrare Arc Publishing - SSO con Azure Active Directory (Azure AD).

L'integrazione di Arc Publishing - SSO con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Arc Publishing - SSO.
- È possibile abilitare gli utenti per l'accesso automatico ad Arc Publishing - SSO (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Arc Publishing - SSO, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per l'accesso Single Sign-On ad Arc Publishing - SSO

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Arc Publishing - SSO dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Aggiunta di Arc Publishing - SSO dalla raccolta
Per configurare l'integrazione di Arc Publishing - SSO in Azure AD, è necessario aggiungere Arc Publishing - SSO dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Arc Publishing - SSO dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Arc Publishing - SSO**, selezionare **Arc Publishing - SSO** dal pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Arc Publishing - SSO nell'elenco risultati](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Arc Publishing - SSO in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Arc Publishing - SSO che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Arc Publishing - SSO.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Arc Publishing - SSO, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Arc Publishing - SSO](#create-an-arc-publishing---sso-test-user)**: per avere una controparte di Britta Simon in Arc Publishing - SSO collegata alla relativa rappresentazione in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Arc Publishing - SSO.

**Per configurare l'accesso Single Sign-On di Azure AD con Arc Publishing - SSO, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Arc Publishing - SSO** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/arc-tutorial/tutorial_arc_samlbase.png)

1. Nella sezione **URL e dominio Arc Publishing - SSO**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Arc Publishing - SSO](./media/arc-tutorial/tutorial_arc_url.png)

    1. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    1. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di Arc Publishing - SSO](./media/arc-tutorial/tutorial_arc_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Arc Publishing - SSO](mailto:inf@washpost.com). 

1. L'applicazione Arc Publishing - SSO prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configure Single Sign-On](./media/arc-tutorial/tutorial_arc_attribute.png)

1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |
    | groups | user.assignedroles |

    1. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

     ![Configure Single Sign-On](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Configure Single Sign-On](./media/arc-tutorial/tutorial_attribute_05.png)
    
    1. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    1. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.
    
    1. Fare clic su **Ok**

    > [!NOTE]
    > In questo caso, il mapping dell'attributo **groups** viene eseguito con **user.assignedroles**. Si tratta di ruoli personalizzati creati in Azure AD per eseguire di nuovo il mapping dei nomi di gruppo nell'applicazione. Per altre informazioni su come creare ruoli personalizzati in Azure AD, vedere [qui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management). 

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/arc-tutorial/tutorial_arc_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/arc-tutorial/tutorial_general_400.png)
    
1. Nella sezione **Configurazione di Arc Publishing - SSO** fare clic su **Configura Arc Publishing - SSO** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Arc Publishing - SSO](./media/arc-tutorial/tutorial_arc_configure.png) 

1. Per configurare l'accesso Single Sign-On sul lato **Arc Publishing - SSO**, è necessario inviare il file di **Certificato (Base64) scaricato, l'URL di disconnessione, l'ID entità SAML e l'URL del servizio Single Sign-On SAML** al [team di supporto di Arc Publishing - SSO](mailto:inf@washpost.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/arc-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/arc-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/arc-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/arc-tutorial/create_aaduser_04.png)

    1. Nella casella **Nome** digitare **BrittaSimon**.

    1. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    1. Fare clic su **Crea**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Creare un utente di test di Arc Publishing - SSO

Questa sezione descrive come creare un utente chiamato Britta Simon in Arc Publishing - SSO. Arc Publishing - SSO supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso ad Arc Publishing - SSO.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Arc Publishing - SSO](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Arc Publishing - SSO.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Arc Publishing - SSO, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Arc Publishing - SSO**.

    ![Collegamento ad Arc Publishing - SSO nell'elenco delle applicazioni](./media/arc-tutorial/tutorial_arc_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Arc Publishing - SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Arc Publishing - SSO.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png


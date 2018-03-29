---
title: 'Esercitazione: Integrazione di Azure Active Directory con ADP | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeedes
ms.openlocfilehash: 038b2337ea49b769c6b19e9d50a5f0f2edd13d56
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Esercitazione: Integrazione di Azure Active Directory con ADP

Questa esercitazione descrive come integrare ADP con Azure Active Directory (Azure AD).

L'integrazione di ADP con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad ADP.
- È possibile abilitare gli utenti per l'accesso automatico ad ADP (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con ADP, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per ADP

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di ADP dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-adp-from-the-gallery"></a>Aggiunta di ADP dalla raccolta
Per configurare l'integrazione di ADP in Azure AD, è necessario aggiungere ADP dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ADP dalla raccolta, seguire questa procedura:**

1.  Accedere al proprio ambiente di provider di identità di Microsoft Azure come amministratore.

2. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

3. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
4. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

5. Nella casella di ricerca digitare **ADP**, selezionare **ADP** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![ADP nell'elenco dei risultati](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con ADP in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di ADP che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in ADP.

Per stabilire la relazione di collegamento, in ADP assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con ADP, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di ADP](#create-an-adp-test-user)**: per avere una controparte di Britta Simon in ADP collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione ADP.

**Per configurare Single Sign-On di Azure AD con ADP, seguire questa procedura:**

1. Nel portale di Azure, nella pagina di integrazione dell'applicazione **ADP** fare clic sulla scheda **Proprietà** e seguire questa procedura: 

    ![Proprietà del servizio Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Impostare il valore del campo **Abilitata per l'accesso degli utenti** su **Sì**.

    b. Copiare l'**URL accesso utente** che dovrà essere incollato nella sezione **Configura URL di accesso**, descritta più avanti nell'esercitazione.

    c. Impostare il valore del campo **Assegnazione di utenti obbligatoria** su **Sì**.

    d. Impostare il valore del campo **Visibile agli utenti** su **No**.

2. Fare clic su **Single Sign-On** nella pagina di integrazione dell'applicazione **ADP**.

    ![Collegamento Configura accesso Single Sign-On][4]

3. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. Nella sezione **URL e dominio ADP** eseguire i passaggi descritti di seguito:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di ADP](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_url.png)

    Nella casella di testo **Identificatore** digitare un URL: `https://fed.adp.com` 
    
5. L'applicazione ADP prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione. Il nome dell'attestazione sarà sempre **"PersonImmutableID"** e il valore di cui è stato eseguito il mapping con **employeeid**. 

    Il mapping degli utenti da Azure AD ad ADP verrà eseguito in base a **employeeid**, ma è possibile eseguire il mapping a un valore diverso, in base alle impostazioni dell'applicazione. Collaborare quindi prima di tutto con il [team di supporto di ADP](https://www.adp.com/contact-us/overview.aspx) per usare l'identificatore corretto di un utente ed eseguire il mapping di tale valore all'attestazione **"PersonImmutableID"**.

    ![Configure Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

    > [!NOTE] 
    > Prima di poter configurare l'asserzione SAML, è necessario contattare il [team di supporto di ADP](https://www.adp.com/contact-us/overview.aspx) e richiedere il valore dell'attributo dell'identificatore univoco per il tenant. Questo valore è necessario per configurare l'attestazione personalizzata per l'applicazione. 

7. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Per configurare l'accesso Single Sign-On sul lato **ADP**, è necessario caricare il file **XML dei metadati** scaricato nel [sito Web di ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Questo processo potrebbe richiedere qualche giorno. 

### <a name="configure-your-adp-services-for-federated-access"></a>Configurare i servizi ADP per l'accesso federato

>[!Important]
> I dipendenti che richiedono l'accesso federato ai servizi ADP devono essere assegnati all'app di servizio ADP e, successivamente, gli utenti devono essere riassegnati al servizio ADP specifico.
Al momento della ricezione della conferma dal rappresentante ADP, configurare i servizi ADP e assegnare/gestire gli utenti per controllare l'accesso al servizio ADP specifico.

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **ADP**, selezionare **ADP** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![ADP nell'elenco dei risultati](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. Nel portale di Azure, nella pagina di integrazione dell'applicazione **ADP** fare clic sulla scheda **Proprietà** e seguire questa procedura:  

    ![Proprietà collegate Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Impostare il valore del campo **Abilitata per l'accesso degli utenti** su **Sì**.

    b.  Impostare il valore del campo **Assegnazione di utenti obbligatoria** su **Sì**.

    c.  Impostare il valore del campo **Visibile agli utenti** su **Sì**.

6. Fare clic su **Single Sign-On** nella pagina di integrazione dell'applicazione **ADP**.

    ![Collegamento Configura accesso Single Sign-On][4]

7. Nella finestra di dialogo **Single Sign-On** selezionare **Modalità** come **Accesso collegato**. Per collegare l'applicazione ad **ADP**.

    ![Single Sign-On collegato](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Nella sezione **Configura URL di accesso** seguire questa procedura:

    ![Proprietà Single Sign-On](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. Incollare l'**URL di accesso utente** copiato in precedenza dalla scheda **Proprietà** (dall'app ADP principale).
                                                             
    b. Le 5 app seguenti supportano **URL di stato dell'inoltro** diversi. È necessario aggiungere manualmente il valore appropriato dell'**URL di stato dell'inoltro** per una particolare applicazione all'**URL di accesso utente**.
    
    * **ADP Workforce Now**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Salvare** le modifiche.

10. Dopo aver ricevuto conferma dal rappresentante ADP, iniziare i test con uno o due utenti.

    a. Assegnare alcuni utenti all'app del servizio ADP per testare l'accesso federato.

    b. Il test ha esito positivo quando gli utenti accedono all'app del servizio ADP nella raccolta e possono accedere al rispettivo servizio ADP.
 
11. Alla conferma dell'esito positivo del test, assegnare il servizio federato ADP a singoli utenti o gruppi di utenti, come illustrato più avanti nell'esercitazione, e distribuirlo ai dipendenti. 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-adp-test-user"></a>Creare un utente di test di ADP

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in ADP. Collaborare con il [team di supporto di ADP](https://www.adp.com/contact-us/overview.aspx) per aggiungere gli utenti nell'account ADP.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad ADP.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad ADP, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **ADP**.

    ![Collegamento di ADP nell'elenco delle applicazioni](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro ADP nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione ADP.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png


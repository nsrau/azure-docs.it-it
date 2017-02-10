---
title: 'Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6d53de158a44116058ab8241d5182414f8f306fb
ms.openlocfilehash: 243e81ecd1e1648bfd6b2ca6e6268ea1a18d2036


---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Atlassian Cloud

Questa esercitazione descrive come integrare Atlassian Cloud con Azure Active Directory (Azure AD).

L'integrazione di Atlassian Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Atlassian Cloud
- È possibile abilitare gli utenti per l'accesso automatico ad Atlassian Cloud (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di Azure classico

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Atlassian Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Atlassian Cloud abilitata per l'accesso Single Sign-On


>[!NOTE] 
>Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Atlassian Cloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-atlassian-cloud-from-the-gallery"></a>Aggiunta di Atlassian Cloud dalla raccolta
Per configurare l'integrazione di Atlassian Cloud in Azure AD, è necessario aggiungere Atlassian Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Atlassian Cloud dalla raccolta, seguire questa procedura:**

1. Nel **portale di Azure classico**fare clic su **Active Directory**nel riquadro di spostamento sinistro.

    ![Active Directory][1]
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.

    ![Applications][2]

4. Fare clic su **Add** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca digitare **Atlassian Cloud**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_01.png)

7. Nel riquadro dei risultati selezionare **Atlassian Cloud** e quindi fare clic su **Completa** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Atlassian Cloud con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Atlassian Cloud che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Atlassian Cloud.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Atlassian Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Atlassian Cloud](#creating-Atlassian-cloud-test-user)**: per avere una controparte di Britta Simon in Atlassian Cloud collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure classico e viene configurato l'accesso Single Sign-On nell'applicazione Atlassian Cloud.


**Per configurare l'accesso Single Sign-On di Azure AD con Atlassian Cloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Atlassian Cloud** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
     
    ![Configura accesso Single Sign-On][6] 

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Atlassian Cloud** selezionare **Single Sign-On di Azure AD** e quindi fare clic su **Avanti**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_03.png) 

3. Nella pagina **Configurare le impostazioni dell'app** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_04.png) 

    a. Nella casella di testo **URL di accesso** digitare l'URL usato dagli utenti per accedere all'applicazione Atlassian Cloud adottando il modello seguente: `https://<instancename>.atlassian.net`
    
    b. Nella casella di testo **Identificatore** digitare l'URL usando il modello seguente: `https://id.atlassian.com/login`

    >[!NOTE] 
    >Per ottenere il valore esatto dell'**Identificatore**, vedere la schermata di configurazione SAML di Atlassian Cloud.

    c. click **Avanti**
 
4. Nella pagina **Configura accesso Single Sign-On in Atlassian Cloud** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_05.png)

    a. Fare clic su **Scarica certificato**e quindi salvare il file nel computer.

    b. Fare clic su **Avanti**.


5. Per ottenere l'accesso Single Sign-On configurato per l'applicazione, accedere al portale di Atlassian usando i diritti di amministratore.

6. Nella sezione Authentication (Autenticazione) a sinistra fare clic su **Domains** (Domini).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

    a. Nella casella di testo digitare il nome di dominio e quindi fare clic su **Add domain** (Aggiungi dominio).
        
    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)

    b. Per verificare il dominio, fare clic su **Verify** (Verifica). 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png)

    c. Scaricare il file HTML di verifica del dominio, caricarlo nella cartella radice del sito Web del dominio, quindi fare clic su **Verify domain** (Verifica dominio).
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)

    d. Dopo la verifica del dominio, il valore del campo **Status** (Stato) è **Verified** (Verificato).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

7. Nella barra di spostamento a sinistra fare clic su **SAML**.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

8. Creare una nuova configurazione SAML e aggiungere la configurazione del provider di identità.

    a. Copiare il valore ID entità da Azure AD e incollarlo nel campo Identity Provider Entity ID (ID entità del provider di identità).
    
    b. Copiare l'URL SSO SAML e incollarlo nella casella Identity Provider SSO URL (URL SSO del provider di identità).

    c. Aprire il certificato scaricato da Azure AD in Blocco note e copiare i valori senza le righe di inizio e di fine, quindi incollarli nella casella Public X509 certificate (Certificato X509 pubblico).
    
    d. Salvare le impostazioni.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)
  
9. Aggiornare le impostazioni di Azure AD per assicurarsi di avere configurato l'URL corretto per l'identificatore.

    a. Copiare l'ID dell'identità SP dalla schermata SAML e incollarlo in Azure AD come valore **Identificatore**.

    b. L'URL di accesso corrisponde all'URL del tenant di Atlassian Cloud.   

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
10. Nel portale classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Avanti**.
    
    ![Single Sign-On di Microsoft Azure AD][10]

7. Nella pagina **Conferma Single Sign-on** fare clic su **Completa**.  
 
    ![Single Sign-On di Microsoft Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
In questa sezione viene creato un utente test chiamato Britta Simon nel portale classico.


![Creare un utente di Azure AD][20]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure classico** fare clic su **Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_09.png) 

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per visualizzare l'elenco di utenti, fare clic su **Utenti**nel menu in alto.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente**, fare clic su **Aggiungi utente** nella barra degli strumenti in basso.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

5. Nella pagina **Informazioni sull'utente** seguire questa procedura:  ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_05.png) 

    a. In Tipo di utente selezionare Nuovo utente nell'organizzazione.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina **Profilo utente** seguire questa procedura: ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_06.png) 

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Nome visualizzato** digitare **Britta Simon**.

    d. Nell'elenco **Ruolo** selezionare **Utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Ottieni password temporanea** fare clic su **crea**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_07.png) 

8. Nella pagina **Ottieni password temporanea** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_08.png) 

    a. Prendere nota del valore visualizzato in **Nuova password**.

    b. Fare clic su **Completa**.   



### <a name="creating-an-atlassian-cloud-test-user"></a>Creazione di un utente di test di Atlassian Cloud


In questa sezione viene creato un utente chiamato Britta Simon in Atlassian Cloud. È importante che l'utente sia presente in Atlassian Cloud prima dell'accesso Single Sign-On. Accedere all'istanza di Atlassian Cloud con diritti di amministratore e seguire questa procedura.

>[!NOTE] 
>È anche possibile creare questi utenti facendo clic sul pulsante **Bulk Create** (Creazione in blocco) nella sezione Users (Utenti).

1. Nella sezione relativa all'amministrazione del sito fare clic sul pulsante **Users** (Utenti).

    ![Creare l'utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Fare clic sul pulsante **Create User** (Crea utente) per creare un utente in Atlassian Cloud.

    ![Creare l'utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Immettere l'indirizzo di posta elettronica, il nome utente e il nome completo dell'utente e assegnare l'accesso all'applicazione. 

    ![Creare l'utente di Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Fare clic su **Create user** (Crea utente). L'invito tramite posta elettronica verrà inviato all'utente. Dopo l'accettazione dell'invito, l'utente sarà attivo nel sistema. 


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Atlassian Cloud.

![Assegna utente][200] 

**Per assegnare Britta Simon ad Atlassian Cloud, seguire questa procedura:**

1. Per aprire la visualizzazione delle applicazioni nel portale classico, nella visualizzazione directory fare clic su **Applicazioni** nel menu in alto.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Atlassian Cloud**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_50.png) 

3. Scegliere **Utenti**dal menu in alto.

    ![Assegna utente][203]

4. Nell'elenco di utenti selezionare **Britta Simon**.

5. Fare clic su **Assegna**sulla barra degli strumenti in basso.

    ![Assegna utente][205]


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Atlassian Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Atlassian Cloud.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->



---
title: 'Esercitazione: integrazione di Azure Active Directory con iLMS | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 22c72020200138e78835ed7dd2661f18b824c785
ms.contentlocale: it-it
ms.lasthandoff: 05/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Esercitazione: Integrazione di Azure Active Directory con iLMS

Questa esercitazione descrive come integrare iLMS con Azure Active Directory (Azure AD).

L'integrazione di iLMS con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a iLMS
- È possibile abilitare gli utenti per l'accesso automatico a iLMS (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iLMS sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di iLMS abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di iLMS dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-ilms-from-the-gallery"></a>Aggiunta di iLMS dalla raccolta
Per configurare l'integrazione di iLMS in Azure AD, è necessario aggiungere iLMS dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere iLMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Per aggiungere una nuova applicazione, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **iLMS**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. Nel pannello dei risultati selezionare **iLMS** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con iLMS in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di iLMS che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iLMS.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in iLMS.

Per configurare e testare l'accesso Single Sign-On di Azure AD con iLMS, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di iLMS](#creating-an-ilms-test-user)** : per avere una controparte di Britta Simon in iLMS collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione iLMS.

**Per configurare Single Sign-On di Azure AD con iLMS, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **iLMS** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. Nella sezione **URL e dominio iLMS** se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. Nella casella di testo **Identificatore** incollare il valore **Identificatore** copiato dalla sezione **Provider di servizi** delle impostazioni SAML nel portale di amministrazione iLMS.

    b. Nella casella di testo **URL di risposta** incollare il valore **Endpoint (URL)** copiato dalla sezione **Provider di servizi** delle impostazioni SAML nel portale di amministrazione iLMS con il modello seguente `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Questo '123456' è un valore di esempio dell'identificatore.

4. Selezionare **Mostra impostazioni URL avanzate**, se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    Nella casella di testo **URL di accesso** incollare il valore **Endpoint (URL)** copiato dalla sezione **Provider di servizi** delle impostazioni SAML nel portale di amministrazione come `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`        

5. Per abilitare il provisioning JIT, l'applicazione iLMS si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/4.png)
    
    Creare gli attributi **Department (Reparto), Region (Area)** e **Division** (Divisione) e aggiungere il nome di questi attributi in iLMS. Tutti gli attributi indicati sopra sono obbligatori.    

    > [!NOTE] 
    > È necessario abilitare **Create Un-recognized User Account** (Crea account utente non riconosciuto) in iLMS per eseguire il mapping di questi attributi. Seguire le istruzioni [qui](http://support.inspiredelearning.com/customer/portal/articles/2204526) per avere un'idea della configurazione degli attributi.

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ---------------| --------------- |    
    | divisione | user.department |
    | region | user.state |
    | department | user. jobtitle |

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **Ok**

7. Nella sezione **Certificato di firma SAML** fare clic su **XML metadati** e quindi salvare il file XML nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. In un'altra finestra del Web browser accedere al **portale di amministrazione di iLMS** come amministratore.

10. Fare clic su **SSO:SAML** nella scheda **Impostazioni** per aprire le impostazioni di SAML ed eseguire la procedura seguente:
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. Espandere la sezione **Provider di servizi** e copiare l'**Identificatore** e il valore **Endpoint (URL)**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. Nella sezione **Provider di identità** fare clic su **Import metadata** (Importa metadati).
    
    c. Selezionare il file **Metadati** scaricato dal portale di Azure dalla sezione **Certificato di firma SAML**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Se si desidera abilitare il provisioning JIT per creare gli account iLMS per utenti non riconosciuti, seguire questa procedura:
        
       - Selezionare **Create Un-recognized User Account** (Crea account utente non riconosciuto).
       
       ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Eseguire il mapping degli attributi in Azure AD con gli attributi in iLMS. Nella colonna dell'attributo specificare il nome degli attributi o il valore predefinito.

    e. Passare alla scheda **Business Rules** (Regole Business) ed eseguire la procedura seguente: 
        
       ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/5.png)

       - Selezionare **Create Un-recognized Regions, Divisions and Departments** (Crea aree, divisioni e reparti non riconosciuti) per creare aree, divisioni e reparti che non esistono già al momento del Single Sign-on.
        
       - Selezionare **Update User Profile During Sign-in** (Aggiorna il profilo utente durante l'accesso) per specificare se il profilo dell'utente viene aggiornato ad ogni Single Sign-on. 
        
       - Se l'opzione **"Update Blank Values for Non Mandatory Fields in User Profile"** (Aggiorna i valori vuoti per i campi non obbligatori nel profilo utente) è selezionata, i campi facoltativi del profilo vuoti al momento dell'accesso faranno sì che il profilo iLMS dell'utente contenga valori vuoti per quei campi.
        
       - Selezionare **Send Error Notification Email** (Invia una notifica di errore via posta elettronica) e immettere l'indirizzo di posta elettronica dell'utente a cui si desidera ricevere la notifica di errore.

11. Fare clic sul pulsante **Save** (Salva) per salvare le impostazioni.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD)
    
### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-ilms-test-user"></a>Creazione di un utente test di iLMS

L'applicazione supporta il provisioning dell'utente just-in-time e dopo l'autenticazione gli utenti vengono automaticamente creati nell'applicazione. JIT funzionerà se si è fatto clic sulla casella di controllo **Create Un-recognized User Account** (Crea account utente non riconosciuto) durante l'impostazione di configurazione SAML presso il portale di amministrazione di iLMS.

Se è necessario creare manualmente un utente, seguire la seguente procedura:

1. Accedere al sito aziendale di iLMS come amministratore.

2. Fare clic su **"Register User"** (Registra utente) nella scheda **Users** (Utenti) per aprire la pagina **Register User** (Registra utente). 
   
   ![Aggiungere un dipendente](./media/active-directory-saas-ilms-tutorial/3.png)

3. Nella pagina **"Register User"** (Registra utente) eseguire la procedura seguente.

    ![Aggiungere un dipendente](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. Nella casella di testo **First Name** (Nome) digitare Britta.
   
    b. Nella casella di testo **Last name** (Cognome) digitare Simon.

    c. Nella casella di testo **ID email** (ID Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    d. Dall'elenco a discesa **Region** (Area), selezionare il valore per l'area.

    e. Dall'elenco a discesa **Division** (Divisione), selezionare il valore per la divisione.

    f. Dall'elenco a discesa **Department** (Reparto), selezionare il valore per il reparto.

    g. Fare clic su **Salva**.

    > [!NOTE] 
    > È possibile inviare all'utente un messaggio di registrazione selezionando la casella di controllo **Send Registration Mail** (Invia email di registrazione).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a iLMS.

![Assegna utente][200] 

**Per assegnare Britta Simon a iLMS seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **iLMS**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro iLMS nel pannello di accesso, si accederà automaticamente all'applicazione iLMS.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png



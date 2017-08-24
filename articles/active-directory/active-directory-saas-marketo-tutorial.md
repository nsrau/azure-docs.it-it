---
title: 'Esercitazione: Integrazione di Azure Active Directory con Marketo | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e146fd5a8075bc9c7ba049b25e5f301fc2645ed9
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Esercitazione: Integrazione di Azure Active Directory con Marketo

Questa esercitazione descrive come integrare Marketo con Azure Active Directory (Azure AD).

L'integrazione di Marketo con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Marketo
- È possibile abilitare gli utenti per l'accesso automatico a Marketo (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Marketo, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Marketo abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Marketo dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-marketo-from-the-gallery"></a>Aggiunta di Marketo dalla raccolta
Per configurare l'integrazione di Marketo in Azure AD, è necessario aggiungere Marketo dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Marketo dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Marketo**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. Nel pannello dei risultati selezionare **Marketo** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Marketo mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Marketo che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Marketo.

Per stabilire la relazione di collegamento, in Marketo assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Marketo, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Marketo](#creating-a-marketo-test-user)**: per avere una controparte di Britta Simon in Marketo collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Marketo.

**Per configurare l'accesso Single Sign-On di Azure AD con Marketo, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Marketo** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. Nella sezione **URL e dominio Marketo** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://saml.marketo.com/sp`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto di Marketo](http://investors.marketo.com/contactus.cfm).
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Marketo** fare clic su **Configura Marketo** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. Per ottenere l'ID Munchkin dell'applicazione, accedere a Marketo usando le credenziali di amministratore ed eseguire le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic sul **collegamento Munchkin**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copiare l'ID Munchkin visualizzato sullo schermo e completare il valore per l'URL di risposta nella configurazione guidata di Azure AD.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. Per configurare l'accesso Single Sign-On nell'applicazione, seguire questa procedura:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu Integration (Integrazione) e fare clic su **Single Sign-On**.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Per abilitare le impostazioni SAML fare clic sul pulsante **Edit** (Modifica).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Impostazioni dell'accesso Single Sign-On **abilitate**.
   
    f. Incollare il valore **SAML Entity ID** (ID entità SAML) nella casella di testo **Issuer ID** (ID autorità emittente).
   
    g. Nella casella di testo **Entity ID** (ID entità) immettere l'URL come `http://saml.marketo.com/sp`.
   
    h. Selezionare User ID Location (Percorso ID utente) come **elemento Name Identifier** (Identificatore nome).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se l'identificatore utente non è un valore UPN, modificarlo nella scheda degli attributi.
   
    i. Caricare il certificato scaricato dalla configurazione guidata di Azure AD. Fare clic su **Save** (Salva) per salvare le impostazioni.
   
    j. Modificare le impostazioni delle pagine di reindirizzamento.
   
    k. Incollare **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) nella casella di testo **URL di accesso**.
   
    l. Incollare il valore di **Sign-Out URL** (URL di disconnessione) nella casella di testo **URL disconnessione**.
   
    m. In **Error URL** (URL errore) copiare l'**URL istanza** di Marketo e fare clic su **Save** (Salva) per salvare le impostazioni.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. Per abilitare l'accesso Single Sign-On per gli utenti, completare le azioni seguenti:
   
    a. Accedere all'app Marketo usando le credenziali di amministratore.
   
    b. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Passare al menu **Security** (Sicurezza) e fare clic su **Login Settings** (Impostazioni di accesso).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Selezionare l'opzione **Require SSO** (Richiedi SSO) e usare **Save** (Salva) per salvare le impostazioni.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-marketo-test-user"></a>Creazione di un utente test di Marketo

In questa sezione viene creato un utente chiamato Britta Simon in Marketo. Seguire questa procedura per creare un utente nella piattaforma Marketo.

1. Accedere all'app Marketo usando le credenziali di amministratore.

2. Fare clic sul pulsante **Admin** (Amministratore) nel riquadro di spostamento in alto.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Passare al menu **Security** (Sicurezza) e fare clic su **Users & Roles** (Utenti e ruoli).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. Fare clic sul collegamento **Invite New User** (Invita nuovo utente) nella scheda Users (Utenti).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. La procedura guidata (Invita nuovo utente) immetterà i valori per le informazioni seguenti.
   
    a. Immettere l'indirizzo di **Email** (Posta elettronica) dell'utente nella casella di testo.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Immettere il valore per **First Name** (Nome) nella casella di testo.
   
    c. Immettere il valore per **Last Name** (Cognome) nella casella di testo.
   
    d. Fare clic su **Avanti**

6. Nella scheda **Permissions** (Autorizzazioni) selezionare i **ruoli utente** e fare clic su **Next** (Avanti).
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Fare clic sul pulsante **Send** (Invia) per inoltrare l'invito all'utente.
   
    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. L'utente riceve la notifica tramite posta elettronica e deve fare clic sul collegamento e modificare la password per attivare l'account. 

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Marketo.

![Assegna utente][200] 

**Per assegnare Britta Simon a Marketo, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni, selezionare **Marketo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Marketo nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Marketo.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png



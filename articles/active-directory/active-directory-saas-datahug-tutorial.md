---
title: 'Esercitazione: integrazione di Azure Active Directory con Datahug | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Datahug.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: ec431dd5ccfa53e4b975e46da247704dd1e15c2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Esercitazione: Integrazione di Azure Active Directory con Datahug

Questa esercitazione descrive come integrare Datahug con Azure Active Directory (Azure AD).

L'integrazione di Datahug con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Datahug.
- È possibile abilitare gli utenti per l'accesso automatico a Datahug (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure Active Directory, vedere: [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Datahug, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Datahug abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Datahug dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-datahug-from-the-gallery"></a>Aggiunta di Datahug dalla raccolta
Per configurare l'integrazione di Datahug in Azure AD, è necessario aggiungere Datahug dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Datahug dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Datahug**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_search.png)

5. Nel pannello dei risultati selezionare **Datahug** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Datahug in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Datahug che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Datahug.

La relazione di collegamento viene stabilita assegnando al valore di **nome utente** in Azure AD come valore dell'attributo **Username** in Datahug.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Datahug, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Datahug](#creating-a-datahug-test-user)**: per avere una controparte di Britta Simon in Datahug collegata alla relativa rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Datahug.

**Per configurare Single Sign-On di Azure AD con Datahug, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Datahug** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_samlbase.png)

3. Nella sezione **URL e dominio Datahug**, se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_ur1.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://apps.datahug.com/identity/<uniqueID>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://apps.datahug.com/identity/<uniqueID>/acs`

4. Selezionare **Mostra impostazioni URL avanzate** se si desidera configurare l'applicazione in modalità avviata da **SP**:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_url2.png)

    Nella casella di testo **URL di accesso** digitare l'URL come: `https://apps.datahug.com/`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. In questo caso, è consigliabile usare in Identificatore e in URL di risposta il valore univoco della stringa. Per ottenere questi valori, contattare il [team di supporto clienti di Datahug](http://datahug.com/about/contact-us/). 

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Selezionare **Mostra impostazioni avanzate per la firma di certificati** ed eseguire la procedura seguente:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_cert.png)

    a. In **Opzione di firma**, selezionare **Firma asserzione SAML**.
    
    b. In **Algoritmo di firma**, selezionare **SHA1**.
 
7. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_general_400.png)
    
8. Nella sezione **Configurazione di Datahug** fare clic su **Configura Datahug** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** e **l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_configure.png) 

9. Per configurare l'accesso Single Sign-On sul lato **Datahug**, è necessario inviare il **file XML dei metadati**, l'**ID entità SAML** e l'**URL del servizio Single Sign-On SAML** al [supporto Datahug](http://datahug.com/about/contact-us/). L'applicazione verrà configurata in modo che la connessione SAML SSO sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-datahug-test-user"></a>Creazione di un utente test di Datahug

Per consentire agli utenti di Azure AD di accedere ad Datahug, è necessario eseguirne il provisioning in Datahug.  
In Datahug il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Datahug come amministratore.

2. Passare il puntatore sull'**ingranaggio** nell'angolo superiore destro e fare clic su **Impostazioni**
   
   ![Aggiungere un dipendente](./media/active-directory-saas-datahug-tutorial/1.png)

3. Scegliere **Persone** e fare clic sulla scheda **Aggiungi utenti**.

    ![Aggiungere un dipendente](./media/active-directory-saas-datahug-tutorial/2.png)

4. Digitare l'indirizzo di posta elettronica della persona per la quale si vuole creare un account e fare clic su **Aggiungi**.

    ![Aggiungere un dipendente](./media/active-directory-saas-datahug-tutorial/3.png)

    > [!NOTE] 
    > È possibile inviare all'utente un messaggio di registrazione selezionando la casella di controllo **Invia messaggio di benvenuto**.  
    > Se si sta creando un account di Salesforce non inviare il messaggio di benvenuto.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Datahug.

![Assegna utente][200] 

**Per assegnare Britta Simon a Datahug, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Datahug**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.
Quando si fa clic sul riquadro Datahug nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Datahug. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_203.png


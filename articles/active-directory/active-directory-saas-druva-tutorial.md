---
title: 'Esercitazione: Integrazione di Azure Active Directory con Druva | Documentazione Microsoft'
description: Informazioni su come usare Druva con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 0c24b01924d5fcec0d4600e044e679cfc295815f
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Esercitazione: Integrazione di Azure Active Directory con Druva
Questa esercitazione descrive l'integrazione di Azure e Druva.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Druva abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Druva potranno accedere all'applicazione tramite il sito aziendale di Druva (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Druva
* Configurazione dell'accesso Single Sign-On
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-druva-tutorial/IC795084.png "Scenario")

## <a name="enable-the-application-integration-for-druva"></a>Abilitare l'integrazione dell'applicazione per Druva
Questa sezione descrive come abilitare l'integrazione dell'applicazione per Druva.

**Per abilitare l'integrazione dell'applicazione per Druva, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-druva-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-druva-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-druva-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Druva**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-druva-tutorial/IC795085.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Druva** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a Druva tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  

Per eseguire questa procedura, è necessario creare un certificato con codifica Base&64;. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

L'applicazione Druva prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token SAML**. 

La schermata seguente illustra un esempio relativo a questa operazione.

![Attributi token SAML](./media/active-directory-saas-druva-tutorial/IC795087.png "Attributi token SAML")

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Druva** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795027.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Druva** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795088.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **Druva Sign On URL** (URL di accesso Druva) della pagina **Configura URL app** digitare l'URL utilizzato dagli utenti per accedere all'applicazione Druva, ad esempio "*https://cloud.druva.com/home/*", e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-druva-tutorial/IC795089.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Druva** fare clic su **Download certificato** per scaricare il file di certificato e quindi salvarlo localmente nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795090.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Druva come amministratore.
6. Passare a **Manage \> Settings**.
   
   ![Impostazioni](./media/active-directory-saas-druva-tutorial/IC795091.png "Impostazioni")
7. Nella finestra di dialogo Single Sign-On Settings seguire questa procedura:
   
   ![Impostazioni accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795092.png "Impostazioni accesso Single Sign-On")   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in Druva** del portale di Azure classico copiare il valore di **URL accesso remoto** e quindi incollarlo nella casella di testo **ID Provider Login URL** (URL di accesso provider di identità).
   2. Nella finestra di dialogo **Configura accesso Single Sign-On in Druva** del portale di Azure classico copiare il valore di **URL disconnessione remota** e quindi incollarlo nella casella di testo **ID Provider Logout URL** (URL di disconnessione provider di identità).
   3. Creare un file **con codifica Base&64;** dal certificato scaricato.  
     >[!TIP]
     >Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
     > 
   4. Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **ID Provider Certificate**
   5. Per aprire la pagina **Settings** fare clic su **Save**.
8. Nella pagina **Settings** fare clic su **Generate SSO Token**.
   
   ![Impostazioni](./media/active-directory-saas-druva-tutorial/IC795093.png "Impostazioni")
9. Nella finestra di dialogo **Single Sign-on Authentication Token** eseguire la procedura seguente:
   
   ![Token SSO](./media/active-directory-saas-druva-tutorial/IC795094.png "Token SSO")
   1. Fare clic su **Copy**.
   2. Fare clic su **Close**.
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-druva-tutorial/IC795095.png "Configurare l'accesso Single Sign-On")
11. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** .
    
    ![Attributi](./media/active-directory-saas-druva-tutorial/IC795096.png "Attributi")
12. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
    
   | Nome attributo | Valore attributo |
   | --- | --- |
   | insync\_auth\_token |<*valore negli Appunti*> |
    
   1. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
   2. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
   3. Nella casella di testo **Valore attributo** , digitare il valore dell'attributo indicato per la riga.
   4. Fare clic su **Complete**.
13. Fare clic su **Applica modifiche**.
    
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Druva, è necessario eseguirne il provisioning in Druva. 

* Nel caso di Druva, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di **Druva** come amministratore.
2. Passare a **Manage (Gestisci) \> Users (Utenti)**.
   
   ![Gestione utenti](./media/active-directory-saas-druva-tutorial/IC795097.png "Gestione utenti")
3. Fare clic su **Create New**.
   
   ![Gestione utenti](./media/active-directory-saas-druva-tutorial/IC795098.png "Gestione utenti")
4. Nella finestra di dialogo Create New User seguire questa procedura:
   
   ![Crea nuovo utente](./media/active-directory-saas-druva-tutorial/IC795099.png "Crea nuovo utente")
   
   1. Digitare l'indirizzo di posta elettronica e il nome di un account utente Azure Active Directory valido di cui si vuole effettuare il provisioning nelle caselle di testo corrispondenti.
   2. Fare clic su **Create User**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Druva per eseguire il provisioning degli account utente di AAD.
> 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Druva, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Druva** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-druva-tutorial/IC795100.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-druva-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



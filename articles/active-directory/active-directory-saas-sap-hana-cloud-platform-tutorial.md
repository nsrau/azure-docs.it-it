---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP HANA Cloud Platform | Microsoft Docs'
description: Informazioni su come usare SAP HANA Cloud Platform con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e03bc2410a8d57363c558f723b3bfd0e69b3f4c0
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Esercitazione: Integrazione di Azure Active Directory con SAP HANA Cloud Platform
In questa esercitazione viene illustrata l'integrazione di Azure e SAP HANA Cloud Platform.

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Account di SAP HANA Cloud Platform

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a SAP HANA Cloud Platform saranno in grado di eseguire l'accesso Single Sign-On all'applicazione seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>È necessario distribuire l'applicazione o la sottoscrizione a un'applicazione per l'account di SAP HANA Cloud Platform per testare l'accesso Single Sign-On. In questa esercitazione, viene distribuita un'applicazione nell'account.
> 
> 

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per SAP HANA Cloud Platform
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Assegnazione di un ruolo a un utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")

## <a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Abilitazione dell'integrazione dell'applicazione per SAP HANA Cloud Platform
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per SAP HANA Cloud Platform.

**Per abilitare l'integrazione dell'applicazione per SAP HANA Cloud Platform, seguire questa procedura:**

1. Nel portale di gestione di Azure fare clic su **Active Directory**nel pannello di navigazione sinistro.
   
    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **SAP HANA Cloud Platform**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **SAP HANA Cloud Platform**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a SAP HANA Cloud Platform tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

Per eseguire questa procedura, è necessario caricare un file di certificato con codifica Base 64 nel proprio tenant SAP HANA Cloud Platform.  

Se non si ha familiarità con questa procedura, vedere il video che descrive [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP HANA Cloud Platform** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a SAP HANA Cloud Platform** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configurare l'accesso Single Sign-On")
3. In una diversa finestra del Web browser accedere al pannello di controllo di SAP HANA Cloud Platform in https://account.\<landscape host\>.ondemand.com/cockpit (ad esempio *https://account.hanatrial.ondemand.com/cockpit*).
4. Scegliere la scheda **Trust** .
   
    ![Trust](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")
5. Nella sezione relativa alla gestione dell'attendibilità, eseguire la procedura seguente:
   
    ![Ottenere i metadati](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Ottenere i metadati")
   
   1. Fare clic sulla scheda **Local Service Provider** .
   2. Per scaricare il file di metadati di SAP HANA Cloud Platform, fare clic su **Get Metadata**.
6. Nella pagina **Configura URL app** del portale di Azure Active classico eseguire la procedura seguente e fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configurare l'URL dell'app")
   
   1. Nella casella di testo **URL di accesso** digitare l'URL utilizzato dagli utenti per accedere all'applicazione **SAP HANA Cloud Platform**. Questo è l'URL specifico dell'account di una risorsa protetta dell'applicazione SAP HANA Cloud Platform. L'URL è basato sul modello seguente: *https://\<nomeApplicazione\>\<nomeAccount\>.\<landscape host\>.ondemand.com/\<percorso\_alle\_risosrse\_protette\>* (ad esempio *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)
      
     >[!NOTE]
     >Questo è l'URL dell'applicazione SAP HANA Cloud Platform che richiede l’autenticazione dell’utente.
     > 

   2. Aprire il file di metadati SAP HANA Cloud piattaforma scaricato e quindi individuare il tag **ns3:AssertionConsumerService** .
   3. Copiare il valore dell'attributo **Percorso** e incollarlo nella casella di testo **SAP HANA Cloud Platform Reply URL**.

7. Nella pagina **Configura accesso Single Sign-On in SAP HANA Cloud Platform** fare clic su **Scarica metadati** per scaricare i metadati e salvare il file nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configurare l'accesso Single Sign-On")
8. Nel pannello di controllo di SAP HANA Cloud Platform, nella sezione **Local Service Provider** , eseguire la procedura seguente:
   
    ![Gestione dell'attendibilità](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Gestione dell'attendibilità")
   
  1. Fare clic su **Modifica**.
  2. In **Configuration Type** selezionare **Custom**.
  3. In **Local Provider Name**lasciare il valore predefinito.
  4. Per generare una **chiave per la firma** e una coppia di chiavi del **certificato di firma**, fare clic su **Generate Key Pair**.
  5. In **Propagazione entità** selezionare **Disabilitato**.
  6. In **Force Authentication** selezionare **Disabled**.
  7. Fare clic su **Save**.

9. Fare clic sulla scheda **Trusted Identity Provider**, quindi fare clic su **Add Trusted Identity Provider**.
   
    ![Gestione dell'attendibilità](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Gestione dell'attendibilità")
   
    >[!NOTE]
    >Per gestire l'elenco dei provider di identità attendibili, è necessario aver scelto il tipo di configurazione Personalizza nella sezione Provider di servizi locale. Per il tipo di configurazione predefinito è presente una relazione di trust implicita e non modificabile per il servizio SAP ID. Per Nessuno, non sono disponibili impostazioni di trust.
    > 
    > 

10. Fare clic sulla scheda **General**, quindi fare clic su **Browse** per caricare il file di metadati scaricati.
    
    ![Gestione dell'attendibilità](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Gestione dell'attendibilità")
    
    >[!NOTE]
    >Dopo aver caricato il file di metadati, i valori per **URL Single Sign-On**, **URL disconnessione singola** e **Certificato di firma** vengono popolati automaticamente.
    > 
    > 

11. Fare clic sulla scheda **Attributes** .
12. Nella scheda **Attributes** eseguire la procedura seguente:
    
    ![Attributi](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributi") 
  * Fare clic su **Add Assertion-Based Attribute** e quindi aggiungere gli attributi basati su asserzione seguenti:
       
    | Attributo di asserzione | Attributo di entità |
    | --- | --- |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |firstname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |Lastname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress |email 
   
     >[!NOTE]
     >La configurazione degli attributi dipende da come le applicazioni per HPC vengono sviluppate, vale a dire quali attributi sono previsti nella risposta SAML e con quale nome (attributo di entità) accedono a questo attributo nel codice.
     > 
     >  

    1.  L'**attributo predefinito** presente nella schermata è solo a scopo illustrativo. Non è necessario per il funzionamento dello scenario.   
    2.  I nomi e i valori per l'**attributo di entità** illustrati nella schermata dipendono dal modo in cui viene sviluppata l'applicazione. È possibile che l'applicazione richieda mapping diversi.
     
13. Nella pagina della finestra di dialogo **Configure single sign-on at SAP HANA Cloud Platform** (Configura accesso Single Sign-On in SAP HANA Cloud Platform) del portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On, quindi fare clic su **Completa**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configurare l'accesso Single Sign-On")

###<a name="assertion-based-groups"></a>Gruppi basati su asserzione
Come passaggio facoltativo, è possibile configurare gruppi basati su asserzione per il provider di identità Azure Active Directory.

L'uso dei gruppi in SAP HANA Cloud Platform consente di assegnare dinamicamente uno o più utenti a uno o più ruoli nelle applicazioni SAP HANA Cloud Platform, determinate dai valori degli attributi nell'asserzione SAML 2.0. 

Ad esempio, se l'asserzione contiene l'attributo "*contract=temporary*", si potrebbe volere che tutti gli utenti interessati siano aggiunti al gruppo"*TEMPORARY*". Il gruppo "*TEMPORARY*" può contenere uno o più ruoli da uno o più applicazioni distribuite nell'account di SAP HANA Cloud Platform.
 
Usare i gruppi basati su asserzione quando si vogliono assegnare simultaneamente diversi utenti a uno o più ruoli delle applicazioni nell'account di SAP HANA Cloud Platform. Per assegnare un singolo utente o un numero ridotto di utenti a ruoli specifici, è consigliabile di eseguirne l'assegnazione diretta nella scheda **Authorizations** (Autorizzazioni) del pannello di controllo di SAP HANA Cloud Platform.

## <a name="assign-a-role-to-a-user"></a>Assegnare un ruolo a un utente
Per consentire agli utenti di Azure AD di accedere a SAP HANA Cloud Platform è necessario assegnare loro i ruoli in SAP HANA Cloud Platform.

**Per assegnare un ruolo a un utente, seguire questa procedura:**

1. Accedere al pannello di controllo di **SAP HANA Cloud Platform** .
2. Eseguire questi passaggi:
   
   ![Autorizzazioni](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorizzazioni")
   
  1. Fare clic su **Authorization**.
  2. Fare clic sulla scheda **Users** .
  3. Nella casella di testo **User** digitare l'indirizzo di posta elettronica dell'utente.
  4. Fare clic su **Assign** per assegnare l'utente a un ruolo.
  5. Fare clic su **Save**.

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a SAP HANA Cloud Platform, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione di **SAP HANA Cloud Platform** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



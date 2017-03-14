---
title: 'Esercitazione: Integrazione di Azure Active Directory con TimeOffManager | Microsoft Docs'
description: Informazioni su come usare TimeOffManager con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/16/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a028279bbc209b9bf9402813b687752f0320ce52
ms.openlocfilehash: 55a13e90ed5f8fd59a93bba507769e56594de568
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Esercitazione: Integrazione di Azure Active Directory con TimeOffManager
In questa esercitazione verrà illustrata l'integrazione di Azure e TimeOffManager.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di TimeOffManager abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a TimeOffManager potranno accedere all'applicazione tramite il sito aziendale con SSO di TimeOffManager (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per TimeOffManager
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scenario")

## <a name="enable-the-application-integration-for-timeoffmanager"></a>Abilitare l'integrazione dell'applicazione per TimeOffManager
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per TimeOffManager.

**Per abilitare l'integrazione dell'applicazione per TimeOffManager, eseguire la procedura seguente:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **TimeOffManager**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **TimeOffManager** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a TimeOffManager tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di caricare un file di certificato codificato in base&64; sul proprio tenant TimeOffManager. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare l'accesso Single Sign-On, seguire questa procedura:
1. Nella pagina di integrazione dell'applicazione **TimeOffManager** nel portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a TimeOffManager** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di risposta TimeOffManager** della pagina **Configura URL app** digitare l'URL di TimeOffManager AssertionConsumerService, ad esempio: "*https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*", quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configurare l'URL dell'app")
   
   È possibile ottenere l'URL di risposta dalla pagina delle impostazioni Single Sign-On di TimeOffManager.
   
   ![Single Sign-On Settings](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Single Sign-On Settings")
4. Nella pagina **Configura accesso Single Sign-On in TimeOffManager** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file di certificato sul computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di TimeOffManager come amministratore.
6. Andare a **Account \> Account Options \> Single Sign-On Settings** (Account > Opzioni account > Impostazioni Single Sign-On).
   
   ![Single Sign-On Settings](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Single Sign-On Settings")
7. Nella sezione **Single Sign-On Settings** , eseguire la procedura seguente:
   
   ![Single Sign-On Settings](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Single Sign-On Settings")
  1.  Creare un file **con codifica Base&64;** dal certificato scaricato.  
   
       >[!TIP] 
       >Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
       > 
  2.  Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509** .
  3.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico copiare il valore di **URL autorità di certificazione** e incollarlo nella casella di testo **Idp Issuer** (Autorità di certificazione IdP).
  4.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico copiare il valore di **URL accesso remoto** e incollarlo nella casella di testo **IdP Endpoint URL** (URL endpoint IdP).
  5.  Per **Enforce SAML** (Applica SAML), selezionare **No**.
  6.  Per **Auto-Create Users** (Crea utenti in automatico), selezionare **Sì**.
  7.  Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico copiare il valore di **URL disconnessione remota** e incollarlo nella casella di testo **Logout URL** (URL disconnessione), quindi fare clic su **Save Changes** (Salva modifiche).

1. Nella pagina della finestra di dialogo **Configura accesso Single Sign-On in TimeOffManager** del portale di Azure classico selezionare la conferma della configurazione e quindi fare clic su **Completa**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configurare l'accesso Single Sign-On")
2. Nel menu in alto fare clic su **Attributi** to open the **SAML Token Attributi** .
   
   ![Attributi](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributi")
3. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
   
   ![attributi token SAML](./media/active-directory-saas-timeoffmanager-tutorial/123.png "attributi token SAML")
   
   | Nome attributo | Valore attributo |
   | --- | --- |
   | Email |User.mail |
   | Firstname |User.givenname |
   | Lastname |User.surname |
  1.  Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
  2.  Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
  3.  Nella casella di testo **Valore attributo** , selezionare il valore dell'attributo indicato per la riga.
  4.  Fare clic su **Completa**.
4. Fare clic su **Applica modifiche**.

## <a name="configure-user-provisioning"></a>Configura provisioning utenti
Per consentire agli utenti di Azure AD di accedere a TimeOffManager, è necessario eseguirne il provisioning in TimeOffManager.  

TimeOffManager supporta solo il provisioning just in time dell'utente. Non è necessario eseguire alcuna operazione.  

Gli utenti vengono aggiunti automaticamente durante l'utilizzo di Single Sign-on primo accesso.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TimeOffManager per eseguire il provisioning degli account utente di Azure AD.
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a TimeOffManager, eseguire la procedura seguente:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **TimeOffManager**, fare clic su **Assign users** (Assegna utenti).
   
   ![Assegnare utenti](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



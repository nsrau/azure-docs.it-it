---
title: 'Esercitazione: Integrazione di Azure Active Directory con Picturepark | Documentazione Microsoft'
description: Informazioni su come usare Picturepark con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a6b6610bb4d4b427f525934146340a9cca6f52cb
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Esercitazione: Integrazione di Azure Active Directory con Picturepark
In questa esercitazione viene illustrata l'integrazione di Azure e Picturepark.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Picturepark

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Picturepark saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Picturepark (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Picturepark
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario")

## <a name="enable-the-application-integration-for-picturepark"></a>Abilitare l'integrazione dell'applicazione per Picturepark
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Picturepark.

**Per abilitare l'integrazione dell'applicazione per Picturepark, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Picturepark**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Picturepark**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")


## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Picturepark tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.  

Per configurare l'accesso SSO per Picturepark è necessario recuperare un valore di identificazione personale da un certificato.  

Se non si ha familiarità con questa procedura, vedere il video che descrive [come recuperare un valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Picturepark** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Picturepark** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **Picturepark Sign On URL** (URL di accesso a Picturepark) usando il modello "*http://company.picturepark.com*" e fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Picturepark** fare clic su **Scarica certificato** per scaricare il certificato e salvare il file del certificato localmente nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Picturepark come amministratore.
6. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi fare clic su **Console di gestione**.
   
   ![Console di gestione](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Console di gestione")
7. Fare clic su **Autenticazione**, quindi su **Provider di identità**.
   
   ![Autenticazione](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Autenticazione")
8. Nella sezione **Configurazione provider di identità** , eseguire questa procedura:
   
   ![Configurazione del provider di identità](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configurazione del provider di identità")
   
   1. Fare clic su **Aggiungi**.
   2. Digitare un nome per la configurazione.
   3. Selezionare **Imposta come predefinito**.
   4. Nella finestra di dialogo **Configure single sign-on at Picturepark** (Configura accesso Single Sign-On in Picturepark) del portale di Azure classico copiare il valore di **URL SSO SAML** e incollarlo nella casella di testo **Issuer URI** (URI autorità di certificazione).
   5. Copiare il valore **Identificazione personale** dal certificato esportato e quindi incollarlo nella casella di testo relativa all'**identificazione personale dell'autorità di certificazione attendibile**.  
      
      >[!TIP]
      >Per informazioni dettagliate, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](http://youtu.be/YKQF266SAxI).
      >
      >

9. Fare clic su **JoinDefaultUsersGroup**.
10. Per impostare l'attributo **Emailaddress** nella casella di testo **Claim** (Attestazione), digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** e fare clic su **Save** (Salva).

      ![Configurazione](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configurazione")
11. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurare l'accesso Single Sign-On")

## <a name="configure-user-provisioning"></a>Configura provisioning utenti
Per consentire agli utenti di Azure AD di accedere a Picturepark, è necessario eseguirne il provisioning in Picturepark.  

 * Nel caso di Picturepark, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Picturepark** .
2. Nella barra degli strumenti in alto fare clic su **Strumenti di amministrazione**, quindi su **Utenti**.
   
   ![Utenti](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Utenti")
3. Nella scheda **Panoramica utenti** fare clic su **Nuovo**.
   
   ![Gestione degli utenti](./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gestione degli utenti")
4. Nella finestra di dialogo **Create User** seguire questa procedura:
   
   ![Crea utente](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Crea utente")
   
  1. Digitare l'**indirizzo di posta elettronica**, la **password**, la **password di conferma**, il **nome**, il **cognome**, l'**azienda**, il **paese**, il **CAP** e la **città** di un utente di Azure Active Directory valido di cui si vuole effettuare il provisioning nelle relative caselle di testo.
  2. Selezionare una **Language**.
  3. Fare clic su **Crea**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Picturepark per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Picturepark, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Picturepark** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



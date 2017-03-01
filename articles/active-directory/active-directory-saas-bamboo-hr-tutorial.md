---
title: 'Esercitazione: Integrazione di Azure Active Directory con BambooHR | Microsoft Docs'
description: Informazioni su come usare BambooHR con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 308c9f4d59f6c6981af74f4208c9963e3d0102dd
ms.openlocfilehash: 6644afc1de68230ccca089529b32195c4c8ddfe7
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Esercitazione: Integrazione di Azure Active Directory con BambooHR
Questa esercitazione descrive l'integrazione di Azure e BambooHR.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di BambooHR abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a BambooHR potranno accedere all'applicazione tramite il sito aziendale di BambooHR (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per BambooHR
* Configurazione dell'accesso Single Sign-On
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")

## <a name="enable-the-application-integration-for-bamboohr"></a>Abilitare l'integrazione dell'applicazione per BambooHR
Questa sezione descrive come abilitare l'integrazione dell'applicazione per BambooHR.

**Per abilitare l'integrazione dell'applicazione per BambooHR, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **BambooHR**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **BambooHR** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Questa sezione descrive come consentire agli utenti di eseguire l'autenticazione a BambooHR tramite il proprio account in Azure AD usando la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **BambooHR** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Scenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a BambooHR** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app** nella casella di testo **BambooHR Sign On URL** (URL di accesso a BambooHR), digitare l'URL usato dagli utenti per accedere all'applicazione BambooHR (ad esempio: https://company.bamboohr.com) e fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in BambooHR** fare clic su **Scarica certificato** e quindi salvare il file di certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di BambooHR come amministratore.
6. Nella pagina iniziale seguire questa procedura:
   
   ![Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Single Sign-On")   
   1. Fare clic su **App**.
   2. Scegliere **Single Sign-On**dal menu delle app a sinistra.
   3. Fare clic su **SAML Single Sign-On**.
7. Nella sezione **SAML Single Sign-On** seguire questa procedura:
   
   ![Single Sign-On SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Single Sign-On SAML")
   
   1. Nella finestra di dialogo **Configure single sign-on at BambooHR** (Configura accesso Single Sign-On in BambooHR) del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **SSO Login URL** (URL di accesso SSO).
   2. Creare un file con **codifica Base&64;** dal certificato scaricato.  
   
      >[!TIP]
      >Per informazioni dettagliate, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o). 
      > 
   3. Aprire il certificato con codifica Base&64; nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato X.509**
   4. Fare clic su **Salva**.
8. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a BambooHR, è necessario eseguirne il provisioning in BambooHR.  

* Nel caso di BambooHR, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **BambooHR** come amministratore.
2. Nel barra degli strumenti in alto fare clic su **Impostazioni**.
   
   ![Impostazione](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Impostazione")
3. Fare clic su **Panoramica**.
4. Nel riquadro di spostamento sinistro passare a **Sicurezza \> Utenti**.
5. Nelle caselle di testo corrispondenti digitare il nome utente, la password e l'indirizzo di posta elettronica di un account AAD valido di cui si vuole eseguire il provisioning.
6. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da BambooHR per eseguire il provisioning degli account utente di Azure AD. 
> 

## <a name="assig-users"></a>Assegnare utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a BambooHR, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **BambooHR** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



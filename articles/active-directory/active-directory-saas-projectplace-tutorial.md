---
title: 'Esercitazione: Integrazione di Azure Active Directory con Projectplace | Documentazione Microsoft'
description: Informazioni su come usare Projectplace con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fcf3332a6674524ea5354b96826586c79a4ba3e1
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Esercitazione: Integrazione di Azure Active Directory con Projectplace
In questa esercitazione viene illustrata l'integrazione di Azure e Projectplace. Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Projectplace abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Projectplace saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Projectplace (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Projectplace
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")

## <a name="enabling-the-application-integration-for-projectplace"></a>Abilitazione dell'integrazione dell'applicazione per Projectplace
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Projectplace.

**Per abilitare l'integrazione dell'applicazione per Projectplace, seguire questa procedura:**
1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Projectplace**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Projectplace**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![ProjectPlace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Projectplace tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Projectplace** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Proectplace** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configurare l'accesso Single Sign-On")
3. Nella pagina **Configura URL app**, nella casella di testo **Projectplace Sign On URL** (URL di accesso a Projectplace), digitare l'URL del tenant Projectplace, ad esempio "*http://company.projectplace.com*", quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Projectplace** fare clic su **Scarica metadati**, quindi salvare il file di metadati nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configurare l'accesso Single Sign-On")
5. Inviare il file di metadati al team di supporto di Projectplace.
   
   >[!NOTE]
   >La configurazione dell'accesso Single Sign-On deve essere eseguita dal team di supporto di Projectplace. Al termine della configurazione, viene ricevuta una notifica.
   > 
   > 
6. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Projectplace, è necessario eseguirne il provisioning in Projectplace. Nel caso di Projectplace, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Projectplace** come amministratore.
2. Passare a **Persone**, quindi fare clic su **Membri**.
   
   ![Persone](./media/active-directory-saas-projectplace-tutorial/IC790228.png "Persone")
3. Fare clic su **Aggiungi membro**.
   
   ![Aggiungere membri](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Aggiungere membri")
4. Nella sezione **Aggiungi membro** , eseguire la procedura seguente:
   
   ![Nuovi membri](./media/active-directory-saas-projectplace-tutorial/IC790233.png "Nuovi membri")
   
   1. Nella casella di testo **Nuovi membri** , digitare l’indirizzo di posta elettronica di un account di Azure Active Directory valido di cui si desidera eseguire il provisioning nelle relative caselle di testo.
   2. Fare clic su **Send**.

Un messaggio di posta elettronica contenente un collegamento da selezionare per confermare e attivare l'account, viene inviato al titolare dell'account Azure Active Directory.


>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Projectplace per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Projectplace, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Projectplace** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



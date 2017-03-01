---
title: 'Esercitazione: Integrazione di Azure Active Directory con Kudos | Documentazione Microsoft'
description: Informazioni su come utilizzare Kudos con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ea39ca12135fded44a8e70ec87616f79007e0200
ms.openlocfilehash: 79915c69e2d40529ec5ad96a632e2e5b2ac4dd5e
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Esercitazione: integrazione di Azure Active Directory con Kudos
In questa esercitazione viene illustrata l'integrazione di Azure e Kudos.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Kudos

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Kudos saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Kudos (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

* Abilitazione dell'integrazione dell'applicazione per Kudos
* Configurazione dell'accesso Single Sign-On (SSO)
* Configurazione del provisioning utente
* Assegnazione degli utenti

![Scenario](./media/active-directory-saas-kudos-tutorial/IC787799.png "Scenario")

## <a name="enabling-the-application-integration-for-kudos"></a>Abilitazione dell'integrazione dell'applicazione per Kudos
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Kudos.

**Per abilitare l'integrazione dell'applicazione per Kudos eseguire la procedura seguente:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-kudos-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-kudos-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-kudos-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Kudos**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-kudos-tutorial/IC787800.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Kudos** e quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Kudos tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.  

Come parte di questa procedura, verrà richiesto di creare un file di certificato con codifica Base&64;. Se non si ha familiarità con questa procedura, vedere il video che illustra [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Kudos** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurare l'accesso Single Sign-On")
2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Kudos** selezionare **Single Sign-On di Microsoft Azure AD** e quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurare l'accesso Single Sign-On")
3. Nella casella di testo **URL di accesso Kudos** della pagina **Configure App URL** (Configura URL app) digitare l'URL usando il modello "**https://company.kudosnow.com**" e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurare l'URL dell'app")
4. Nella pagina **Configura accesso Single Sign-On in Kudos** fare clic su **Scarica certificato** e quindi salvare il file del certificato nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurare l'accesso Single Sign-On")
5. In un'altra finestra del Web browser accedere al sito aziendale di Kudos come amministratore.
6. Nel menu in alto fare clic su **Impostazioni**.
   
   ![Impostazioni](./media/active-directory-saas-kudos-tutorial/IC787806.png "Impostazioni")
7. Fare clic su **Integrations (Integrazioni) \> SSO**.
8. Nella sezione **SSO** eseguire la procedura seguente:
   
   ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")
   
   1. Nella finestra di dialogo **Configura accesso Single Sign-On in Kudos** del portale di Azure classico copiare il valore di **URL servizio Single Sign-On** e incollarlo nella casella di testo **Sign on URL** (URL di accesso).
   2. Creare un file **con codifica Base&64;** dal certificato scaricato. 
   
      >[!TIP]
      >Per informazioni dettagliate, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o) 
      > 
   3. Aprire il certificato con codifica base&64; nel blocco note, copiarne il contenuto negli appunti e incollarlo nella casella di testo **Certificato X.509**
   4. Nella finestra di dialogo **Configura accesso Single Sign-On in Kudos** del portale di Azure classico copiare il valore di **URL servizio Single Sign-Out** e incollarlo nella casella di testo **Logout To URL** (URL di disconnessione).
   5. Nella casella di testo **URL di Kudos** digitare il nome della società.
   6. Fare clic su **Save**.
9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere a Kudos, è necessario eseguirne il provisioning in Kudos. 

* Nel caso di Kudos, il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Kudos** come amministratore.
2. Nel menu in alto fare clic su **Impostazioni**.
   
   ![Impostazioni](./media/active-directory-saas-kudos-tutorial/IC787806.png "Impostazioni")
3. Fare clic su **User Admin**.
4. Fare clic sulla scheda **Users** (Utenti) e quindi su **Add a User** (Aggiungi un utente).
   
   ![Amministratore utenti](./media/active-directory-saas-kudos-tutorial/IC787809.png "Amministratore utenti")
5. Nella sezione **Aggiungi un utente** eseguire la procedura seguente:
   
   ![Aggiungere un utente](./media/active-directory-saas-kudos-tutorial/IC787810.png "Aggiungere un utente")
   
  1. Nelle caselle **First Name**, **Last Name**, **Email** e così via immettere il nome, cognome, indirizzo di posta elettronica e altri dettagli di un account Azure Active Directory valido di cui si vuole eseguire il provisioning.
   2. Fare clic su **Crea utente**.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Kudos o le API fornite da Kudos per eseguire il provisioning degli account utente di AAD.
>  

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Kudos, eseguire la procedura seguente:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Kudos** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-kudos-tutorial/IC787811.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-kudos-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



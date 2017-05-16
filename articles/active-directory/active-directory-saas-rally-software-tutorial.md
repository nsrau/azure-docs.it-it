---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rally Software | Documentazione Microsoft'
description: Informazioni su come usare Rally Software con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8d913c024fce9945cfd5eaf336deed259dfbef50
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Esercitazione: Integrazione di Azure Active Directory con Rally Software
In questa esercitazione viene illustrata l'integrazione di Azure e Rally Software.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Tenant Rally Software

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Rally Software
2. Configurazione dell'accesso Single Sign-On (SSO)
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")

## <a name="enable-the-application-integration-for-rally-software"></a>Abilitare l'integrazione dell'applicazione per Rally Software
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Rally Software.

**Per abilitare l'integrazione dell'applicazione per Rally Software, seguire questa procedura:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.

3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
    ![Applicazioni](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applicazioni")

4. Fare clic su **Add** nella parte inferiore della pagina.
   
    ![Aggiungere un'applicazione](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Aggiungere un'applicazione")

5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
    ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6. Nella **casella di ricerca** digitare **rally**.
   
    ![Raccolta di applicazioni](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Raccolta di applicazioni")

7. Nel riquadro dei risultati selezionare **Rally Software**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
   
## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Rally Software tramite il proprio account di Azure AD usando la federazione basata sul protocollo SAML.

Per eseguire questa procedura, è necessario caricare un certificato in Rally Software.

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Rally Software** del portale di Azure classico fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurare l'accesso Single Sign-On")

2. Nella pagina **Stabilire come si desidera che gli utenti accedano a Rally** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
    ![Accesso Single Sign-On di Microsoft Azure AD](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Accesso Single Sign-On di Microsoft Azure AD")

3. Nella pagina **Configura URL app** digitare l'URL nella casella di testo **URL del tenant Rally Software** usando il modello seguente "*https://\<tenant-name\>.rally.com*" e fare clic su **Avanti**.
   
    ![Configurare l'URL dell'app](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurare l'URL dell'app")

4. Nella pagina **Configura accesso Single Sign-On in Rally** fare clic su Scarica metadati, quindi salvare il file nel computer.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurare l'accesso Single Sign-On")

5. Accedere al tenant **Rally Software** .

6. Nella barra degli strumenti in alto fare clic su **Setup** (Configurazione), quindi selezionare **Subscription** (Sottoscrizione).
   
    ![Sottoscrizione](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Sottoscrizione")

7. Scegliere il pulsante **Action** (Azione) nella barra degli strumenti in alto a destra e selezionare **Edit Subscription** (Modifica sottoscrizione).

8. Nella finestra di dialogo **Subscription** (Sottoscrizione) eseguire la procedura seguente, quindi fare clic su **Save & Close** (Salva e chiudi):
   
    ![Autenticazione](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticazione")
   
  1. Selezionare **Rally or SSO authentication** (Autenticazione Rally o SSO) dall'elenco a discesa Authentication (Autenticazione).
  2. Nella finestra di dialogo **Configura accesso Single Sign-On in Rally Software** del portale di Azure classico copiare il valore di **ID provider di identità** e quindi incollarlo nella casella di testo **Identity Provider URL** (URL del provider di identità).
  3. Nella finestra di dialogo **Configure single sign-on at Rally Software** (Configura accesso Single Sign-On in Rally Software) del portale di Azure classico copiare il valore **URL disconnessione remota**.

9. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
   
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurare l'accesso Single Sign-On")
   
## <a name="configure-user-provisioning"></a>Configura provisioning utenti

Per consentire agli utenti di Azure AD di accedere, è necessario eseguirne il provisioning nell’applicazione Rally Software utilizzando i relativi nomi utente di Azure Active Directory.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al tenant Rally Software.

2. Passare a **Impostazione \> UTENTI** e quindi fare clic su **+ Aggiungi nuovo**.
   
    ![Utenti](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Utenti")

3. Digitare il nome nella casella di testo Nuovo utente, quindi fare clic su **Aggiungi con dettagli**.

4. Nella sezione **Crea utente** , eseguire la procedura seguente:
   
    ![Crea utente](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Crea utente")
   
   1. Nella casella di testo **Nome utente** digitare il nome dell'utente di Azure AD di cui si desidera eseguire il provisioning.
   2. Nella casella di testo **Indirizzo di posta elettronica** digitare l’indirizzo di posta elettronica dell'utente di Azure AD di cui si desidera eseguire il provisioning.
   3. Fare clic su **Salva e chiudi**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente offerti da Rally Software per eseguire il provisioning degli account utente di Azure AD.
> 
> 

## <a name="assign-users"></a>Assegna utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Rally Software, seguire questa procedura:**

1. Nel portale di Azure classico creare un account di test.

2. Nella pagina di integrazione dell'applicazione **Rally Software** fare clic su **Assegna utenti**.
   
    ![Assegnare utenti](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assegnare utenti")

3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
    ![Sì](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sì")

Per testare le impostazioni di SSO, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



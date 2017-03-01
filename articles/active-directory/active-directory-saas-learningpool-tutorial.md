---
title: 'Esercitazione: Integrazione di Azure Active Directory con Learningpool | Microsoft Docs'
description: Informazioni su come utilizzare Learningpool con Azure Active Directory per abilitare l&quot;accesso Single Sign-On, il provisioning automatizzato e altro ancora.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83afd8112cf7835528a1e3e8f30b52d4cf863ae3
ms.openlocfilehash: c5c530fcc158a75b1f1ba140e364c702350ce975
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Esercitazione: Integrazione di Azure Active Directory con Learningpool
In questa esercitazione viene illustrata l'integrazione di Azure e Learningpool.  

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Sottoscrizione di Azure valida
* Sottoscrizione di Learningpool abilitata per l'accesso Single Sign-On (SSO)

Al termine dell'esercitazione, gli utenti di Azure AD assegnati a Learningpool saranno in grado di eseguire l’accesso Single Sign-On all'applicazione tramite il sito aziendale di Learningpool (accesso avviato dal provider di servizi) o seguendo le istruzioni riportate in [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)

Lo scenario descritto in questa esercitazione include i blocchi predefiniti seguenti:

1. Abilitazione dell'integrazione dell'applicazione per Learningpool
2. Configurazione dell'accesso Single Sign-On
3. Configurazione del provisioning utente
4. Assegnazione degli utenti

![Scenario](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-learningpool"></a>Abilitazione dell'integrazione dell'applicazione per Learningpool
In questa sezione viene descritto come abilitare l'integrazione dell'applicazione per Learningpool.

**Per abilitare l'integrazione dell'applicazione per Learningpool, eseguire la procedura seguente:**

1. Nel portale di Azure classico fare clic su **Active Directory**nel riquadro di spostamento sinistro.
   
   ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")
2. Nell'elenco **Directory** selezionare la directory per la quale si desidera abilitare l'integrazione delle directory.
3. Per aprire la visualizzazione applicazioni, nella visualizzazione directory fare clic su **Applications** nel menu superiore.
   
   ![Applicazioni](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applicazioni")
4. Fare clic su **Add** nella parte inferiore della pagina.
   
   ![Aggiungere un'applicazione](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Aggiungere un'applicazione")
5. Nella finestra di dialogo **Come procedere** fare clic su **Aggiungere un'applicazione dalla raccolta**.
   
   ![Aggiungere un'applicazione dalla raccolta](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")
6. Nella **casella di ricerca** digitare **Learningpool**.
   
   ![Raccolta di applicazioni](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Raccolta di applicazioni")
7. Nel riquadro dei risultati selezionare **Learningpool**, quindi fare clic su **Completa** per aggiungere l'applicazione.
   
   ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
   
## <a name="configuring-single-sign-on"></a>Configurazione dell'accesso Single Sign-On

In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Learningpool tramite il relativo account in Azure AD utilizzando la federazione basata sul protocollo SAML.

L'applicazione Learningpool prevede le asserzioni SAML in un formato specifico. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli **attributi del token saml**.  
La schermata seguente illustra un esempio relativo a questa operazione.

![Attributi token SAML](./media/active-directory-saas-learningpool-tutorial/IC795074.png "Attributi token SAML")

**Per configurare l'accesso Single Sign-On, seguire questa procedura:**

1. Nel menu visualizzato nella parte superiore della pagina di integrazione dell'applicazione **Learningpool** del portale di Azure classico fare clic su **Attributi** per aprire la finestra di dialogo **Attributi token SAML**.
   
   ![Attributi](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributi")
2. Per aggiungere i mapping di attributi obbligatori, eseguire la procedura seguente:
   
   ### 
   | Nome attributo | Valore attributo |
   | --- | --- |
   | urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname |
   |  urn:oid:2.5.4.42 |User.givenname |
   | urn:oid:0.9.2342.19200300.100.1.3 |User.mail |
   | urn:oid:2.5.4.4 |User.surname |
   
   1. Per ogni riga di dati nella tabella precedente, fare clic su **aggiungi attributo utente**.
   2. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo indicato per quella riga.
   3. Nell’elenco **Valore attributo** selezionare il valore dell'attributo indicato per quella riga.
   4. Fare clic su **Complete**.
3. Fare clic su **Applica modifiche**.
4. Nel browser fare clic su **Indietro** per aprire nuovamente la finestra di dialogo **Avvio rapido**.
5. Fare clic su **Configura accesso Single Sign-On** per aprire la finestra di dialogo **Configura accesso Single Sign-On**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurare l'accesso Single Sign-On")
6. Nella pagina **Stabilire come si desidera che gli utenti accedano a Learningpool** selezionare **Single Sign-On di Microsoft Azure AD**, quindi fare clic su **Avanti**.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurare l'accesso Single Sign-On")
7. Nella casella di testo **Learningpool Sign On URL** (URL di accesso Learningpool) della pagina **Configura URL app** digitare l'URL usato dagli utenti per accedere all'applicazione Learningpool, ad esempio https://parliament.preview.learningpool.com/auth/shibboleth/index.php e quindi fare clic su **Avanti**.
   
   ![Configurare l'URL dell'app](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurare l'URL dell'app")
8. Nella pagina **Configura accesso Single Sign-On in Learningpool** fare clic su **Scarica metadati** per scaricare i metadati, quindi salvare il file di certificato localmente nel computer.
   
   ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurare l'accesso Single Sign-On")
9. Inoltrare il file dei metadati al team di supporto di Learningpool.
   
   >[!NOTE]
   >L'accesso Single Sign-On deve essere abilitato dal team di supporto di Learningpool.
   > 
   
10. Nel portale di Azure classico selezionare la conferma della configurazione dell'accesso Single Sign-On e quindi fare clic su **Complete** per chiudere la finestra di dialogo **Configura accesso Single Sign-On**.
    
    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurare l'accesso Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>Configurazione del provisioning utente

Per consentire agli utenti di Azure AD di accedere a Learningpool, è necessario eseguirne il provisioning in Learningpool.

Non è richiesta alcuna operazione per configurare il provisioning degli utenti in Learningpool.  
Gli utenti dovranno essere creati dal team di supporto di Learningpool.

>[!NOTE]
>È possibile utilizzare qualsiasi altro strumento di creazione di account utente di Learningpool o le API fornite da Learningpool per eseguire il provisioning degli account utente di AAD. 
> 

## <a name="assigning-users"></a>Assegnazione degli utenti
Per testare la configurazione, è necessario concedere l'accesso all'applicazione agli utenti di Azure AD a cui si vuole consentirne l'uso, assegnando tali utenti all'applicazione.

**Per assegnare gli utenti a Learningpool eseguire la procedura seguente:**

1. Nel portale di Azure classico creare un account di test.
2. Nella pagina di integrazione dell'applicazione **Learningpool** fare clic su **Assegna utenti**.
   
   ![Assegnare utenti](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assegnare utenti")
3. Selezionare l'utente di test, fare clic su **Assegna** e quindi su **Sì** per confermare l'assegnazione.
   
   ![Sì](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Sì")

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).



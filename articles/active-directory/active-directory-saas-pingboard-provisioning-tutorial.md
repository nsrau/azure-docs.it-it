---
title: 'Esercitazione: Configurazione di Pingboard per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Pingboard.
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 36fc33813595183856713f01b5a94f84e1f3db4e
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Esercitazione: Configurare Pingboard per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire per abilitare il provisioning e il deprovisioning automatici degli account utente da Azure Active Directory (Azure AD) a Pingboard.

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   Un [account Pro](https://pingboard.com/pricing) del tenant di Pingboard 
*   Un account utente in Pingboard con autorizzazioni di amministratore 

> [!NOTE] 
> L'integrazione del provisioning di Azure AD si basa sull'[API Pingboard](`https://your_domain.pingboard.com/scim/v2`), disponibile per l'account in uso.

## <a name="assign-users-to-pingboard"></a>Assegnare utenti a Pingboard

Per determinare gli utenti che dovranno ricevere l'accesso alle applicazioni selezionate, Azure AD usa un concetto detto "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti assegnati a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti in Azure AD devono accedere all'app Pingboard. È quindi possibile assegnare tali utenti all'app Pingboard seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Suggerimenti importanti per l'assegnazione di utenti a Pingboard

È consigliabile assegnare un singolo utente di Azure AD a Pingboard per testare la configurazione del provisioning. È possibile assegnare utenti aggiuntivi in un secondo momento.

## <a name="configure-user-provisioning-to-pingboard"></a>Configurare il provisioning utenti in Pingboard 

Questa sezione descrive la connessione di Azure AD all'API di provisioning degli account utente Pingboard. Il servizio di provisioning viene anche configurato per creare, aggiornare e disabilitare gli account utente assegnati in Pingboard in base alle assegnazioni utente in Azure AD.

> [!TIP]
> Per abilitare l'accesso Single Sign-On basato su SAML per Pingboard, seguire le istruzioni fornite nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Pingboard con Azure AD

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory** > **App aziendali** > **Tutte le applicazioni**.

2. Se si è già configurato Pingboard per l'accesso Single Sign-On, cercare l'istanza di Pingboard usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Pingboard** nella raccolta di applicazioni. Selezionare **Pingboard** nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

3. Selezionare l'istanza di Pingboard e quindi la scheda **Provisioning**.

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning in Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5. Nella sezione **Credenziali amministratore** seguire questa procedura:

    a. In **URL tenant**, immettere `https://your_domain.pingboard.com/scim/v2` e sostituire "your_domain" con il dominio effettivo.

    b. Accedere a [Pingboard](https://pingboard.com/) usando l'account amministratore.

    c. Selezionare **Add-Ons** > **Integrations** > **Azure Active Directory** (Componenti aggiuntivi > Integrazioni > Azure Active Directory).

    d. Passare alla scheda **Configure** (Configura) e selezionare **Enable user provisioning from Azure** (Abilita provisioning utenti da Azure).

    e. Copiare il token in **OAuth Bearer Token** (Token di connessione OAuth) e immetterlo in **Token segreto**.

6. Nel portale di Azure selezionare **Test connessione** per verificare che Azure AD possa connettersi all'app Pingboard. Se la connessione non riesce, verificare che l'account Pingboard abbia le autorizzazioni di amministrazione e ripetere il passaggio **Test connessione**.

7. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Indirizzo di posta elettronica per le notifiche**. Selezionare la casella di controllo sottostante.

8. Selezionare **Salva**. 

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Pingboard** (Sincronizza utenti di Azure Active Directory in Pingboard).

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente da sincronizzare da Azure AD a Pingboard. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Pingboard per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche. Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi del provisioning](active-directory-saas-customizing-attribute-mappings.md).

11. Per abilitare il servizio di provisioning di Azure AD per Pingboard, nella sezione **Impostazioni** impostare **Stato del provisioning** su **Sì**.

12. Fare clic su **Salva** per avviare la sincronizzazione iniziale degli utenti assegnati a Pingboard.

La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 20 minuti per tutto il tempo che il servizio è in esecuzione. Usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning. I report descrivono tutte le azioni eseguite dal servizio di provisioning nell'app Pingboard.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configurare l'accesso Single Sign-On](active-directory-saas-pingboard-tutorial.md)

---
title: 'Esercitazione: provisioning utenti per Pingboard-Azure AD'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Pingboard.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64c40d93f9b525ac6adeca276797df65f32ef3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77061276"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Esercitazione: Configurare Pingboard per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire per abilitare il provisioning e il deprovisioning automatici degli account utente da Azure Active Directory (Azure AD) a Pingboard.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD
* Un account Pingboard tenant [Pro](https://pingboard.com/pricing)
* Un account utente in Pingboard con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'[API Pingboard](https://pingboard.docs.apiary.io/#), disponibile per l'account in uso.

## <a name="assign-users-to-pingboard"></a>Assegnare utenti a Pingboard

Per determinare gli utenti che dovranno ricevere l'accesso alle applicazioni selezionate, Azure AD usa un concetto detto "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti assegnati a un'applicazione in Azure AD. 

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti in Azure AD devono accedere all'app Pingboard. È quindi possibile assegnare tali utenti all'app Pingboard seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Suggerimenti importanti per l'assegnazione di utenti a Pingboard

È consigliabile assegnare un singolo utente di Azure AD a Pingboard per testare la configurazione del provisioning. È possibile assegnare utenti aggiuntivi in un secondo momento.

## <a name="configure-user-provisioning-to-pingboard"></a>Configurare il provisioning utenti in Pingboard 

Questa sezione descrive la connessione di Azure AD all'API di provisioning degli account utente Pingboard. Il servizio di provisioning viene anche configurato per creare, aggiornare e disabilitare gli account utente assegnati in Pingboard in base alle assegnazioni utente in Azure AD.

> [!TIP]
> Per abilitare l'accesso Single Sign-On basato su SAML per Pingboard, seguire le istruzioni fornite nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in Pingboard con Azure AD

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory** > **App aziendali** > **Tutte le applicazioni**.

1. Se si è già configurato Pingboard per l'accesso Single Sign-On, cercare l'istanza di Pingboard usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **Pingboard** nella raccolta di applicazioni. Selezionare **Pingboard** nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di Pingboard e quindi la scheda **Provisioning**.

1. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning in Pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Nella sezione **Credenziali amministratore** seguire questa procedura:

    a. In **URL tenant**, immettere `https://your_domain.pingboard.com/scim/v2` e sostituire "your_domain" con il dominio effettivo.

    b. Accedere a [Pingboard](https://pingboard.com/) usando l'account amministratore.

    c. Selezionare **Add-Ons** > **integrazioni** > componenti aggiuntivi**Azure Active Directory**.

    d. Passare alla scheda **Configure** (Configura) e selezionare **Enable user provisioning from Azure** (Abilita provisioning utenti da Azure).

    e. Copiare il token in **OAuth Bearer Token** (Token di connessione OAuth) e immetterlo in **Token segreto**.

1. Nel portale di Azure selezionare **Test connessione** per verificare che Azure AD possa connettersi all'app Pingboard. Se la connessione non riesce, verificare che l'account Pingboard abbia le autorizzazioni di amministrazione e ripetere il passaggio **Test connessione**.

1. Immettere l'indirizzo di posta elettronica di una persona o di un gruppo per cui si desidera ricevere notifiche di errore di provisioning nel **messaggio di posta elettronica di notifica**. Selezionare la casella di controllo sottostante.

1. Selezionare **Salva**.

1. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Pingboard**.

1. Nella sezione **Mapping degli attributi** esaminare gli attributi utente da sincronizzare da Azure AD a Pingboard. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Pingboard per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche. Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi del provisioning](../app-provisioning/customize-application-attributes.md).

1. Per abilitare il servizio di provisioning di Azure AD per Pingboard, nella sezione **Impostazioni** impostare **Stato del provisioning** su **Sì**.

1. Fare clic su **Salva** per avviare la sincronizzazione iniziale degli utenti assegnati a Pingboard.

La sincronizzazione iniziale richiede più tempo di esecuzione delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. Usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log attività di provisioning. I log descrivono tutte le azioni eseguite dal servizio di provisioning nell'app Pingboard.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura accesso Single Sign-On](pingboard-tutorial.md)

---
title: 'Esercitazione: configurare NetSuite OneWorld per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e NetSuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 8406ee5647d02cc917a0fdb1daf2355611bb781d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792209"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Esercitazione: configurazione di NetSuite per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in NetSuite OneWorld e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a NetSuite.

> [!NOTE]
> Questa integrazione attualmente esegue l'autenticazione usando l'autenticazione di base (nome utente e password). NetSuite ha implementato un requisito di autenticazione a più fattori che impedisce ai clienti di usare questa integrazione, a meno che non dispongano di un'esenzione da questo requisito. Si sta lavorando con NetSuite per aggiornare questa integrazione a un metodo di autenticazione più recente per consentire ai clienti senza alcuna esenzione di riutilizzarla. Questo documento verrà aggiornato con un valore di ETA, una volta che ne è disponibile uno.

Azione consigliata: attendere fino a quando non viene rilasciato un aggiornamento del comportamento di autenticazione per questa integrazione oppure contattare il supporto di NetSuite per richiedere un'esenzione per il requisito di autenticazione a più fattori.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Sottoscrizione di NetSuite OneWorld. Si noti che il provisioning utenti automatico è attualmente supportato solo con NetSuite OneWorld.
*   Un account utente in Netsuite con autorizzazioni di amministratore.
*   Per l'integrazione con Azure AD è necessaria un'esenzione 2FA. Per richiedere l'esenzione, contattare il team di supporto di NetSuite.

## <a name="assigning-users-to-netsuite-oneworld"></a>Assegnazione di utenti a NetSuite OneWorld

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app NetSuite. Dopo aver stabilito questo, è possibile assegnare questi utenti all'app NetSuite seguendo le istruzioni riportate qui:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Suggerimenti importanti per l'assegnazione di utenti a NetSuite OneWorld

*   È consigliabile assegnare un singolo Azure AD utente a NetSuite per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a NetSuite, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione del Azure AD all'API di provisioning degli account utente di NetSuite e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in NetSuite in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP] 
> È anche possibile scegliere di abilitare il Sign-On Single basato su SAML per NetSuite, seguendo le istruzioni fornite in [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning"></a>Per configurare il provisioning degli account utente:

Questa sezione descrive come abilitare il provisioning degli account utente Active Directory a NetSuite.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni** .

1. Se NetSuite è già stato configurato per Single Sign-On, cercare l'istanza di NetSuite usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **NetSuite** nella raccolta di applicazioni. Selezionare NetSuite nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di NetSuite, quindi selezionare la scheda **provisioning** .

1. Impostare **Modalità di provisioning** su **Automatico** . 

    ![Screenshot mostra la pagina di provisioning di NetSuite, con la modalità di provisioning impostata su automatico e altri valori che è possibile impostare.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **nome utente amministratore** Digitare un nome di account NetSuite a cui è assegnato il profilo **amministratore di sistema** in NetSuite.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.
      
1. Nella portale di Azure fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi all'app NetSuite.

1. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo.

1. Fare clic su **Salva** .

1. Nella sezione Mapping selezionare **sincronizza Azure Active Directory utenti a NetSuite.**

1. Nella sezione **mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure ad a NetSuite. Si noti che gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in NetSuite per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

1. Per abilitare il servizio di provisioning Azure AD per NetSuite, impostare **stato del provisioning** **su** attivato nella sezione Impostazioni.

1. Fare clic su **Salva** .

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a NetSuite nella sezione utenti e gruppi. Notare che la sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app NetSuite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura accesso Single Sign-on](netsuite-tutorial.md)

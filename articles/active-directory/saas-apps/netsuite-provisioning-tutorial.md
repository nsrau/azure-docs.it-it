---
title: 'Esercitazione: Configurare NetSuite OneWorld per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NetSuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: a5b995fd273fcfa231e101bc77b11d268be728fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359136"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di NetSuite per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in NetSuite OneWorld e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a NetSuite.

> [!NOTE]
> Questa integrazione attualmente esegue l'autenticazione usando l'autenticazione di base (nome utente e password). NetSuite ha implementato un requisito di autenticazione a più fattori che impedisce ai clienti di usare questa integrazione, a meno che non siano esentati da questo requisito. Siamo collaborando con NetSuite per aggiornare questa integrazione con un nuovo metodo di autenticazione che consenta ai clienti privi di esenzione di riutilizzarla. Questo documento verrà aggiornato con il tempo di completamento stimato non appena sarà disponibile.

Azione consigliata: Attendere il rilascio di un aggiornamento del metodo di autenticazione per questa integrazione oppure contattare il supporto di NetSuite per richiedere un'esenzione per il requisito di autenticazione a più fattori.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Tenant di Azure Active Directory.
*   Una sottoscrizione di NetSuite OneWorld. Si noti che il provisioning utenti automatico è attualmente supportato solo con NetSuite OneWorld.
*   Un account utente in Netsuite con autorizzazioni di amministratore.
*   Per l'integrazione con Azure AD è necessaria un'esenzione 2FA. Per richiedere l'esenzione, contattare il team di supporto di NetSuite.

## <a name="assigning-users-to-netsuite-oneworld"></a>Assegnazione di utenti a NetSuite OneWorld

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, vengono sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere all'app NetSuite. Dopo averlo stabilito, è possibile assegnare gli utenti all'app NetSuite seguendo le istruzioni riportate in:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Suggerimenti importanti per l'assegnazione di utenti a Netsuite OneWorld

*   È consigliabile assegnare un singolo utente di Azure AD a NetSuite per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a NetSuite, è necessario selezionare un ruolo utente valido. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="enable-user-provisioning"></a>Abilitare il provisioning utenti

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente di NetSuite e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in NetSuite in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP] 
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per NetSuite, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-user-account-provisioning"></a>Per configurare il provisioning degli account utente:

Questa sezione descrive come abilitare il provisioning degli account utente di Active Directory in NetSuite.

1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

1. Se si è già configurato NetSuite per l'accesso Single Sign-On, cercare l'istanza di NetSuite usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **NetSuite** nella raccolta di applicazioni. Selezionare NetSuite nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

1. Selezionare l'istanza di NetSuite e quindi la scheda **Provisioning**.

1. Impostare **Modalità di provisioning** su **Automatico**. 

    ![Screenshot che mostra la pagina di provisioning di NetSuite, con l'opzione Modalità di provisioning impostata su Automatica e altri valori che è possibile impostare.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Nella sezione **Credenziali di amministratore** specificare le impostazioni di configurazione seguenti:
   
    a. Nella casella di testo **Nome utente amministratore** digitare un nome di account NetSuite che abbia il profilo **Amministratore di sistema** assegnato in NetSuite.com.
   
    b. Nella casella di testo **Password amministratore** digitare la password per questo account.
      
1. Nel portale di Azure fare clic su **Test connessione** per verificare che Azure AD possa connettersi all'app NetSuite.

1. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo.

1. Fare clic su **Salva**.

1. Nella sezione Mapping selezionare **Synchronize Azure Active Directory Users to Netsuite** (Sincronizza utenti di Azure Active Directory in NetSuite).

1. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a NetSuite. Si noti che gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in NetSuite per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

1. Per abilitare il servizio di provisioning di Azure AD per NetSuite, impostare **Stato del provisioning** su **Sì** nella sezione Impostazioni

1. Fare clic su **Salva**.

Viene avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a NetSuite nella sezione Utenti e gruppi. Notare che la sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti per tutto il tempo che il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app NetSuite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configurare l'accesso Single Sign-On](netsuite-tutorial.md)

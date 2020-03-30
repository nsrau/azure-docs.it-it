---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184351"
---
#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Nella pagina **Panoramica dell'app registrata** selezionare **Impostazioni**.
1. In **Accesso API**selezionare Autorizzazioni **necessarie**.
1. Selezionare **Microsoft Graph**.
1. In **Autorizzazioni applicazione**selezionare la casella di controllo dell'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **Lettura di tutti i dati**del log di controllo : Selezionare questa autorizzazione per la lettura dei registri di controllo della directory.
    * **Lettura e scrittura**dei dati della directory : selezionare questa autorizzazione per la migrazione degli utenti o per gli scenari di gestione degli utenti.
    * Leggere e scrivere i criteri del framework di **attendibilità dell'organizzazione:** selezionare questa autorizzazione per scenari di integrazione continua/distribuzione continua (CI/CD). Ad esempio, la distribuzione di criteri personalizzati con le pipeline di Azure.For example, custom policy deployment with Azure Pipelines.
1. Selezionare **Salva**.
1. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**. La propagazione completa delle autorizzazioni potrebbe richiedere alcuni minuti.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **Microsoft API** , quindi **selezionare Microsoft Graph**.
1. Selezionare **Autorizzazioni applicazione**.
1. Espandere il gruppo di autorizzazioni appropriato e selezionare la casella di controllo dell'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **AuditLog** > **AuditLog.Read.All:** per la lettura dei log di controllo della directory.
    * **Directory** > **Directory.ReadWrite.All:** per la migrazione degli utenti o per gli scenari di gestione degli utenti.
    * **Policy** > **Policy.ReadWrite.TrustFramework:** per scenari di integrazione continua/distribuzione continua (CI/CD). Ad esempio, la distribuzione di criteri personalizzati con le pipeline di Azure.For example, custom policy deployment with Azure Pipelines.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)**.
1. Selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna**, quindi verificare che "Concesso per ..." viene visualizzato in **Stato**. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

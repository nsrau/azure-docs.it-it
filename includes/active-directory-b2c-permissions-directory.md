---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: f8c972bdb9195008c2983d3993e8d9369749b284
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200176"
---
#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/) 

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **API Microsoft** , quindi selezionare **Microsoft Graph**.
1. Selezionare **Autorizzazioni applicazione**.
1. Espandere il gruppo di autorizzazioni appropriato e selezionare la casella di controllo relativa all'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **Auditlog**  >  **Auditlog. Read. All**: per leggere i log di controllo della directory.
    * **Directory**  >  di **Directory. ReadWrite. All**: per gli scenari di migrazione utenti o gestione utenti.
    * **Criteri**  >  di **Policy. ReadWrite. TrustFramework**: per gli scenari di integrazione continua/recapito continuo (ci/CD). Ad esempio, la distribuzione di criteri personalizzati con Azure Pipelines.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Se non si è attualmente connessi con l'account amministratore globale, accedere con un account nel tenant di Azure AD B2C a cui è stato assegnato almeno il ruolo di *amministratore dell'applicazione cloud* , quindi selezionare **concedi il consenso dell'amministratore per (il nome del tenant)**.
1. Selezionare **Aggiorna**, quindi verificare che "concesso per..." viene visualizzato in **stato**. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Nella pagina Panoramica dell' **app registrata** selezionare **Impostazioni**.
1. In **accesso all'API**selezionare **autorizzazioni necessarie**.
1. Selezionare **Microsoft Graph**.
1. In **Autorizzazioni applicazione**selezionare la casella di controllo relativa all'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **Leggi tutti i dati del registro di controllo**: selezionare questa autorizzazione per la lettura dei log di controllo della directory.
    * **Lettura e scrittura dei dati della directory**: selezionare questa autorizzazione per la migrazione degli utenti o gli scenari di gestione degli utenti.
    * **Leggere e scrivere i criteri del Framework di attendibilità dell'organizzazione**: selezionare questa autorizzazione per gli scenari di integrazione continua/recapito continuo (ci/CD). Ad esempio, la distribuzione di criteri personalizzati con Azure Pipelines.
1. Selezionare **Salva**.
1. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**. Potrebbero essere necessari alcuni minuti per la propagazione completa delle autorizzazioni.

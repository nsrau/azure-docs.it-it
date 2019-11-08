---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 43bcd1f11eb228bd1454b2ad0f2addb851029f2f
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799911"
---
#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Nella pagina Panoramica dell' **app registrata** selezionare **Impostazioni**.
1. In **accesso all'API**selezionare **autorizzazioni necessarie**.
1. Selezionare **Windows Azure Active Directory**.
1. In **Autorizzazioni applicazione**selezionare **lettura e scrittura dati directory**.
1. Selezionare **Salva**.
1. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**. Potrebbero essere necessari alcuni minuti per la propagazione completa delle autorizzazioni.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **autorizzazioni configurate**selezionare **Aggiungi un'autorizzazione**.
1. Selezionare **Azure Active Directory grafico**.
1. Selezionare **Autorizzazioni applicazione**.
1. Espandere **directory** e quindi selezionare la casella di controllo **Directory. ReadWrite. All** .
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere al passaggio successivo.
1. Selezionare **concedi il consenso dell'amministratore per (nome del tenant)** .
1. Selezionare un account amministratore tenant.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna**, quindi verificare che "concesso per..." viene visualizzato in **stato**. Potrebbero essere necessari alcuni minuti per la propagazione delle autorizzazioni.
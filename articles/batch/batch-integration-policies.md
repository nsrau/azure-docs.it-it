---
title: Integrazione con i criteri di Azure
description: Criteri di Azure è un servizio di Azure che consente di creare, assegnare e gestire criteri che applicano regole sulle risorse.
ms.topic: article
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 74c087031a024b0c50d6c40057a4e2eda554be6f
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116078"
---
# <a name="integration-with-azure-policy"></a>Integrazione con criteri di Azure

Criteri di Azure è un servizio di Azure che consente di creare, assegnare e gestire criteri che applicano regole sulle risorse per garantire che le risorse rimangano conformi agli standard aziendali e ai contratti di servizio. Criteri di Azure valuta le risorse per la mancata conformità con i criteri assegnati. 

Azure Batch dispone di due estensioni predefinite che consentono di gestire la conformità dei criteri. 

|**Nome**...|   **Descrizione**|**Effetto/i**|  **Versione**|    **origine**
|----------------|----------|----------|----------------|---------------|
|È consigliabile abilitare i log di diagnostica negli account Batch|   Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa|AuditIfNotExists, Disabled|  2.0.0|  GitHub|
|È consigliabile configurare le regole di avviso per le metriche negli account Batch| Controlla la configurazione delle regole di avviso delle metriche per l'account Batch per abilitare la metrica richiesta|   AuditIfNotExists, Disabled| 1.0.0|  GitHub|

Le definizioni dei criteri descrivono le condizioni che devono essere soddisfatte. Una condizione Confronta la proprietà della risorsa con un valore obbligatorio. È possibile accedere ai campi delle proprietà delle risorse utilizzando alias predefiniti. Gli alias delle proprietà vengono usati per accedere a proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

Le risorse richieste da batch includono: account, nodo di calcolo, pool, processo e attività. Quindi, si useranno alias di proprietà per accedere a proprietà specifiche per queste risorse. Altre informazioni sugli [alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Per assicurarsi di avere familiarità con gli alias correnti ed esaminare le risorse e i criteri, usare l'estensione di criteri di Azure per Visual Studio Code. Può essere installato in tutte le piattaforme supportate da Visual Studio Code. Questo supporto include Windows, Linux e macOS. Vedere [linee guida](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode)per l'installazione.




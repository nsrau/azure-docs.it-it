---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164915"
---
A ogni log viene assegnato un *livello di registrazione* . Il valore è un numero intero che indica l'importanza relativa:

|LogLevel    |Codice| Descrizione |
|------------|---|--------------|
|Trace       | 0 |Log che contengono i messaggi più dettagliati. Questi messaggi possono contenere dati sensibili dell'applicazione. Questi messaggi sono disabilitati per impostazione predefinita e non devono mai essere abilitati in un ambiente di produzione.|
|Debug       | 1 | Log usati per l'analisi interattiva durante lo sviluppo. Questi log devono contenere principalmente informazioni utili per il debug e non hanno un valore a lungo termine. |
|Informazioni | 2 | Log che tengono traccia del flusso generale dell'applicazione. Questi log devono avere un valore a lungo termine. |
|Avviso     | 3 | Log che evidenziano un evento anomalo o imprevisto nel flusso dell'applicazione, ma non provocano altrimenti l'arresto dell'esecuzione dell'applicazione. |
|Errore       | 4 | Log che evidenziano il momento in cui il flusso di esecuzione corrente viene arrestato a causa di un errore. Questi errori dovrebbero indicare un errore nell'attività corrente, non un errore a livello di applicazione. |
|Critico    | 5 | Log che descrivono un arresto anomalo irreversibile di un'applicazione o del sistema o un errore irreversibile che richiede attenzione immediata. |
|nessuno        | 6 | Disabilita la registrazione per la categoria specificata. |

Il [ *host.js* ](../articles/azure-functions/functions-host-json.md) di configurazione dei file determina la quantità di registrazione inviata da un'app per le funzioni al Application Insights.  

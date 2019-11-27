---
title: Spostare le risorse di Azure in un'altra area
description: Viene fornita una panoramica dello stato di trasferimento delle risorse di Azure tra aree di Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308115"
---
# <a name="moving-azure-resources-across-regions"></a>Trasferimento di risorse di Azure tra aree

Questo articolo fornisce informazioni sullo trasferimento di risorse di Azure tra aree di Azure.

Aree geografiche e zone di disponibilità di Azure costituiscono la base dell'infrastruttura globale di Azure. Le aree [geografiche](https://azure.microsoft.com/global-infrastructure/geographies/) di Azure contengono in genere due o più [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). Un'area è un'area all'interno di una geografia che contiene zone di disponibilità e più data center. 

Dopo la distribuzione delle risorse in un'area specifica di Azure, è possibile che si voglia spostare le risorse in un'area diversa.

- **Allinea a un'area di avvio**: spostare le risorse in un'area di Azure appena introdotta che non era disponibile in precedenza.
- **Allinea per servizi/funzionalità**: spostare le risorse per sfruttare i vantaggi dei servizi o delle funzionalità disponibili in un'area specifica.
- **Rispondere agli sviluppi aziendali**: spostare le risorse in un'area in risposta alle modifiche aziendali, ad esempio fusioni o acquisizioni.
- **Allinea per prossimità**: sposta le risorse in un'area locale nell'azienda.
- **Soddisfare i requisiti dei dati**: spostare le risorse per allinearli ai requisiti di residenza dei dati o alle esigenze di classificazione dei dati. [Altre informazioni](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)
- **Rispondere ai requisiti di distribuzione**: spostare le risorse distribuite in errore o spostarle in risposta alle esigenze di capacità. 
- **Rispondere alla rimozione delle autorizzazioni**: spostare le risorse dovute alla rimozione delle autorizzazioni delle aree.

## <a name="move-process"></a>Sposta processo

Il processo di spostamento effettivo dipende dalle risorse che si stanno spostando. Tuttavia, esistono alcuni passaggi chiave comuni:

- **Verificare i prerequisiti**: i prerequisiti includono assicurarsi che le risorse necessarie siano disponibili nell'area di destinazione, verificare di disporre di una quota sufficiente e verificare che la sottoscrizione sia in grado di accedere all'area di destinazione.
- **Analizzare le dipendenze**: le risorse potrebbero avere dipendenze da altre risorse. Prima dello spostamento, determinare le dipendenze in modo che le risorse spostate continuino a funzionare come previsto dopo lo spostamento.
- **Prepararsi per lo spostamento**: questi sono i passaggi da eseguire nell'area primaria prima dello spostamento. Ad esempio, potrebbe essere necessario esportare un modello di Azure Resource Manager o avviare la replica delle risorse dall'origine alla destinazione.
- **Spostare le risorse: il**modo in cui si spostano le risorse dipende da quali sono. Potrebbe essere necessario distribuire un modello nell'area di destinazione o eseguire il failover delle risorse nella destinazione.
- **Scartare le risorse di destinazione**: dopo lo stato di trasferimento delle risorse, è possibile esaminare le risorse ora nell'area di destinazione e decidere se sono presenti operazioni non necessarie.
- Eseguire **il commit dello spostamento**: dopo aver verificato le risorse nell'area di destinazione, alcune risorse potrebbero richiedere un'azione di commit finale. Ad esempio, in un'area di destinazione che è ora l'area primaria, potrebbe essere necessario configurare il ripristino di emergenza in una nuova area secondaria. 
- **Pulire l'origine**: Infine, dopo che tutto è in esecuzione nella nuova area, è possibile pulire e rimuovere le autorizzazioni create per lo spostamento e le risorse nell'area primaria.



## <a name="next-steps"></a>Passaggi successivi

Per un elenco delle risorse che supportano lo spostamento tra le aree, vedere [spostare il supporto delle operazioni per le risorse](region-move-support.md).

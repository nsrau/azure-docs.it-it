---
title: Spostare le risorse di Azure in un'altra area
description: Viene fornita una panoramica dello stato di trasferimento delle risorse di Azure tra aree di Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90007639"
---
# <a name="moving-azure-resources-across-regions"></a>Trasferimento di risorse di Azure tra aree

Questo articolo fornisce informazioni sullo trasferimento di risorse di Azure tra aree di Azure.

Le aree geografiche, le aree e le zone di disponibilità di Azure costituiscono la base dell'infrastruttura globale di Azure. Le aree [geografiche](https://azure.microsoft.com/global-infrastructure/geographies/) di Azure contengono in genere due o più [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). Un'area è un'area all'interno di una geografia che contiene zone di disponibilità e più data center. 

Dopo la distribuzione delle risorse in un'area specifica di Azure, è possibile che si voglia spostare le risorse in un'area diversa.

- **Allinea a un'area di avvio**: spostare le risorse in un'area di Azure appena introdotta che non era disponibile in precedenza.
- **Usufruire di servizi/funzionalità**: spostare le risorse per sfruttare servizi o funzionalità disponibili in un'area specifica.
- **Rispondere a sviluppi aziendali**: spostare le risorse in un'area in risposta a modifiche aziendali, ad esempio fusioni o acquisizioni.
- **Allinearsi per prossimità**: spostare le risorse in un'area in cui si trova l'azienda.
- **Soddisfare i requisiti dei dati**: spostare le risorse per allinearli ai requisiti di residenza dei dati o alle esigenze di classificazione dei dati. [Altre informazioni](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)
- **Rispondere a requisiti di distribuzione**: spostare le risorse distribuite per errore o spostarle in risposta a esigenze di capacità. 
- **Rispondere alla rimozione delle autorizzazioni**: spostare le risorse dovute alla rimozione delle autorizzazioni delle aree.

## <a name="move-resources-with-resource-mover"></a>Spostare le risorse con il motore di risorse

È possibile spostare le risorse in un'area diversa con [Azure Resource Mover](../../resource-mover/overview.md). Spostamento risorse offre quanto segue:

- Un singolo hub per lo spostamento di risorse tra aree.
- Riduzione dei tempi richiesti e della complessità degli spostamenti. Tutto il necessario in un'unica posizione.
- Un'esperienza semplice e coerente per spostare diversi tipi di risorse di Azure.
- Un modo semplice per identificare le dipendenze tra le risorse da spostare. È quindi possibile spostare contemporaneamente le risorse correlate in modo che dopo lo spostamento tutto funzioni come previsto nell'area di destinazione.
- Pulizia automatica delle risorse nell'area di origine, se si preferisce eliminarle dopo lo spostamento.
- Test È possibile provare a eseguire uno spostamento e quindi annullarlo se non si vuole procedere a uno spostamento completo.

È possibile spostare le risorse in un'altra area usando un paio di metodi diversi:

- **Avviare lo spostamento delle risorse da un gruppo di risorse**: con questo metodo è possibile avviare lo spostamento dell'area dall'interno di un gruppo di risorse. Dopo aver selezionato le risorse che si desidera spostare, il processo continua nell'hub di Resource Mover, per verificare le dipendenze delle risorse e orchestrare il processo di spostamento. [Altre informazioni](../../resource-mover/move-region-within-resource-group.md)
- **Iniziare a spostare le risorse direttamente dall'hub di Resource Mover**: con questo metodo è possibile avviare il processo di spostamento dell'area direttamente nell'hub. [Altre informazioni](../../resource-mover/tutorial-move-region-virtual-machines.md)


## <a name="support-for-region-move"></a>Supporto per lo spostamento nell'area

È attualmente possibile usare il motore di risorse per spostare queste risorse in un'altra area:

- Macchine virtuali di Azure e dischi associati
- Schede di interfaccia di rete
- Set di disponibilità
- Reti virtuali di Azure
- Indirizzi IP pubblici
- Gruppi di sicurezza di rete:
- Servizi di bilanciamento del carico interni e pubblici
- Database SQL di Azure e pool elastici

## <a name="region-move-process"></a>Processo di spostamento dell'area

Il processo effettivo per lo trasferimento delle risorse tra le aree dipende dalle risorse che si stanno muovendo. Tuttavia, esistono alcuni passaggi chiave comuni:

1. **Verificare i prerequisiti**: i prerequisiti includono assicurarsi che le risorse necessarie siano disponibili nell'area di destinazione, verificare di disporre di una quota sufficiente e verificare che la sottoscrizione sia in grado di accedere all'area di destinazione.
2. **Analizzare le dipendenze**: le risorse potrebbero avere dipendenze da altre risorse. Prima dello spostamento, determinare le dipendenze in modo che le risorse spostate continuino a funzionare come previsto dopo lo spostamento.
3. **Prepararsi per lo spostamento**: questi sono i passaggi da eseguire nell'area primaria prima dello spostamento. Ad esempio, potrebbe essere necessario esportare un modello di Azure Resource Manager o avviare la replica delle risorse dall'origine alla destinazione.
4. **Spostare le risorse: il**modo in cui si spostano le risorse dipende da quali sono. Potrebbe essere necessario distribuire un modello nell'area di destinazione o eseguire il failover delle risorse nella destinazione.
5. **Scartare le risorse di destinazione**: dopo lo stato di trasferimento delle risorse, è possibile esaminare le risorse ora nell'area di destinazione e decidere se sono presenti operazioni non necessarie.
6. Eseguire **il commit dello spostamento**: dopo aver verificato le risorse nell'area di destinazione, alcune risorse potrebbero richiedere un'azione di commit finale. Ad esempio, in un'area di destinazione che è ora l'area primaria, potrebbe essere necessario configurare il ripristino di emergenza in una nuova area secondaria. 
7. **Pulire l'origine**: Infine, dopo che tutto è in esecuzione nella nuova area, è possibile pulire e rimuovere le autorizzazioni create per lo spostamento e le risorse nell'area primaria.



## <a name="next-steps"></a>Passaggi successivi

[Altre](../../resource-mover/about-move-process.md) informazioni sul processo di spostamento nel motore di risorse.

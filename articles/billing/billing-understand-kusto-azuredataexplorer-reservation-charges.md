---
title: Informazioni su come viene applicato lo sconto per la prenotazione ad Esplora dati di Azure
description: Informazioni su come viene applicato lo sconto per la prenotazione al contatore di ricarico di Esplora dati di Azure.
services: data-explorer
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 658ef17632a30877867c887360d127792370bde2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770017"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Informazioni su come viene applicato lo sconto per la prenotazione ad Esplora dati di Azure

Dopo aver acquistato capacità riservata per Esplora dati di Azure, lo sconto per la prenotazione viene automaticamente applicato alle risorse di Esplora dati di Azure che corrispondono agli attributi e alla quantità della prenotazione. In una prenotazione sono inclusi gli addebiti di ricarico di Esplora dati di Azure e non le risorse di calcolo, di rete, di archiviazione o di qualsiasi altro tipo usate per il funzionamento del cluster di Esplora dati di Azure. Le prenotazioni di queste risorse devono essere acquistate separatamente.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Sconto per la prenotazione applicato ai cluster di Esplora dati di Azure.

Lo sconto per la prenotazione viene applicato al consumo di ricarico di Esplora dati di Azure su base oraria. Per le risorse di Esplora dati di Azure che non vengono eseguite per l'intera ora, lo sconto per la prenotazione viene automaticamente applicato ad altre risorse di Esplora dati di Azure che corrispondono agli attributi della prenotazione. Lo sconto può essere applicato a risorse di Esplora dati di Azure in esecuzione simultaneamente. Se non ci sono risorse di Esplora dati di Azure in esecuzione per l'ora intera e che corrispondono agli attributi della prenotazione, non è possibile ottenere il vantaggio completo dello sconto sulla prenotazione per questa ora.

> [!NOTE]
> * È **vivamente consigliato** acquistare [capacità di prenotazione](../virtual-machines/windows/prepay-reserved-vm-instances.md) per le macchine virtuali usate per il cluster di Esplora dati di Azure per ottimizzare il risparmio sulla capacità riservata.
> * L'acquisto della prenotazione determinerà l'applicazione di uno sconto in tutte le aree geografiche.

## <a name="examples"></a>Esempi

Gli esempi seguenti mostrano come viene applicato lo sconto per la capacità riservata di Esplora dati di Azure a seconda del numero di unità di ricarico acquistate e dei tempi di esecuzione.
Per le dimensioni del cluster motore, ad esempio: **2 macchine virtuali D11_v2**, gli addebiti su richiesta totali sono per quattro unità del contatore di ricarico di Esplora dati di Azure all'ora. 

**Scenario 1** 

Si acquista capacità riservata di Esplora dati di Azure per 8 unità di ricarico di Esplora dati di Azure. Si esegue un cluster motore di due macchine virtuali D13_v2 con un totale di 16 core che copre 16 unità di ricarico di Esplora dati di Azure all'ora e corrisponde al resto degli attributi della prenotazione. Viene addebitata la tariffa con pagamento in base al consumo per 8 core di utilizzo delle risorse di calcolo di Esplora dati di Azure e si ottiene lo sconto di prenotazione per un'ora di utilizzo di otto core dell'unità di ricarico di Esplora dati di Azure.

Per gli altri esempi, si presuppone che la capacità riservata di Esplora dati di Azure acquistata sia per cluster di Esplora dati di Azure da 16 core e che gli altri attributi della prenotazione corrispondano al cluster di Esplora dati di Azure in esecuzione.

**Scenario 2** 

Si eseguono due cluster motore di Esplora dati di Azure con otto core ciascuno per un'ora in due diverse aree geografiche. Viene applicato lo sconto di prenotazione di 16 core sia per il cluster sia per le 16 unità di ricarico di Esplora dati di Azure usate.

**Scenario 3** 

Si esegue un cluster motore di Esplora dati di Azure a 16 core dalle 13:00 alle 13:30. Si esegue un altro cluster motore di Esplora dati di Azure a 16 core dalle 13:30 alle 14:00. A entrambi i database viene applicato lo sconto sulla prenotazione.

**Scenario 4** 

Si esegue un cluster motore di Esplora dati di Azure a 16 core dalle 13:00 alle 13:45. Si esegue un altro cluster motore di Esplora dati di Azure a 16 core dalle 13:30 alle 14:00. Viene addebitata la tariffa con pagamento in base al consumo per la sovrapposizione di 15 minuti. Lo sconto sulla prenotazione viene applicato all'utilizzo del ricarico di Esplora dati di Azure per il resto del tempo.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Pagamento anticipato per le risorse di calcolo di Esplora dati di Azure con la capacità riservata di Esplora dati di Azure](../data-explorer/pricing-reserved-capacity.md)  
* [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)  
* [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)  
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
* [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)

---
title: Informazioni su come viene applicato lo sconto per la prenotazione all'archiviazione su disco di Azure
description: Informazioni su come viene applicato lo sconto per i dischi riservati di Azure ai dischi gestiti SSD Premium.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902135"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Informazioni su come viene applicato lo sconto per la prenotazione all'archiviazione su disco di Azure

Dopo aver acquistato la capacità riservata dell'archiviazione su disco di Azure, lo sconto per la prenotazione viene applicato automaticamente alle risorse del disco che soddisfano le condizioni della prenotazione. Lo sconto per la prenotazione si applica solo alla capacità del disco. Agli snapshot dei dischi vengono addebitate le tariffe in base al consumo.

Per altre informazioni sulla prenotazione dischi di Azure, vedere [Ridurre i costi con la prenotazione dischi di Azure](../../virtual-machines/linux/disks-reserved-capacity.md).
Per informazioni sui prezzi della prenotazione dischi di Azure, vedere la pagina sui [prezzi dei dischi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione dischi di Azure è uno sconto basato sul principio "use-it-or-lose-it", ovvero se non viene usato va perso, e viene applicato alle risorse del disco gestito su base oraria. Se non sono presenti risorse del disco gestito che soddisfano le condizioni della prenotazione in un'ora specifica, si perderà una quantità di prenotazione per quell'ora. Le ore prenotate inutilizzate non vengono riportate.

Quando si elimina una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa idonea nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno perse.

## <a name="discount-examples"></a>Esempi di sconto

Gli esempi seguenti illustrano la modalità di applicazione dello sconto per la prenotazione dischi di Azure a seconda delle distribuzioni:

Si supponga di aver acquistato 100 dischi P30 (~1 TiB per ogni disco) di capacità riservata nell'area Stati Uniti occidentali 2 per un periodo di un anno. Si supponga che il costo di questa prenotazione di esempio sia $ 140.100. È possibile scegliere di pagare l'importo totale in anticipo o di pagare rate mensili fisse di $ 11.675 per i 12 mesi successivi.
Per questi esempi si supponga di aver effettuato l'iscrizione per un piano di pagamento per una prenotazione mensile. Negli scenari seguenti viene descritto cosa accade in caso di sottoutilizzo, uso eccessivo o suddivisione in livelli della capacità riservata.

### <a name="underusing-your-capacity"></a>Sottoutilizzo della capacità

Si supponga che in una determinata ora del periodo di prenotazione siano state distribuite solo 99 unità SSD Premium P30 della prenotazione dischi di 100 unità P30. L'unità P30 rimanente non verrà applicata per quell'ora e non verrà riportata.

### <a name="overusing-your-capacity"></a>Uso eccessivo della capacità

Si supponga che in una determinata ora del periodo di prenotazione vengano usate 101 unità SSD Premium P30. Lo sconto relativo alla prenotazione si applica solo a 100 dischi P30 e per il rimanente disco P30 vengono applicate le tariffe a consumo per quell'ora. Per l'ora successiva, se si scende a 100 dischi P30 l'intero l'utilizzo sarà coperto dalla prenotazione.

### <a name="tiering-your-capacity"></a>Suddivisione in livelli della capacità

Si supponga che in una determinata ora del periodo di prenotazione si intenda usare un totale di 200 unità SSD Premium P30. Per i primi 30 minuti, si usano solo 100 unità. In questo periodo di tempo, l'uso è totalmente coperto poiché è stata eseguita una prenotazione per 100 dischi P30. Se in seguito si interrompe l'uso delle prime 100 unità (portando quindi l'utilizzo a zero) e si iniziano a usare le altre 100 unità per i 30 minuti rimanenti, tale utilizzo viene coperto dalla prenotazione.

![Illustrazione degli esempi di sottoutilizzo, uso eccessivo e suddivisione in livelli della capacità](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Ridurre i costi con la prenotazione dischi di Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Ridurre i costi con la prenotazione dischi di Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
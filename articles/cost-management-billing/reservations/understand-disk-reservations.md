---
title: Informazioni su come viene applicato lo sconto della prenotazione all'archiviazione su disco di Azure
description: Informazioni su come viene applicato lo sconto per i dischi riservati di Azure ai dischi gestiti SSD Premium di Azure.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 18fdda3e28761fcf912b716f51b5e270a9b224d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77586649"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Informazioni su come viene applicato lo sconto della prenotazione all'archiviazione su disco di Azure

Dopo aver acquistato la capacità riservata dei dischi di Azure, lo sconto della prenotazione viene applicato automaticamente alle risorse disco che ne soddisfano le condizioni. Lo sconto della prenotazione si applica solo agli SKU dei dischi. I costi degli snapshot dei dischi vengono addebitati alle tariffe con pagamento in base al consumo.

Per altre informazioni sulla prenotazione di dischi di Azure, vedere [Risparmiare sui costi con la prenotazione di dischi di Azure](../../virtual-machines/linux/disks-reserved-capacity.md). Per informazioni sui prezzi della prenotazione di dischi di Azure, vedere la pagina sui [prezzi dei dischi gestiti di Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione di dischi di Azure è basato sul principio "use-it-or-lose-it", ovvero se non viene usato va perso. Viene applicato alle risorse dischi gestiti su base oraria. Se per una data ora non sono presenti risorse dischi gestiti che soddisfano le condizioni della prenotazione, la quantità della prenotazione per quell'ora andrà persa. Le ore inutilizzate non vengono riportate.

Quando si elimina una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa idonea nell'ambito specificato. Se non vengono trovate risorse corrispondenti, le ore riservate vanno perse.

## <a name="discount-examples"></a>Esempi di sconto

Gli esempi seguenti illustrano come viene applicato lo sconto per la prenotazione di dischi di Azure a seconda della distribuzione.

Si supponga di acquistare e riservare 100 dischi P30 nell'area Stati Uniti occidentali 2 per un periodo di un anno. Ogni disco ha circa 1 TiB di spazio di archiviazione. Si supponga che il costo di questa prenotazione di esempio sia $ 140.100. È possibile scegliere di pagare l'intero importo in anticipo o in rate mensili fisse di $ 11.675 per i 12 mesi successivi.

Negli scenari seguenti viene descritto cosa succede in caso di sottoutilizzo, sovrautilizzo o suddivisione in livelli della capacità riservata. Per questi esempi si supponga di aver effettuato l'iscrizione a un piano di pagamento mensile per una prenotazione.

### <a name="underusing-your-capacity"></a>Sottoutilizzo della capacità

Si supponga di distribuire solo 99 dei 100 dischi SSD P30 Premium di Azure riservati, per un'ora nel periodo della prenotazione. Il disco P30 rimanente non viene applicato durante l'ora. Inoltre, non viene riportato al periodo successivo.

### <a name="overusing-your-capacity"></a>Uso eccessivo della capacità

Si supponga che in una determinata ora del periodo di prenotazione vengano usati 101 dischi SSD Premium P30. Lo sconto della prenotazione si applica solo a 100 dischi P30. Il costo del disco P30 rimanente viene addebitato alle tariffe con pagamento in base al consumo per quell'ora. Per l'ora successiva, se si scende a 100 dischi P30, l'intero l'utilizzo sarà coperto dalla prenotazione.

### <a name="tiering-your-capacity"></a>Suddivisione in livelli della capacità

Si supponga che in una determinata ora nel periodo di prenotazione si intenda usare un totale di 200 dischi SSD P30 Premium. Si supponga inoltre di usarne solo 100 per i primi 30 minuti. Durante questo periodo, l'uso è totalmente coperto perché è stata effettuata una prenotazione per 100 dischi P30. Se in seguito si interrompe l'uso delle prime 100 unità (portando quindi l'utilizzo a zero) e si iniziano a usare le altre 100 unità per i 30 minuti rimanenti, tale utilizzo viene coperto dalla prenotazione.

![Esempio di sottoutilizzo, sovrautilizzo e suddivisione in livelli della capacità](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Ridurre i costi con la prenotazione dischi di Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Ridurre i costi con la prenotazione dischi di Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)

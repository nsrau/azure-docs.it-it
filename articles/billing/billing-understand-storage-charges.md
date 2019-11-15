---
title: Informazioni su come viene applicato lo sconto per la prenotazione ad Archiviazione di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per la capacità riservata per le risorse Azure Data Lake Storage Gen2 e BLOB in blocchi.
author: tamram
ms.service: billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: ebef727cfa291744b3c97299da2a1340f34f5d72
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73746278"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Informazioni su come viene applicato lo sconto per la prenotazione ad Archiviazione di Azure

Dopo aver acquistato la capacità riservata di Archiviazione di Azure, lo sconto per la prenotazione viene applicato automaticamente alle risorse Azure Data Lake Storage Gen2 e BLOB in blocchi che soddisfano le condizioni della prenotazione. Lo sconto per la prenotazione si applica solo alla capacità di archiviazione. La larghezza di banda e la frequenza delle richieste sono addebitate in base alle tariffe a consumo.

Per altre informazioni sulla capacità riservata di Archiviazione di Azure, vedere [Ottimizzare i costi per l'archiviazione BLOB con capacità riservata](../storage/blobs/storage-blob-reserved-capacity.md).

Per informazioni sui prezzi di prenotazione per Archiviazione di Azure, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/) e [Prezzi di Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la capacità riservata di Archiviazione di Azure viene applicato alle risorse Azure Data Lake Storage Gen2 e BLOB in blocchi su base oraria.

Lo sconto per la capacità riservata di Archiviazione di Azure è uno sconto basato sul principio "use-it-or-lose-it", ovvero se non viene usato va perso. Se non si dispone di risorse Azure Data Lake Storage Gen2 o BLOB in blocchi che soddisfano le condizioni della prenotazione per un'ora specifica, si perderà una quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si elimina una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa idonea nell'ambito specificato. Se non si trovano risorse idonee nell'ambito specificato, le ore prenotate andranno perse.

## <a name="discount-examples"></a>Esempi di sconto

Gli esempi seguenti illustrano la modalità di applicazione dello sconto per la capacità riservata di Archiviazione di Azure a seconda delle distribuzioni.

Si supponga di avere acquistato 100 TB di capacità riservata nell'area Stati Uniti occidentali 2 per un periodo di un anno. La prenotazione è specifica dell'archiviazione con ridondanza locale nel livello di accesso frequente.

Si supponga che il costo di questa prenotazione di esempio sia $ 18.540. È possibile scegliere di pagare la quantità totale in anticipo o di pagare rate mensili fisse di $ 1.545 per i dodici mesi successivi.

Per questi esempi si supponga di aver effettuato l'iscrizione per un piano di pagamento per una prenotazione mensile. Negli scenari seguenti viene descritto cosa accade in caso di sottoutilizzo o di uso eccessivo della capacità riservata.

### <a name="underusing-your-capacity"></a>Sottoutilizzo della capacità

Si supponga che in una determinata ora del periodo di prenotazione siano stati usati solo 80 TB della capacità riservata di 100 TB. I rimanenti 20 TB non verranno applicati per quell'ora e non verranno mantenuti a riporto.

### <a name="overusing-your-capacity"></a>Uso eccessivo della capacità

Si supponga che in una determinata ora del periodo di prenotazione siano stati usati 101 TB di capacità di archiviazione. Lo sconto relativo alla prenotazione si applica a 100 TB di dati, per il rimanente TB vengono applicate le tariffe a consumo per quell'ora. Se nell'ora successiva si usano 100 TB, tutto l'utilizzo sarà coperto dalla prenotazione.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Ottimizzare i costi per l'archiviazione BLOB con capacità riservata](../storage/blobs/storage-blob-reserved-capacity.md)
- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)

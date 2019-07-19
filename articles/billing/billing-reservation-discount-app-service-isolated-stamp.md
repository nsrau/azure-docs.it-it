---
title: Come vengono applicati gli sconti per le prenotazioni per app Azure timbri isolati del servizio
description: Scopri in che modo gli sconti per la prenotazione si applicano agli indicatori isolati del servizio app Azure
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298256"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Come vengono applicati gli sconti per le prenotazioni per app Azure timbri isolati del servizio

Dopo aver acquistato la capacità riservata dello Stamp servizio app Isolato, lo sconto per la prenotazione viene applicato automaticamente alla tariffa di timbro in un'area. Lo sconto relativo alla prenotazione si applica all'utilizzo emesso dal contatore delle tariffe per gli Stamp isolati. I ruoli di lavoro, i front-end aggiuntivi e tutte le altre risorse associate al timbro continueranno a essere fatturati alla tariffa standard.

## <a name="reservation-discount-application"></a>Applicazione sconto prenotazione

Lo sconto sulla tariffa per il timbro servizio app Isolato viene applicato per l'esecuzione di timbri isolati su base oraria. Se non è stato distribuito un timbro per un'ora, la capacità riservata viene sprecata per quell'ora. Non viene ripreso.

Dopo l'acquisto, la prenotazione acquistata viene confrontata con un indicatore isolato in esecuzione in un'area specifica. Se si arresta tale timbro, gli sconti per le prenotazioni vengono applicati automaticamente a qualsiasi altro timbro in esecuzione nell'area. Quando non sono presenti timbri, la prenotazione viene applicata al timbro successivo creato nell'area.

Quando i timbri non vengono eseguiti per un'ora completa, la prenotazione si applica automaticamente ad altri timbri corrispondenti nella stessa area durante la stessa ora.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Scegliere un tipo di timbro: Windows o Linux

Per impostazione predefinita, un timbro isolato vuoto emette il contatore Windows Stamp. Ad esempio, quando non viene distribuito alcun thread di lavoro. Continua a emettere il contatore quando vengono distribuiti i ruoli di lavoro di Windows. Se si distribuisce un ruolo di lavoro Linux, il misuratore passa al contatore di indicatori Linux. Il timbro emette il contatore Windows quando vengono distribuiti sia Linux che Windows Worker.

Di conseguenza, il contatore dei timbri può variare tra Windows e Linux in base alla durata del timbro. Nel frattempo, le prenotazioni sono specifiche del sistema operativo. È necessario acquistare una prenotazione che supporti i thread di lavoro che si prevede di distribuire nell'indicatore. Per gli indicatori e i timbri solo Windows viene utilizzata la prenotazione di Windows. I timbri con solo i ruoli di lavoro Linux usano la prenotazione di Linux.

L'unico momento in cui è necessario acquistare una prenotazione di Linux è quando si prevede di avere _solo_ i ruoli di lavoro Linux.

## <a name="discount-examples"></a>Esempi di sconto

Negli esempi seguenti viene illustrato il modo in cui viene applicato lo sconto dell'istanza riservata per la tassa dei timbri isolati, a seconda delle distribuzioni.

- **Esempio 1**: Si acquista un'istanza di capacità di timbro riservata isolata in un'area senza servizio app Isolato timbri. Si distribuisce un nuovo timbro nell'area e si pagano le tariffe riservate per tale timbro.
- **Esempio 2:** Si acquista un'istanza di capacità di timbro riservata isolata in un'area in cui è già distribuito un timbro servizio app Isolato. Si inizia a ricevere la tariffa riservata per l'indicatore distribuito.
- **Esempio 3**: Si acquista un'istanza di capacità di timbro riservata isolata in un'area con un timbro servizio app Isolato già distribuito. Si inizia a ricevere la tariffa riservata nell'indicatore distribuito. Successivamente, eliminare il timbro e distribuirne uno nuovo. Si riceve la tariffa riservata per il nuovo timbro. Gli sconti non riportano le durate senza timbri distribuiti.
- **Esempio 4**: Si acquista un'istanza di capacità di timbro riservata Linux isolata in un'area e quindi si distribuisce un nuovo timbro nell'area. Quando il timbro viene inizialmente distribuito senza thread di lavoro, emette il contatore Windows Stamp. Non viene ricevuto alcun sconto. Quando viene distribuito il timbro, il primo ruolo di lavoro Linux emette il contatore per gli Stamp Linux e viene applicato lo sconto relativo alla prenotazione. Se un ruolo di lavoro di Windows viene distribuito in un secondo momento, il contatore del timbro viene ripristinato in Windows. Non si riceve più uno sconto per la prenotazione di timbri riservati Linux isolata.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).
- Per altre informazioni su come pre-acquisto servizio app Isolato la capacità riservata di timbro per risparmiare denaro, vedere [la pagina relativa alla tariffa per gli Stamp isolati con la capacità riservata di app Azure Service](billing-prepay-app-service-isolated-stamp.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Gestire le prenotazioni in Azure](billing-manage-reserved-vm-instance.md)
  - [Informazioni sull'utilizzo della prenotazione per una sottoscrizione con tariffe con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

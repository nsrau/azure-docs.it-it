---
title: Modalità di applicazione degli sconti per le prenotazioni alle tariffe stamp per il servizio app Isolato di Azure
description: Informazioni sulla modalità di applicazione degli sconti per le prenotazioni alle tariffe stamp per il servizio app Isolato di Azure.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "68298256"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Modalità di applicazione degli sconti per le prenotazioni alle tariffe stamp per il servizio app Isolato di Azure

Dopo aver acquistato la capacità riservata della tariffa stamp per il servizio app Isolato, lo sconto per la prenotazione viene applicato automaticamente alla tariffa stamp in un'area. Lo sconto per la prenotazione si applica ai dati di utilizzo generati dal contatore della tariffa stamp per ambiente isolato. I ruoli di lavoro, i front-end aggiuntivi ed eventuali altre risorse associate allo stamp continueranno a essere fatturati in base alla tariffa standard.

## <a name="reservation-discount-application"></a>Applicazione dello sconto per la prenotazione

Lo sconto per la tariffa stamp per il servizio app Isolato viene applicato all'esecuzione di stamp per ambiente isolato su base oraria. Se non è presente uno stamp distribuito per un'ora, la capacità riservata risulta sprecata per tale ora e non viene mantenuta.

Dopo l'acquisto la prenotazione acquistata viene confrontata con uno stamp per ambiente isolato in esecuzione in un'area specifica. Se si arresta tale stamp, gli sconti per le prenotazioni vengono applicati automaticamente a qualsiasi altro stamp in esecuzione nell'area. Quando non sono presenti stamp, la prenotazione viene applicata allo stamp successivo creato nell'area.

Quando gli stamp non vengono eseguiti per un'intera ora, la prenotazione viene applicata automaticamente ad altri stamp corrispondenti presenti nella stessa area nella stessa ora.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Scegliere un tipo di stamp: Windows o Linux

Per impostazione predefinita, con uno stamp per ambiente isolato vuoto viene generato il contatore dello stamp Windows, ad esempio quando non viene distribuito alcun ruolo di lavoro. Continua a generare il contatore quando vengono distribuiti ruoli di lavoro di Windows. Se si distribuisce un ruolo di lavoro Linux, il contatore passa al contatore dello stamp Linux. Quando vengono distribuiti ruoli di lavoro sia Linux che Windows, lo stamp genera il contatore di Windows.

Di conseguenza, il contatore dello stamp può quindi passare da Windows a Linux e viceversa durante il ciclo di vita dello stamp. Nel frattempo le prenotazioni sono specifiche del sistema operativo. È necessario acquistare una prenotazione che supporti i ruoli di lavoro che si prevede di distribuire nello stamp. Per gli stamp solo Windows e quelli misti viene usata la prenotazione di Windows. Per gli stamp con solo ruoli di lavoro Linux viene usata la prenotazione di Linux.

Una prenotazione di Linux deve essere acquistata esclusivamente se si prevede che lo stamp conterrà _solo_ ruoli di lavoro di Linux.

## <a name="discount-examples"></a>Esempi di sconto

Gli esempi seguenti illustrano la modalità di applicazione dello sconto per l'istanza riservata della tariffa stamp per ambiente isolato a seconda delle distribuzioni.

- **Esempio 1**: si acquista un'istanza della capacità riservata per lo stamp per ambiente isolato in un'area senza stamp per il servizio app Isolato. Si distribuisce un nuovo stamp nell'area e si pagano le tariffe riservate per tale stamp.
- **Esempio 2:** si acquista un'istanza della capacità riservata per lo stamp per ambiente isolato in un'area in cui è già distribuito uno stamp per il servizio app Isolato. Si inizia a ricevere la tariffa riservata per lo stamp distribuito.
- **Esempio 3**: si acquista un'istanza della capacità riservata per lo stamp per ambiente isolato in un'area con uno stamp per il servizio app Isolato già distribuito. Si inizia a ricevere la tariffa riservata per lo stamp distribuito. Successivamente, si elimina lo stamp e se ne distribuisce uno nuovo. Si riceve la tariffa riservata per il nuovo stamp. Gli sconti non vengono mantenuti per l'intera durata senza stamp distribuiti.
- **Esempio 4**: si acquista un'istanza della capacità riservata per lo stamp Linux per ambiente isolato in un'area e quindi si distribuisce un nuovo stamp nell'area. Quando lo stamp viene inizialmente distribuito senza ruoli di lavoro, viene generato il contatore di stamp Windows. Non viene ricevuto alcuno sconto. Quando nello stamp viene distribuito il primo ruolo di lavoro Linux, viene generato il contatore di stamp Linux e viene applicato lo sconto relativo alla prenotazione. Se successivamente nello stamp viene distribuito un ruolo di lavoro di Windows, viene ripristinato il contatore di stamp Windows. Non si riceve più uno sconto per la prenotazione dello stamp riservato Linux per ambiente isolato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).
- Per altre informazioni sul preacquisto di capacità riservata dello stamp per il servizio app Isolato per risparmiare denaro, vedere [Pagare in anticipo la tariffa stamp per il servizio app Isolato di Azure con capacità riservata](billing-prepay-app-service-isolated-stamp.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Gestire le prenotazioni in Azure](billing-manage-reserved-vm-instance.md)
  - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

---
title: Informazioni sullo sconto per le prenotazioni per i database SQL di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per le prenotazioni ai database SQL di Azure in esecuzione.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 4b4c6b390e9b3a0cf764f998523fe3c1cdc66026
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "60370288"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Come viene applicato lo sconto per le prenotazioni ai database SQL di Azure

Dopo avere acquistato capacità riservata per il database SQL di Azure, lo sconto sulla prenotazione viene automaticamente applicato ai database SQL che corrispondono agli attributi e alla quantità della prenotazione. Una prenotazione comprende i costi delle risorse di calcolo del database SQL. Vengono addebitate le tariffe normali per software, archiviazione e rete. È possibile coprire i costi di licenza per i database SQL con il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

Per le istanze di macchina virtuale riservate, vedere [Informazioni sullo sconto per le istanze di macchina virtuale riservate di Azure](billing-understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="discount-applied-to-sql-databases"></a>Sconto applicato ai database SQL

 Lo sconto sulla capacità riservata del database SQL viene applicato su base oraria ai database SQL in esecuzione. La prenotazione acquistata viene confrontata con l'utilizzo delle risorse di calcolo generato dai database SQL in esecuzione. Per i database che non vengono eseguiti per un'ora intera, la prenotazione viene automaticamente applicata ad altri database SQL che corrispondono agli attributi della prenotazione. Lo sconto può essere applicato a database SQL in esecuzione simultaneamente. Se nessun database SQL in esecuzione per l'ora intera corrisponde agli attributi della prenotazione, non è possibile ottenere il vantaggio completo dello sconto sulla prenotazione per questa ora.

Gli esempi seguenti mostrano come viene applicato lo sconto sulla capacità riservata dei database SQL a seconda del numero di core acquistati e dei tempi di esecuzione.

- Scenario 1: si acquista capacità riservata per un database SQL con 8 core. Si esegue un database SQL con 16 core che corrisponde agli altri attributi della prenotazione. Viene addebitata la tariffa con pagamento in base al consumo per 8 core di utilizzo delle risorse di calcolo del database SQL. Si ottiene lo sconto sulla prenotazione per un'ora di utilizzo delle risorse di calcolo del database SQL con 8 core.

Per gli altri esempi, si presuppone che la capacità riservata del database SQL acquistata sia per un database SQL con 16 core e che gli altri attributi della prenotazione corrispondano ai database SQL in esecuzione.

- Scenario 2: si eseguono per un'ora due database SQL con 8 core ciascuno. Lo sconto sulla prenotazione di 16 core viene applicato all'utilizzo delle risorse di calcolo per entrambi i database SQL con 8 core.
- Scenario 3: si esegue un database SQL con 16 core dalle 13.00 alle 13.30. Si esegue un altro database SQL con 16 core dalle 13.30 alle 14.00. A entrambi i database viene applicato lo sconto sulla prenotazione.
- Scenario 4: si esegue un database SQL con 16 core dalle 13.00 alle 13.45. Si esegue un altro database SQL con 16 core dalle 13.30 alle 14.00. Viene addebitata la tariffa con pagamento in base al consumo per la sovrapposizione di 15 minuti. Lo sconto sulla prenotazione viene applicato all'utilizzo delle risorse di calcolo per il resto del tempo.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](/partner-center/azure-reservations)

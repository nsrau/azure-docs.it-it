---
title: Informazioni sullo sconto sulle prenotazioni di Azure | Microsoft Docs
description: Informazioni su come viene applicato ai database SQL in esecuzione lo sconto sulla prenotazione.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: b39c56e8dcae6cdac45160240a6b0f12a1ac0f2e
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902551"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Informazioni su come viene applicato ai database SQL lo sconto sulla prenotazione

Dopo avere acquistato capacità riservata per il database SQL di Azure, lo sconto sulla prenotazione viene automaticamente applicato ai database SQL che corrispondono agli attributi e alla quantità della prenotazione. Una prenotazione comprende i costi delle risorse di calcolo del database SQL. Vengono addebitate le tariffe normali per software, archiviazione e rete. È possibile coprire i costi di licenza per i database SQL con il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

Per le istanze di macchina virtuale riservate, vedere [Informazioni sullo sconto per le istanze di macchina virtuale riservate di Azure](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Sconto sulla prenotazione applicato ai database SQL

 Lo sconto sulla capacità riservata del database SQL viene applicato su base oraria ai database SQL in esecuzione. La prenotazione acquistata viene confrontata con l'utilizzo delle risorse di calcolo generato dai database SQL in esecuzione. Per i database che non vengono eseguiti per un'ora intera, la prenotazione viene automaticamente applicata ad altri database SQL che corrispondono agli attributi della prenotazione. Lo sconto può essere applicato a database SQL in esecuzione simultaneamente. Se nessun database SQL in esecuzione per l'ora intera corrisponde agli attributi della prenotazione, non è possibile ottenere il vantaggio completo dello sconto sulla prenotazione per questa ora.

Gli esempi seguenti mostrano come viene applicato lo sconto sulla capacità riservata dei database SQL a seconda del numero di core acquistati e dei tempi di esecuzione.

- Scenario 1: si acquista capacità riservata per un database SQL con 8 core. Si esegue un database SQL con 16 core che corrisponde agli altri attributi della prenotazione. Viene addebitata la tariffa con pagamento in base al consumo per 8 core di utilizzo delle risorse di calcolo del database SQL. Si ottiene lo sconto sulla prenotazione per un'ora di utilizzo delle risorse di calcolo del database SQL con 8 core.

Per gli altri esempi, si presuppone che la capacità riservata del database SQL acquistata sia per un database SQL con 16 core e che gli altri attributi della prenotazione corrispondano ai database SQL in esecuzione.

- Scenario 2: si eseguono per un'ora due database SQL con 8 core ciascuno. Lo sconto sulla prenotazione di 16 core viene applicato all'utilizzo delle risorse di calcolo per entrambi i database SQL con 8 core.
- Scenario 3: si esegue un database SQL con 16 core dalle 13.00 alle 13.30. Si esegue un altro database SQL con 16 core dalle 13.30 alle 14.00. A entrambi i database viene applicato lo sconto sulla prenotazione.
- Scenario 4: si esegue un database SQL con 16 core dalle 13.00 alle 13.45. Si esegue un altro database SQL con 16 core dalle 13.30 alle 14.00. Viene addebitata la tariffa con pagamento in base al consumo per la sovrapposizione di 15 minuti. Lo sconto sulla prenotazione viene applicato all'utilizzo delle risorse di calcolo per il resto del tempo.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

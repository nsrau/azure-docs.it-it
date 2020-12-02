---
title: Sconti sul piano di prenotazione Red Hat - Azure
description: Informazioni su come vengono applicati gli sconti del piano Red Hat al software Red Hat delle macchine virtuali.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 8e735d623cc991d2e3720e0ec418eee77aa8c46c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350858"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Informazioni su come viene applicato lo sconto del piano di prenotazione del software Red Hat Linux Enterprise per Azure

Dopo aver acquistato un piano Red Hat Linux, lo sconto viene applicato automaticamente alle macchine virtuali (VM) Red Hat distribuite che corrispondono alla prenotazione. Un piano Red Hat Linux copre i costi di esecuzione del software Red Hat su una macchina virtuale di Azure.

Per acquistare il piano Red Hat Linux appropriato è necessario capire quali VM Red Hat si eseguono e il numero di vCPU su tali VM. Usare le sezioni seguenti per identificare il piano da acquistare in base al file CSV relativo all'utilizzo.

## <a name="discount-applies-to-different-vm-sizes"></a>Lo sconto si applica a diverse dimensioni di VM

Come le istanze di macchina virtuale riservate, gli acquisti di piani Red Hat offrono flessibilità delle dimensioni istanza. Ciò significa che lo sconto si applica anche quando si distribuisce una VM con un numero di vCPU diverso. Lo sconto si applica a diverse dimensioni VM all'interno del piano software.

L'importo dello sconto dipende dal rapporto riportato nelle tabelle seguenti. Il rapporto mette a confronto il footprint relativo per ogni dimensione di VM di quel gruppo. Il rapporto varia a seconda delle vCPU delle VM. Usare il valore del rapporto per calcolare a quante istanze di macchina virtuale si applica lo sconto relativo al piano Red Hat Linux.

Ad esempio, se si acquista un piano per Red Hat Linux Enterprise Server per una VM con 1-4 vCPU, il rapporto per tale prenotazione è 1. Lo sconto copre il costo del software Red Hat per:

- 1 VM distribuita con un 1- 4 vCPU
- o 0,46 oppure circa il 46% dei costi di Red Hat Enterprise Linux per una VM con 5 o più vCPU.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Nomi del marketplace del portale di Azure:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Controllare i contatori di Red Hat Enterprise Linux a cui è applicabile il piano](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
- [Pagare in anticipo i piani software Red Hat con le prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Gestire le prenotazioni per Azure](manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
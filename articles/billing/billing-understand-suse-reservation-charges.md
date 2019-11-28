---
title: Sconto del piano software - Azure | Microsoft Docs
description: Informazioni su come vengono applicati gli sconti del piano software al software delle macchine virtuali.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: 76fc2d782571818d14a3008c10a0c3fae07a2b7d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225732"
---
# <a name="azure-software-plan-discount"></a>Sconto del piano software di Azure

I piani software di Azure per SUSE e RedHat sono prenotazioni applicabili alle VM distribuite. Lo sconto del piano software viene applicato all'utilizzo del software delle VM distribuite che corrispondono alla prenotazione.

Quando si arresta una VM, lo sconto viene applicato automaticamente a un'altra VM corrispondente, se disponibile. Un piano software copre il costo di esecuzione del software in una VM. Gli altri costi, ad esempio per il calcolo, l'elaborazione e la rete, vengono addebitati separatamente.

Per acquistare il piano appropriato, è necessario avere i dati sull'utilizzo delle VM e sul numero di vCPU al loro interno. Usare le sezioni seguenti per identificare il piano da acquistare in base ai dati di utilizzo.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Esaminare l'utilizzo delle VM RedHat prima dell'acquisto

Ottenere il nome del prodotto dai dati di utilizzo e acquistare il piano RedHat con lo stesso tipo e le stesse dimensioni.

Se ad esempio l'utilizzo è per il prodotto con **licenza Red Hat Enterprise Linux - VM con 1-4 vCPU**, è necessario acquistare **Red Hat Enterprise Linux** per **VM con 1-4 vCPU**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Esaminare l'utilizzo delle VM SUSE prima dell'acquisto

Ottenere il nome del prodotto dai dati di utilizzo e acquistare il piano SUSE con lo stesso tipo e le stesse dimensioni.

Se ad esempio l'utilizzo è per il prodotto **SUSE Linux Enterprise Server Priority - VM con 2-4 vCPU**, è necessario acquistare **SUSE Linux Enterprise Server Priority** per **VM con 2-4 vCPU**.

![Esempio di selezione del prodotto da acquistare](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Lo sconto si applica alle diverse dimensioni di VM per i piani SUSE

Come le istanze di macchina virtuale riservate, gli acquisti di piani SUSE offrono flessibilità in termini di dimensioni dell'istanza. Ciò significa che lo sconto si applica anche quando si distribuisce una VM con un numero di vCPU diverso. Lo sconto si applica a diverse dimensioni VM all'interno del piano software.

L'importo dello sconto dipende dal rapporto riportato nelle tabelle seguenti. Il rapporto mette a confronto il footprint relativo per ogni contatore di quel gruppo. Il rapporto varia a seconda delle vCPU delle VM. Usare il valore del rapporto per calcolare a quante istanze di macchina virtuale si applica lo sconto relativo al piano SUSE Linux.

Ad esempio, se si acquista un piano per SUSE Linux Enterprise Server per HPC - Priority per una VM con 3 o 4 vCPU, il rapporto per tale prenotazione è 2. Lo sconto copre il costo del software SUSE per:

- 2 VM distribuite con 1 o 2 vCPU,
- 1 VM distribuita con 3 o 4 vCPU,
- oppure 0,77 o circa il 77% di una VM con 5 o più vCPU.

Il rapporto per 5 o più vCPU è 2,6. Quindi una prenotazione per SUSE con una VM con 5 o più vCPU copre una sola parte del costo del software, che equivale a circa il 77%.

Le tabelle seguenti mostrano i piani software per i quali è possibile acquistare una prenotazione, i relativi contatori di utilizzo e i rapporti per ciascuno.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server per HPC - Priority

Nome del marketplace del portale di Azure:

- SLES 12 SP3 per HPC (Priority)

|VM SUSE | ID contatore| Rapporto| Dimensione VM di esempio|
| -------| ------------------------| --- |--- |
|SLES per HPC 1-2 vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES per HPC 3-4 vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES per HPC 5+ vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server per HPC - Standard

Nome del marketplace del portale di Azure:

- SLES 12 SP3 per HPC

|VM SUSE | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- | --- | ------------------------| --- |
|SLES per HPC 1-2 vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES per HPC 3-4 vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES per HPC 5+ vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server per SAP - Priority

Nomi del marketplace del portale di Azure:

- SLES per SAP 15 (Priority)
- SLES per SAP 12 SP3 (Priority)
- SLES per SAP 12 SP2 (Priority)

|VM SUSE | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- | --- |
|SLES per SAP Priority 1-2 vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES per SAP Priority 3-4 vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES per SAP Priority 5+ vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server - Priority

Nomi del marketplace del portale di Azure:

- SLES 15 (PRIORITY)
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|VM SUSE | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2-4 vCPU |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2-4 vCPU |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 vCPU |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vCPU |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 vCPU core |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 vCPU |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 vCPU |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|SLES 24 vCPU core |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES 32 vCPU |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES 40 vCPU core |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 vCPU |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 vCPU core |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 vCPU core |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 vCPU core |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server - Standard

Nomi del marketplace del portale di Azure:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|VM SUSE | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- |--- |
|SLES 1-2 vCPU core |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3-4 vCPU core |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES 5+ vCPU |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo per i piani software SUSE con le prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

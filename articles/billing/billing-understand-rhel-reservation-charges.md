---
title: Comprendere lo sconto del piano di prenotazione di Red Hat e dell'utilizzo - Azure | Microsoft Docs
description: Informazioni su come gli sconti di Red Hat piano vengono applicati al software di Red Hat in macchine virtuali.
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
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: 9eba59bcdc9974519fc1ed37078a205eff28f116
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653081"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Informazioni su come viene applicato lo sconto relativo piano di impegno software Red Hat Linux Enterprise per Azure

Dopo aver acquistato un piano di Red Hat Linux, lo sconto viene applicato automaticamente alle macchine virtuali Red Hat distribuite (VM) che soddisfano la prenotazione. Un piano di Red Hat Linux copre il costo dell'esecuzione del software di Red Hat in una VM di Azure.

Per acquistare il piano giusto di Red Hat Linux, è necessario comprendere quali VM sono Red Hat si esegue e il numero di Vcpu su tali macchine virtuali. Usare le sezioni seguenti per identificare il piano da acquistare in base al file CSV relativo all'utilizzo.

## <a name="discount-applies-to-different-vm-sizes"></a>Viene applicato uno sconto per diverse dimensioni delle macchine Virtuali

Ad esempio istanze di VM riservate, gli acquisti di piano di Red Hat offrono flessibilità le dimensioni dell'istanza. Ciò significa che lo sconto si applica anche quando si distribuisce una VM con un numero di vCPU diverso. Lo sconto si applica a diverse dimensioni VM all'interno del piano software.

L'importo dello sconto dipende dal rapporto riportato nelle tabelle seguenti. Il rapporto mette a confronto il footprint relativo per ogni contatore di quel gruppo. Il rapporto varia a seconda delle vCPU delle VM. Usare il valore di percentuale per la quale calcolare il numero di istanze di macchina virtuale otterrà lo sconto relativo piano di Red Hat Linux.

Ad esempio, se si acquista un piano per Red Hat Linux Enterprise Server per una macchina virtuale con 3 o 4 Vcpu, il rapporto per tale prenotazione è 2. Lo sconto copre il costo di software di Red Hat per:

- 2 VM distribuite con 1 o 2 vCPU,
- 1 VM distribuita con 3 o 4 vCPU,
- oppure 0,77 o circa il 77% di una VM con 5 o più vCPU.

Il rapporto per 5 o più vCPU è 2,6. Quindi, una prenotazione per Red Hat con una macchina virtuale con 5 o più Vcpu copre una sola parte del costo del software, ovvero circa 77%.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Comprendere l'utilizzo della macchina virtuale Red Hat prima dell'acquisto

Le tabelle seguenti mostrano i piani software per i quali è possibile acquistare una prenotazione, i relativi contatori di utilizzo e i rapporti per ciascuno.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux.

Nomi del marketplace del portale di Azure:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (più recente lvm)

|Red Hat VM | ID contatore| Rapporto| Dimensione VM di esempio|
| -------| ------------------------| --- |--- |
|1-4 vCPU licenza della macchina virtuale|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1-4 vCPU licenza della macchina virtuale|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1-4 vCPU licenza della macchina virtuale|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5 + vCPU licenza della macchina virtuale|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|5 + vCPU licenza della macchina virtuale|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|vCPU 44 licenza della macchina virtuale|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|60 vCPU licenza della macchina virtuale|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux per SAP con disponibilità elevata

Nome del marketplace del portale di Azure:

|Red Hat VM | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- | --- | ------------------------| --- | --- |
|1-4 vCPU licenza della macchina virtuale |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5 + vCPU licenza della macchina virtuale|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux con disponibilità elevata

Nomi del marketplace del portale di Azure:

|Red Hat VM | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- | --- |
|1-4 vCPU licenza della macchina virtuale|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5 + vCPU licenza della macchina virtuale|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Nomi del marketplace del portale di Azure:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat VM | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- |--- |
|1 vCPU licenza della macchina virtuale|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 vCPU licenza della macchina virtuale|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 vCPU licenza della macchina virtuale|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 vCPU licenza della macchina virtuale|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|8 vCPU licenza della macchina virtuale|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|12 vCPU licenza della macchina virtuale|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 vCPU licenza della macchina virtuale|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|20 vCPU licenza della macchina virtuale|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 vCPU licenza della macchina virtuale|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 vCPU licenza della macchina virtuale|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|40 vCPU licenza della macchina virtuale|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|vCPU 44 licenza della macchina virtuale|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|60 vCPU licenza della macchina virtuale|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 vCPU licenza della macchina virtuale|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 vCPU licenza della macchina virtuale|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|96 vCPU licenza della macchina virtuale|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 vCPU licenza della macchina virtuale|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Nomi del marketplace del portale di Azure:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat VM | ID contatore | Rapporto|Dimensione VM di esempio|
| ------- |------------------------| --- |--- |
|1 vCPU licenza della macchina virtuale|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 vCPU licenza della macchina virtuale|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 vCPU licenza della macchina virtuale|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 vCPU licenza della macchina virtuale|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|8 vCPU licenza della macchina virtuale|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|12 vCPU licenza della macchina virtuale|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 vCPU licenza della macchina virtuale|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|20 vCPU licenza della macchina virtuale|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 vCPU licenza della macchina virtuale|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 vCPU licenza della macchina virtuale|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|40 vCPU licenza della macchina virtuale|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|vCPU 44 licenza della macchina virtuale|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|60 vCPU licenza della macchina virtuale|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 vCPU licenza della macchina virtuale|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 vCPU licenza della macchina virtuale|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|96 vCPU licenza della macchina virtuale|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 vCPU licenza della macchina virtuale|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo per i piani software Red Hat con le prenotazioni di Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le prenotazioni per Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
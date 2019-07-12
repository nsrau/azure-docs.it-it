---
title: Come viene applicato uno sconto di acquisto anticipato di Azure Databricks
description: Informazioni su come viene applicato uno sconto di acquisto anticipato di Azure Databricks per l'utilizzo.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827633"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Come viene applicato lo sconto di acquisto anticipato di Azure Databricks

È possibile usare unità di commit pre-acquistate Azure Databricks (DBCU) in qualsiasi momento durante il periodo di validità di acquisto. Qualsiasi utilizzo di Azure Databricks detrae dal pre-acquistate DBCUs automaticamente.

A differenza delle macchine virtuali, unità di pre-acquistate non scadono su base oraria. È possibile usarli in qualsiasi momento durante il periodo di validità dell'acquisto. Per ottenere sconti preacquisto, non è necessario ridistribuire o assegnare un piano di pre-acquistato per le aree di lavoro Azure Databricks per l'utilizzo.

Lo sconto preacquisto valido solo per Azure Databricks (DBU) utilizzo. Altri costi, ad esempio calcolo, archiviazione e rete vengono addebitati separatamente.

## <a name="pre-purchase-discount-application"></a>Applicazione dello sconto preacquisto

Preacquisto Databricks si applica a tutti i livelli e i carichi di lavoro di Databricks. È possibile considerare il preacquisto come un pool di unità di commit Databricks prepagato. Utilizzo viene dedotto dal pool, indipendentemente dal carico di lavoro o a livelli. Utilizzo viene dedotto il rapporto tra i seguenti:

| **Carico di lavoro** | **Rapporto tra applicazioni DBU-livello Standard** | **Rapporto tra applicazioni DBU-livello Premium** |
| --- | --- | --- |
| Analisi dei dati | 0,4 | 0.55 |
| Ingegneria dei dati | 0,15 | 0.30 |
| Data Engineering Light | 0,07 | 0.22 |

Ad esempio, quando viene utilizzata una quantità di dati di Analitica: livello Standard, le unità di commit Databricks pre-acquistate viene sottratto da 0,4 unità di misura. Quando una quantità di luce ingegneria dei dati: livello Standard viene usato, l'unità di commit Databricks pre-acquistate viene sottratto dalle unità 0,07

## <a name="determine-plan-use"></a>Determinare l'utilizzo di piano

Per determinare l'utilizzo di piano DBCU, passare al portale di Azure > **prenotazioni** e fare clic sul piano di Databricks acquistato. Vengano illustrato l'utilizzo fino a oggi con qualsiasi rimanenti unità. Per altre informazioni sulla determinazione della prenotazione, vedere la [vedere l'utilizzo della prenotazione](billing-reservation-apis.md#see-reservation-usage) articolo.

## <a name="how-discount-application-shows-in-usage-data"></a>Come applicazione dello sconto Mostra dati di utilizzo

Quando viene applicato lo sconto di acquisto anticipato per l'utilizzo di Databricks, i costi su richiesta vengono visualizzati come zero nei dati di utilizzo. Per altre informazioni sull'utilizzo e costi di prenotazione, vedere [utilizzo e costi di prenotazione ottenere contratto Enterprise Agreement](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).
- Per altre informazioni sulla pre-acquisto di Azure Databricks per risparmiare denaro, vedere [i costi di ottimizzare Azure Databricks con un acquisto anticipato](billing-prepay-databricks-reserved-capacity.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Gestire le prenotazioni in Azure](billing-manage-reserved-vm-instance.md)
  - [Comprendere l'utilizzo della prenotazione per una sottoscrizione con tariffe a consumo](billing-understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

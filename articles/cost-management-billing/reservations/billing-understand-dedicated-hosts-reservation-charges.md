---
title: Informazioni sullo sconto per le istanze riservate applicato agli host dedicati di Azure
description: Informazioni su come viene applicato lo sconto per le istanze di macchine virtuali riservate di Azure agli host dedicati di Azure.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: f9080d13c4358a03b3f13ffcf346af42e49c47c6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689457"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Informazioni su come viene applicato lo sconto della prenotazione di Azure agli host dedicati di Azure

Dopo avere acquistato un'istanza riservata di host dedicati di Azure, lo sconto della prenotazione viene applicato automaticamente agli host dedicati corrispondenti agli attributi e alla quantità della prenotazione. Una prenotazione copre i costi delle risorse di calcolo degli host dedicati.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Se si elimina un host dedicato, lo sconto della prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Sconto della prenotazione per gli host dedicati

L'istanza riservata di host dedicati di Azure offre uno sconto sul costo dell'infrastruttura di calcolo usata per gli host dedicati. Lo sconto si applica agli host dedicati indipendentemente dal fatto che vengano utilizzati o meno dalle macchine virtuali. La prenotazione non copre i costi aggiuntivi come le licenze, l'utilizzo della rete o il consumo di risorse di archiviazione da parte della macchina virtuale distribuita nell'host dedicato.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza,  [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Uso di host dedicati di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Prezzi degli host dedicati](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Gestire le prenotazioni per Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)


---
title: Informazioni sullo sconto per le istanze riservate applicato agli host dedicati di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per le istanze di macchine virtuali riservate di Azure agli host dedicati di Azure.
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 26b71952e3d24214331b314f723728b56e3c4254
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207773"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Informazioni su come viene applicato lo sconto della prenotazione di Azure agli host dedicati di Azure

Dopo avere acquistato un'istanza riservata di host dedicati di Azure, lo sconto della prenotazione viene applicato automaticamente agli host dedicati corrispondenti agli attributi e alla quantità della prenotazione. Una prenotazione copre i costi delle risorse di calcolo degli host dedicati.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Se si elimina un host dedicato, lo sconto della prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non sono disponibili risorse corrispondenti nell'ambito specificato, le ore prenotate vanno  *perse*.

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


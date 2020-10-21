---
title: Ammortizzare i costi di Prenotazione di Azure
description: Informazioni su come visualizzare i costi di Prenotazione di Azure per il chargeback.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: aba6ea467788c51d179ef9377243efb6035b6f98
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148366"
---
# <a name="charge-back-azure-reservation-costs"></a>Ammortizzare i costi di Prenotazione di Azure

I lettori di fatturazione del Contratto Enterprise e del Contratto del cliente Microsoft possono visualizzare i dati sui costi ammortizzati per le prenotazioni. Possono usare i dati sui costi per addebitare il valore monetario per una sottoscrizione, un gruppo di risorse, una risorsa o un tag ai partner. Nei dati ammortizzati il prezzo effettivo è il costo di prenotazione oraria ripartito. Il costo è il costo totale di utilizzo della prenotazione da parte della risorsa in quel giorno.

Gli utenti con una singola sottoscrizione possono ottenere i dati sui costi ammortizzati dal relativo file di utilizzo. Quando una risorsa riceve uno sconto per la prenotazione, la sezione *AdditionalInfo* nel file di utilizzo contiene i dettagli della prenotazione. Per altre informazioni, vedere [Scaricare l’utilizzo dal portale di Azure](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="get-reservation-charge-back-data-for-chargeback"></a>Ottenere i dati di addebito della prenotazione per il chargeback

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Gestione costi e fatturazione**.
1. In **Costo effettivo**, selezionare la metrica **Costo ammortizzato**.
1. Per vedere quali risorse sono state usate da una prenotazione, applicare un filtro per **Prenotazione** e quindi selezionare prenotazioni.
1. Impostare la **Granularità** su **Mensile** o **Giornaliera**.
1. Impostare il tipo di grafico su **Tabella**.
1. Impostare l'opzione **Raggruppa per** su **Risorsa**.

[![Esempio che mostra i costi delle risorse di prenotazione che è possibile usare per il chargeback](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Ecco un video che illustra come visualizzare i costi di utilizzo della prenotazione nel portale di Azure.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
  - [Gestire le prenotazioni in Azure](manage-reserved-vm-instance.md)
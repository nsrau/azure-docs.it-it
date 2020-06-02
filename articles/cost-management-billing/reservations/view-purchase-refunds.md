---
title: Visualizzare le transazioni di acquisto e di rimborso di Prenotazione di Azure
description: Informazioni su come visualizzare le transazioni di acquisto e di rimborso di Prenotazione di Azure.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: banders
ms.openlocfilehash: 0767ec7b4815fa7aadfef2bd6204627c30173993
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715360"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Visualizzare le transazioni di acquisto e di rimborso della prenotazione

Esistono diversi modi per visualizzare le transazioni di acquisto e di rimborso delle prenotazioni. È possibile usare le API REST, Power BI e il portale di Azure.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Visualizzare le transazioni di prenotazione nel portale di Azure

Un amministratore di fatturazione dell’iscrizione Enterprise o del Contratto del cliente Microsoft può visualizzare le transazioni di prenotazione in Gestione costi e fatturazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione dei costi e fatturazione**.
1. Selezionare **Transazioni di prenotazione**.
1. Per filtrare i risultati, selezionare **Intervallo di tempo**, **Tipo**, o **Descrizione**.
1. Selezionare **Applica**.

[![Screenshot che mostra le transazioni di prenotazione nel portale di Azure](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Visualizzare le transazioni di prenotazione in Power BI

Un amministratore di fatturazione dell’iscrizione Enterprise o del Contratto del cliente Microsoft può visualizzare le transazioni di prenotazione con l'app Power BI Gestione costi.

1. Scaricare l’[app Power BI Gestione costi](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Passare al report Acquisti istanza riservata.

[![Esempio che mostra le transazioni di prenotazione](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Per altre informazioni, vedere [App Power BI Gestione costi di Azure per contratti Enterprise](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

## <a name="use-apis-to-get-reservation-transactions"></a>Usare le API per ottenere le transazioni di prenotazione

Gli utenti del Contratto Enterprise e del Contratto del cliente Microsoft possono ottenere i dati delle transazioni di prenotazione usando [Transazioni di prenotazione - Elenca API](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
  - [Gestire le prenotazioni in Azure](manage-reserved-vm-instance.md)

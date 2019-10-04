---
title: Risparmiare sui costi addebitati per SQL Data Warehouse con la capacità riservata di Azure
description: Informazioni su come risparmiare sui costi addebitati per SQL Data Warehouse con la capacità riservata.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70806273"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Risparmiare sui costi addebitati per SQL Data Warehouse con la capacità riservata

È possibile risparmiare denaro con Azure SQL Data Warehouse impegnando una prenotazione per l'utilizzo di DWU a elevato utilizzo di calcolo per un periodo di uno o tre anni. Per acquistare capacità riservata di SQL Data Warehouse, è necessario scegliere l'area di Azure e il periodo di validità. Aggiungere quindi lo SKU di SQL Data Warehouse al carrello e scegliere la quantità di unità DWU a elevato utilizzo di calcolo da acquistare.

Quando si acquista una prenotazione, l'utilizzo di SQL Data Warehouse corrispondente agli attributi della prenotazione non viene più addebitato in base alle tariffe con pagamento in base al consumo.

Una prenotazione non copre gli addebiti per risorse di archiviazione o di rete associate all'utilizzo di SQL Data Warehouse.

Allo scadere della capacità riservata, l'esecuzione delle istanze di SQL Data Warehouse continua, ma le istanze vengono addebitate in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

Per informazioni sui prezzi, vedere l'[offerta relativa alla capacità riservata di SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

È possibile acquistare capacità riservata di SQL Data Warehouse nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Usare [pagamenti anticipati o mensili](billing-monthly-payments-reservations.md) per acquistare la prenotazione. Per acquistare capacità riservata:

- È necessario disporre del ruolo di proprietario per almeno una sottoscrizione Enterprise o con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, l'opzione **Aggiungi istanze riservate** deve essere abilitata in [EA Portal](https://ea.azure.com/). Se questa impostazione è disabilitata, è necessario essere un amministratore del contratto EA.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o di vendita possono acquistare capacità riservata di SQL Data Warehouse.

Per altre informazioni sul modo in cui gli acquisti di prenotazioni vengono addebitati ai clienti Enterprise e ai clienti con pagamento in base al consumo, vedere [Informazioni sull'utilizzo della prenotazione di Azure per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md) e [Informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Scegliere le dimensioni corrette prima dell'acquisto

Le dimensioni della prenotazione di SQL Data Warehouse devono essere basate sul totale delle unità DWU a elevato utilizzo di calcolo utilizzate. Gli acquisti vengono effettuati in incrementi di 100 DWU a elevato utilizzo di calcolo.

Si supponga, ad esempio, che il consumo totale di SQL Data Warehouse sia DW3000c. Si vuole acquistare la capacità riservata per coprire interamente il consumo. È quindi necessario acquistare 30 unità di capacità riservata da 100 DWU a elevato utilizzo di calcolo.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Acquistare capacità riservata di SQL Data Warehouse

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare una sottoscrizione. Usare l'elenco Sottoscrizione per scegliere la sottoscrizione usata per pagare la capacità riservata. I costi relativi alla capacità riservata vengono addebitati in base al metodo di pagamento della sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P).
   - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
   - Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.
4. Selezionare un ambito. Usare l'elenco Ambito per scegliere un ambito della sottoscrizione.
   - **Gruppo di risorse singolo**: lo sconto della prenotazione viene applicato solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.
   - **Sottoscrizione singola**: lo sconto della prenotazione viene applicato solo alle risorse corrispondenti incluse nella sottoscrizione selezionata.
   - **Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise Agreement, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.
   - Per i clienti Enterprise, il contesto di fatturazione è la registrazione EA.
   - Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.
5. Selezionare un'area per scegliere un'area di Azure coperta dalla capacità riservata.
6. Scegliere una quantità. Immettere la quantità di 100 unità DWU a elevato utilizzo di calcolo che si intende acquistare.    
   Specificando una quantità pari a 30, ad esempio, si otterrebbero 3.000 DWU a elevato utilizzo di calcolo di capacità riservata ogni ora.
7. Rivedere il costo della prenotazione della capacità riservata di SQL Data Warehouse nella sezione **Costi**.
8. Selezionare **Acquisto**.
9. Selezionare **Visualizza questa prenotazione** per visualizzare lo stato dell'acquisto.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

Uno sconto sulla prenotazione viene applicato automaticamente al numero di istanze di SQL Data Warehouse corrispondenti all'ambito e all'area della capacità riservata di SQL Data Warehouse. È possibile aggiornare l'ambito della capacità riservata di SQL Data Warehouse nel [portale di Azure](https://portal.azure.com/), in PowerShell, nell'interfaccia della riga di comando o tramite l'API.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla modalità di applicazione degli sconti per la prenotazione di SQL Data Warehouse di Azure, vedere [Modalità di applicazione degli sconti per le prenotazioni in Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
  - [Comprendere lo sconto sulle prenotazioni di Azure](billing-understand-reservation-charges.md)
  - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

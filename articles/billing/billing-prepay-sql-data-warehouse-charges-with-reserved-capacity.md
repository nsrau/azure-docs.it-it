---
title: Pagare in anticipo per gli addebiti di SQL Data Warehouse con capacità riservata di Azure | Microsoft Docs
description: Informazioni su come è possibile pagare in anticipo per gli addebiti di SQL Data Warehouse con capacità riservata per risparmiare denaro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371190"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Pagare in anticipo per gli addebiti di SQL Data Warehouse con capacità riservata

È possibile risparmiare denaro con Azure SQL Data Warehouse dal pagamento anticipato per l'utilizzo di Dwu per una durata di uno o tre anni. Per acquistare capacità riservata SQL Data Warehouse, è necessario scegliere Azure area e durata del contratto. Quindi, aggiungere lo SKU di SQL Data Warehouse al carrello e scegliere la quantità di unità Dwu che si desidera acquistare.

Quando si acquista una prenotazione, SQL Data Warehouse non è più sull'utilizzo che corrisponde agli attributi della prenotazione viene addebitato il pagamento in base al capacità di passare tariffe.

Una prenotazione non copre l'archiviazione o tariffe di rete associati all'utilizzo di SQL Data Warehouse.

Quando la capacità riservata scade, le istanze di SQL Data Warehouse continuano a essere eseguiti, ma addebitate la tariffa con pagamento capacità di passare. Le prenotazioni non vengono rinnovati automaticamente.

Per informazioni sui prezzi, vedere la [SQL Data Warehouse riservato offerta di capacità](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

È possibile acquistare capacità riservata di Azure SQL Data Warehouse nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Per acquistare capacità riservata:

- È necessario disporre del ruolo di proprietario per almeno un enterprise o una sottoscrizione con pagamento a consumo.
- Per le sottoscrizioni Enterprise, il **aggiungere le istanze riservate** opzione deve essere abilitata nel [portale EA](https://ea.azure.com/). Se l'impostazione è disabilitata, è necessario essere un amministratore con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo il agenti di amministrazione o gli agenti di vendita possono acquistare capacità di SQL Data Warehouse sono riservati.

Per altre informazioni sul modo in cui i clienti aziendali e i clienti con pagamento a consumo vengono addebitati per gli acquisti di prenotazioni, vedere [comprendere l'utilizzo della prenotazione di Azure per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md) e [comprendere Azure utilizzo della prenotazione per la sottoscrizione con pagamento a consumo](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Scegliere le dimensioni corrette prima dell'acquisto

La dimensione di prenotazione deve basarsi sul totale di SQL Data Warehouse di calcolo unità data warehouse (Dwu) utilizzate. Gli acquisti vengono effettuati in incrementi di 100 Dwu.

Si supponga, ad esempio, che il consumo totale di SQL Data Warehouse è DW3000c. Si desidera acquistare capacità riservata per tutto. Pertanto, è necessario acquistare 30 unità di capacità Dwu sono riservati.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Acquistare capacità riservata SQL Data Warehouse

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare una sottoscrizione. Usare l'elenco delle sottoscrizioni per scegliere la sottoscrizione che viene usata per pagare la capacità riservata. Il metodo di pagamento della sottoscrizione viene addebitato il costo iniziale per la capacità riservata. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P).
  - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
  - Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.
4. Selezionare un ambito. Usare l'elenco di ambito per scegliere un ambito della sottoscrizione.
  - Con il **singolo** opzione, lo sconto della prenotazione viene applicato a SQL Data Warehouse distribuito nella sottoscrizione selezionata.
  - Con il **condiviso** opzione, lo sconto della prenotazione viene applicato alle istanze in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione.
    - Per i clienti aziendali, il contesto di fatturazione è la registrazione con contratto Enterprise.
    - Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.
5. Selezionare un'area per scegliere un'area di Azure sottoposti alla capacità riservata.
6. Scegliere una quantità. Immettere la quantità di 100 unità di Data Warehouse (Dwu) che si desidera acquistare.    
  Ad esempio, una quantità pari a 30 produrrebbe è 3000 Dwu di capacità prenotata a ogni ora.
7. SQL Data Warehouse riservato costo prenotazione della capacità nella revisione il **costi** sezione.
8. Selezionare **Acquisto**.
9. Selezionare **Visualizza questa prenotazione** per visualizzare lo stato dell'acquisto.

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Se è necessario annullare il SQL Data Warehouse capacità riservata, potrebbe esserci una tariffa per la terminazione anticipata 12%. I rimborsi si basano sul prezzo più basso tra il prezzo di acquisto o il prezzo corrente della prenotazione. Rimborsi sono limitati a $50,000.00 all'anno. La restituzione che viene visualizzato è il saldo ripartito rimanente, meno il 12% costo per la risoluzione. Per richiedere un annullamento, andare alla prenotazione nel portale di Azure e selezionare **Rimborso** per creare una richiesta di supporto.

Se è necessario modificare la capacità di SQL Data Warehouse sono riservati a un'altra area o un termine, si può scambiarlo con un altro impegni di valore uguale o superiore. Come data di inizio del periodo della nuova prenotazione non viene conservata quella della prenotazione scambiata. L'uno o tre anni inizia quando si crea la nuova prenotazione. Per richiedere uno scambio, aprire la prenotazione nel portale di Azure e selezionare **Exchange** per creare una richiesta di supporto.

Per altre informazioni su come le prenotazioni di exchange o un rimborso, vedere [scambi di prenotazione e rimborsi](billing-azure-reservations-self-service-exchange-and-refund.md).

Lo sconto della prenotazione viene applicato automaticamente al numero di istanze di SQL Data Warehouse corrispondenti all'ambito di capacità riservata SQL Data Warehouse e area. È possibile aggiornare l'ambito della capacità riservata SQL Data Warehouse con il [portale di Azure](https://portal.azure.com/), PowerShell, CLI o tramite l'API.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come vengono applicati sconti prenotazione Azure SQL Data Warehouse, vedere [come vengono applicati sconti prenotazione Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
  - [Comprendere lo sconto sulle prenotazioni di Azure](billing-understand-reservation-charges.md)
  - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

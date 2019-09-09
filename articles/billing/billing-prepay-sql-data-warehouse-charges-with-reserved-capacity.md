---
title: Risparmio sui costi per SQL Data Warehouse addebiti con la capacità riservata di Azure
description: Scopri come risparmiare sui costi per SQL Data Warehouse addebiti con capacità riservata per risparmiare denaro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806273"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Risparmio sui costi per SQL Data Warehouse addebiti con capacità riservata

È possibile risparmiare denaro con Azure SQL Data Warehouse eseguendo il commit di una prenotazione per l'utilizzo di DWU per una durata di uno o tre anni. Per acquistare SQL Data Warehouse capacità riservata, è necessario scegliere l'area di Azure e il termine. Aggiungi quindi lo SKU di SQL Data Warehouse al carrello e scegli la quantità di unità DWU a elevato utilizzo di calcolo da acquistare.

Quando acquisti una prenotazione, l'utilizzo di SQL Data Warehouse corrispondente agli attributi della prenotazione non viene più addebitato in base alle tariffe con pagamento in base al consumo.

Una prenotazione non copre gli addebiti per risorse di archiviazione o di rete associate all'utilizzo di SQL Data Warehouse.

Allo scadere della capacità riservata, l'esecuzione delle istanze di SQL Data Warehouse continua, ma le istanze vengono addebitate in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

Per informazioni sui prezzi, vedere la [SQL data warehouse offerta di capacità riservata](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

È possibile acquistare Azure SQL Data Warehouse capacità riservata nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Pagare per la prenotazione [prima o con pagamenti mensili](billing-monthly-payments-reservations.md). Per acquistare capacità riservata:

- È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o con pagamento in base al consumo.
- Per le sottoscrizioni aziendali, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). Se l'impostazione è disabilitata, è necessario essere un amministratore EA.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare SQL Data Warehouse capacità riservata.

Per altre informazioni su come i clienti aziendali e i clienti con pagamento in base al consumo vengono addebitati per gli acquisti di prenotazione, vedere [informazioni sull'utilizzo della prenotazione di Azure per la registrazione Enterprise](billing-understand-reserved-instance-usage-ea.md) e [informazioni sull'utilizzo della prenotazione di Azure per il Sottoscrizione con pagamento in base al](billing-understand-reserved-instance-usage.md)consumo.

## <a name="choose-the-right-size-before-purchase"></a>Scegli le dimensioni corrette prima dell'acquisto

Le dimensioni di prenotazione del SQL Data Warehouse devono essere basate sul totale delle unità di calcolo data warehouse (DWU) utilizzate. Gli acquisti vengono effettuati in incrementi di 100 DWU.

Si supponga, ad esempio, che il consumo totale di SQL Data Warehouse sia DW3000c. Si vuole acquistare la capacità riservata per tutte le funzionalità. È quindi necessario acquistare 30 unità di capacità riservata DWU.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Acquistare SQL Data Warehouse capacità riservata

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Tutti i servizi** > **Prenotazioni**.
3. Selezionare una sottoscrizione. Usare l'elenco sottoscrizione per scegliere la sottoscrizione usata per pagare la capacità riservata. Al metodo di pagamento della sottoscrizione vengono addebitati i costi per la capacità riservata. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P).
   - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
   - Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.
4. Selezionare un ambito. Usare l'elenco ambito per scegliere un ambito di sottoscrizione.
   - **Ambito del gruppo di risorse singolo** : applica lo sconto di prenotazione alle risorse corrispondenti solo nel gruppo di risorse selezionato.
   - **Singolo ambito di sottoscrizione** : applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.
   - **Ambito condiviso** : applica lo sconto di prenotazione alle risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per Enterprise Agreement clienti, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni con tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.
   - Per i clienti aziendali, il contesto di fatturazione è la registrazione EA.
   - Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.
5. Selezionare un'area per scegliere un'area di Azure coperta dalla capacità riservata.
6. Scegliere una quantità. Immettere la quantità di 100 unità di data warehouse (DWU) che si desidera acquistare.    
   Una quantità pari a 30, ad esempio, darebbe 3.000 DWU di capacità riservata ogni ora.
7. Esaminare il costo di prenotazione per la capacità riservata SQL Data Warehouse nella sezione **costi** .
8. Selezionare **Acquisto**.
9. Selezionare **Visualizza questa prenotazione** per visualizzare lo stato di acquisto.

## <a name="cancel-exchange-or-refund-reservations"></a>Prenotazioni per annullamento, scambio o rimborso

È possibile annullare, scambiare o rimborsare le prenotazioni con determinate limitazioni. Per altre informazioni, vedere [scambi e rimborsi self-service per le prenotazioni di Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

Uno sconto di prenotazione viene applicato automaticamente al numero di istanze SQL Data Warehouse che corrispondono all'ambito della capacità riservata SQL Data Warehouse e all'area. È possibile aggiornare l'ambito del SQL Data Warehouse capacità riservata con l' [portale di Azure](https://portal.azure.com/), PowerShell, l'interfaccia della riga di comando o tramite l'API.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sul modo in cui si applicano gli sconti per Azure SQL Data Warehouse, vedere la pagina relativa [alla modalità di applicazione degli sconti per Azure SQL data warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
  - [Comprendere lo sconto sulle prenotazioni di Azure](billing-understand-reservation-charges.md)
  - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

---
title: Prepararsi ad acquistare una prenotazione di Azure
description: Informazioni sui punti importanti prima di acquistare una prenotazione di Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235661"
---
# <a name="prepare-to-buy-a-reservation"></a>Prepararsi ad acquistare una prenotazione

Le prenotazioni di Azure consentono di risparmiare denaro a fronte di un impegno di acquisto di un piano di uno o tre anni per numerose risorse di Azure. Prima di sottoscrivere un impegno di acquisto per una prenotazione, esaminare le sezioni seguenti per prepararsi all'acquisto.

## <a name="who-can-buy-a-reservation"></a>Chi può acquistare una prenotazione

Per acquistare un piano, è necessario avere un ruolo di proprietario di una sottoscrizione Enterprise (MS-AZR-0017P o MS-AZR-0148P), di una sottoscrizione con pagamento in base al consumo (MS-AZR-0003P o MS-AZR-0023P) o di una sottoscrizione Contratto del cliente Microsoft. Per acquistare prenotazioni di Azure, i partner CSP (Cloud Solution Provider) possono usare il portale di Azure o il  [Centro per i partner](/partner-center/azure-reservations) .

I clienti con contratto Enterprise Agreement (EA) possono limitare gli acquisti agli amministratori EA disabilitando l'opzione **Aggiungi istanze riservate** in EA Portal. Gli amministratori EA devono essere proprietari di almeno una sottoscrizione EA per acquistare una prenotazione. Questa opzione è utile per le organizzazioni che vogliono che le prenotazioni per diversi centri di costo vengano acquistate da un team centralizzato. Dopo l'acquisto, i team centralizzati possono aggiungere proprietari del centro di costo alle prenotazioni. I proprietari possono quindi definire l'ambito della prenotazione per le sottoscrizioni. Il team centralizzato non deve necessariamente avere accesso come proprietario della sottoscrizione in cui viene acquistata la prenotazione.

Lo sconto per la prenotazione si applica solo alle risorse associate alle sottoscrizioni acquistate tramite piani Enterprise, Cloud Solution Provider (CSP), Contratto del cliente Microsoft e singoli piani con pagamento in base al consumo.

## <a name="scope-reservations"></a>Definire l'ambito delle prenotazioni

Come ambito di una prenotazione è possibile definire una sottoscrizione o gruppi di risorse. Quando si imposta l'ambito di una prenotazione, si selezionano le risorse a cui verrà applicato il risparmio corrispondente. Se come ambito della prenotazione si sceglie un gruppo di risorse, gli sconti della prenotazione si applicano solo al gruppo di risorse, non all'intera sottoscrizione.

### <a name="reservation-scoping-options"></a>Opzioni dell'ambito delle prenotazioni

Sono disponibili tre opzioni per definire l'ambito di una prenotazione, a seconda delle esigenze:

- **Ambito gruppo di risorse singolo**: lo sconto per la prenotazione si applica alle risorse corrispondenti incluse solo nel gruppo di risorse selezionato.
- **Ambito sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.
- **Ambito condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise Agreement, il contesto di fatturazione è la registrazione. Per i clienti con Contratto del cliente Microsoft, l'ambito di fatturazione corrisponde al profilo di fatturazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.

Per l'applicazione degli sconti sull'utilizzo di risorse, Azure elabora la prenotazione nell'ordine seguente:

1. Prenotazioni con ambito costituito da un gruppo di risorse
2. Prenotazioni con ambito singolo
3. Prenotazioni con ambito condiviso

Un singolo gruppo di risorse può ottenere sconti da più prenotazioni, a seconda dell'ambito definito.

Dopo aver acquistato una prenotazione, è sempre possibile aggiornare l'ambito. A tale scopo, passare alla prenotazione, fare clic su **Configurazione** e ridefinire l'ambito della prenotazione. La ridefinizione dell'ambito di una prenotazione non è una transazione commerciale. Il periodo di prenotazione non cambia. Per altre informazioni sull'aggiornamento dell'ambito, vedere [Aggiornare l'ambito dopo l'acquisto di una prenotazione](manage-reserved-vm-instance.md#change-the-reservation-scope).

![Esempio di modifica dell'ambito di una prenotazione](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>Acquistare prenotazioni

È possibile acquistare prenotazioni tramite il portale di Azure, le API, PowerShell e l'interfaccia della riga di comando. Quando si è pronti per acquistare una prenotazione, leggere gli articoli seguenti in base alle esigenze:

- [Servizio app](prepay-app-service-isolated-stamp.md)
- [Cache Redis di Azure](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Esplora dati](../../data-explorer/pricing-reserved-capacity.md)
- [Archiviazione su disco](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Host dedicato](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Piani software](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Database SQL](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [Macchine virtuali](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le prenotazioni per le risorse di Azure](manage-reserved-vm-instance.md)

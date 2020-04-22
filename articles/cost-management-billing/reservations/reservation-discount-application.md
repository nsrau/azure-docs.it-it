---
title: Come viene applicato lo sconto per la prenotazione di Azure
description: Questo articolo fornisce informazioni su come vengono in genere applicati gli sconti per le istanze riservate.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 5781ade7b2f3cfc7514208861de025cc84944fcd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380641"
---
# <a name="how-a-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Questo articolo fornisce informazioni su come vengono in genere applicati gli sconti per le istanze riservate. Lo sconto per la prenotazione si applica all'utilizzo delle risorse in base agli attributi selezionati al momento dell'acquisto. Gli attributi includono l'ambito in cui vengono eseguiti i database SQL, Azure Cosmos DB, le macchine virtuali o altre risorse corrispondenti. Se ad esempio si vuole usufruire di uno sconto per la prenotazione di quattro macchine virtuali Standard D2 nell'area Stati Uniti occidentali, selezionare la sottoscrizione in cui sono in esecuzione le VM.

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

Ad esempio, è possibile creare una risorsa in un secondo momento e avere una prenotazione corrispondente sottoutilizzata. Lo sconto per la prenotazione si applica automaticamente alla nuova risorsa corrispondente.

Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni. Dopo aver acquistato una prenotazione è possibile modificare l'ambito. Per altre informazioni, vedere [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).

Lo sconto per la prenotazione si applica solo alle risorse associate a sottoscrizioni Enterprise, Contratto del cliente Microsoft, CSP o con pagamento in base al consumo. Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="when-the-reservation-term-expires"></a>Quando il periodo di prenotazione termina

La fine del periodo di prenotazione comporta anche la scadenza dello sconto sulla fatturazione e a quel punto le risorse vengono fatturate al prezzo in base al consumo. Per impostazione predefinita, le prenotazioni non sono configurate per il rinnovo automatico. È possibile scegliere di abilitare il rinnovo automatico di una prenotazione selezionando l'opzione nelle relative impostazioni. Con il rinnovo automatico, alla scadenza della prenotazione esistente ne viene acquistata una sostitutiva. Per impostazione predefinita, la prenotazione sostitutiva ha gli stessi attributi di quella in scadenza. Facoltativamente, è possibile cambiare la frequenza di fatturazione, il periodo o la quantità nelle impostazioni del rinnovo. Qualsiasi utente con accesso di proprietario per la prenotazione e per la sottoscrizione usata per la fatturazione può configurare il rinnovo.  

## <a name="discount-applies-to-different-sizes"></a>Lo sconto si applica a dimensioni diverse

Quando si acquista una prenotazione, è possibile applicare lo sconto ad altre istanze con gli attributi compresi nello stesso gruppo di dimensioni. Questa funzionalità è nota come flessibilità delle dimensioni istanza. La flessibilità della copertura degli sconti dipende dal tipo di prenotazione e dagli attributi scelti all'acquisto della prenotazione.

Piani di servizio:

- Istanze di macchina virtuale riservate: quando si acquista la prenotazione, se si seleziona **Ottimizzato per: Flessibilità delle dimensioni istanza**, la copertura dello sconto dipende dalle dimensioni delle VM selezionate. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacità riservata di Archiviazione di Azure: è possibile acquistare capacità riservata per gli account di Archiviazione di Azure standard in unità di 100 TiB o 1 PiB al mese. Per informazioni sulle aree che supportano la capacità riservata di Archiviazione di Azure, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/). La capacità riservata di Archiviazione di Azure è disponibile per tutti i livelli di accesso (ad accesso frequente, sporadico e archivio) e per qualsiasi configurazione di replica (archiviazione con ridondanza locale, con ridondanza geografica o con ridondanza della zona).
- Capacità riservata del database SQL: la copertura degli sconti dipende dal livello di prestazioni selezionato. Per altre informazioni, vedere [Informazioni su come viene applicato ai database SQL lo sconto sulla prenotazione](understand-reservation-charges.md).
- Capacità riservata di Azure Cosmos DB: la copertura degli sconti dipende dalla velocità effettiva di cui viene effettuato il provisioning. Per altre informazioni, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Come si applicano gli sconti a servizi di Azure specifici

Per informazioni su come vengono applicati gli sconti a un servizio di Azure specifico, leggere gli articoli seguenti in base alle esigenze:

- [Servizio app](reservation-discount-app-service-isolated-stamp.md)
- [Cache Redis di Azure](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database di Azure per MariaDB](understand-reservation-charges-mariadb.md)
- [Database di Azure per MySQL](understand-reservation-charges-mysql.md)
- [Database di Azure per PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Esplora dati](understand-azure-data-explorer-reservation-charges.md)
- [Host dedicati](billing-understand-dedicated-hosts-reservation-charges.md)
- [Archiviazione su disco](understand-disk-reservations.md)
- [Red Hat Enterprise Linux](understand-rhel-reservation-charges.md)
- [Piani software](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [Database SQL](understand-reservation-charges.md)
- [SQL Data Warehouse](reservation-discount-azure-sql-dw.md)
- [Macchine virtuali](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Passaggi successivi

- [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](reserved-instance-windows-software-costs.md)
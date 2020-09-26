---
title: Backup e ripristino – iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Protezione dei dati dal danneggiamento o dall'eliminazione accidentale
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 2d781ca7293d4bd95ae62eadc50295ca14c2d381
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314931"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Backup e ripristino nel database di Azure per PostgreSQL-iperscalabilità (CITUS)

Database di Azure per PostgreSQL – iperscalabilità (CITUS) crea automaticamente i backup di ogni nodo e li archivia nell'archiviazione con ridondanza locale. I backup possono essere usati per ripristinare il cluster iperscalabile (CITUS) a un tempo specificato. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

## <a name="backups"></a>Backup

Almeno una volta al giorno, database di Azure per PostgreSQL esegue backup di snapshot dei file di dati e del log delle transazioni del database. I backup consentono di ripristinare un server in qualsiasi punto nel tempo entro il periodo di conservazione. Il periodo di memorizzazione è attualmente di 35 giorni per tutti i cluster. Tutti i backup vengono crittografati con la crittografia AES a 256 bit.

Nelle aree di Azure che supportano le zone di disponibilità, gli snapshot di backup vengono archiviati in tre zone di disponibilità. Fino a quando almeno una zona di disponibilità è online, il cluster iperscalabile (CITUS) è ripristinabile.

Non è possibile esportare i file di backup. Possono essere usati solo per le operazioni di ripristino nel database di Azure per PostgreSQL.

### <a name="backup-storage-cost"></a>Costo dell'archiviazione dei backup

Per i prezzi correnti di archiviazione di backup, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)di database di Azure per PostgreSQL-iperscalabile (CITUS).

## <a name="restore"></a>Restore

Nel database di Azure per PostgreSQL il ripristino di un cluster con iperscalabilità (CITUS) crea un nuovo cluster dai backup dei nodi originali. 

> [!IMPORTANT]
>È possibile ripristinare solo il cluster iperscalabile (CITUS) all'interno della stessa sottoscrizione e del gruppo di risorse e con un nome di cluster diverso.


> [!IMPORTANT]
> Non è possibile ripristinare i cluster CITUS (overscale) eliminati. Se si elimina il cluster, tutti i nodi appartenenti al cluster vengono eliminati e non possono essere recuperati. Per proteggere le risorse del cluster, post-distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare i [blocchi di gestione](/azure/azure-resource-manager/management/lock-resources).

### <a name="point-in-time-restore-pitr"></a>Ripristino temporizzato (ripristino temporizzato)

È possibile ripristinare un cluster in qualsiasi punto nel tempo entro gli ultimi 35 giorni.
Il ripristino temporizzato è utile in più scenari, Ad esempio, quando un utente elimina accidentalmente i dati, rilascia una tabella o un database importante o se un'applicazione sovrascrive accidentalmente dati appropriati con dati non validi.

Il processo di ripristino crea un nuovo cluster nella stessa area di Azure, sottoscrizione e gruppo di risorse dell'originale. Il cluster ha la configurazione originale: lo stesso numero di nodi, il numero di Vcore, le dimensioni di archiviazione, i ruoli utente, la versione di PostgreSQL e la versione dell'estensione CITUS.

Le impostazioni del firewall e i parametri del server PostgreSQL non vengono conservati dal gruppo di server originale, vengono reimpostati sui valori predefiniti. Il firewall eviterà tutte le connessioni. Sarà necessario modificare manualmente queste impostazioni dopo il ripristino.

> [!IMPORTANT]
> È necessario aprire una richiesta di supporto per eseguire il ripristino temporizzato del cluster iperscalabile (CITUS).

### <a name="post-restore-tasks"></a>Attività successive al ripristino

Dopo un ripristino da uno dei due meccanismi di ripristino, è necessario eseguire le operazioni seguenti per eseguire il backup e l'esecuzione di utenti e applicazioni:

* Se il nuovo server è destinato a sostituire il server originale, reindirizzare i client e le applicazioni client al nuovo server
* Assicurarsi che le regole del firewall a livello di server e del VNet siano appropriate per consentire agli utenti di connettersi. Queste regole non vengono copiate dal gruppo di server originale.
* Modificare i parametri del server PostgreSQL in base alle esigenze. I parametri non vengono copiati dal gruppo di server originale.
* Verificare che siano presenti gli account di accesso e le autorizzazioni a livello di database appropriati
* Configurare gli avvisi in base alle proprie esigenze.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [zone di disponibilità di Azure](/azure/availability-zones/az-overview).
* Impostare [avvisi suggeriti](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts) per i gruppi di server iperscalare (CITUS).

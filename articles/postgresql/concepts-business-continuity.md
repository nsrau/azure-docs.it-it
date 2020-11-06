---
title: Continuità aziendale-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la continuità aziendale (ripristino temporizzato, interruzione del data center, ripristino geografico e repliche) quando si usa database di Azure per PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: cf3c07f32f15ff176974219bd8143a1ea315c945
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423046"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Panoramica della continuità aziendale con database di Azure per PostgreSQL-server singolo

Questa panoramica descrive le funzionalità offerte da Database di Azure per PostgreSQL per la continuità aziendale e il ripristino di emergenza. Informazioni sulle opzioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità che è possibile usare per assicurare la continuità aziendale

Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Si tratta dell'obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione riesce a tollerare durante il ripristino dopo l'evento di arresto improvviso, ovvero l'obiettivo del punto di recupero (RPO).

Database di Azure per PostgreSQL offre funzionalità di continuità aziendale che includono backup con ridondanza geografica con la possibilità di avviare il ripristino geografico e di distribuire le repliche di lettura in un'area diversa. Ognuna presenta caratteristiche diverse per il tempo di recupero e la potenziale perdita di dati. Con la funzionalità di [ripristino geografico](concepts-backup.md) , viene creato un nuovo server usando i dati di backup replicati da un'altra area. Il tempo complessivo necessario per il ripristino e il recupero dipende dalle dimensioni del database e dalla quantità di log da ripristinare. Il tempo complessivo necessario per stabilire il server varia da pochi minuti ad alcune ore. Con le [repliche di lettura](concepts-read-replicas.md), i log delle transazioni dal database primario vengono trasmessi in modo asincrono alla replica. In caso di interruzione di un database primario a causa di un errore a livello di zona o di area, il failover alla replica fornisce una RTO più breve e una riduzione della perdita di dati.

> [!NOTE]
> Il ritardo tra il database primario e la replica dipende dalla latenza tra i siti, dalla quantità di dati da trasmettere e soprattutto dal carico di lavoro di scrittura del server primario. Carichi di lavoro di scrittura intensivi possono generare un ritardo significativo. 
>
> A causa della natura asincrona della replica usata per le repliche di lettura, **non devono** essere considerate come una soluzione a disponibilità elevata, poiché i ritardi più elevati possono significare più RTO e RPO. Solo per i carichi di lavoro in cui il ritardo rimane inferiore nelle ore di punta e non di punta del carico di lavoro, le repliche di lettura possono fungere da alternativa a disponibilità elevata. In caso contrario, le repliche di lettura sono destinate a una vera e propria scalabilità in lettura per i carichi di lavoro pesanti pronti e per gli scenari di ripristino di emergenza

Nella tabella seguente vengono confrontati RTO e RPO in uno scenario di **carico di lavoro tipico** :

| **Capacità** | **Basic** | **Utilizzo generico** | **Ottimizzate per la memoria** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Ripristino temporizzato dal backup | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione |
| Ripristino geografico dai backup con replica geografica | Non supportato | RTO-varia <br/>RPO < 1 ora | RTO-varia <br/>RPO < 1 ora |
| Repliche in lettura | RTO minuti * <br/>RPO < 5 min * | RTO minuti * <br/>RPO < 5 min *| RTO minuti * <br/>RPO < 5 min *|

 \* RTO e RPO **possono essere molto più elevati** in alcuni casi, a seconda dei diversi fattori, tra cui la latenza tra siti, la quantità di dati da trasmettere e un carico di lavoro di scrittura database primario. 

## <a name="recover-a-server-after-a-user-or-application-error"></a>Ripristinare un server in seguito a errore di un'applicazione o di un utente

È possibile usare i backup del servizio per ripristinare un server da svariati eventi che possono causare interruzioni del funzionamento. Un utente può accidentalmente eliminare alcuni dati, una tabella importante o addirittura un intero database. Un'applicazione potrebbe sovrascrivere accidentalmente dei dati con dati errati a causa di un difetto dell'applicazione e così via.

È possibile eseguire un **ripristino temporizzato** per creare una copia del server a un momento valido noto. Questo punto nel tempo deve essere compreso nel periodo di conservazione di backup configurato per il server. Dopo il ripristino dei dati nel nuovo server, è possibile sostituire il server originale con il server appena ripristinato o copiare i dati necessari dal server ripristinato nel server originale.

> [!IMPORTANT]
> **Non è possibile** ripristinare i server eliminati. Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Usare [blocco risorse di Azure](../azure-resource-manager/management/lock-resources.md) per evitare l'eliminazione accidentale del server.

## <a name="recover-from-an-azure-data-center-outage"></a>Ripristino da un'interruzione del data center di Azure

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti, ma anche ore.

Una delle opzioni è attendere che il server torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono permettersi di avere il server offline per un certo periodo, ad esempio gli ambienti di sviluppo. Quando si verifica un'interruzione di un data center, non si sa quanto tempo può durare l'interruzione, quindi questa opzione funziona solo se non è necessario il server per un periodo di tempo.

## <a name="geo-restore"></a>Ripristino geografico

La funzionalità di ripristino geografico ripristina il server usando i backup con ridondanza geografica. I backup sono ospitati nell' [area abbinata](../best-practices-availability-paired-regions.md)del server. È possibile eseguire il ripristino da questi backup in qualsiasi altra area. Il ripristino geografico crea un nuovo server con i dati dei backup. Per altre informazioni sul ripristino geografico, vedere l' [articolo Concetti relativi a backup e ripristino](concepts-backup.md).

> [!IMPORTANT]
> Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica. Per passare da backup con ridondanza locale a backup con ridondanza geografica per un server esistente, è necessario eseguire un dump del server esistente usando pg_dump e quindi ripristinarlo in un nuovo server configurato per i backup con ridondanza geografica.

## <a name="cross-region-read-replicas"></a>Repliche Read tra aree
È possibile usare le repliche di lettura tra aree per migliorare la continuità aziendale e la pianificazione del ripristino di emergenza. Le repliche di lettura vengono aggiornate in modo asincrono usando la tecnologia di replica fisica di PostgreSQL e possono ritardare il database primario. Altre informazioni sulle repliche di lettura, le aree disponibili e su come eseguire il failover dall' [articolo leggere i concetti relativi alle repliche](concepts-read-replicas.md). 

## <a name="faq"></a>Domande frequenti
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Dove database di Azure per PostgreSQL archivia i dati dei clienti?
Per impostazione predefinita, database di Azure per PostgreSQL non sposta o archivia i dati dei clienti dall'area in cui è distribuito. Tuttavia, i clienti possono scegliere di abilitare i [backup con ridondanza geografica](concepts-backup.md#backup-redundancy-options) o creare una [replica di lettura tra aree](concepts-read-replicas.md#cross-region-replication) per archiviare i dati in un'altra area.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [backup automatizzati in Database di Azure per PostgreSQL](concepts-backup.md). 
- Informazioni su come effettuare il ripristino usando il [portale di Azure](howto-restore-server-portal.md) o l'[interfaccia della riga di comando di Azure](howto-restore-server-cli.md).
- Altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).

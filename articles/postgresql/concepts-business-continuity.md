---
title: Continuità aziendale - Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive la continuità aziendale (ripristino temporizzato, interruzione del data center, ripristino geografico) quando si usa il database di Azure per PostgreSQL.This article describes business continuity (point in time restore, data center outage, geo-restore) when using Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: afa03399933bdc8bd8ff869125955cfd9e0abecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981922"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Panoramica della continuità aziendale con Database di Azure per PostgreSQL - Server singoloOverview of business continuity with Azure Database for PostgreSQL - Single Server

Questa panoramica descrive le funzionalità offerte da Database di Azure per PostgreSQL per la continuità aziendale e il ripristino di emergenza. Informazioni sulle opzioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità che è possibile usare per assicurare la continuità aziendale

Database di Azure per PostgreSQL offre funzionalità di continuità aziendale che includono i backup automatizzati e la possibilità per gli utenti di avviare il ripristino geografico. Ogni funzionalità presenta caratteristiche diverse in termini di tempo di recupero stimato (ERT) e di potenziale perdita di dati. Dopo aver compreso le opzioni disponibili, è possibile scegliere una di esse e usarle in modo combinato per i diversi scenari. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Si tratta dell'obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione riesce a tollerare durante il ripristino dopo l'evento di arresto improvviso, ovvero l'obiettivo del punto di recupero (RPO).

La tabella seguente mette a confronto i valori ERT e RPO per le funzionalità disponibili:

| **Funzionalità** | **Base** | **Scopo generale** | **Ottimizzate per la memoria** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Ripristino temporizzato dal backup | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione |
| Ripristino geografico dai backup con replica geografica | Non supportate | ERT < 12 ore<br/>RPO < 1 ora | ERT < 12 ore<br/>RPO < 1 ora |

> [!IMPORTANT]
> **Non è possibile** ripristinare i server eliminati. Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Usare [il blocco delle risorse](../azure-resource-manager/management/lock-resources.md) di Azure per impedire l'eliminazione accidentale del server.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Ripristinare un server in seguito a errore di un'applicazione o di un utente

È possibile usare i backup del servizio per ripristinare un server da svariati eventi che possono causare interruzioni del funzionamento. Un utente può accidentalmente eliminare alcuni dati, una tabella importante o addirittura un intero database. Un'applicazione potrebbe sovrascrivere accidentalmente dei dati con dati errati a causa di un difetto dell'applicazione e così via.

È possibile eseguire un **ripristino temporizzato** per creare una copia del server in un punto valido noto nel tempo. Questo punto nel tempo deve essere compreso nel periodo di conservazione di backup configurato per il server. Dopo il ripristino dei dati nel nuovo server, è possibile sostituire il server originale con il server appena ripristinato o copiare i dati necessari dal server ripristinato nel server originale.

## <a name="recover-from-an-azure-data-center-outage"></a>Ripristinare un'interruzione del data center di AzureRecover from an Azure data center outage

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti, ma anche ore.

Una delle opzioni è attendere che il server torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono permettersi di avere il server offline per un certo periodo, ad esempio gli ambienti di sviluppo. Quando un data center presenta un'interruzione, non si sa quanto potrebbe durare l'interruzione, pertanto questa opzione funziona solo se il server non è necessario per un certo periodo di tempo.

## <a name="geo-restore"></a>Ripristino geografico

La funzionalità di ripristino geografico consente di ripristinare il server utilizzando backup con ridondanza geografica. I backup sono ospitati [nell'area associata](../best-practices-availability-paired-regions.md)del server. È possibile eseguire il ripristino da questi backup in qualsiasi altra area. Il ripristino geografico crea un nuovo server con i dati dei backup. Ulteriori informazioni sul ripristino geografico sono nell'articolo sui concetti di [backup e ripristino.](concepts-backup.md)

> [!IMPORTANT]
> Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica. Per passare da backup con ridondanza locale a backup con ridondanza geografica per un server esistente, è necessario eseguire un dump del server esistente usando pg_dump e quindi ripristinarlo in un nuovo server configurato per i backup con ridondanza geografica.

## <a name="cross-region-read-replicas"></a>Repliche di lettura tra areeCross-region read replicas
È possibile usare le repliche di lettura tra aree per migliorare la continuità aziendale e la pianificazione del ripristino di emergenza. Le repliche di lettura vengono aggiornate in modo asincrono utilizzando la tecnologia di replica fisica di PostgreSQL.Read replicas are updated asynchronously using PostgreSQL's physical replication technology. Altre informazioni sulle repliche di lettura, sulle aree disponibili e su come eseguire il failover sono disponibili nell'articolo sui concetti relativi alle repliche di [lettura.](concepts-read-replicas.md) 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [backup automatizzati in Database di Azure per PostgreSQL](concepts-backup.md). 
- Informazioni su come effettuare il ripristino usando il [portale di Azure](howto-restore-server-portal.md) o l'[interfaccia della riga di comando di Azure](howto-restore-server-cli.md).
- Altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
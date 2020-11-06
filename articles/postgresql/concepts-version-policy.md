---
title: Criteri di controllo delle versioni-database di Azure per PostgreSQL-server singolo e server flessibile (anteprima)
description: Descrive i criteri relativi alle versioni principale e secondaria di Postgres nel database di Azure per PostgreSQL-server singolo.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f325a43895e1e9d73b11c06662851d7654d31ddb
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331822"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Criteri di controllo delle versioni di database di Azure per PostgreSQL

Questa pagina descrive i criteri di controllo delle versioni di database di Azure per PostgreSQL ed è applicabile alle modalità di distribuzione di database di Azure per PostgreSQL-server singolo e database di Azure per PostgreSQL-flessibili (anteprima).

## <a name="supported--postgresql-versions"></a>Versioni supportate di PostgreSQL

Database di Azure per PostgreSQL supporta le versioni di database seguenti.

| Versione | Server unico | Server flessibile (anteprima) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9,6 | X |  |
| PostgreSQL 9,5 | X |  |

## <a name="major-version-support"></a>Supporto della versione principale
Ogni versione principale di PostgreSQL sarà supportata da database di Azure per PostgreSQL dalla data in cui Azure inizia a supportare la versione fino a quando la versione non viene ritirata dalla community di PostgreSQL, come specificato nei [criteri di controllo delle versioni della community PostgreSQL](https://www.postgresql.org/support/versioning/).

## <a name="minor-version-support"></a>Supporto della versione secondaria
Database di Azure per PostgreSQL esegue automaticamente aggiornamenti della versione secondaria per la versione di PostgreSQL preferita di Azure nell'ambito della manutenzione periodica. 

## <a name="major-version-retirement-policy"></a>Criteri di ritiro della versione principale
La tabella seguente fornisce i dettagli relativi al ritiro per le versioni principali di PostgreSQL. Le date seguono i [criteri di controllo delle versioni della community PostgreSQL](https://www.postgresql.org/support/versioning/).

| Versione | Novità | Data di inizio del supporto di Azure | Data di ritiro|
| ----- | ----- | ------ | ----- |
| PostgreSQL 9,5| [Funzionalità](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 18 aprile 2018    | 11 febbraio 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Funzionalità](https://wiki.postgresql.org/wiki/NewIn96) | 18 aprile 2018  | 11 novembre 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Funzionalità](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4 giugno 2018  | 10 novembre 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Funzionalità](https://www.postgresql.org/docs/11/release-11.html) | 24 luglio 2019  | 9 novembre, 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Funzionalità](https://www.postgresql.org/docs/12/release-12.html) | 22 settembre 2020  | 14 novembre 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Versioni del motore PostgreSQL ritirate non supportate nel database di Azure per PostgreSQL

Dopo la data di ritiro per ogni versione del database PostgreSQL, se si continua a eseguire la versione ritirata, tenere presenti le restrizioni seguenti:
- Poiché la community non rilascerà ulteriori correzioni di bug o correzioni di sicurezza, il database di Azure per PostgreSQL non patcherà il motore di database ritirato per eventuali bug o problemi di sicurezza o per adottare misure di sicurezza in relazione al motore di database ritirato. È possibile che si verifichino vulnerabilità della sicurezza o altri problemi di conseguenza. Tuttavia, Azure continuerà a eseguire periodicamente interventi di manutenzione e applicazione di patch per l'host, il sistema operativo, i contenitori e tutti gli altri componenti correlati ai servizi.
- Se è possibile che si verifichi un problema di supporto correlato al database PostgreSQL, è possibile che l'utente non sia in grado di fornire supporto. In questi casi, sarà necessario aggiornare il database in modo da fornire supporto.
- Non sarà possibile creare nuovi server di database per la versione ritirata. Sarà tuttavia possibile eseguire recuperi temporizzati e creare repliche di lettura per i server esistenti.
- Le nuove funzionalità del servizio sviluppate da database di Azure per PostgreSQL possono essere disponibili solo per le versioni del server di database supportate.
- I contratti di servizio con tempo di attività verranno applicati esclusivamente ai problemi relativi al servizio del database di Azure per PostgreSQL e non ai tempi di inattività causati da bug correlati al motore di database.  
- Nell'evento estremo di una grave minaccia per il servizio causato dalla vulnerabilità del motore di database PostgreSQL identificata nella versione del database ritirata, Azure può scegliere di arrestare il server di database per proteggere il servizio. In tal caso, si riceverà una notifica per aggiornare il server prima di portare online il server.

## <a name="postgresql-version-syntax"></a>Sintassi della versione PostgreSQL
Prima di PostgreSQL versione 10, i [criteri di controllo delle versioni di PostgreSQL](https://www.postgresql.org/support/versioning/) consideravano un aggiornamento della _versione principale_ come un aumento del primo _o_ del secondo numero. Ad esempio, 9,5 a 9,6 è stato considerato un aggiornamento della versione _principale_ . A partire dalla versione 10, solo una modifica nel primo numero viene considerata un aggiornamento della versione principale. 10,0 a 10,1, ad esempio, è un aggiornamento della versione _secondaria_ . La versione da 10 a 11 è un aggiornamento della versione _principale_ .

## <a name="next-steps"></a>Passaggi successivi
- Vedere database di Azure per PostgreSQL: versioni a server singolo [supportate](./concepts-supported-versions.md)
- Vedere le [versioni supportate](flexible-server/concepts-supported-versions.md) di database di Azure per PostgreSQL-server flessibili (anteprima)
- Per informazioni su come eseguire gli aggiornamenti della versione principale, vedere la documentazione relativa agli [aggiornamenti della versione principale](how-to-upgrade-using-dump-and-restore.md) .
- Per informazioni sulle estensioni PostgreSQL supportate, vedere [il documento sulle estensioni](concepts-extensions.md).

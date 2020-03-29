---
title: Versioni supportate - Database di Azure per PostgreSQL - Server singolo
description: Descrive le versioni principali e secondarie di Postgres supportate nel database di Azure per PostgreSQL - Server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792229"
---
# <a name="supported-postgresql-major-versions"></a>Versioni principali supportate di PostgreSQL
Microsoft ha lo scopo di supportare le versioni n-2 del motore PostgreSQL nel database di Azure per PostgreSQL - Server singolo. Le versioni sono la versione principale attuale in Azure (n) e le due versioni principali precedenti (-2).

Il database di Azure per PostgreSQL supporta attualmente le versioni principali seguenti:Azure Database for PostgreSQL currently supports the following major versions:

## <a name="postgresql-version-11"></a>PostgreSQL versione 11
L'attuale versione secondaria è 11.5. Fare riferimento alla [documentazione PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-5.html) per ulteriori informazioni sui miglioramenti e le correzioni in questa versione secondaria.

## <a name="postgresql-version-10"></a>PostgreSQL versione 10
La versione secondaria corrente è 10.10. Fare riferimento alla [documentazione PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-10.html) per ulteriori informazioni sui miglioramenti e le correzioni in questa versione secondaria.

## <a name="postgresql-version-96"></a>PostgreSQL versione 9.6
L'attuale versione secondaria è 9.6.15. Fare riferimento alla [documentazione PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) per ulteriori informazioni sui miglioramenti e le correzioni in questa versione secondaria.

## <a name="postgresql-version-95"></a>PostgreSQL versione 9.5
L'attuale versione secondaria è 9.5.19. Fare riferimento alla [documentazione PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) per informazioni sui miglioramenti e le correzioni in questa versione secondaria.

## <a name="managing-upgrades"></a>Gestione degli aggiornamenti
Il progetto PostgreSQL rilascia regolarmente rilasci minori per correggere i bug segnalati. Il database di Azure per PostgreSQL applica automaticamente patch ai server con versioni secondarie durante le distribuzioni mensili del servizio. 

L'aggiornamento automatico della versione principale non è supportato. Ad esempio, non è disponibile alcun aggiornamento automatico da PostgreSQL 9.5 a PostgreSQL 9.6. Per eseguire l'aggiornamento alla versione principale successiva, creare un [database dump e ripristinarlo](./howto-migrate-using-dump-and-restore.md) in un server creato con la nuova versione del motore.

### <a name="version-syntax"></a>Sintassi della versione
Prima di PostgreSQL versione 10, i criteri di [controllo delle versioni PostgreSQL](https://www.postgresql.org/support/versioning/) consideravano l'aggiornamento di una _versione principale_ come un aumento nel primo _o_ nel secondo numero. Ad esempio, da 9,5 a 9,6 è stato considerato un aggiornamento della versione _principale._ A partire dalla versione 10, solo una modifica nel primo numero viene considerata un aggiornamento della versione principale. Ad esempio, da 10.0 a 10.1 è un aggiornamento di versione _secondaria._ La versione 10-11 è un aggiornamento della versione _principale._

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle estensioni PostgreSQL supportate, vedere [il documento sulle estensioni](concepts-extensions.md).

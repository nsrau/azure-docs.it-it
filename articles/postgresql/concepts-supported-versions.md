---
title: 'Versioni supportate: database di Azure per PostgreSQL-server singolo'
description: Descrive le versioni principale e secondaria di Postgres supportate nel database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/17/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 61d9712026daab63a002793adf2e17b9db943f94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976898"
---
# <a name="supported-postgresql-major-versions"></a>Versioni principali di PostgreSQL supportate
Microsoft mira a supportare le versioni n-2 del motore PostgreSQL nel database di Azure per PostgreSQL: singolo server. Le versioni sono la versione principale attuale in Azure (n) e le due versioni principali precedenti (-2).

Database di Azure per PostgreSQL supporta attualmente le versioni principali seguenti:

## <a name="postgresql-version-11"></a>PostgreSQL versione 11
La versione secondaria corrente è 11,6. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) .

## <a name="postgresql-version-10"></a>PostgreSQL versione 10
La versione secondaria corrente è 10,11. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) .

## <a name="postgresql-version-96"></a>PostgreSQL versione 9,6
La versione secondaria corrente è 9.6.16. Per ulteriori informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) .

## <a name="postgresql-version-95"></a>PostgreSQL versione 9,5
La versione secondaria corrente è 9.5.20. Per informazioni sui miglioramenti e sulle correzioni in questa versione secondaria, vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) .

## <a name="managing-upgrades"></a>Gestione degli aggiornamenti
Il progetto PostgreSQL rilascia periodicamente versioni secondarie per correggere i bug segnalati. Database di Azure per PostgreSQL applica automaticamente patch ai server con versioni minori durante le distribuzioni mensili del servizio. 

L'aggiornamento automatico della versione principale non è supportato. Ad esempio, non è disponibile alcun aggiornamento automatico da PostgreSQL 9.5 a PostgreSQL 9.6. Per eseguire l'aggiornamento alla versione principale successiva, creare un [database dump e ripristinarlo](./howto-migrate-using-dump-and-restore.md) in un server creato con la nuova versione del motore.

### <a name="version-syntax"></a>Sintassi della versione
Prima di PostgreSQL versione 10, i [criteri di controllo delle versioni di PostgreSQL](https://www.postgresql.org/support/versioning/) consideravano un aggiornamento della _versione principale_ come un aumento del primo _o_ del secondo numero. Ad esempio, 9,5 a 9,6 è stato considerato un aggiornamento della versione _principale_ . A partire dalla versione 10, solo una modifica nel primo numero viene considerata un aggiornamento della versione principale. 10,0 a 10,1, ad esempio, è un aggiornamento della versione _secondaria_ . La versione da 10 a 11 è un aggiornamento della versione _principale_ .

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle estensioni PostgreSQL supportate, vedere [il documento sulle estensioni](concepts-extensions.md).

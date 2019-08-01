---
title: Versioni supportate nel database di Azure per PostgreSQL-server singolo
description: Descrive le versioni supportate nel database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551357"
---
# <a name="supported-postgresql-database-versions"></a>Versione supportate del database PostgreSQL
Microsoft mira a supportare le versioni n-2 del motore PostgreSQL nel database di Azure per PostgreSQL: singolo server. Le versioni sono la versione principale attuale in Azure (n) e le due versioni principali precedenti (-2).

Al momento il Database di Azure per PostgreSQL supporta le versioni seguenti:

## <a name="postgresql-version-112"></a>PostgreSQL versione 11,2
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

## <a name="postgresql-version-107"></a>PostgreSQL versione 10,7
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

## <a name="postgresql-version-9612"></a>Versione di PostgreSQL 9.6.12
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

## <a name="postgresql-version-9516"></a>Versione di PostgreSQL 9.5.16
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Database di Azure per PostgreSQL gestisce automaticamente le patch per le versioni secondarie. L'aggiornamento di versione principale non è attualmente supportato. Ad esempio, l'aggiornamento da PostgreSQL 9.5 a PostgreSQL 9.6 non è supportato. Se si vuole eseguire l'aggiornamento alla versione principale successiva, creare un database [dump e ripristinarlo](./howto-migrate-using-dump-and-restore.md) in un server creato con la nuova versione del motore.

> Si noti che prima di PostgreSQL versione 10, [i criteri di controllo delle versioni di PostgreSQL](https://www.postgresql.org/support/versioning/) consideravano un aggiornamento della _versione principale_ come un aumento del primo _o_ del secondo numero (ad esempio, 9,5 a 9,6 era considerato un aggiornamento della versione _principale_ ).
> A partire dalla versione 10, solo una modifica nel primo numero viene considerata un aggiornamento della versione principale (ad esempio, 10,0 a 10,1 è un aggiornamento della versione _secondaria_ e da 10 a 11 è un aggiornamento della versione _principale_ ).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sul supporto delle diverse estensioni di PostgreSQL, vedere [Estensioni di PostgreSQL](concepts-extensions.md).

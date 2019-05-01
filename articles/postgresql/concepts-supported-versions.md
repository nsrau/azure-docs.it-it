---
title: Versioni supportate in Database di Azure per PostgreSQL
description: In questo articolo vengono descritte le versioni supportate nel Database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 16064d662c5b101e30f8d2fbb64b39db0848f49f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702316"
---
# <a name="supported-postgresql-database-versions"></a>Versione supportate del database PostgreSQL
Microsoft punta a supportare n-2 versioni del motore PostgreSQL nel servizio Database di Azure per PostgreSQL. Le versioni sono la versione principale attuale in Azure (n) e le due versioni principali precedenti (-2).

Al momento il Database di Azure per PostgreSQL supporta le versioni seguenti:

## <a name="postgresql-version-107"></a>PostgreSQL versione 10.7
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

## <a name="postgresql-version-9612"></a>PostgreSQL versione 9.6.12
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

## <a name="postgresql-version-9516"></a>PostgreSQL versione 9.5.16
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Database di Azure per PostgreSQL gestisce automaticamente le patch per le versioni secondarie. L'aggiornamento di versione principale non è attualmente supportato. Ad esempio, l'aggiornamento da PostgreSQL 9.5 a PostgreSQL 9.6 non è supportato. Se si vuole eseguire l'aggiornamento alla versione principale successiva, creare un database [dump e ripristinarlo](./howto-migrate-using-dump-and-restore.md) in un server creato con la nuova versione del motore.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sul supporto delle diverse estensioni di PostgreSQL, vedere [Estensioni di PostgreSQL](concepts-extensions.md).

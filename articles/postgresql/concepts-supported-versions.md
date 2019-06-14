---
title: Versioni supportate nel Database di Azure per PostgreSQL - Server singolo
description: Descrive le versioni supportate nel Database di Azure per PostgreSQL - singolo Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 0fc4eb90d9bed3877b4618fd839d9e8bf1608718
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063730"
---
# <a name="supported-postgresql-database-versions"></a>Versione supportate del database PostgreSQL
Microsoft intende supportare n-2 versioni del motore PostgreSQL nel Database di Azure per PostgreSQL - singolo Server. Le versioni sono la versione principale attuale in Azure (n) e le due versioni principali precedenti (-2).

Al momento il Database di Azure per PostgreSQL supporta le versioni seguenti:

## <a name="postgresql-version-112"></a>PostgreSQL versione 11.2
Vedere la [documentazione di PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) per ottenere altre informazioni sulle correzioni e sui miglioramenti apportati in questa versione secondaria.

>[!NOTE]
> PostgreSQL versione 11 è disponibile in anteprima. Supporto per la creazione nel portale di Azure viene implementato e potrebbe non essere ancora disponibile nella propria area. È possibile usare la [CLI Azure](quickstart-create-server-database-azure-cli.md) per creare un server Postgres 11 in qualsiasi area. Ad esempio: `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11`.

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

---
title: Versioni supportate in Database di Azure per PostgreSQL
description: In questo articolo vengono descritte le versioni supportate nel Database di Azure per PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 2065631922d25deaa94601484da9b8de3fd62b22
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="supported-postgresql-database-versions"></a>Versioni supportate del Database PostgreSQL
Microsoft intende supportare n-2 versioni del motore PostgreSQL nel servizio Database di Azure per PostgreSQL, vale a dire la versione principale attuale (n) e le due versioni principali precedenti (-2).

Al momento il Database di Azure per PostgreSQL supporta le versioni seguenti:

## <a name="postgresql-version-962"></a>PostgreSQL versione 9.6.2
Consultare la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-2.html) per avere altre informazioni sui miglioramenti e sulle correzioni presenti in PostgreSQL 9.6.2.

## <a name="postgresql-version-957"></a>PostgreSQL versione 9.5.7
Consultare la [documentazione di PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-7.html) per le informazioni sui miglioramenti e sulle correzioni presenti in PostgreSQL 9.5.7.

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti
Il Database di Azure per PostgreSQL gestisce automaticamente l'applicazione di patch per gli aggiornamenti di versione secondari. L'aggiornamento di versione principale non è attualmente supportato in anteprima pubblica. Ad esempio, l'aggiornamento da PostgreSQL 9.5 a PostgreSQL 9.6 non è supportato.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sul supporto delle differenti estensioni di PostgreSQL, vedere [Estensioni di PostgreSQL](concepts-extensions.md)

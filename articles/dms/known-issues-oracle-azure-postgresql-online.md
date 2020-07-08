---
title: 'Problemi noti: Eseguire la migrazione da Oracle a Database di Azure per PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Informazioni sui problemi noti e sulle limitazioni per le migrazioni online da Oracle a Database di Azure per PostgreSQL (server singolo) con il Servizio Migrazione del database di Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 05/20/2020
ms.openlocfilehash: 2cf8ff446fe3441fc039ef3c2afef6308224666f
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701212"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Problemi noti/limitazioni per le migrazioni online da Oracle a Database di Azure per PostgreSQL (server singolo)

Le sezioni seguenti illustrano i problemi noti e le limitazioni inerenti alle migrazioni online da Oracle a Database di Azure per PostgreSQL (server singolo) con il Servizio Migrazione del database di Azure.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versioni di Oracle supportate come database di origine

Il Servizio Migrazione del database di Azure supporta la connessione a:

- Oracle versione 10g, 11g e 12c.
- Oracle Enterprise, Standard, Express e Personal Edition.

Il Servizio Migrazione del database di Azure non supporta la connessione a database contenitore multi-tenant.

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versioni di PostgreSQL supportate come database di destinazione

Il Servizio Migrazione del database di Azure supporta le migrazioni a Database di Azure per PostgreSQL (server singolo) versioni 9.5, 9.6, 10 e 11. Vedere l'articolo [Versione supportate del database PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) per informazioni aggiornate sulle versioni supportate da Database di Azure per PostgreSQL (server singolo).

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

**Non** è possibile eseguire la migrazione dei datatype seguenti:

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Tabelle nidificate
- Tipi di dati definiti dall'utente
- Note
- Colonne virtuali
- Viste materializzate basate sulla colonna ROWID

Le colonne BLOB/CLOB vuote, inoltre, vengono mappate a NULL nella destinazione.

## <a name="lob-limitations"></a>Limitazioni relative ai tipi di dati LOB

- Quando è abilitata la modalità LOB limitata, i LOB vuoti vengono replicati nell'origine Oracle come valori NULL.
- Non sono supportati nomi di oggetto lunghi (oltre 30 byte).
- I dati nelle colonne LONG e LONG RAW non possono superare 64K. In caso contrario, i dati verranno troncati.
- Solo in Oracle 12 non sono supportate modifiche alle colonne LOB (migrate).
- Non sono supportate le colonne da UPDATEs a XMLTYPE e le colonne LOB (migrate).

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

- L'utente deve disporre dei privilegi di amministratore di database sul server Oracle.
- Non viene eseguita la migrazione di eventuali modifiche ai dati risultanti da operazioni di partizione/sub-partizione (ADD, DROP, EXCHANGE e TRUNCATE), da cui potrebbero essere generati gli errori seguenti:
  - Per le operazioni ADD, eventuali aggiornamenti o eliminazioni sui dati aggiunti possono restituire l'avviso "0 righe interessate".
  - Per le operazioni DROP e TRUNCATE, i nuovi inserimenti possono generare errori di "duplicati".
  - Per le operazioni EXCHANGE, è possibile che venga generato sia l'avviso "0 righe interessate" sia l'errore sui "duplicati".
- Non possono essere replicate tabelle il cui nome contiene un apostrofo.

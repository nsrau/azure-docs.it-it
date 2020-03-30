---
title: 'Problemi noti: Eseguire la migrazione da Oracle al database di Azure per PostgreSQLKnown issues: Migrate from Oracle to Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Informazioni sui problemi noti e sulle limitazioni della migrazione con le migrazioni online da Oracle al database di Azure per il server PostgreSQL-Single usando il servizio Migrazione del database di Azure.Learn about known issues and migration limitations with online migrations from Oracle to Azure Database for PostgreSQL-Single server using the Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235250"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Problemi noti/limitazioni di migrazione con le migrazioni online da Oracle ad Azure DB per PostgreSQL-Single Server

I problemi noti e le limitazioni associate alle migrazioni online da Oracle al database di Azure per server PostgreSQL-Single sono descritti nelle sezioni seguenti.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versioni Oracle supportate come database di origine

Il servizio Migrazione del database di Azure supporta la connessione a:Azure Database Migration Service supports connecting to:

- Oracle versione 10g, 11g e 12c.
- Oracle Enterprise, Standard, Express e Personal Edition.

Il servizio Migrazione del database di Azure non supporta la connessione ai database contenitore multi-tenant (CDB).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versioni PostgreSQL supportate come database di destinazionePostgreSQL versions supported as a target database

Il servizio di migrazione del database di Azure supporta le migrazioni al database di Azure per PostgreSQL-Single Server versione 9.5, 9.6, 10 e 11.Azure Database Migration Service supports migrations to Azure Database for PostgreSQL-Single server version 9.5, 9.6, 10 and 11. Vedere l'articolo Versioni di [database PostgreSQL supportate](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) per informazioni aggiornate sul supporto delle versioni nel database di Azure per postgreSQL-singolo server.

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

Non verrà eseguita la migrazione dei tipi di dati seguenti:The following datatypes **won't** be migrated:

- BFILE
- ROWID
- REF
- UROWID
- DATI ANYDATA
- SDO_GEOMETRY
- Tabelle nidificate
- Tipi di dati definiti dall'utente
- Note
- Colonne virtuali
- Visualizzazioni materializzate basate sulla colonna ROWID

Inoltre, le colonne BLOB/CLOB vuote vengono mappate a NULL nella destinazione.

## <a name="lob-limitations"></a>Limitazioni relative ai tipi di dati LOB

- Quando è attivata la modalità LOB di dimensioni limitate, i LOB vuoti nell'origine Oracle vengono replicati come valori NULL.
- I nomi di oggetti lunghi (oltre 30 byte) non sono supportati.
- I dati nelle colonne LONG e LONG RAW non possono superare 64k. Tutti i dati oltre a 64k verranno troncati.
- Solo in Oracle 12, tutte le modifiche alle colonne LOB non sono supportate (migrate).
- Gli UPDAT e le colonne XMLTYPE e LOB non sono supportati (migrati).

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

- I clienti devono utilizzare SYSDBA per connettersi a Oracle.
- Le modifiche ai dati risultanti dalle operazioni di partizione/sottopartizione (ADD, DROP, EXCHANGE e TRUNCATE) non verranno migrate e potrebbero causare i seguenti errori:
  - Per le operazioni ADD, gli aggiornamenti e le eliminazioni sui dati aggiunti possono restituire un avviso "0 righe interessate".
  - Per le operazioni DROP e TRUNCATE, i nuovi inserimenti possono generare errori di "duplicati".
  - Per le operazioni di EXCHANGE, possono verificarsi entrambi gli errori di avviso "0 righe interessate" e "duplicati".
- Le tabelle i cui nomi contengono apostrofi non possono essere replicate.

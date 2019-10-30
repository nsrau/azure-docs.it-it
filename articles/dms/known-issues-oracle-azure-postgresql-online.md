---
title: Articolo sui problemi noti/limitazioni della migrazione con migrazioni online da Oracle al database di Azure per PostgreSQL-server singolo | Microsoft Docs
description: Informazioni sui problemi noti/limitazioni della migrazione con migrazioni online da Oracle al database di Azure per PostgreSQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/03/2019
ms.openlocfilehash: cc7e510d9a6db93edc211799eda8df79116236a7
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053643"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Problemi noti/limitazioni della migrazione con migrazioni online da Oracle al database di Azure per PostgreSQL-server singolo

Le sezioni seguenti illustrano i problemi noti e le limitazioni associate alle migrazioni online da Oracle al database di Azure per PostgreSQL: un server singolo.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versioni di Oracle supportate come database di origine

Il servizio migrazione del database di Azure supporta la connessione a:

- Oracle versione 10g, 11C e 12C.
- Oracle Enterprise, standard, Express e Personal Edition.

Il servizio migrazione del database di Azure non supporta la connessione a database contenitore multi-tenant (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versioni di PostgreSQL supportate come database di destinazione

Il servizio migrazione del database di Azure supporta le migrazioni al database di Azure per PostgreSQL: versione a server singolo 9,5, 9,6, 10 e 11. Per informazioni aggiornate sul supporto della versione nel database di Azure per PostgreSQL-server singolo, vedere l'articolo [versioni del database PostgreSQL supportate](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) .

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

**Non** verrà eseguita la migrazione dei seguenti tipi di oggetto:

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

Inoltre, le colonne BLOB/CLOB vuote sono mappate a NULL nella destinazione.

## <a name="lob-limitations"></a>Limitazioni relative ai tipi di dati LOB

- Quando è abilitata la modalità LOB a dimensione limitata, LOB vuoti nell'origine Oracle vengono replicati come valori NULL.
- I nomi di oggetti lunghi (oltre 30 byte) non sono supportati.
- I dati nella colonna non ELABORAta LONG e LONG non possono superare 64K. Tutti i dati oltre 64K verranno troncati.
- Solo in Oracle 12, le modifiche apportate alle colonne LOB non sono supportate (migrate).
- Gli aggiornamenti alle colonne XmlType e LOB non sono supportati (migrati).

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

- I clienti devono usare SYSDBA per connettersi a Oracle.
- Non verrà eseguita la migrazione delle modifiche dei dati risultanti da operazioni di partizione/Sottopartizione (ADD, DROP, EXCHANGE e Truncate) e potrebbero verificarsi i seguenti errori:
  - Per aggiungere operazioni, gli aggiornamenti e le eliminazioni sui dati aggiunti possono restituire un avviso "0 righe interessate".
  - Per le operazioni DROP e TRUNCATE, i nuovi inserimenti possono generare errori di "duplicati".
  - Per le operazioni di EXCHANGE è possibile che si verifichino errori di avviso "0 righe interessate" e "duplicati".
- Le tabelle i cui nomi contengono apostrofi non possono essere replicate.

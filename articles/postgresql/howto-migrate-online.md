---
title: "Migrazione di tempi di inattività minimi per Database di Azure per PostgreSQL | Documenti Microsoft"
description: "In questo articolo viene descritto come eseguire una migrazione di tempi di inattività minimi per l'estrazione di un database PostgreSQL in un file di dump, ripristinare il database PostgreSQL da un file di archivio creato da pg_dump nel Database di Azure per PostgreSQL e configurazione di caricamento iniziale e sincronizzazione dati continua dal database di origine al database di destinazione tramite replica di Attunity per Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrazione di tempi di inattività minimi per Database di Azure per PostgreSQL
È possibile eseguire la migrazione del database PostgreSQL esistente al Database di Azure per PostgreSQL tramite replica di Attunity per Microsoft Migrations, un'offerta di CO-sponsorizzata, comune di Attunity e Microsoft fornito con il servizio di migrazione di Database di Azure senza alcun costo aggiuntivo per i clienti Microsoft. Replica di Attunity per Microsoft Migrations consente inoltre di periodi di inattività minimo per la migrazione di database e il database di origine continua a essere operativi durante il processo di migrazione.

Replica di Attunity è uno strumento di replica di dati che consente la sincronizzazione dei dati tra un'ampia gamma di origini e destinazioni, propagazione lo script di creazione dello schema e i dati associati a ogni tabella di database. Replica di Attunity non si propaga gli eventuali altri elementi (ad esempio, SP, trigger, funzioni, e così via) o convert, ad esempio, un codice PL/SQL ospitato in tali elementi, a T-SQL.

> [!NOTE]
> Durante la replica Attunity supporta una vasta gamma di scenari di migrazione, replica di Attunity per Microsoft Migrations è incentrata sul supporto per uno specifico sottoinsieme di coppie di origine/destinazione.

Include una panoramica del processo di esecuzione di una migrazione di tempi di inattività minimi:

1. **La migrazione dello schema di origine PostgreSQL** a un Database di Azure per database PostgreSQL mediante il [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) comando con il parametro - n e quindi utilizzando il [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) comando.

2. **Impostazione di caricamento iniziale e la sincronizzazione dati continua dal database di origine al database di destinazione** tramite replica di Attunity per Microsoft Migrations. In questo modo riduce al minimo il tempo che il database di origine deve essere impostato come di sola lettura durante la preparazione passare alle applicazioni di database PostgreSQL di destinazione in Azure.

Per ulteriori informazioni su questa replica di Attunity per offerta Migrations Microsoft, è possibile visualizzare le risorse seguenti:
 - La replica di Attunity per Microsoft Migrations [pagina web](https://aka.ms/attunity-replicate).
 - [Scaricare](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Replicate di Attunity per le migrazioni da Microsoft.
 - La replica di Attunity [Community](https://microsoft.attunity.com/), con una Guida introduttiva, esercitazioni e supporto.
 - Per istruzioni dettagliate sull'utilizzo di Attunity per la migrazione da PostgreSQL al Database di Azure per PostgreSQL, consultare il [Guida alla migrazione di Database](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).
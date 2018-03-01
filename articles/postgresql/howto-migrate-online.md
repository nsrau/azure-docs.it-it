---
title: "Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL"
description: "Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo estraendo un database PostgreSQL in un file dump, ripristinando il database PostgreSQL da un file di archivio creato da pg_dump in Database di Azure per PostgreSQL e configurando il carico iniziale e la continua sincronizzazione dei dati dal database di origine al database di destinazione tramite Attunity Replicate for Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 48cf460405ae3985553f9bff29f4fd7abb008196
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migrazione con tempo di inattività minimo a Database di Azure per PostgreSQL
È possibile eseguire la migrazione di un database PostgreSQL esistente in Database di Azure per PostgreSQL tramite Attunity Replicate for Microsoft Migrations. Attunity Replicate è un'offerta congiunta proposta da Attunity e Microsoft. Viene fornito ai clienti Microsoft senza costi aggiuntivi insieme al servizio Migrazione del database di Azure. 

Attunity Replicate contribuisce a ridurre al minimo il tempo di inattività durante le migrazioni di database e mantiene operativo il database di origine durante l'intero processo.

Attunity Replicate è uno strumento di replica dei dati che consente la sincronizzazione dei dati tra un'ampia gamma di origini e destinazioni. Propaga lo script di creazione dello schema e i dati associati a ogni tabella di database. Attunity Replicate non propaga nessun altro elemento (ad esempio SP, trigger, funzioni e così via) né, ad esempio, converte in T-SQL il codice PL/SQL ospitato in tali elementi.

> [!NOTE]
> Anche se Attunity Replicate supporta una vasta gamma di scenari di migrazione, si incentra sul supporto di uno specifico sottoinsieme di coppie origine/destinazione.

Di seguito è illustrata una panoramica del processo cui attenersi per eseguire una migrazione con tempo di inattività minimo:

* **Migrazione dello schema di origine PostgreSQL** in Database di Azure per PostgreSQL usando il comando [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) con il parametro -n e quindi il comando [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html).

* **Impostazione del carico iniziale e della sincronizzazione continua dei dati dal database di origine al database di destinazione** tramite Attunity Replicate for Microsoft Migrations. In questo modo è possibile ridurre al minimo il tempo in cui il database di origine deve essere impostato in sola lettura durante la preparazione del trasferimento delle applicazioni nel database PostgreSQL di destinazione in Azure.

Per altre informazioni sulla soluzione Attunity Replicate for Microsoft Migrations, vedere le risorse seguenti:
 - Visitare la pagina Web di [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Scaricare [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Accedere alla [Attunity Replicate Community](https://aka.ms/attunity-community/) per una guida introduttiva, esercitazioni e supporto.
 - Per istruzioni dettagliate sull'utilizzo di Attunity Replicate per la migrazione da PostgreSQL a Database di Azure per PostgreSQL, vedere [Guida alla migrazione di database](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).
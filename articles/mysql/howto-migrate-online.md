---
title: "Migrazione con tempo di inattività minimo a Database di Azure per MySQL | Microsoft Docs"
description: "Questo articolo descrive come eseguire una migrazione con tempo di inattività minimo di un database MySQL in Database di Azure per MySQL e come configurare il carico iniziale e la continua sincronizzazione dei dati dal database di origine al database di destinazione tramite Attunity Replicate for Microsoft Migrations."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: d23628fd8446f6e7e0e5ed14b98da13c09b2d592
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migrazione con tempo di inattività minimo a Database di Azure per MySQL
È possibile eseguire la migrazione di un database MySQL esistente in Database di Azure per MySQL tramite Attunity Replicate for Microsoft Migrations. Attunity Replicate è un'offerta congiunta proposta da Attunity e Microsoft. Viene fornito ai clienti Microsoft senza costi aggiuntivi insieme al servizio Migrazione del database di Azure. 

Attunity Replicate contribuisce a ridurre al minimo il tempo di inattività durante le migrazioni di database e mantiene operativo il database di origine durante l'intero processo.

Attunity Replicate è uno strumento di replica dei dati che consente la sincronizzazione dei dati tra un'ampia gamma di origini e destinazioni. Propaga lo script di creazione dello schema e i dati associati a ogni tabella di database. Attunity Replicate non propaga nessun altro elemento (ad esempio SP, trigger, funzioni e così via) né, ad esempio, converte in T-SQL il codice PL/SQL ospitato in tali elementi.

> [!NOTE]
> Anche se Attunity Replicate supporta una vasta gamma di scenari di migrazione, si incentra sul supporto di uno specifico sottoinsieme di coppie origine/destinazione.

Di seguito è illustrata una panoramica del processo cui attenersi per eseguire una migrazione con tempo di inattività minimo:

* **Migrazione dello schema di origine MySQL** in un servizio di database gestito Database di Azure per MySQL tramite [MySQL Workbench](https://www.mysql.com/products/workbench/).

* **Impostazione del carico iniziale e della sincronizzazione continua dei dati dal database di origine al database di destinazione** tramite Attunity Replicate for Microsoft Migrations. In questo modo è possibile ridurre al minimo il tempo in cui il database di origine deve essere impostato in sola lettura durante la preparazione del trasferimento delle applicazioni nel database MySQL di destinazione in Azure.

Per altre informazioni sulla soluzione Attunity Replicate for Microsoft Migrations, vedere le risorse seguenti:
 - Visitare la pagina Web di [Attunity Replicate for Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Scaricare [Attunity Replicate for Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Accedere alla [Attunity Replicate Community](https://aka.ms/attunity-community) per una guida introduttiva, esercitazioni e supporto.
 - Per istruzioni dettagliate sull'utilizzo di Attunity Replicate per la migrazione di un database MySQL a Database di Azure per MySQL, vedere [Guida alla migrazione di database](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).
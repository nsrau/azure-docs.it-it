---
title: Rilevamento delle minacce per i servizi dati nel centro sicurezza di Azure | Microsoft Docs
description: Questo articolo presenta gli avvisi di servizi dati disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665735"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Rilevamento delle minacce per i servizi dati nel centro sicurezza di Azure

 Il Centro sicurezza di Azure analizza i log dei servizi di archiviazione dei dati e attiva gli avvisi quando rileva una minaccia per le risorse di dati. Questo articolo elenca gli avvisi generati dal centro sicurezza per i servizi seguenti:

* [Database SQL di Azure e SQL Data Warehouse di Azure](#data-sql)
* [Archiviazione di Azure](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## Database SQL e SQL Data Warehouse<a name="data-sql"></a>

Advanced Threat Protection per il database SQL di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Verranno visualizzati gli avvisi in caso di attività di database sospette, vulnerabilità potenziali o attacchi SQL injection, nonché di modelli di query e di accesso al database anomali.

Advanced Threat Protection per il database SQL di Azure e SQL fa parte del pacchetto unificato Advanced [Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) per le funzionalità avanzate di sicurezza di SQL, per i database SQL di Azure, le istanze gestite del database SQL di Azure, i database di Azure SQL data warehouse e i server SQL in macchine virtuali di Azure.

Per scoprire di più, vedi:

* [Come abilitare Advanced Threat Protection per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Come abilitare Advanced Threat Protection per SQL Server in macchine virtuali di Azure](security-center-iaas-advanced-data.md)
* [Elenco di avvisi di protezione dalle minacce per il database SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Archiviazione di Azure<a name="azure-storage"></a>

Advanced Threat Protection per l'archiviazione (attualmente disponibile solo per l'archiviazione BLOB) rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione. Questo livello di protezione consente di risolvere le minacce senza che sia necessario essere un esperto di sicurezza e consente di gestire i sistemi di monitoraggio della sicurezza.

>[!NOTE]
>Advanced Threat Protection per l'archiviazione non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Per scoprire di più, vedi:

* [Come abilitare Advanced Threat Protection per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Elenco degli avvisi di protezione dalle minacce per archiviazione di Azure](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Gli avvisi Azure Cosmos DB vengono generati da tentativi insoliti e potenzialmente dannosi di accesso o di exploit Azure Cosmos DB account.

Per scoprire di più, vedi:

* [Advanced Threat Protection per Azure Cosmos DB (anteprima)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Elenco di avvisi di protezione dalle minacce per Azure Cosmos DB (anteprima)](alerts-reference.md#alerts-azurecosmos)

---
title: Guida introduttiva di Fivetran con Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come iniziare rapidamente a usare Fivetran e Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355293"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Iniziare rapidamente a usare Fivetran e SQL Data Warehouse

Questa guida introduttiva presuppone che l'utente abbia già un'istanza pre-esistente di SQL Data Warehouse.

## <a name="setup-connection"></a>Configurare la connessione

1. Trovare il nome completo del server e del database per la connessione ad Azure SQL Data Warehouse.

   [Come trovare il nome del server e del database dal portale?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. Durante l'installazione guidata decidere se ci si vuole connettere al database direttamente o tramite un tunnel SSH.

   Se si decide di connettersi direttamente al database, sarà necessario creare una regola del firewall per consentire l'accesso. Questo metodo è quello più semplice e sicuro.

   Se si decide di connettersi tramite un tunnel SSH, Fivetran si connetterà a un server separato all'interno della rete che fornisce un tunnel SSH per il database. Questo metodo è necessario se il database si trova in una subnet inaccessibile in una rete virtuale.

3. Aggiungere l'indirizzo IP "52.0.2.4" nel firewall a livello di server per consentire le connessioni in ingresso ad Azure SQL Data Warehouse da Fivetran.

   [Come aggiungere un firewall a livello di server?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>Configurare le credenziali dell'utente

Connettersi ad Azure SQL Data Warehouse usando SQL Server Management Studio o uno strumento a scelta come utente amministratore del server ed eseguire i comandi SQL seguenti per creare un utente per Fivetran:

Nel database master: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

Nel database di SQL Data Warehouse:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

Dopo aver creato l'utente fivetran, concedere le autorizzazioni seguenti per il warehouse:

```
GRANT CONTROL to fivetran;
```

Aggiungere una classe di risorse appropriata per l'utente creato in base al requisito di memoria per la creazione dell'indice columnstore. Ad esempio, integrazioni come Marketo e Salesforce necessitano di una classe di risorse superiore a causa del numero elevato di colonne o del maggiore volume di dati, che richiede più memoria per creare gli indici columnstore.

È consigliabile usare classi di risorse statiche. È possibile iniziare con la classe di risorse `staticrc20`, che consente di allocare 200 MB per l'utente indipendentemente dal livello di prestazioni in uso. Se la creazione dell'indice columnstore ha esito negativo con la classe di risorse corrente, è necessario aumentare la classe di risorse.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

Per altre informazioni, fare riferimento ai documenti per i [limiti di memoria e concorrenza](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) e le [classi di risorse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory).

È necessaria l'autorizzazione CONTROL per creare credenziali con ambito database che verranno usate durante il caricamento dei file dall'archivio BLOB tramite PolyBase.

Immettere le credenziali per accedere ad Azure SQL Data Warehouse

1. Host (nome del server)
2. Porta
3. Database
4. Utente (il nome utente deve essere `fivetran@<server_name>`, dove `<server_name>` fa parte dell'URI dell'host di Azure: `<server_name>.database.windows.net`)
5. Password
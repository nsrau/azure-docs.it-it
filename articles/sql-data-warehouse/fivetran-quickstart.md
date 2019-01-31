---
title: Guida introduttiva Fivetran per Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come iniziare rapidamente a usare Fivetran e Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 3b354ebf5436a6637fe126085225b0ae6c8f1dcc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465861"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Iniziare rapidamente a usare Fivetran e SQL Data Warehouse

Questa guida introduttiva descrive come configurare un nuovo utente Fivetran per lavorare con Azure SQL Data Warehouse. L'articolo presuppone che esista un'istanza di SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Configurare una connessione

1. Trovare il nome completo del server e del database da usare per la connessione ad Azure SQL Data Warehouse.
    
    Se occorre assistenza per trovare queste informazioni, vedere [Connettersi ad Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. Durante la procedura guidata di installazione, decidere se ci si vuole connettere al database direttamente o tramite un tunnel SSH.

   Se si decide di connettersi direttamente al database, sarà necessario creare una regola di firewall per consentire l'accesso. Questo metodo è quello più semplice e sicuro.

   Se si sceglie di connettersi tramite un tunnel SSH, Fivetran si connette a un server separato sulla rete. Il server fornisce un tunnel SSH al database. È necessario usare questo metodo se il database si trova in una subnet inaccessibile in una rete virtuale.

3. Aggiungere l'indirizzo IP **52.0.2.4** nel firewall a livello di server per consentire le connessioni in ingresso all'istanza Azure SQL Data Warehouse da Fivetran.

   Per altre informazioni, vedere [Creare una regola di firewall a livello di server](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurare le credenziali utente

1. Connettersi ad Azure SQL Data Warehouse con SQL Server Management Studio o lo strumento che si preferisce. Accedere come utente amministratore del server. Quindi, eseguire i comandi SQL seguenti per creare un utente per Fivetran:
    - Nel database master: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Nel database di SQL Data Warehouse:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Concedere all'utente Fivetran le autorizzazioni seguenti per il warehouse:

    ```
    GRANT CONTROL to fivetran;
    ```

    È necessaria l'autorizzazione CONTROLLO per creare credenziali con ambito database che vengono usate quando un utente carica i file dalla risorsa di archiviazione Blob tramite PolyBase.

3. Aggiungere una classe di risorse appropriata all'utente di Fivetran. La classe di risorse che si usa dipende dalla memoria necessaria per creare un indice columnstore. Per esempio, le integrazioni con prodotti come Marketo e Salesforce richiedono una classe di risorse superiore a causa dell'elevato numero di colonne e del volume più grande di dati che i prodotti usano. Una classe di risorse superiore richiede più memoria per creare gli indici columnstore.

    È consigliabile usare classi di risorse statiche. È possibile iniziare con la classe di risorse `staticrc20`. La classe di risorse `staticrc20` alloca 200 MB per ogni utente, indipendentemente dal livello di prestazioni che si usa. Se l'indice columnstore ha esito negativo al livello iniziale di classe di risorse, aumentare la classe di risorse.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Per altre informazioni, leggere i materiali riguardanti i [limiti di memoria e concorrenza](memory-and-concurrency-limits.md) e le [classi di risorse](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Accedere a Fivetran

Per accedere a Fivetran, immettere le credenziali usate per accedere a SQL Data Warehouse: 

* Host (nome del server).
* Porta.
* Database.
* Utente (il nome utente deve essere **fivetran @_server_name_** in cui *server_name* fa parte dell'URI host Azure: **server_name.database.windows.net**).
* Password.

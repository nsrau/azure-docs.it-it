---
title: 'Guida introduttiva: Fivetran e data warehouse'
description: Introduzione a Fivetran e ai data warehouse di Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e679c0b284cc649dbde3fba1b640f4e09df05e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001848"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Guida introduttiva: Fivetran con data warehouse 

Questo modulo di avvio rapido descrive come configurare un nuovo utente Fivetran per lavorare con un data warehouse di Azure Synapse Analytics in cui è stato effettuato il provisioning di un pool SQL. L'articolo presuppone che si disponga di un data warehouse esistente.

## <a name="set-up-a-connection"></a>Configurare una connessione

1. Trovare il nome completo del server e del database da usare per la connessione al data warehouse.
    
    Se occorre assistenza per trovare queste informazioni, vedere [Connettersi al data warehouse](../sql/connect-overview.md).

2. Durante la procedura guidata di installazione, decidere se ci si vuole connettere al database direttamente o tramite un tunnel SSH.

   Se si decide di connettersi direttamente al database, sarà necessario creare una regola di firewall per consentire l'accesso. Questo metodo è quello più semplice e sicuro.

   Se si sceglie di connettersi tramite un tunnel SSH, Fivetran si connette a un server separato sulla rete. Il server fornisce un tunnel SSH al database. È necessario usare questo metodo se il database si trova in una subnet inaccessibile in una rete virtuale.

3. Aggiungere l'indirizzo IP **52.0.2.4** nel firewall a livello di server per consentire le connessioni in ingresso all'istanza del data warehouse da Fivetran.

   Per altre informazioni, vedere [Creare una regola di firewall a livello di server](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurare le credenziali utente

1. Connettersi al data warehouse con SQL Server Management Studio (SSMS) o lo strumento che si preferisce. Accedere come utente amministratore del server. Quindi, eseguire i comandi SQL seguenti per creare un utente per Fivetran:

    - Nel database master: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Nel database del data warehouse:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Concedere all'utente Fivetran le autorizzazioni seguenti per il data warehouse:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    È necessaria l'autorizzazione CONTROLLO per creare credenziali con ambito database che vengono usate quando un utente carica i file dalla risorsa di archiviazione Blob tramite PolyBase.

3. Aggiungere una classe di risorse appropriata all'utente di Fivetran. La classe di risorse che si usa dipende dalla memoria necessaria per creare un indice columnstore. Per esempio, le integrazioni con prodotti come Marketo e Salesforce richiedono una classe di risorse superiore a causa dell'elevato numero di colonne e del volume più grande di dati che i prodotti usano. Una classe di risorse superiore richiede più memoria per creare gli indici columnstore.

    È consigliabile usare classi di risorse statiche. È possibile iniziare con la classe di risorse `staticrc20`. La classe di risorse `staticrc20` alloca 200 MB per ogni utente, indipendentemente dal livello di prestazioni che si usa. Se l'indice columnstore ha esito negativo al livello iniziale di classe di risorse, aumentare la classe di risorse.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Per altre informazioni, leggere i materiali riguardanti i [limiti di memoria e concorrenza](memory-concurrency-limits.md) e le [classi di risorse](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="connect-from-fivetran"></a>Connettersi da Fivetran

Per connettersi al data warehouse dall'account Fivetran, immettere le credenziali usate per accedere al data warehouse: 

* Host (nome del server).
* Porta.
* Database.
* Utente (il nome utente deve essere **fivetran\@_server_name_** in cui *server_name* fa parte dell'URI host Azure: **_server\_name_.database.windows.net**).
* Password.

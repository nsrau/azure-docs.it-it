---
title: Abilitazione delle funzionalità dell'area di lavoro sinapsi in un pool SQL dedicato (in precedenza SQL DW)
description: In questo documento viene descritto in che modo un cliente può accedere e utilizzare l'istanza autonoma di SQL DW esistente nell'area di lavoro.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: f3c40e4c7b00a5c78872a60af25e3b19fe08f324
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467693"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Abilitazione delle funzionalità dell'area di lavoro sinapsi in un pool SQL dedicato esistente (in precedenza SQL DW)

Tutti i clienti di SQL data warehouse ora possono accedere e usare un'istanza del pool SQL dedicato esistente (in precedenza SQL DW) tramite sinapsi studio e l'area di lavoro, senza alcun effetto sull'automazione, le connessioni o gli strumenti. Questo articolo illustra in che modo un cliente esistente di Azure sinapsi Analytics può creare ed espandere la soluzione di analisi esistente sfruttando le nuove funzionalità ricche di funzionalità ora disponibili tramite l'area di lavoro sinapsi e studio.   

## <a name="experience"></a>Esperienza
 
Ora che l'area di lavoro sinapsi è GA, una nuova funzionalità è disponibile nella sezione Panoramica del portale DW che consente di creare un'area di lavoro sinapsi per le istanze del pool SQL dedicato esistente (in precedenza SQL DW). Questa nuova funzionalità consentirà di connettere il server logico che ospita le istanze di data warehouse esistenti a una nuova area di lavoro sinapsi. La connessione garantisce che tutti i data warehouse ospitati nel server siano resi accessibili dall'area di lavoro e da studio e possano essere usati insieme ai servizi del partner sinapsi (pool SQL senza server, pool di Apache Spark e ADF). È possibile iniziare ad accedere e usare le risorse non appena sono state completate le operazioni di provisioning e la connessione è stata stabilita per l'area di lavoro appena creata.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Area di lavoro sinapsi connessa":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Uso dell'area di lavoro sinapsi e delle funzionalità di studio per accedere e usare un pool SQL dedicato (in precedenza SQL DW)
 
Le informazioni seguenti si applicano quando si usa un'operazione SQL DW dedicata (denominata in precedenza SQL DW) con le funzionalità dell'area di lavoro sinapsi abilitate: 
- **Funzionalità SQL** Tutte le funzionalità SQL rimarranno con SQL Server logico dopo l'abilitazione della funzionalità dell'area di lavoro sinapsi. L'accesso al server tramite il provider di risorse SQL sarà ancora possibile dopo l'abilitazione dell'area di lavoro. Tutte le funzioni di gestione possono essere avviate tramite l'area di lavoro e l'operazione verrà eseguita nel SQL Server logico che ospita i pool SQL. Quando un'area di lavoro è abilitata, l'automazione, gli strumenti o le connessioni esistenti non verranno interrotti o interrotti.  
- **Spostamento di risorse**  Se si avvia uno spostamento di risorse in un server con la funzionalità dell'area di lavoro sinapsi abilitata, il collegamento tra il server e l'area di lavoro verrà interrotta e non sarà più possibile accedere alle istanze del pool SQL dedicato (precedentemente SQL DW) esistenti dall'area di lavoro. Per assicurarsi che la connessione venga mantenuta, è consigliabile che entrambe le risorse rimangano nella stessa sottoscrizione e nello stesso gruppo di risorse. 
- **Monitoraggio** di Le richieste SQL inviate tramite sinapsi studio in un'area di lavoro abilitata per il pool SQL dedicato (in precedenza SQL DW) possono essere visualizzate nell'hub di monitoraggio. Per tutte le altre attività di monitoraggio, è possibile passare a portale di Azure monitoraggio del pool SQL dedicato (in precedenza SQL DW). 
- Controlli di **sicurezza** e **accesso** come indicato in precedenza, tutte le funzioni di gestione per SQL Server e le istanze di pool SQL dedicate (in precedenza SQL DW) continueranno a risiedere in SQL Server logico. Queste funzioni includono la gestione delle regole del firewall, l'impostazione del Azure AD amministratore del server e il controllo di accesso per i dati nel pool SQL dedicato (in precedenza SQL DW). È necessario eseguire i passaggi seguenti per assicurarsi che il pool SQL dedicato (in precedenza SQL DW) sia accessibile e possa essere usato tramite l'area di lavoro sinapsi. Le appartenenze ai ruoli dell'area di lavoro non forniscono agli utenti le autorizzazioni per i dati nelle istanze del pool SQL dedicato (in precedenza SQL DW). Seguire i normali criteri di [autenticazione SQL](sql-data-warehouse-authentication.md) per assicurarsi che gli utenti possano accedere alle istanze del pool SQL dedicato (in precedenza SQL DW) nel server logico. 

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > L'area di lavoro connessa sinapsi Studio visualizzerà i nomi dei pool dedicati in base alle autorizzazioni di cui dispone l'utente in Azure. Gli oggetti inclusi nei pool non saranno accessibili se l'utente non dispone delle autorizzazioni per i pool SQL. 

- **Sicurezza di rete** Se l'area di lavoro sinapsi abilitata nel pool SQL dedicato esistente (in precedenza SQL DW) è abilitata per la protezione dell'infiltrazione dei dati. Creare una connessione all'endpoint privato gestito dall'area di lavoro al server SQL logico. Approva la richiesta di connessione all'endpoint privato per consentire le comunicazioni tra il server e l'area di lavoro.
- **Studio** Pool SQL nell' **hub dati** un'area di lavoro è stato abilitato un pool SQL dedicato (in precedenza SQL DW) che può essere identificato tramite la descrizione comando nell'hub dati. 
- **creazione di un nuovo pool SQL dedicato (in precedenza SQL DW)** È possibile creare nuovi pool SQL dedicati tramite l'area di lavoro sinapsi e studio dopo che la funzionalità dell'area di lavoro è stata abilitata e il provisioning di un nuovo pool verrà eseguito sul server SQL logico. Le nuove risorse verranno visualizzate nel portale e in studio al termine del provisioning.      

## <a name="next-steps"></a>Passaggi successivi
Abilitare le [funzionalità dell'area di lavoro sinapsi](workspace-connected-create.md) nel pool SQL dedicato esistente (in precedenza SQL DW)
---
title: Installare i componenti con licenza per il runtime di integrazione SSIS di Azure
description: Informazioni su come un fornitore di software indipendente (ISV) può sviluppare e installare componenti personalizzati, a pagamento o concessi in licenza, per il runtime di integrazione Azure-SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 599b54f8a5d97ee5ed29ce4df16980f456ffb919
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914575"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Installare componenti personalizzati a pagamento o concessi in licenza per il runtime di integrazione Azure-SSIS

Questo articolo illustra il modo in cui un fornitore di software indipendente può sviluppare e installare componenti personalizzati, a pagamento o concessi in licenza, per il runtime di integrazione Azure-SSIS.

## <a name="the-problem"></a>Problema

La natura del runtime di integrazione Azure-SSIS comporta varie sfide che rendono inadeguati i metodi tipici di gestione delle licenze usati per l'installazione locale di componenti personalizzati. Il runtime di integrazione Azure-SSIS richiede quindi un approccio diverso.

-   I nodi del runtime di integrazione Azure-SSIS sono volatili e possono essere allocati o rilasciati in qualsiasi momento. Ad esempio, è possibile avviare o arrestare nodi per gestire i costi oppure aumentare e ridurre le prestazioni modificando le dimensioni dei nodi. Di conseguenza, associare la licenza di un componente di terze parti a un particolare nodo usando informazioni specifiche sul computer, ad esempio l'indirizzo MAC o l'ID della CPU, non è più possibile.

-   È anche possibile aumentare o ridurre il numero di istanze del runtime di integrazione Azure-SSIS, in modo che il numero di nodi possa aumentare o ridursi in qualsiasi momento.

## <a name="the-solution"></a>Soluzione

Viste le limitazioni dei metodi tradizionali di gestione delle licenze descritte nella sezione precedente, il runtime di integrazione Azure-SSIS fornisce una nuova soluzione. Questa soluzione usa le variabili di ambiente Windows e le variabili di sistema SSIS per l'associazione delle licenze e la convalida dei componenti di terze parti. I fornitori di software indipendenti possono usare queste variabili per ottenere informazioni univoche e permanenti per un runtime di integrazione Azure-SSIS, ad esempio ID del cluster e numero di nodi del cluster. Con queste informazioni, i fornitori di software indipendenti possono associare la licenza per i propri componenti a un runtime di integrazione Azure-SSIS *come cluster*. Questa associazione usa un ID che resta invariato quando i clienti avviano o arrestano il runtime di integrazione Azure-SSIS, aumentano o riducono il numero di istanze, aumentano o riducono le prestazioni o modificano in qualsiasi modo la configurazione.

Il diagramma seguente mostra i flussi tipici di installazione, attivazione, associazione della licenza e convalida per i componenti di terze parti che usano queste nuove variabili:

![Installazione dei componenti concessi in licenza](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Istruzioni
1. I fornitori di software indipendenti possono offrire i propri componenti concessi in licenza in più SKU o livelli (ad esempio nodo singolo, fino a 5 nodi, fino a 10 nodi e così via). Quando i clienti acquistano un prodotto, l'ISV fornisce il codice Product Key corrispondente. Può fornire anche un contenitore BLOB di Archiviazione di Azure che include uno script di installazione creato dall'ISV stesso e i file associati. I clienti possono copiare questi file nel proprio contenitore di archiviazione e modificarli inserendo il proprio codice Product Key (ad esempio, eseguendo `IsvSetup.exe -pid xxxx-xxxx-xxxx`). I clienti possono quindi eseguire il provisioning del runtime di integrazione Azure-SSIS o riconfigurarlo usando come parametro l'URI SAS del proprio contenitore. Per altre informazioni, vedere [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Configurazione personalizzata per il runtime di integrazione SSIS di Azure).

2. Quando vengono eseguiti il provisioning o la configurazione del runtime di integrazione Azure-SSIS, lo script di installazione dell'ISV viene eseguito in ogni nodo per recuperare le variabili di ambiente Windows, `SSIS_CLUSTERID` e `SSIS_CLUSTERNODECOUNT`. Quindi, il runtime di integrazione Azure-SSIS invia l'ID cluster e il codice Product Key al server di attivazione dell'ISV per generare una chiave di attivazione.

3. Dopo aver ricevuto la chiave di attivazione, lo script di installazione dell'ISV può archiviare la chiave in locale in ogni nodo, ad esempio nel Registro di sistema.

4. Quando i clienti eseguono un pacchetto che usa il componente concesso in licenza dell'ISV in un nodo del runtime di integrazione Azure-SSIS, il pacchetto legge la chiave di attivazione archiviata in locale e la convalida rispetto all'ID cluster del nodo. Facoltativamente, il pacchetto può anche segnalare al server di attivazione dell'ISV il numero di nodi del cluster.

    Ecco un esempio di codice che convalida la chiave di attivazione e segnala il numero dei nodi del cluster:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>Partner ISV

È possibile trovare un elenco di partner fornitori software indipendenti che hanno adattato i componenti e le estensioni per il runtime di integrazione SSIS Azure alla fine di questo post di blog: [Enterprise Edition, Custom Setup, and 3rd Party Extensibility for SSIS in ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360) (Enterprise Edition, installazione personalizzata ed estendibilità di terze parti SSIS in Azure Data Factory).

## <a name="next-steps"></a>Passaggi successivi

-   [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition del runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

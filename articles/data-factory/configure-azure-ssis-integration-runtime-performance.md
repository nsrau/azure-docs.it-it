---
title: Configurare il runtime di integrazione Azure-SSIS per garantire prestazioni elevate | Microsoft Docs
description: "Informazioni su come configurare le proprietà del runtime di integrazione Azure-SSIS per garantire prestazioni elevate"
services: data-factory
ms.date: 01/10/2018
ms.topic: article
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d0e75ad85731b10f9a993c2fa62f30c0142ed05
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configurare il runtime di integrazione Azure-SSIS per garantire prestazioni elevate

Questo articolo descrive come configurare un runtime di integrazione Azure-SSIS per garantire prestazioni elevate. Il runtime di integrazione Azure-SSIS consente di distribuire ed eseguire pacchetti SQL Server Integration Services (SSIS) in Azure. Per altre informazioni sul runtime di integrazione Azure-SSIS, vedere l'articolo [Runtime di integrazione](concepts-integration-runtime.md#azure-ssis-integration-runtime). Per informazioni sulla distribuzione e l'esecuzione di pacchetti SSIS in Azure, vedere [Spostare i carichi di lavoro di SQL Server Integration Services nel cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Questo articolo contiene i risultati delle prestazioni e le osservazioni derivanti dai test interni effettuati da membri del team di sviluppo SSIS. I risultati ottenuti dall'utente possono variare. Eseguire test personalizzati prima di completare le impostazioni di configurazione, che interessano sia i costi che le prestazioni.

## <a name="properties-to-configure"></a>Proprietà da configurare

La parte di script di configurazione riportata di seguito mostra le proprietà che è possibile configurare quando si crea un runtime di integrazione Azure-SSIS. Per lo script di PowerShell completo e la relativa descrizione, vedere [Distribuire pacchetti SQL Server Integration Services in Azure](tutorial-deploy-ssis-packages-azure.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** è la posizione per il nodo del ruolo di lavoro del runtime di integrazione. Il nodo del ruolo di lavoro mantiene una connessione costante al database del catalogo SSIS (SSISDB) in un database SQL di Azure. Impostare **AzureSSISLocation** sulla stessa posizione del server del database SQL che ospita SSISDB affinché il runtime di integrazione lavori nel modo più efficiente possibile.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
L'anteprima pubblica di Azure Data Factory v2, incluso il runtime di integrazione Azure-SSIS, supporta le opzioni seguenti:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2.

Nei test interni non ufficiali condotti dal team di progettazione SSIS, la serie D sembra essere più adatta all'esecuzione di pacchetti SSIS rispetto alla serie A.

-   Il rapporto tra prezzo e prestazioni nella serie D è migliore rispetto alla serie A.
-   A pari prezzo, la velocità effettiva per la serie D è superiore a quella per la serie A.

### <a name="configure-for-execution-speed"></a>Configurare la velocità di esecuzione
Se non si dispone di molti pacchetti da eseguire e si desidera che vengano eseguiti rapidamente, usare le informazioni presentate nel grafico seguente per scegliere un tipo di macchina virtuale adatto allo scenario in uso.

Questi dati rappresentano l'esecuzione di un singolo pacchetto in un singolo nodo del ruolo di lavoro. Il pacchetto carica 10 milioni di record con le colonne Nome e Cognome da Archiviazione BLOB di Azure, genera una colonna Nome completo e scrive in Archiviazione BLOB di Azure i record nei quali il nome completo è costituito da più di 20 caratteri.

![Velocità di esecuzione del pacchetto del runtime di integrazione SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Configurare la velocità effettiva complessiva

Se i pacchetti da eseguire sono molti e la velocità effettiva complessiva è un requisito prioritario, usare le informazioni presentate nel grafico seguente per scegliere il tipo di macchina virtuale adatto allo scenario in uso.

![Velocità effettiva complessiva massima del runtime di integrazione SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** regola la scalabilità del runtime di integrazione. La velocità effettiva del runtime di integrazione è proporzionale al valore di **AzureSSISNodeNumber**. Impostare inizialmente **AzureSSISNodeNumber** su un valore contenuto, monitorare la velocità effettiva del runtime di integrazione, quindi regolare il valore per lo scenario in uso. Per riconfigurare il conteggio dei nodi del ruolo di lavoro, vedere [Gestire un runtime di integrazione SSIS-Azure](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Quando si usa già un nodo del ruolo di lavoro potente per eseguire i pacchetti, se si aumenta il valore di **AzureSSISMaxParallelExecutionsPerNode**, può aumentare la velocità effettiva complessiva del runtime di integrazione. Per i nodi Standard_D1_v2 sono supportate da 1 a 4 esecuzioni parallele per nodo. Per tutti gli altri tipi di nodi sono supportate da 1 a 8 esecuzioni parallele per nodo.
È possibile stimare il valore appropriato in base al costo del pacchetto e alle configurazioni seguenti per i nodi del ruolo di lavoro. Per altre informazioni, vedere [Dimensioni delle macchine virtuali di utilizzo generico](../virtual-machines/windows/sizes-general.md).

| Dimensione             | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/prestazioni rete previste (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2/2 x 500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4/4 x 500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8/8 x 500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16/16 x 500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8/8 x 500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16/16 x 500                       | 8 / 2000                                       |

Di seguito sono riportate le linee guida per l'impostazione del valore corretto per la proprietà **AzureSSISMaxParallelExecutionsPerNode**: 

1. Impostare la proprietà inizialmente su un valore ridotto.
2. Aumentare il valore di una piccola quantità per controllare se la velocità effettiva complessiva migliora.
3. Non aumentare più il valore quando la velocità effettiva complessiva raggiunge il valore massimo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** è il piano tariffario per il database di catalogo SSIS (SSISDB) in un database SQL di Azure. Questa impostazione influisce sul numero massimo di ruoli di lavoro nell'istanza del runtime di integrazione, sulla velocità di accodamento dell'esecuzione di un pacchetto e sulla velocità di caricamento del log di esecuzione.

-   Se non si è interessati alla velocità di accodamento dell'esecuzione del pacchetto e di caricamento del log di esecuzione, è possibile scegliere il piano tariffario per database minimo. Il database SQL di Azure con prezzi Basic supporta 8 ruoli di lavoro in un'istanza di runtime di integrazione.

-   Se il numero dei ruoli di lavoro è maggiore di 8 o il numero di core è superiore a 50, scegliere un database più potente del piano Basic. In caso contrario, il database diventa il collo di bottiglia dell'istanza di runtime di integrazione e si verifica un rallentamento delle prestazioni complessive.

È inoltre possibile regolare il piano tariffario del database in base alle informazioni sull'utilizzo in [unità di trasmissione dati](../sql-database/sql-database-what-is-a-dtu.md) (DTU) disponibili nel portale di Azure.

## <a name="design-for-high-performance"></a>Progettare per prestazioni elevate
Progettare un pacchetto SSIS da eseguire in Azure è diverso dal progettare un pacchetto da eseguire in locale. Affinché il runtime di integrazione Azure-SSIS venga eseguito in modo più efficiente, è consigliabile separare le attività indipendenti in più pacchetti anziché combinarle nello stesso pacchetto. Creare un'esecuzione di pacchetto per ogni pacchetto, affinché ogni pacchetto non debba attendere il completamento del pacchetto precedente. Questo approccio beneficia della scalabilità del runtime di integrazione Azure-SSIS e migliora la velocità effettiva complessiva.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul runtime di integrazione di Azure-SSIS. Vedere [Runtime di integrazione Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
---
title: Configurare le prestazioni per il Azure-SSIS Integration Runtime
description: Informazioni su come configurare le proprietà del runtime di integrazione Azure-SSIS per garantire prestazioni elevate
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: 15aac35a7ebc505e76ddfd0c538c4fddb7b2d9ff
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930553"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configurare il runtime di integrazione Azure-SSIS per garantire prestazioni elevate

Questo articolo descrive come configurare un runtime di integrazione Azure-SSIS per garantire prestazioni elevate. Il runtime di integrazione Azure-SSIS consente di distribuire ed eseguire pacchetti SQL Server Integration Services (SSIS) in Azure. Per altre informazioni sul runtime di integrazione Azure-SSIS, vedere l'articolo [Runtime di integrazione](concepts-integration-runtime.md#azure-ssis-integration-runtime). Per informazioni sulla distribuzione e l'esecuzione di pacchetti SSIS in Azure, vedere [Spostare i carichi di lavoro di SQL Server Integration Services nel cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Questo articolo contiene i risultati delle prestazioni e le osservazioni derivanti dai test interni effettuati da membri del team di sviluppo SSIS. I risultati ottenuti dall'utente possono variare. Eseguire test personalizzati prima di completare le impostazioni di configurazione, che interessano sia i costi che le prestazioni.

## <a name="properties-to-configure"></a>Proprietà da configurare

La parte di script di configurazione riportata di seguito mostra le proprietà che è possibile configurare quando si crea un runtime di integrazione Azure-SSIS. Per lo script di PowerShell completo e la relativa descrizione, vedere [Distribuire pacchetti SQL Server Integration Services in Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** è la posizione per il nodo del ruolo di lavoro del runtime di integrazione. Il nodo del ruolo di lavoro mantiene una connessione costante al database del catalogo SSIS (SSISDB) in un database SQL di Azure. Impostare **AzureSSISLocation** sulla stessa posizione del server di database SQL che ospita SSISDB affinché il runtime di integrazione lavori nel modo più efficiente possibile.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, incluso il runtime di integrazione Azure-SSIS, supporta le opzioni seguenti:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

Nei test interni non ufficiali condotti dal team di progettazione SSIS, la serie D sembra essere più adatta all'esecuzione di pacchetti SSIS rispetto alla serie A.

-   Il rapporto prestazioni/prezzo della serie D è superiore alla serie A e il rapporto prestazioni/prezzo della serie V3 è superiore alla serie V2.
-   La velocità effettiva della serie D è superiore alla serie a allo stesso prezzo e la velocità effettiva per la serie V3 è superiore alla serie V2 allo stesso prezzo.
-   I nodi della serie V2 di Azure-SSIS IR non sono adatti per l'installazione personalizzata. utilizzare invece i nodi della serie V3. Se si usano già i nodi della serie V2, passare a usare i nodi della serie V3 il prima possibile.
-   La serie E è una macchina virtuale con ottimizzazione per la memoria che fornisce un rapporto tra memoria e CPU superiore rispetto ad altri computer. Se il pacchetto richiede una grande quantità di memoria, è possibile scegliere una VM serie E.

### <a name="configure-for-execution-speed"></a>Configurare la velocità di esecuzione
Se non si dispone di molti pacchetti da eseguire e si desidera che vengano eseguiti rapidamente, usare le informazioni presentate nel grafico seguente per scegliere un tipo di macchina virtuale adatto allo scenario in uso.

Questi dati rappresentano l'esecuzione di un singolo pacchetto in un singolo nodo del ruolo di lavoro. Il pacchetto carica 3 milioni record con le colonne nome e cognome dall'archiviazione BLOB di Azure, genera una colonna con nome completo e scrive i record il cui nome completo è composto da più di 20 caratteri nell'archivio BLOB di Azure.

![Velocità di esecuzione del pacchetto del runtime di integrazione SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Configurare la velocità effettiva complessiva

Se i pacchetti da eseguire sono molti e la velocità effettiva complessiva è un requisito prioritario, usare le informazioni presentate nel grafico seguente per scegliere il tipo di macchina virtuale adatto allo scenario in uso.

![Velocità effettiva complessiva massima del runtime di integrazione SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** regola la scalabilità del runtime di integrazione. La velocità effettiva del runtime di integrazione è proporzionale al valore di **AzureSSISNodeNumber**. Impostare inizialmente **AzureSSISNodeNumber** su un valore contenuto, monitorare la velocità effettiva del runtime di integrazione, quindi regolare il valore per lo scenario in uso. Per riconfigurare il conteggio dei nodi del ruolo di lavoro, vedere [Gestire un runtime di integrazione SSIS-Azure](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Quando si usa già un nodo del ruolo di lavoro potente per eseguire i pacchetti, se si aumenta il valore di **AzureSSISMaxParallelExecutionsPerNode**, può aumentare la velocità effettiva complessiva del runtime di integrazione. Per i nodi Standard_D1_v2 sono supportate da 1 a 4 esecuzioni parallele per nodo. Per tutti gli altri tipi di nodi, sono supportati 1-max (2 x numero di core, 8) esecuzioni parallele per nodo. Se si vuole che **AzureSSISMaxParallelExecutionsPerNode** superi il valore massimo supportato, è possibile aprire un ticket di supporto ed è possibile aumentare il valore massimo per l'utente e dopo che è necessario usare Azure PowerShell per aggiornare **AzureSSISMaxParallelExecutionsPerNode**.
È possibile stimare il valore appropriato in base al costo del pacchetto e alle configurazioni seguenti per i nodi del ruolo di lavoro. Per altre informazioni, vedere [Dimensioni delle macchine virtuali di utilizzo generico](../virtual-machines/windows/sizes-general.md).

| Dimensioni             | vCPU | Memoria: GiB | GiB di archiviazione temporanea (unità SSD) | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps di lettura/Mbps di scrittura | Velocità effettiva/disco di dati massimo: IOPS | Schede di interfaccia di rete max/prestazioni rete previste (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2/2 x 500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4/4 x 500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8/8 x 500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16/16 x 500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8/8 x 500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16/16 x 500                       | 8 / 2000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4/6X500                         | 2 / 1000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/48x500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4/6X500                         | 2 / 1000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

Di seguito sono riportate le linee guida per l'impostazione del valore corretto per la proprietà **AzureSSISMaxParallelExecutionsPerNode**: 

1. Impostare la proprietà inizialmente su un valore ridotto.
2. Aumentare il valore di una piccola quantità per controllare se la velocità effettiva complessiva migliora.
3. Non aumentare più il valore quando la velocità effettiva complessiva raggiunge il valore massimo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** è il piano tariffario per il database di catalogo SSIS (SSISDB) in un database SQL di Azure. Questa impostazione influisce sul numero massimo di ruoli di lavoro nell'istanza del runtime di integrazione, sulla velocità di accodamento dell'esecuzione di un pacchetto e sulla velocità di caricamento del log di esecuzione.

-   Se non si è interessati alla velocità di accodamento dell'esecuzione del pacchetto e di caricamento del log di esecuzione, è possibile scegliere il piano tariffario per database minimo. Il database SQL di Azure con prezzi Basic supporta 8 ruoli di lavoro in un'istanza di runtime di integrazione.

-   Se il numero dei ruoli di lavoro è maggiore di 8 o il numero di core è superiore a 50, scegliere un database più potente del piano Basic. In caso contrario, il database diventa il collo di bottiglia dell'istanza di runtime di integrazione e si verifica un rallentamento delle prestazioni complessive.

-   Scegliere un database più potente, ad esempio S3 se il livello di registrazione è impostato su Verbose. Secondo il test interno non ufficiale, il piano tariffario S3 può supportare l'esecuzione di pacchetti SSIS con 2 nodi, conteggi paralleli 128 e un livello di registrazione dettagliata.

È inoltre possibile regolare il piano tariffario del database in base alle informazioni sull'utilizzo in [unità di trasmissione dati](../sql-database/sql-database-what-is-a-dtu.md) (DTU) disponibili nel portale di Azure.

## <a name="design-for-high-performance"></a>Progettare per prestazioni elevate
Progettare un pacchetto SSIS da eseguire in Azure è diverso dal progettare un pacchetto da eseguire in locale. Affinché il runtime di integrazione Azure-SSIS venga eseguito in modo più efficiente, è consigliabile separare le attività indipendenti in più pacchetti anziché combinarle nello stesso pacchetto. Creare un'esecuzione di pacchetto per ogni pacchetto, affinché ogni pacchetto non debba attendere il completamento del pacchetto precedente. Questo approccio beneficia della scalabilità del runtime di integrazione Azure-SSIS e migliora la velocità effettiva complessiva.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul runtime di integrazione di Azure-SSIS. Vedere [Runtime di integrazione Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

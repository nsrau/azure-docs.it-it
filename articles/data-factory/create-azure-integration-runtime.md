---
title: Creare un runtime di integrazione di Azure in Azure Data Factory
description: Informazioni su come creare il runtime di integrazione di Azure in Azure Data Factory, che viene usato per copiare i dati e inviare le attività di trasformazione.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: c7880fd7fb687483409ce591059e0f5b2d2e2991
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84659702"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Come creare e configurare il runtime di integrazione di Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il runtime di integrazione è l'infrastruttura di calcolo usata da Azure Data Factory per fornire le funzionalità di integrazione di dati in diversi ambienti di rete. Per altre informazioni sul runtime di integrazione, vedere [Integration runtime](concepts-integration-runtime.md) (Runtime di integrazione).

Il runtime di integrazione di Azure fornisce una funzionalità di calcolo completamente gestita per eseguire in modo nativo lo spostamento dei dati e inviare le attività di trasformazione dei dati ai servizi di calcolo, ad esempio HDInsight. È ospitato nell'ambiente Azure e supporta la connessione alle risorse nell'ambiente di rete pubblica con endpoint accessibili pubblicamente.

Questo documento illustra come creare e configurare il runtime di integrazione di Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Runtime di integrazione di Azure predefinito
Per impostazione predefinita, ogni data factory ha un runtime di integrazione di Azure nel back-end che supporta le operazioni negli archivi dati cloud e i servizi di calcolo nella rete pubblica. Il percorso di tale Azure IR è la risoluzione automaticamente. Se la proprietà **connectVia** non viene specificata nella definizione del servizio collegato, viene usato il runtime di integrazione di Azure predefinito. È necessario creare in modo esplicito un runtime di integrazione di Azure solo quando si vuole definire in modo esplicito la località del runtime di integrazione o si vogliono raggruppare le esecuzioni di attività in runtime di integrazione diversi a scopo di gestione. 

## <a name="create-azure-ir"></a>Creare il runtime di integrazione di Azure

Per creare e configurare una Azure IR, è possibile utilizzare le procedure seguenti.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Creare una Azure IR tramite Azure PowerShell
È possibile creare Integration Runtime usando il cmdlet di PowerShell **set-AzDataFactoryV2IntegrationRuntime** . Per creare una Azure IR, è necessario specificare il nome, il percorso e il tipo per il comando. Ecco un comando di esempio per creare un runtime di integrazione di Azure con la località impostata su "Europa occidentale":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Per il runtime di integrazione di Azure, il tipo deve essere impostato su **Managed**. Non è necessario specificare i dettagli del calcolo perché è completamente gestito in modo elastico nel cloud. Specificare i dettagli del calcolo, ad esempio le dimensioni del nodo e il numero di nodi, quando si vuole creare un runtime di integrazione SSIS di Azure. Per altre informazioni, vedere [Creare e configurare il runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md).

È possibile configurare un Azure IR esistente per modificarne il percorso usando il cmdlet di PowerShell set-AzDataFactoryV2IntegrationRuntime. Per altre informazioni sulla località di un runtime di integrazione di Azure, vedere [Introduction to integration runtime](concepts-integration-runtime.md) (Introduzione al runtime di integrazione).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Creare una Azure IR tramite Azure Data Factory interfaccia utente
Usare la procedura seguente per creare un Azure IR usando Azure Data Factory interfaccia utente.

1. Nella pagina **Attività iniziali** dell'interfaccia utente di Azure Data Factory selezionare la [scheda Gestisci](https://docs.microsoft.com/azure/data-factory/author-management-hub) nel riquadro all'estrema sinistra.

   ![Pulsante Gestisci nella home page](media/doc-common-process/get-started-page-manage-button.png)

1. Selezionare **Runtime di integrazione** nel riquadro sinistro, quindi selezionare **+ Nuovo**.

   ![Creare un runtime di integrazione](media/doc-common-process/manage-new-integration-runtime.png)

1. Nella pagina di **installazione di Integration Runtime** selezionare **Azure, self-hosted**e quindi selezionare **continue (continua**). 

1. Nella pagina seguente selezionare **Azure** per creare una Azure IR e quindi fare clic su **continua**.
   ![Creare un runtime di integrazione](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Immettere un nome per il Azure IR e selezionare **Crea**.
   ![Creare una Azure IR](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Al termine della creazione verrà visualizzata una notifica popup. Nella pagina **Integration Runtimes (runtime di integrazione** ) assicurarsi che nell'elenco sia visualizzato il runtime di integrazione appena creato.

## <a name="use-azure-ir"></a>Usare il runtime di integrazione di Azure

Dopo la creazione di un runtime di integrazione di Azure, è possibile farvi riferimento nella definizione del servizio collegato. Segue un esempio di come fare riferimento al runtime di integrazione di Azure creato sopra da un servizio collegato Archiviazione di Azure:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Passaggi successivi
Per creare altri tipi di runtime di integrazione, vedere gli articoli seguenti:

- [Creare un runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md)
- [Creare il runtime di integrazione SSIS di Azure](create-azure-ssis-integration-runtime.md)
 

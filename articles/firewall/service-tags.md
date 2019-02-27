---
title: Panoramica dei tag del servizio Firewall di Azure
description: Questo articolo è una panoramica dei tag del servizio Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/21/2019
ms.author: victorh
ms.openlocfilehash: 9ecc5d3779ac6632f4a5c05914cbb0e756c79e91
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458218"
---
# <a name="azure-firewall-service-tags"></a>Tag del servizio Firewall di Azure

Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP inclusi in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

I tag del servizio Firewall di Azure possono essere usati nel campo di destinazione delle regole di rete. È possibile usarli al posto di indirizzi IP specifici.

> [!NOTE]
> I tag di servizio vengono aggiunti alle aree in modo incrementale e saranno disponibili in tutte le aree nel prossimo futuro.

## <a name="supported-service-tags"></a>Tag di servizio supportati

Nelle regole di rete di Firewall di Azure possono essere usati i tag di servizio seguenti:

* **AzureCloud** (solo Resource Manager): Questo tag identifica lo spazio indirizzi IP per Azure che include tutti gli [indirizzi IP pubblici dei data center](https://www.microsoft.com/download/details.aspx?id=41653). Se si specifica *AzureCloud* come valore, verrà consentito o impedito il traffico verso gli indirizzi pubblici di Azure. Se si vuole consentire l'accesso ad AzureCloud solo in una determinata [area](https://azure.microsoft.com/regions), è possibile specificare tale area. Se ad esempio si vuole consentire l'accesso ad AzureCloud di Azure solo nell'area Stati Uniti orientali, è possibile specificare *Azurecloud.EastUS* come tag di servizio. 
* **AzureTrafficManager** (solo Resource Manager): Questo tag identifica lo spazio indirizzi IP per gli indirizzi IP probe di Gestione traffico di Azure. Per altre informazioni sugli IP probe di Gestione traffico, consultare [Domande frequenti su Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Storage** (solo Resource Manager): Questo tag identifica lo spazio indirizzi IP per il servizio Archiviazione di Azure. Se si specifica *Storage* come valore, verrà consentito o impedito il traffico verso il servizio di archiviazione. Se si vuole consentire l'accesso al servizio di archiviazione solo in una determinata [area](https://azure.microsoft.com/regions), è possibile specificare tale area. Se ad esempio si vuole consentire l'accesso ad Archiviazione di Azure solo nell'area Stati Uniti orientali, è possibile specificare *Storage.EastUS* come tag di servizio. Il tag rappresenta il servizio, ma non istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Archiviazione di Azure, ma non uno specifico account di archiviazione di Azure.
* **Sql** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo dei servizi database SQL di Azure e Azure SQL Data Warehouse. Se si specifica *Sql* come valore, verrà consentito o impedito il traffico verso SQL. Se si vuole consentire l'accesso a SQL solo in una determinata [area](https://azure.microsoft.com/regions), è possibile specificare tale area. Se ad esempio si vuole consentire l'accesso a Database SQL di Azure solo nell'area Stati Uniti orientali, è possibile specificare *Sql.EastUS* come tag di servizio. Il tag rappresenta il servizio, ma non istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non uno specifico server o database SQL.
* **AzureCosmosDB** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure Cosmos DB. Se si specifica *AzureCosmosDB* come valore, verrà consentito o impedito il traffico verso AzureCosmosDB. Se si vuole solo consentire l'accesso ad AzureCosmosDB in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato AzureCosmosDB.[nome dell'area].
* **AzureKeyVault** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure KeyVault. Se si specifica *AzureKeyVault* come valore, verrà consentito o impedito il traffico verso AzureKeyVault. Se si vuole solo consentire l'accesso ad AzureKeyVault in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato AzureKeyVault.[nome dell'area].
* **EventHub** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure EventHub. Se si specifica *EventHub* come valore, verrà consentito o impedito il traffico verso EventHub. Se si vuole solo consentire l'accesso a EventHub in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato EventHub.[nome dell'area]. 
* **ServiceBus** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure ServiceBus. Se si specifica *ServiceBus* come valore, verrà consentito o impedito il traffico verso ServiceBus. Se si vuole solo consentire l'accesso a ServiceBus in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato ServiceBus.[nome dell'area].
* **MicrosoftContainerRegistry** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Registro Container di Microsoft. Se si specifica *MicrosoftContainerRegistry* come valore, verrà consentito o impedito il traffico verso MicrosoftContainerRegistry. Se si vuole solo consentire l'accesso a MicrosoftContainerRegistry in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato MicrosoftContainerRegistry.[nome dell'area].
* **AzureContainerRegistry** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Registro Azure Container. Se si specifica *AzureContainerRegistry* come valore, verrà consentito o impedito il traffico verso AzureContainerRegistry. Se si vuole solo consentire l'accesso ad AzureContainerRegistry in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato AzureContainerRegistry.[nome dell'area]. 
* **AppService** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure AppService. Se si specifica *AppService* come valore, verrà consentito o impedito il traffico verso AppService. Se si vuole solo consentire l'accesso ad AppService in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato AppService.[nome dell'area]. 
* **AppServiceManagement** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure AppService Management. Se si specifica *AppServiceManagement* come valore, verrà consentito o impedito il traffico verso AppServiceManagement. 
* **ApiManagement** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Gestione API di Azure. Se si specifica *ApiManagement* come valore, verrà consentito o impedito il traffico verso ApiManagement.  
* **AzureConnectors** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure Connectors. Se si specifica *AzureConnectors* come valore, verrà consentito o impedito il traffico verso AzureConnectors. Se si vuole solo consentire l'accesso ad AzureConnectors in un'[area](https://azure.microsoft.com/regions) specifica, è possibile specificare l'area usando il formato AzureConnectors.[nome dell'area].
* **AzureDataLake** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio Azure Data Lake. Se si specifica *AzureDataLake* come valore, verrà consentito o impedito il traffico verso AzureDataLake.
* **AzureActiveDirectory** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio AzureActiveDirectory. Se si specifica *AzureActiveDirectory* come valore, verrà consentito o impedito il traffico verso AzureActiveDirectory.
* **AzureMonitor** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio AzureMonitor. Se si specifica *AzureMonitor* come valore, verrà consentito o impedito il traffico verso AzureMonitor.
* **ServiceFabric** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio ServiceFabric. Se si specifica *ServiceFabric* come valore, verrà consentito o impedito il traffico verso ServiceFabric.
* **AzureMachineLearning** (solo Resource Manager): Questo tag identifica i prefissi di indirizzo del servizio AzureMachineLearning. Se si specifica *AzureMachineLearning* come valore, verrà consentito o impedito il traffico verso AzureMachineLearning.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle regole di Firewall di Azure, vedere [Logica di elaborazione delle regole del Firewall di Azure](rule-processing.md).
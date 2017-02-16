---
title: Modello di Resource Manager per l&quot;archiviazione | Microsoft Docs
description: Mostra lo schema di Gestione risorse per la distribuzione di account di archiviazione tramite un modello.
services: azure-resource-manager,storage
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 25d35683-fe99-4a11-8b1a-b80accab58e7
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: b089bc075ec3ec718eb21357bbbc5c4af09f4af1
ms.openlocfilehash: b01125160875e367b9b6e1d02031c5dd80b41594


---
# <a name="storage-account-template-schema"></a>Account di archiviazione - Schema del modello
Crea un account di archiviazione.

## <a name="schema-format"></a>Formato dello schema
Per creare un account di archiviazione, aggiungere lo schema seguente alla sezione delle risorse del modello.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Valori
Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Nome | Valore |
| --- | --- |
| type |Enum<br />Obbligatorio<br />**Microsoft.Storage/storageAccounts**<br /><br />Tipo di risorsa da creare. |
| apiVersion |Enum<br />Obbligatorio<br />**2015-06-15** o **2015-05-01-preview**<br /><br />Versione dell'API da usare per creare la risorsa. |
| Nome |String<br />Obbligatorio<br />Tra 3 e 24 caratteri, solo numeri e lettere minuscole.<br /><br />Nome dell'account di archiviazione da creare. Il nome deve essere univoco in Azure. È consigliabile utilizzare la funzione [uniqueString](resource-group-template-functions.md#uniquestring) con la convenzione di denominazione, come illustrato nell'esempio riportato di seguito. |
| location |string<br />Obbligatorio<br />Area che supporta gli account di archiviazione. Per determinare le aree valide, vedere [Supported regions](resource-manager-supported-services.md#supported-regions) (Aree supportate).<br /><br />Area che deve ospitare l'account di archiviazione. |
| properties |Oggetto<br />Obbligatorio<br />[oggetto delle proprietà](#properties)<br /><br />Oggetto che specifica il tipo dell'account di archiviazione da creare. |

<a id="properties" />

### <a name="properties-object"></a>oggetto delle proprietà
| Nome | Valore |
| --- | --- |
| accountType |String<br />Obbligatorio<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS** o **Premium_LRS**<br /><br />Tipo di account di archiviazione. I valori consentiti corrispondono a Standard con ridondanza locale, Standard con ridondanza della zona, Standard con ridondanza geografica, Standard con ridondanza geografica e accesso in lettura e Premium con ridondanza locale. Per informazioni su questi tipi di account, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md). |

## <a name="examples"></a>esempi
L'esempio seguente consente di distribuire un account di archiviazione con ridondanza locale standard con un nome univoco in base all'id del gruppo di risorse.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modelli di Guida introduttiva
Sono disponibili numerosi modelli di Guida introduttiva che includono un account di archiviazione. I modelli seguenti illustrano alcuni scenari comuni:

* [Creare un account di archiviazione standard](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create)
* [Distribuzione semplice di una macchina virtuale Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Distribuzione semplice di una macchina virtuale Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Create a CDN Profile, a CDN Endpoint with a Storage Account as origin](Creare un profilo di rete CDN, un endpoint di rete CDN con un account di archiviazione come origine)https://github.com/Azure/azure-quickstart-templates/tree/master/201-cdn-with-storage-account
* [Creare una farm di SharePoint a disponibilità elevata con nove macchine virtuali usando l'estensione DSC PowerShell](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-server-farm-ha)
* [Distribuzione semplice di un cluster di Service Fabric sicuro a cinque nodi con WAD abilitato](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)
* [Creare una macchina virtuale da un'immagine di Windows con quattro dischi dati vuoti](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sull'archiviazione, vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).
* Per esempi di modelli che usano un nuovo account di archiviazione con una macchina virtuale, vedere [Deploy a simple Linux VM](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) (Disstribuire una VM Linux semplice) o [Deploy a simple Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/) (Distribuire una VM Windows semplice).




<!--HONumber=Jan17_HO1-->



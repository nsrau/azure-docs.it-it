<properties
   pageTitle="Modello di gestione risorse per l’archiviazione | Microsoft Azure"
   description="Mostra lo schema di Gestione risorse per la distribuzione di account di archiviazione tramite un modello."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Account di archiviazione - Schema del modello

Crea un account di archiviazione.

## Formato dello schema

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

## Valori

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Nome | Valore |
| ---- | ---- |
| type | Enum<br />Obbligatorio<br />**Microsoft.Storage/storageAccounts**<br /><br />Tipo di risorsa da creare. |
| apiVersion | Enum<br />Obbligatorio<br />**2015-06-15** o **2015-05-01-preview**<br /><br />Versione dell'API da usare per creare la risorsa. | 
| name | String<br />Obbligatorio<br />Tra 3 e 24 caratteri, solo numeri e lettere minuscole.<br /><br />Nome dell'account di archiviazione da creare. Il nome deve essere univoco in Azure. È consigliabile utilizzare la funzione [uniqueString](resource-group-template-functions.md#uniquestring) con la convenzione di denominazione, come illustrato nell'esempio riportato di seguito. |
| location | String<br />Obbligatorio<br />Area che supporta gli account di archiviazione. Per determinare le aree valide, vedere le [aree supportate](resource-manager-supported-services.md#supported-regions).<br /><br />Area che deve ospitare l'account di archiviazione. |
| properties | Oggetto<br />Obbligatorio<br />[oggetto properties](#properties)<br /><br />Oggetto che specifica il tipo dell'account di archiviazione da creare. |

<a id="properties" />
### oggetto delle proprietà

| Nome | Valore |
| ---- | ---- | 
| accountType | String<br />Obbligatorio<br />**Standard\_LRS**, **Standard\_ZRS**, **Standard\_GRS**, **Standard\_RAGRS** o **Premium\_LRS**<br /><br />Tipo di account di archiviazione. I valori consentiti corrispondono a Standard con ridondanza locale, Standard con ridondanza della zona, Standard con ridondanza geografica, Standard con ridondanza geografica e accesso in lettura e Premium con ridondanza locale. Per informazioni su questi tipi di account, vedere [replica di archiviazione Azure](./storage/storage-redundancy.md). |

	
## esempi

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

## Modelli di Guida introduttiva

Sono disponibili numerosi modelli di Guida introduttiva che includono un account di archiviazione. I modelli seguenti illustrano alcuni scenari comuni:

- [Creare un account di archiviazione standard](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Distribuzione semplice di una macchina virtuale Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Distribuzione semplice di una macchina virtuale Linux](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Creare un profilo di rete CDN, un endpoint di rete CDN con un account di archiviazione come origine](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Creare una farm di SharePoint a disponibilità elevata con nove macchine virtuali usando l'estensione DSC PowerShell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Distribuzione semplice di un cluster di Service Fabric sicuro a cinque nodi con WAD abilitato](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Creare una macchina virtuale da un'immagine di Windows con quattro dischi dati vuoti](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## Passaggi successivi

- Per altre informazioni sull’archiviazione, vedere [Introduzione ad Archiviazione di Microsoft Azure](./storage/storage-introduction.md).
- Ad esempio modelli che utilizzano un nuovo account di archiviazione con una macchina virtuale, vedere [distribuire una semplice VM Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) o [distribuire una macchina virtuale Windows semplice](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=AcomDC_0406_2016-->
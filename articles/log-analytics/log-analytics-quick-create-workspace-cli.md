---
title: Creare un'area di lavoro di Log Analytics usando l'interfaccia della riga di comando di Azure| Microsoft Docs
description: Informazioni su come creare un'area di lavoro di Log Analytics per abilitare soluzioni di gestione e la raccolta dei dati dagli ambienti cloud e locali con l'interfaccia della riga di comando di Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptal
ms.date: 09/19/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 47e2db42e21be6b540882a9c316517cfb4fb82e7
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054445"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Creare un'area di lavoro di Log Analytics con l'interfaccia della riga di comando di Azure 2.0

L'interfaccia della riga di comando di Azure 2.0 viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. La presente guida introduttiva illustra come usare l'interfaccia della riga di comando di Azure 2.0 per distribuire un'area di lavoro di Log Analytics, ovvero un ambiente univoco che contiene un archivio dati, origini dati e soluzioni.  I passaggi descritti in questo articolo sono obbligatori se si intende raccogliere i dati dalle origini seguenti:

* Risorse di Azure nella sottoscrizione  
* Computer locali monitorati tramite System Center Operations Manager  
* Raccolte di dispositivi da System Center Configuration Manager  
* Dati di diagnostica o dei log dall'archiviazione di Azure  
 
Per altre origini, ad esempio macchine virtuali di Azure e macchine virtuali di Windows o Linux presenti nell'ambiente, vedere gli argomenti seguenti:

* [Raccogliere dati dalle macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md)
* [Raccogliere dati dal computer Linux ibrido](log-analytics-quick-collect-linux-computer.md)
* [Raccogliere dati dal computer Windows ibrido](log-analytics-quick-collect-windows-computer.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo avvio rapido richiede la versione 2.0.30 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Creare un'area di lavoro
Creare un'area di lavoro con [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). L'esempio seguente crea un'area di lavoro denominata *TestWorkspace* nel gruppo di risorse *Lab* in posizione *eastus* usando un modello di Resource Manager dal computer locale. Il modello JSON è configurato in modo da richiedere solo il nome dell'area di lavoro e specifica un valore predefinito per gli altri parametri che potrebbero essere usati come configurazione standard nell'ambiente in uso. È possibile, in alternativa, archiviarli in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

I parametri seguenti impostano un valore predefinito:

* location: il valore predefinito è Stati Uniti orientali
* sku: il valore predefinito è il nuovo piano tariffario per GB rilasciato nel modello di prezzi di aprile 2018

>[!WARNING]
>Se si crea o si configura un'area di lavoro di Log Analytics in una sottoscrizione basata sul nuovo modello di prezzi di aprile 2018, l'unico piano tariffario di Log Analytics valido è **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Creare e distribuire il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Modificare il modello in base alle esigenze.  Rivedere il riferimento del [modello Microsoft.OperationalInsights/workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) per informazioni sulle proprietà e sui valori supportati. 
3. Salvare questo file come **deploylaworkspacetemplate.json** in una cartella locale.   
4. A questo punto è possibile distribuire il modello. Usare i comandi seguenti dalla cartella che contiene il modello:

    ```azurecli
    azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
    ```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

![Esempio di risultato al termine della distribuzione](./media/log-analytics-template-workspace-configuration/template-output-01.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un'area di lavoro, è possibile configurare la raccolta di dati di telemetria di monitoraggio, eseguire ricerche nei log per analizzare i dati e aggiungere una soluzione di gestione per fornire informazioni analitiche dettagliate e dati aggiuntivi.  

* Per abilitare la raccolta di dati dalle risorse di Azure con Diagnostica di Azure o l'archiviazione di Azure, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](log-analytics-azure-storage.md).  
* Aggiungere [System Center Operations Manager come origine dati](log-analytics-om-agents.md) per raccogliere i dati da agenti di creazione report per il gruppo di gestione Operations Manager e archiviarli nell'area di lavoro di Log Analytics.  
* Connettere [Configuration Manager](log-analytics-sccm.md) per importare computer che sono membri di raccolte nella gerarchia.  
* Esaminare le [soluzioni di gestione](log-analytics-add-solutions.md) disponibili e verificare come aggiungere o rimuovere una soluzione dall'area di lavoro.
---
title: Creare un'area di lavoro Log Analytics usando l'interfaccia della riga di comando di Azure| Microsoft Docs
description: Informazioni su come creare un'area di lavoro Log Analytics per abilitare soluzioni di gestione e la raccolta dei dati dagli ambienti cloud e locali con l'interfaccia della riga di comando di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 7421162ed68a879d9f935a3efd5c6267e159a648
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324302"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Creare un'area di lavoro Log Analytics con l'interfaccia della riga di comando di Azure 2.0

L'interfaccia della riga di comando di Azure 2.0 viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure 2.0 per distribuire un'area di lavoro Log Analytics in Monitoraggio di Azure. Un'area di lavoro Log Analytics è un ambiente univoco per i dati di log di Monitoraggio di Azure. Ogni area di lavoro ha un proprio repository di dati e una propria configurazione, mentre le origini dati e le soluzioni sono configurate per l'archiviazione dei dati in un'area di lavoro specifica. È necessaria un'area di lavoro Log Analytics se si intende raccogliere dati dalle origini seguenti:

* Risorse di Azure nella sottoscrizione  
* Computer locali monitorati tramite System Center Operations Manager  
* Raccolte di dispositivi da Configuration Manager  
* Dati di diagnostica o dei log dall'archiviazione di Azure  

Per altre origini, ad esempio macchine virtuali di Azure e macchine virtuali di Windows o Linux presenti nell'ambiente, vedere gli argomenti seguenti:

* [Raccogliere dati dalle macchine virtuali di Azure](./quick-collect-azurevm.md)
* [Raccogliere dati dal computer Linux ibrido](./quick-collect-linux-computer.md)
* [Raccogliere dati dal computer Windows ibrido](quick-collect-windows-computer.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo avvio rapido richiede la versione 2.0.30 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Creare un'area di lavoro
Creare un'area di lavoro con [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Nell'esempio seguente viene creata un'area di lavoro nella località *eastus* usando un modello di Resource Manager presente nel computer locale. Il modello JSON è configurato in modo da richiedere solo il nome dell'area di lavoro e specifica un valore predefinito per gli altri parametri che potrebbero essere usati come configurazione standard nell'ambiente in uso. È possibile, in alternativa, archiviarli in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Per informazioni sulle aree geografiche supportate, vedere le [aree geografiche in cui è disponibile Log Analytics](https://azure.microsoft.com/regions/services/) e cercare Monitoraggio di Azure nel campo **Cerca un prodotto**.

I parametri seguenti impostano un valore predefinito:

* location: il valore predefinito è Stati Uniti orientali
* sku: il valore predefinito è il nuovo piano tariffario per GB rilasciato nel modello di prezzi di aprile 2018

>[!WARNING]
>Se si crea o si configura un'area di lavoro Log Analytics in una sottoscrizione basata sul nuovo modello di prezzi di aprile 2018, l'unico piano tariffario di Log Analytics valido è **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Creare e distribuire il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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
            "apiVersion": "2015-11-01-preview",
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

2. Modificare il modello in base alle esigenze. Rivedere il riferimento del [modello Microsoft.OperationalInsights/workspaces](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) per informazioni sulle proprietà e sui valori supportati.
3. Salvare questo file come **deploylaworkspacetemplate.json** in una cartella locale.   
4. A questo punto è possibile distribuire il modello. Usare i comandi seguenti dalla cartella che contiene il modello. Quando viene richiesto un nome di area di lavoro, specificarne uno univoco a livello globale in tutte le sottoscrizioni di Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

![Esempio di risultato al termine della distribuzione](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi
Quando si crea un'area di lavoro eliminata negli ultimi 14 giorni e in [stato di eliminazione temporanea](../platform/delete-workspace.md#soft-delete-behavior), l'operazione potrebbe avere risultati diversi a seconda della configurazione dell'area di lavoro:
1. Se si specificano lo stesso nome dell'area di lavoro, gruppo di risorse, sottoscrizione e area dell'area di lavoro eliminata, l'area di lavoro verrà ripristinata, inclusi i dati, la configurazione e gli agenti connessi.
2. Se si usa lo stesso nome dell'area di lavoro, ma un gruppo di risorse, una sottoscrizione o un'area diversa, si otterrà un errore *Il nome dell'area di lavoro 'workspace-name' non è univoco*, o dei *conflitti*. Per eseguire l'override dell'eliminazione temporanea ed eliminare definitivamente l'area di lavoro e creare una nuova area di lavoro con lo stesso nome, attenersi alla procedura seguente per ripristinare prima l'area di lavoro ed eseguire l'eliminazione permanente:
   * [Recuperare](../platform/delete-workspace.md#recover-workspace) l'area di lavoro
   * [Eliminare definitivamente](../platform/delete-workspace.md#permanent-workspace-delete) l'area di lavoro
   * Creare una nuova area di lavoro usando il nome della stessa area di lavoro

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un'area di lavoro, è possibile configurare la raccolta di dati di telemetria di monitoraggio, eseguire ricerche nei log per analizzare i dati e aggiungere una soluzione di gestione per fornire informazioni analitiche dettagliate e dati aggiuntivi.  

* Per abilitare la raccolta di dati dalle risorse di Azure con Diagnostica di Azure o l'archiviazione di Azure, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](../platform/resource-logs.md#send-to-log-analytics-workspace).  
* Aggiungere [System Center Operations Manager come origine dati](../platform/om-agents.md) per raccogliere i dati da agenti di creazione report per il gruppo di gestione Operations Manager e archiviarli nell'area di lavoro Log Analytics.  
* Connettere [Configuration Manager](../platform/collect-sccm.md) per importare computer che sono membri di raccolte nella gerarchia.  
* Esaminare le [soluzioni di monitoraggio](../insights/solutions.md) disponibili e verificare come aggiungere o rimuovere una soluzione dall'area di lavoro.


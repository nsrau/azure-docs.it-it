---
title: Spostare una rete virtuale di Azure in un'altra area di Azure usando il portale di Azure.Move an Azure virtual network to another Azure region using the Azure portal.
description: Spostare una rete virtuale di Azure da un'area di Azure a un'altra usando un modello di Resource Manager e il portale di Azure.Move an Azure virtual network from an Azure region to another by using a Resource Manager template and the Azure portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640789"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Spostare una rete virtuale di Azure in un'altra area tramite il portale di AzureMove an Azure virtual network to another region by using the Azure portal

Esistono vari scenari per spostare una rete virtuale di Azure esistente da un'area a un'altra. Ad esempio, è possibile creare una rete virtuale con la stessa configurazione per il test e la disponibilità della rete virtuale esistente. In alternativa, è possibile spostare una rete virtuale di produzione in un'altra area come parte della pianificazione del ripristino di emergenza.

È possibile usare un modello di Azure Resource Manager per completare lo spostamento della rete virtuale in un'altra area. A tale scopo, esportare la rete virtuale in un modello, modificare i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area. Per altre informazioni sui modelli di Resource Manager, vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure.For more information about Resource Manager templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che la rete virtuale si trovi nell'area di Azure da cui si vuole spostare il numero.

- Per esportare una rete virtuale e distribuire un modello per creare una rete virtuale in un'altra area, è necessario disporre del ruolo Collaboratore di rete o versione successiva.

- I peering di rete virtuale non verranno ricreati e avranno esito negativo se sono ancora presenti nel modello. Prima di esportare il modello, è necessario rimuovere tutti i peer di rete virtuale. È quindi possibile ristabilirli dopo lo spostamento della rete virtuale.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato ai servizi di bilanciamento del carico, ai gruppi di sicurezza di rete (NSG) e agli indirizzi IP pubblici.

- Verificare che la sottoscrizione di Azure consenta di creare reti virtuali nell'area di destinazione. Per abilitare la quota richiesta, contattare il supporto tecnico.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di reti virtuali per questo processo. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Preparati per il trasloco
In questa sezione si prepara la rete virtuale per lo spostamento usando un modello di Resource Manager.In this section, you prepare the virtual network for the move by using a Resource Manager template. È quindi possibile spostare la rete virtuale nell'area di destinazione tramite il portale di Azure.Then move the virtual network to the target region by using the Azure portal.

Per esportare la rete virtuale e distribuire la rete virtuale di destinazione tramite il portale di Azure, eseguire le operazioni seguenti:To export the virtual network and deploy the target virtual network by using the Azure portal, do the following:

1. Accedere al [portale](https://portal.azure.com)di Azure e quindi selezionare **Gruppi di risorse**.
1. Individuare il gruppo di risorse che contiene la rete virtuale di origine e quindi selezionarlo.
1. Selezionare **Impostazioni** > **modello di esportazione**.
1. Nel riquadro **Esporta modello** selezionare **Distribuisci**.
1. Per aprire il file *parameters.json* nell'editor online, selezionare**Parametri di modifica** **modello** > .
1. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **value** in **parameters**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. Nell'editor modificare il valore del nome della rete virtuale di origine nell'editor con un nome desiderato per la rete virtuale di destinazione. Assicurarsi di racchiudere il nome tra virgolette.

1. Selezionare **Salva** nell'editor.

1. Per aprire il file *template.json* nell'editor online, selezionare **Template** > **Edit template .**

1. Nell'editor online, per modificare l'area di destinazione in cui verrà spostata la rete virtuale, modificare la proprietà **location** in **resources**:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. Per ottenere i codici di ubicazione, vedere [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area, senza spazi (ad esempio, **Central US** = **centralus**).

1. (Facoltativo) È inoltre possibile modificare altri parametri nel modello, a seconda dei requisiti:

    * **Spazio indirizzo**: prima di salvare il file, è possibile modificare lo spazio degli indirizzi della rete virtuale modificando la sezione**addressSpace** delle **risorse** > e modificando la proprietà **addressPrefixes:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Subnet**: È possibile modificare o aggiungere il nome della subnet e lo spazio degli indirizzi della subnet modificando la **sezione** subnet del modello. È possibile modificare il nome della subnet modificando la proprietà **name.** È inoltre possibile modificare lo spazio degli indirizzi della subnet modificando la proprietà **addressPrefix:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Per modificare il prefisso dell'indirizzo nel file *template.json,* modificarlo in due posizioni: nel codice nella sezione precedente e nella sezione **type** del codice seguente. Modificare la proprietà **addressPrefix** nel codice seguente in modo che corrisponda alla proprietà **addressPrefix** nel codice della sezione precedente.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Nell'editor online selezionare **Salva**.

1. Per scegliere la sottoscrizione in cui verrà distribuita la rete virtuale di destinazione, selezionare**Sottoscrizione** **di base** > .

1. Per scegliere il gruppo di risorse in cui verrà distribuita la rete virtuale di destinazione, selezionare**Gruppo di risorse**di **base** > . 

    Se è necessario creare un nuovo gruppo di risorse per la rete virtuale di destinazione, selezionare **Crea nuovo**. Assicurarsi che il nome non sia uguale al nome del gruppo di risorse di origine nella rete virtuale esistente.

1. Verificare che**Percorso** **di** > base sia impostato sul percorso di destinazione in cui si vuole distribuire la rete virtuale.

1. In **Impostazioni**verificare che il nome corrisponda al nome immesso in precedenza nell'editor dei parametri.

1. Selezionare la casella di **controllo Termini e condizioni.**

1. Per distribuire la rete virtuale di destinazione, selezionare **Acquista**.

## <a name="delete-the-target-virtual-network"></a>Eliminare la rete virtuale di destinazioneDelete the target virtual network

Per eliminare la rete virtuale di destinazione, eliminare il gruppo di risorse che contiene la rete virtuale di destinazione. A tale scopo, procedere come indicato di seguito:
1. Nel dashboard del portale di Azure selezionare il gruppo di risorse.
1. Nella parte superiore del riquadro **Panoramica** selezionare **Elimina**.

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eliminare la rete virtuale di origine o il gruppo di risorse. A tale scopo, procedere come indicato di seguito:
1. Nel dashboard del portale di Azure selezionare la rete virtuale o il gruppo di risorse.
1. Nella parte superiore di ogni riquadro selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una rete virtuale di Azure da un'area a un'altra usando il portale di Azure e quindi è stata pulita le risorse di origine non necessarie. Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, see:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare le macchine virtuali di Azure in un'altra areaMove Azure virtual machines to another region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

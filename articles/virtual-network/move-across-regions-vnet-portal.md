---
title: Spostare una rete virtuale di Azure in un'altra area di Azure usando il portale di Azure.
description: Spostare una rete virtuale di Azure da un'area di Azure a un'altra usando un modello di Gestione risorse e il portale di Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d6f417e53e7d7a1a242a0c0dc56c2356f78f5344
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828958"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Spostare una rete virtuale di Azure in un'altra area usando il portale di Azure

Esistono diversi scenari per lo trasferimento di una rete virtuale di Azure esistente da un'area a un'altra. Ad esempio, potrebbe essere necessario creare una rete virtuale con la stessa configurazione per il test e la disponibilità della rete virtuale esistente. In alternativa, potrebbe essere necessario spostare una rete virtuale di produzione in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

È possibile usare un modello di Azure Resource Manager per completare lo spostamento della rete virtuale in un'altra area. A tale scopo, esportare la rete virtuale in un modello, modificare i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area. Per ulteriori informazioni sui modelli di Gestione risorse, vedere [Quickstart: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che la rete virtuale si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Per esportare una rete virtuale e distribuire un modello per creare una rete virtuale in un'altra area, è necessario avere il ruolo Collaboratore rete o versione successiva.

- I peering di rete virtuale non verranno ricreati e avranno esito negativo se sono ancora presenti nel modello. Prima di esportare il modello, è necessario rimuovere tutti i peer di rete virtuale. Sarà quindi possibile ristabilirli dopo lo spostamento della rete virtuale.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include, tuttavia, i bilanciamenti del carico, i gruppi di sicurezza di rete (gruppi) e gli indirizzi IP pubblici.

- Verificare che la sottoscrizione di Azure consenta di creare reti virtuali nell'area di destinazione. Per abilitare la quota richiesta, contattare il supporto tecnico.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di reti virtuali per questo processo. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Preparare lo spostamento
In questa sezione si prepara la rete virtuale per lo spostamento usando un modello di Gestione risorse. Si sposta quindi la rete virtuale nell'area di destinazione usando il portale di Azure.

Per esportare la rete virtuale e distribuire la rete virtuale di destinazione usando il portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com), quindi selezionare **gruppi di risorse**.
1. Individuare il gruppo di risorse che contiene la rete virtuale di origine e quindi selezionarlo.
1. Selezionare **Impostazioni** > **Esporta modello**.
1. Nel riquadro **Esporta modello** selezionare **Distribuisci**.
1. Per aprire il file *Parameters. JSON* nell'editor online, selezionare **modello** > **modifica parametri**.
1. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **value** in **Parameters**:

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

1. Nell'editor, modificare il valore del nome della rete virtuale di origine nell'editor con il nome desiderato per la rete virtuale di destinazione. Assicurarsi di racchiudere il nome tra virgolette.

1. Selezionare **Save (Salva** ) nell'editor.

1. Per aprire il file *template. JSON* nell'editor online, selezionare **template** > **Edit template**.

1. Nell'editor online per modificare l'area di destinazione in cui verrà spostata la rete virtuale, modificare la proprietà **location** in **Resources**:

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

1. Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area, senza spazi, ad esempio **Stati Uniti centrali** = **centralus**.

1. Opzionale È anche possibile modificare altri parametri nel modello, a seconda dei requisiti:

    * **Spazio degli indirizzi**: Prima di salvare il file, è possibile modificare lo spazio degli indirizzi della rete virtuale modificando la sezione **resources** > **addressSpace** e modificando la proprietà **addressPrefixes** :

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

    * **Subnet**: È possibile modificare o aggiungere al nome della subnet e allo spazio di indirizzi della subnet modificando la sezione **subnet** del modello. È possibile modificare il nome della subnet modificando la proprietà **Name** . È possibile modificare lo spazio degli indirizzi della subnet modificando la proprietà **addressPrefix** :

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

        Per modificare il prefisso dell'indirizzo nel file *template. JSON* , modificarlo in due posizioni: nel codice della sezione precedente e nella sezione **Type** del codice seguente. Modificare la proprietà **addressPrefix** nel codice seguente in modo che corrisponda alla proprietà **addressPrefix** nel codice della sezione precedente.

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

1. Per scegliere la sottoscrizione in cui verrà distribuita la rete virtuale di destinazione, selezionare **nozioni di base** > **sottoscrizione**.

1. Per scegliere il gruppo di risorse in cui verrà distribuita la rete virtuale di destinazione, selezionare **nozioni di base** > **gruppo di risorse**. 

    Se è necessario creare un nuovo gruppo di risorse per la rete virtuale di destinazione, selezionare **Crea nuovo**. Verificare che il nome non sia uguale al nome del gruppo di risorse di origine nella rete virtuale esistente.

1. Verificare che i **concetti di base** > **percorso** siano impostati sul percorso di destinazione in cui si desidera distribuire la rete virtuale.

1. In **Impostazioni**verificare che il nome corrisponda al nome immesso in precedenza nell'editor dei parametri.

1. Selezionare la casella di controllo **termini e condizioni** .

1. Per distribuire la rete virtuale di destinazione, selezionare **Acquista**.

## <a name="delete-the-target-virtual-network"></a>Eliminare la rete virtuale di destinazione

Per rimuovere la rete virtuale di destinazione, eliminare il gruppo di risorse che contiene la rete virtuale di destinazione. A tale scopo:
1. Nel dashboard portale di Azure selezionare il gruppo di risorse.
1. Nella parte superiore del riquadro **Panoramica** selezionare **Elimina**.

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eliminare la rete virtuale o il gruppo di risorse di origine. A tale scopo:
1. Nel dashboard portale di Azure selezionare la rete virtuale o il gruppo di risorse.
1. Nella parte superiore di ogni riquadro selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una rete virtuale di Azure da un'area a un'altra usando il portale di Azure e quindi puliti le risorse di origine non necessarie. Per ulteriori informazioni sullo stato di trasferimento delle risorse tra le aree e il ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

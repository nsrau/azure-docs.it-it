---
title: Spostare la rete virtuale di Azure in un'altra area di Azure usando il portale di Azure.
description: Usare il modello di Azure Resource Manager per spostare la rete virtuale di Azure da un'area di Azure a un'altra usando il portale di Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d0f8a52eace9af4dc0865e1a2f277b9ab31c6858
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997419"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Spostare la rete virtuale di Azure in un'altra area usando il portale di Azure

Esistono diversi scenari in cui si vuole spostare le reti virtuali di Azure esistenti (reti virtuali) da un'area a un'altra. Ad esempio, è possibile creare una rete virtuale con la stessa configurazione per il test e la disponibilità della rete virtuale esistente. Potrebbe anche essere necessario spostare una rete virtuale di produzione in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

È possibile usare un modello di Azure Resource Manager per completare lo spostamento della rete virtuale in un'altra area. A tale scopo, esportare la rete virtuale in un modello, modificare i parametri in modo che corrispondano all'area di destinazione e quindi distribuire il modello nella nuova area.  Per ulteriori informazioni su Gestione risorse e sui modelli, vedere [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che la rete virtuale di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Per esportare una rete virtuale e distribuire un modello per creare una rete virtuale in un'altra area, è necessario il ruolo Collaboratore rete o versione successiva.

- I peering di rete virtuale non verranno ricreati e avranno esito negativo se sono ancora presenti nel modello.  È necessario rimuovere tutti i peer di rete virtuale prima di esportare il modello e ristabilire i peer dopo lo spostamento della rete virtuale.
    
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include, tuttavia, i bilanciamenti del carico, i gruppi di sicurezza di rete (gruppi) e gli indirizzi IP pubblici.

- Verificare che la sottoscrizione di Azure consenta di creare reti virtuali nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di reti virtuali per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare la rete virtuale per lo spostamento usando un modello di Gestione risorse e spostare la rete virtuale nell'area di destinazione usando il portale.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Esportare il modello e distribuirlo dal portale

1. Accedere ai**gruppi di risorse** [portale di Azure](http://portal.azure.com) > .
2. Individuare il gruppo di risorse che contiene la rete virtuale di origine e fare clic su di esso.
3. Selezionare > **Impostazioni** > **Esporta modello**.
4. Scegliere **Distribuisci** nel pannello **Esporta modello** .
5. Fare clic su **modello** > **modifica parametri** per aprire il file **Parameters. JSON** nell'editor online.
6. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **value** in **Parameters**:
    
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
7. Modificare il valore del nome della rete virtuale di origine nell'editor con il nome desiderato per il VNET di destinazione. Assicurarsi di racchiudere il nome tra virgolette.

8.  Fare clic su **Salva** nell'editor.

9.  Fare clic **su modello** **modifica modello** per aprire il file **template. JSON** nell'editor online. >  

10. Per modificare l'area di destinazione in cui verrà spostato il VNET, modificare la proprietà **location** in **Resources** nell'editor online:

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
 
11. Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per un'area è il nome dell'area senza spazi, Central **Stati Uniti** = **centrali.**
 
12. È anche possibile modificare altri parametri nel modello, se si sceglie, e sono facoltativi in base ai requisiti:

    * **Spazio degli indirizzi** : lo spazio degli indirizzi di VNET può essere modificato nel modello prima del salvataggio modificando la sezione **Resources** > **addressSpace** e modificando la proprietà **addressPrefixes** in **template. JSON.** file:
    
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

    * **Subnet** : il nome della subnet e lo spazio degli indirizzi della subnet possono essere modificati o aggiunti a modificando la sezione **Subnet** del file **template. JSON** . Il nome della subnet può essere modificato modificando la proprietà **Name** nel file **template. JSON** .  Lo spazio degli indirizzi della subnet può essere modificato modificando la proprietà **addressPrefix** nel file **template. JSON** :
    
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
                    ],
    ```

    Nel file **template. JSON** , per modificare il prefisso dell'indirizzo, è necessario modificarlo in due posizioni, la sezione sopra indicata e la sezione **Type** elencata di seguito.  Modificare la proprietà **addressPrefix** in modo che corrisponda a quella precedente:
                
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

13. Fare clic su **Salva** nell'editor online.

14. Fare clic su**sottoscrizione** di **base** > per scegliere la sottoscrizione in cui verrà distribuita la VNET di destinazione.

15. Fare clic su**gruppo di risorse** **nozioni di base** > per scegliere il gruppo di risorse in cui verrà distribuito il VNET di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per il VNET di destinazione.  Verificare che il nome non sia uguale al gruppo di risorse di origine del VNET esistente. 

16. Il**percorso** di **base** > della verifica è impostato sul percorso di destinazione in cui si desidera distribuire il vnet.

17. Verificare in **Impostazioni** che il nome corrisponda al nome immesso nell'editor di parametri precedente.

18. Selezionare la casella in **termini e condizioni**.

19. Fare clic sul pulsante **Acquista** per distribuire la rete virtuale di destinazione.

## <a name="discard"></a>Annulla 

Se si vuole rimuovere la rete virtuale di destinazione, eliminare il gruppo di risorse che contiene la rete virtuale di destinazione.  A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore della pagina Overview (panoramica).

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale, eliminare la rete virtuale o il gruppo di risorse di origine. A tale scopo, selezionare la rete virtuale o il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una rete virtuale di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo trasferimento di risorse tra aree e ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

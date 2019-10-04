---
title: Spostare Load Balancer interni di Azure in un'altra area di Azure usando il portale di Azure
description: Usare il modello di Azure Resource Manager per spostare Load Balancer interni di Azure da un'area di Azure a un'altra usando il portale di Azure
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 88aedb97f659725887026d0c83be88cbde27ae4f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219671"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Spostare Load Balancer interni di Azure in un'altra area usando il portale di Azure

Esistono diversi scenari in cui si vuole spostare il servizio di bilanciamento del carico interno esistente da un'area a un'altra. Ad esempio, è possibile creare un servizio di bilanciamento del carico interno con la stessa configurazione per il test. Potrebbe anche essere necessario spostare un servizio di bilanciamento del carico interno in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

I bilanciamenti del carico interno di Azure non possono essere spostati da un'area all'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente e la rete virtuale di un servizio di bilanciamento del carico interno.  È quindi possibile organizzare la risorsa in un'altra area esportando il servizio di bilanciamento del carico e la rete virtuale in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire i modelli nella nuova area.  Per ulteriori informazioni su Gestione risorse e sui modelli, [vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Prerequisiti

- Verificare che il servizio di bilanciamento del carico interno di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- Non è possibile spostare i bilanciamenti del carico interno di Azure tra le aree.  È necessario associare il nuovo servizio di bilanciamento del carico alle risorse nell'area di destinazione.

- Per esportare una configurazione del servizio di bilanciamento del carico interno e distribuire un modello per creare un servizio di bilanciamento del carico interno in un'altra area, è necessario il ruolo Collaboratore rete o versione successiva.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a bilanciamento del carico, gruppi di sicurezza di rete, macchine virtuali e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare bilanciamenti del carico interno nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta dei bilanciamenti del carico per questo processo.  Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare il servizio di bilanciamento del carico interno per lo spostamento usando un modello di Gestione risorse e spostare la configurazione del servizio di bilanciamento del carico interno nell'area di destinazione usando il portale di Azure.  Come parte di questo processo, la configurazione della rete virtuale del servizio di bilanciamento del carico interno deve essere inclusa e deve essere eseguita prima di trasferire il servizio di bilanciamento del carico interno.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Esportare il modello di rete virtuale e distribuirlo dalla portale di Azure

1. Accedere ai**gruppi di risorse** [portale di Azure](https://portal.azure.com) > .
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

8. Fare clic su **Salva** nell'editor.

9. Fare clic **su modello** **modifica modello** per aprire il file **template. JSON** nell'editor online. > 

10. Per modificare l'area di destinazione in cui verrà spostato il VNET, modificare la proprietà **location** in Resources:

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

12. È anche possibile modificare altri parametri nel file **template. JSON** se si sceglie e sono facoltativi in base ai requisiti:

    * **Spazio degli indirizzi** : lo spazio degli indirizzi di VNET può essere modificato prima del salvataggio modificando la sezione **Resources** > **addressSpace** e modificando la proprietà **addressPrefixes** nel file **template. JSON** :

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

    * **Subnet** : il nome della subnet e lo spazio degli indirizzi della subnet possono essere modificati o aggiunti a modificando la sezione **Subnet** del file **template. JSON** . Il nome della subnet può essere modificato modificando la proprietà **Name** . Lo spazio degli indirizzi della subnet può essere modificato modificando la proprietà **addressPrefix** nel file **template. JSON** :

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

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Esportare il modello del servizio di bilanciamento del carico interno e distribuirlo da Azure PowerShell

1. Accedere ai**gruppi di risorse** [portale di Azure](https://portal.azure.com) > .
2. Individuare il gruppo di risorse che contiene il servizio di bilanciamento del carico interno di origine e fare clic su di esso.
3. Selezionare > **Impostazioni** > **Esporta modello**.
4. Scegliere **Distribuisci** nel pannello **Esporta modello** .
5. Fare clic su **modello** > **modifica parametri** per aprire il file **Parameters. JSON** nell'editor online.

6. Per modificare il parametro del nome del servizio di bilanciamento del carico interno, modificare la proprietà **DefaultValue** del nome del servizio di bilanciamento del carico interno di origine con il nome del servizio di bilanciamento del carico interno di destinazione, verificare che il nome sia racchiuso tra virgolette:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Per modificare il valore della rete virtuale di destinazione spostata sopra, è innanzitutto necessario ottenere l'ID risorsa e quindi copiarlo e incollarlo nel file **Parameters. JSON** . Per ottenere l'ID:

    1. Accedere ai**gruppi di risorse** [portale di Azure](https://portal.azure.com) > in un'altra scheda o finestra del browser.
    2. Individuare il gruppo di risorse di destinazione che contiene la rete virtuale spostata nei passaggi precedenti e fare clic su di essa.
    3. Selezionare >**Proprietà** **Impostazioni** > .
    4. Nel pannello a destra evidenziare l' **ID risorsa** e copiarlo negli Appunti.  In alternativa, è possibile fare clic sul pulsante **copia negli Appunti** a destra del percorso dell' **ID risorsa** .
    5. Incollare l'ID risorsa nella proprietà **DefaultValue** nell'editor **modifica parametri** aperto nell'altra finestra o scheda del browser:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Fare clic su **Salva** nell'editor online.

7. Fare clic **su modello** **modifica modello** per aprire il file **template. JSON** nell'editor online. > 
8. Per modificare l'area di destinazione in cui verrà spostata la configurazione del servizio di bilanciamento del carico interno, modificare la proprietà **location** in **Resources** nel file **template. JSON** :

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per un'area è il nome dell'area senza spazi, Central **Stati Uniti** = **centrali.**

10. È anche possibile modificare altri parametri nel modello, se si sceglie, e sono facoltativi in base ai requisiti:

    * **SKU** : è possibile modificare lo SKU del servizio di bilanciamento del carico interno nella configurazione da standard a Basic o Basic a standard modificando la proprietà **SKU** > **Name** nel file **template. JSON** :

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Per altre informazioni sulle differenze tra i bilanciamenti del carico SKU Basic e standard, vedere [Panoramica di Azure Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Regole di bilanciamento del carico** : è possibile aggiungere o rimuovere le regole di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella sezione **loadBalancingRules** del file **template. JSON** :

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Per ulteriori informazioni sulle regole di bilanciamento del carico, vedere [che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Probe** : è possibile aggiungere o rimuovere un probe per il servizio di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella sezione **Probe** del file **template. JSON** :

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Per altre informazioni sui Probe di integrità di Azure Load Balancer, vedere [Probe di integrità Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Regole NAT in ingresso** : è possibile aggiungere o rimuovere le regole NAT in ingresso per il servizio di bilanciamento del carico aggiungendo o rimuovendo le voci nella sezione **inboundNatRules** del file **template. JSON** :

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Per completare l'aggiunta o la rimozione di una regola NAT in ingresso, è necessario che la regola sia presente o rimossa come proprietà del **tipo** alla fine del file **template. JSON** :

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Per ulteriori informazioni sulle regole NAT in ingresso, vedere [che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. Fare clic su **Salva** nell'editor online.

13. Fare clic su**sottoscrizione** di **base** > per scegliere la sottoscrizione in cui verrà distribuito il servizio di bilanciamento del carico interno di destinazione.

15. Fare clic su**gruppo di risorse** di **base** > per scegliere il gruppo di risorse in cui verrà distribuito il servizio di bilanciamento del carico di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per il servizio di bilanciamento del carico interno di destinazione oppure scegliere il gruppo di risorse esistente creato in precedenza per la rete virtuale.  Verificare che il nome non sia uguale al gruppo di risorse di origine del servizio di bilanciamento del carico interno di origine esistente.

16. Il**percorso** di **base** > della verifica è impostato sul percorso di destinazione in cui si desidera distribuire il servizio di bilanciamento del carico interno.

17. Verificare in **Impostazioni** che il nome corrisponda al nome immesso nell'editor di parametri precedente.  Verificare che gli ID risorsa siano popolati per tutte le reti virtuali nella configurazione.

18. Selezionare la casella in **termini e condizioni**.

19. Fare clic sul pulsante **Acquista** per distribuire la rete virtuale di destinazione.

## <a name="discard"></a>Annulla

Se si vuole rimuovere la rete virtuale di destinazione e il servizio di bilanciamento del carico interno, eliminare il gruppo di risorse che contiene la rete virtuale di destinazione e il servizio di bilanciamento del carico interno.  A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore della pagina Overview (panoramica).

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale e del servizio di bilanciamento del carico interno, eliminare la rete virtuale di origine e il servizio di bilanciamento del carico interno o il gruppo di risorse. A tale scopo, selezionare la rete virtuale e il servizio di bilanciamento del carico interno o il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un servizio di bilanciamento del carico interno di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo trasferimento di risorse tra aree e ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

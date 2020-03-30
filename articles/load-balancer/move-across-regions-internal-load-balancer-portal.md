---
title: Spostare Azure Internal Load Balancer in un'altra area di Azure usando il portale di AzureMove Azure internal Load Balancer to another Azure region using the Azure portal
description: Usare il modello di Azure Resource Manager per spostare Azure Internal Load Balancer da un'area di Azure a un'altra usando il portale di AzureUse Azure Resource Manager template to move Azure internal Load Balancer from an Azure region to another using the Azure portal
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638808"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Spostare Azure Internal Load Balancer in un'altra area usando il portale di AzureMove Azure internal Load Balancer to another region using the Azure portal

Esistono vari scenari in cui si desidera spostare il servizio di bilanciamento del carico interno esistente da un'area a un'altra. Ad esempio, è possibile creare un servizio di bilanciamento del carico interno con la stessa configurazione per il test. È anche possibile spostare un servizio di bilanciamento del carico interno in un'altra area come parte della pianificazione del ripristino di emergenza.

I servizi di bilanciamento del carico interni di Azure non possono essere spostati da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente e la rete virtuale di un servizio di bilanciamento del carico interno.  È quindi possibile creare temporaneamente la risorsa in un'altra area esportando il servizio di bilanciamento del carico e la rete virtuale in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuire i modelli nella nuova area.  Per altre informazioni su Resource Manager e sui modelli, vedere Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure.For more information on Resource Manager and templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che il servizio di bilanciamento del carico interno di Azure si trovi nell'area di Azure da cui si vuole spostare.

- I servizi di bilanciamento del carico interni di Azure non possono essere spostati da un aree all'altro.  È necessario associare il nuovo servizio di bilanciamento del carico alle risorse nell'area di destinazione.

- Per esportare una configurazione del servizio di bilanciamento del carico interno e distribuire un modello per creare un servizio di bilanciamento del carico interno in un'altra area, è necessario il ruolo Collaboratore di rete o versione successiva.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a servizi di bilanciamento del carico, gruppi di sicurezza di rete, macchine virtuali e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare servizi di bilanciamento del carico interni nell'area di destinazione usata. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta di servizi di bilanciamento del carico per questo processo.  Vedere Limiti, quote e vincoli di sottoscrizione e servizio di [AzureSee Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Preparare e spostare
I passaggi seguenti illustrano come preparare il servizio di bilanciamento del carico interno per lo spostamento usando un modello di Resource Manager e spostare la configurazione del servizio di bilanciamento del carico interno nell'area di destinazione usando il portale di Azure.The following steps show how to prepare the internal load balancer for the move using a Resource Manager template, and move the internal load balancer configuration to the target region using the Azure portal.  Come parte di questo processo, la configurazione della rete virtuale del servizio di bilanciamento del carico interno deve essere inclusa e deve essere eseguita prima di spostare il servizio di bilanciamento del carico interno.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Esportare il modello di rete virtuale e distribuirlo dal portale di AzureExport the virtual network template and deploy from the Azure portal

1. Accedere a**Gruppi di risorse**del portale > di [Azure](https://portal.azure.com).
2. Individuare il gruppo di risorse che contiene la rete virtuale di origine e fare clic su di esso.
3. Selezionare >**modello di esportazione** **impostazioni** > .
4. Scegliere **Distribuisci** nel pannello **Esporta modello.**
5. Fare clic su **MODELLO** > **Modificare i parametri** per aprire il file **parameters.json** nell'editor online.
6. Per modificare il parametro del nome della rete virtuale, modificare la proprietà **value** in **parameters**:

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
7. Modificare il valore del nome della rete virtuale di origine nell'editor con un nome di scelta per la rete virtuale di destinazione. Assicurarsi di racchiudere il nome tra virgolette.

8. Fare clic su **Salva** nell'editor.

9. Fare clic su **MODELLO** > **Modifica modello** per aprire il file **template.json** nell'editor online.

10. Per modificare l'area di destinazione in cui verrà spostata la rete virtuale, modificare la proprietà **location** in risorse:

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

11. Per ottenere i codici di ubicazione, vedere [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per una regione è il nome dell'area senza spazi, **Central US** = **centralus**.

12. È inoltre possibile modificare altri parametri nel file **template.json,** se lo si desidera, e sono facoltativi a seconda dei requisiti:

    * **Spazio indirizzi** - Lo spazio degli indirizzi della rete virtuale può essere modificato prima di salvare modificando la sezione**addressSpace** **delle risorse** > e modificando la proprietà **addressPrefixes** nel file **template.json:**

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

    * **Subnet:** il nome della subnet e lo spazio degli indirizzi della subnet possono essere modificati o aggiunti modificando la sezione **subnets** del file **template.json.** Il nome della subnet può essere modificato modificando la proprietà **name.** Lo spazio degli indirizzi della subnet può essere modificato modificando la proprietà **addressPrefix** nel file **template.json:**

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

         Nel file **template.json,** per modificare il prefisso dell'indirizzo, è necessario modificarlo in due posizioni, la sezione elencata sopra e la sezione **del tipo** elencata di seguito.  Modificare la proprietà **addressPrefix** in modo che corrisponda a quella precedente:

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

14. Fare clic su**Sottoscrizione** **BASICS** > per scegliere la sottoscrizione in cui verrà distribuita la rete virtuale di destinazione.

15. Fare clic su Gruppo di**risorse** **BASICS** > per scegliere il gruppo di risorse in cui verrà distribuita la rete virtuale di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per la rete virtuale di destinazione.  Verificare che il nome non sia uguale al gruppo di risorse di origine della rete virtuale esistente.

16. Verificare**che** **Percorso BASICS** > sia impostato sul percorso di destinazione in cui si desidera distribuire il disco rigido virtuale.

17. Verificare in **CELLE** che il nome corrisponda al nome immesso nell'editor dei parametri precedente.

18. Selezionare la casella in **TERMINI E CONDIZIONI**.

19. Fare clic sul pulsante **Acquista** per distribuire la rete virtuale di destinazione.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Esportare il modello di bilanciamento del carico interno e distribuirlo da Azure PowerShellExport the internal load balancer template and deploy from Azure PowerShell

1. Accedere a**Gruppi di risorse**del portale > di [Azure](https://portal.azure.com).
2. Individuare il gruppo di risorse che contiene il servizio di bilanciamento del carico interno di origine e fare clic su di esso.
3. Selezionare >**modello di esportazione** **impostazioni** > .
4. Scegliere **Distribuisci** nel pannello **Esporta modello.**
5. Fare clic su **MODELLO** > **Modificare i parametri** per aprire il file **parameters.json** nell'editor online.

6. Per modificare il parametro del nome del servizio di bilanciamento del carico interno, modificare la proprietà **defaultValue** del nome del servizio di bilanciamento del carico interno di origine con il nome del servizio di bilanciamento del carico interno di destinazione, verificare che il nome sia tra virgolette:

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

6. Per modificare il valore della rete virtuale di destinazione spostata in precedenza, è necessario innanzitutto ottenere l'ID risorsa, quindi copiarlo e incollarlo nel file **parameters.json.** Per ottenere l'ID:

    1. Accedere > **ai gruppi di risorse** del portale di [Azure](https://portal.azure.com)in un'altra scheda o finestra del browser.
    2. Individuare il gruppo di risorse di destinazione che contiene la rete virtuale spostata dai passaggi precedenti e fare clic su di esso.
    3. Selezionare >**Proprietà** **impostazioni** > .
    4. Nel pannello a destra, evidenziare **l'ID risorsa** e copiarlo negli Appunti.  In alternativa, è possibile fare clic sul pulsante **Copia negli Appunti** a destra del percorso ID **risorsa.**
    5. Incollare l'ID risorsa nella proprietà **defaultValue** nell'editor **Modifica parametri** aperto nell'altra finestra del browser o scheda:

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

7. Fare clic su **MODELLO** > **Modifica modello** per aprire il file **template.json** nell'editor online.
8. Per modificare l'area di destinazione in cui verrà spostata la configurazione del servizio di bilanciamento del carico interno, modificare la proprietà location in resources nel file **template.json:To** edit the target region where the internal load balancer configuration will be moved, change the **location** property under **resources** in the template.json file:

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

9.  Per ottenere i codici di ubicazione, vedere [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per una regione è il nome dell'area senza spazi, **Central US** = **centralus**.

10. È inoltre possibile modificare altri parametri nel modello, se lo si desidera, e sono facoltativi a seconda dei requisiti:

    * **Sku** - È possibile modificare lo sku del servizio di bilanciamento del carico interno nella configurazione da standard a basic o basic a standard modificando la proprietà del**nome** **sku** > nel file **template.json:**

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
      Per altre informazioni sulle differenze tra i servizi di bilanciamento del carico sku di base e standard, vedere Panoramica di [Azure Standard Load BalancerFor](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) more information on the differences between basic and standard sku load balancers, see Azure Standard Load Balancer overview

    * **Regole di bilanciamento del carico:** è possibile aggiungere o rimuovere regole di bilanciamento del carico nella configurazione aggiungendo o rimuovendo voci nella sezione **loadBalancingRules** del file **template.json:**

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
       Per altre informazioni sulle regole di bilanciamento del carico, vedere [Che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Probe** - È possibile aggiungere o rimuovere un probe per il servizio di bilanciamento del carico nella configurazione aggiungendo o rimuovendo voci nella sezione **probes** del file **template.json:**

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
       Per altre informazioni sui probe di integrità di Azure Load Balancer, vedere Test di integrità di Load BalancerFor more information on Azure Load Balancer health [probes,](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) see Load Balancer health probes

    * **Regole NAT in ingresso:** è possibile aggiungere o rimuovere regole NAT in ingresso per il servizio di bilanciamento del carico aggiungendo o rimuovendo voci nella sezione **inboundNatRules** del file **template.json:**

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
        Per completare l'aggiunta o la rimozione di una regola NAT in ingresso, la regola deve essere presente o rimossa come proprietà **di tipo** alla fine del file **template.json:**

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
        Per altre informazioni sulle regole NAT in ingresso, vedere [Che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. Fare clic su **Salva** nell'editor online.

13. Fare clic su**Sottoscrizione** **BASICS** > per scegliere la sottoscrizione in cui verrà distribuito il servizio di bilanciamento del carico interno di destinazione.

15. Fare clic su**Gruppo di risorse** **BASICS** > per scegliere il gruppo di risorse in cui verrà distribuito il servizio di bilanciamento del carico di destinazione.  È possibile fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse per il servizio di bilanciamento del carico interno di destinazione o scegliere il gruppo di risorse esistente creato in precedenza per la rete virtuale.  Verificare che il nome non sia uguale al gruppo di risorse di origine del servizio di bilanciamento del carico interno di origine esistente.

16. Verificare**che** **Percorso BASICS** > sia impostato sul percorso di destinazione in cui si vuole distribuire il servizio di bilanciamento del carico interno.

17. Verificare in **CELLE** che il nome corrisponda al nome immesso nell'editor dei parametri precedente.  Verificare che gli ID di risorsa siano popolati per tutte le reti virtuali nella configurazione.

18. Selezionare la casella in **TERMINI E CONDIZIONI**.

19. Fare clic sul pulsante **Acquista** per distribuire la rete virtuale di destinazione.

## <a name="discard"></a>Discard

Se si desidera eliminare la rete virtuale di destinazione e il servizio di bilanciamento del carico interno, eliminare il gruppo di risorse che contiene la rete virtuale di destinazione e il servizio di bilanciamento del carico interno.  A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore della pagina di panoramica.

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento della rete virtuale e del servizio di bilanciamento del carico interno, eliminare la rete virtuale di origine e il servizio di bilanciamento del carico interno o il gruppo di risorse. A tale scopo, selezionare la rete virtuale e il servizio di bilanciamento del carico interno o il gruppo di risorse dal dashboard nel portale e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un servizio di bilanciamento del carico interno di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

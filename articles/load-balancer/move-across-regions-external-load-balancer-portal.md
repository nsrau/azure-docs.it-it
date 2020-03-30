---
title: Spostare un servizio di bilanciamento del carico esterno di Azure in un'altra area di Azure tramite il portale di AzureMove an Azure external load balancer to another Azure region by using the Azure portal
description: Usare un modello di Azure Resource Manager per spostare un servizio di bilanciamento del carico esterno da un'area di Azure a un'altra usando il portale di Azure.Use an Azure Resource Manager template to move an external load balancer from an Azure region to another by using the Azure portal.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638528"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Spostare un servizio di bilanciamento del carico esterno in un'altra area tramite il portale di AzureMove an external load balancer to another region by using the Azure portal

Esistono vari scenari in cui si desidera spostare un servizio di bilanciamento del carico esterno da un'area a un'altra. Ad esempio, è possibile creare un altro servizio di bilanciamento del carico esterno con la stessa configurazione per il test. È anche possibile spostare un servizio di bilanciamento del carico esterno in un'altra area come parte della pianificazione del ripristino di emergenza.

In senso letterale, non è possibile spostare un servizio di bilanciamento del carico esterno di Azure da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente e l'indirizzo IP pubblico di un servizio di bilanciamento del carico esterno. È quindi possibile gestire temporaneamente la risorsa in un'altra area esportando il servizio di bilanciamento del carico e l'indirizzo IP pubblico in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuendo il modello nella nuova area. Per ulteriori informazioni su Resource Manager e sui modelli, vedere [Esportare gruppi](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)di risorse in modelli .


## <a name="prerequisites"></a>Prerequisiti

- Verificare che il servizio di bilanciamento del carico esterno di Azure si trovi nell'area di Azure da cui si vuole spostarsi.

- I servizi di bilanciamento del carico esterni di Azure non possono essere spostati da un aree all'altro. È necessario associare il nuovo servizio di bilanciamento del carico alle risorse nell'area di destinazione.

- Per esportare una configurazione del servizio di bilanciamento del carico esterno e distribuire un modello per creare un servizio di bilanciamento del carico esterno in un'altra area, è necessario disporre del ruolo Collaboratore rete o versione successiva.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a servizi di bilanciamento del carico, gruppi di sicurezza di rete, indirizzi IP pubblici e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare servizi di bilanciamento del carico esterno nell'area di destinazione. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta dei servizi di bilanciamento del carico. Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Preparare e spostare
Le procedure seguenti illustrano come preparare il servizio di bilanciamento del carico esterno per lo spostamento usando un modello di Resource Manager e spostare la configurazione del servizio di bilanciamento del carico esterno nell'area di destinazione tramite il portale di Azure.The following procedures show how to prepare the external load balancer for the move by using a Resource Manager template and move the external load balancer configuration to the target region by using the Azure portal. È innanzitutto necessario esportare la configurazione IP pubblica del servizio di bilanciamento del carico esterno.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Esportare il modello IP pubblico e distribuire l'IP pubblico dal portale

1. Accedere al [portale](https://portal.azure.com) di Azure e selezionare **Gruppi di**risorse .
2. Individuare il gruppo di risorse che contiene l'indirizzo IP pubblico di origine e selezionarlo.
3. Selezionare **Impostazioni** > **modello di esportazione**.
4. Selezionare **Distribuisci** in **Esporta modello**.
5. Selezionare **TEMPLATE** > **Modificare i parametri** per aprire il file parameters.json nell'editor online.
8. Per modificare il parametro del nome IP pubblico, modificare la proprietà **del valore** in **parametri** dal nome IP pubblico di origine al nome dell'indirizzo IP pubblico di destinazione. Racchiudere il nome tra virgolette.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Selezionare **Salva** nell'editor.

9.  Selezionare TEMPLATE Edit template **(Modifica** > **modello)** per aprire il file template.json nell'editor online.

10. Per modificare l'area di destinazione in cui verrà spostato l'IP pubblico, modificare la proprietà **location** in **resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    Per ottenere i codici di ubicazione delle aree, vedere [Percorsi](https://azure.microsoft.com/global-infrastructure/locations/)di Azure . Il codice per un'area è il nome dell'area senza spazi. Ad esempio, il codice per Stati Uniti centrali è **centralus**.
    
12. È inoltre possibile modificare altri parametri nel modello se si desidera o è necessario, a seconda dei requisiti:

    * **SKU**. È possibile modificare lo SKU dell'IP pubblico nella configurazione da standard a base o da basic a standard modificando la proprietà **name** in **sku** nel file template.json:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Per informazioni sulle differenze tra gli indirizzi IP pubblici dello SKU di base e standard, vedere [Creare, modificare o eliminare un indirizzo IP pubblico.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

    * **Metodo di allocazione IP pubblico** e **timeout di inattività**. È possibile modificare il metodo di allocazione IP pubblico modificando la proprietà **publicIPAllocationMethod** da **Dynamic** a **Static** o da **Static** a **Dynamic**. È possibile modificare il timeout di inattività impostando la proprietà **idleTimeoutInMinutes** sul valore desiderato. Il valore predefinito è **4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Per informazioni sui metodi di allocazione e sui valori di timeout inattivi, vedere [Creare, modificare o eliminare un indirizzo IP pubblico.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

 
13. Selezionare **Salva** nell'editor online.

14. Selezionare**Sottoscrizione** **BASICS** > per scegliere la sottoscrizione in cui verrà distribuito l'indirizzo IP pubblico di destinazione.

15. Selezionare Gruppo di**risorse** **BASICS** > per scegliere il gruppo di risorse in cui verrà distribuito l'indirizzo IP pubblico di destinazione. È possibile selezionare **Crea nuovo** per creare un nuovo gruppo di risorse per l'indirizzo IP pubblico di destinazione. Assicurarsi che il nome non sia uguale al gruppo di risorse di origine dell'indirizzo IP pubblico di origine esistente.

16. Verificare che **BasicS** > **Location** sia impostato sul percorso di destinazione in cui si desidera distribuire l'indirizzo IP pubblico.

17. In **IMPOSTAMENTI**verificare che il nome corrisponda al nome immesso in precedenza nell'editor dei parametri.

18. Selezionare la casella di controllo **TERMINI E CONDIZIONI.**

19. Selezionare **Acquista** per distribuire l'IP pubblico di destinazione.

20. Se si dispone di un altro indirizzo IP pubblico utilizzato per NAT in uscita per il servizio di bilanciamento del carico spostato, ripetere i passaggi precedenti per esportare e distribuire il secondo ip pubblico in uscita nell'area di destinazione.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Esportare il modello di bilanciamento del carico esterno e distribuire il servizio di bilanciamento del carico dal portale di AzureExport the external load balancer template and deploy the load balancer from the Azure portal

1. Accedere al [portale](https://portal.azure.com) di Azure e selezionare **Gruppi di**risorse .
2. Individuare il gruppo di risorse che contiene il servizio di bilanciamento del carico esterno di origine e selezionarlo.
3. Selezionare **Impostazioni** > **modello di esportazione**.
4. Selezionare **Distribuisci** in **Esporta modello**.
5. Selezionare **TEMPLATE** > **Modificare i parametri** per aprire il file parameters.json nell'editor online.

5. Per modificare il parametro del nome del servizio di bilanciamento del carico esterno, modificare la proprietà **value** del nome del servizio di bilanciamento del carico esterno di origine con il nome del servizio di bilanciamento del carico esterno di destinazione. Racchiudere il nome tra virgolette.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Per modificare il valore dell'ip pubblico di destinazione spostato nei passaggi precedenti, è necessario innanzitutto ottenere l'ID risorsa e quindi incollarlo nel file parameters.json. Per ottenere l'ID:

    1. In un'altra scheda o finestra del browser accedere al portale di [Azure](https://portal.azure.com) e selezionare **Gruppi di**risorse.
    2. Individuare il gruppo di risorse di destinazione che contiene l'indirizzo IP pubblico spostato nei passaggi precedenti. Selezionarlo.
    3. Selezionare**Proprietà** **impostazioni** > .
    4. Nel pannello a destra, evidenziare **l'ID risorsa** e copiarlo negli Appunti. In alternativa, è possibile selezionare **copia negli Appunti** a destra del percorso ID **risorsa.**
    5. Incollare l'ID risorsa nella proprietà **value** nell'editor **Modifica parametri** aperto nell'altra finestra o scheda del browser:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Selezionare **Salva** nell'editor online.


7.  Se sono state configurate le regole NAT in uscita e in uscita per il servizio di bilanciamento del carico, verrà visualizzata una terza voce in questo file per l'ID esterno dell'indirizzo IP pubblico in uscita. Ripetere i passaggi precedenti **nell'area** di destinazione per ottenere l'ID per l'indirizzo IP pubblico in uscita. Incollare l'ID nel file parameters.json:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Selezionare TEMPLATE Edit template **(Modifica** > **modello)** per aprire il file template.json nell'editor online.
9.  Per modificare l'area di destinazione in cui verrà spostata la configurazione del servizio di bilanciamento del carico esterno, modificare la proprietà location in resources nel file template.json:To edit the target region to which the external load balancer configuration will be moved, change the **location** property under **resources** in the template.json file:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Per ottenere i codici di ubicazione delle aree, vedere [Percorsi](https://azure.microsoft.com/global-infrastructure/locations/)di Azure . Il codice per un'area è il nome dell'area senza spazi. Ad esempio, il codice per Stati Uniti centrali è **centralus**.

11. È inoltre possibile modificare altri parametri nel modello se si desidera o è necessario, a seconda dei requisiti:

    * **SKU**. È possibile modificare lo SKU del servizio di bilanciamento del carico esterno nella configurazione da standard a base o da basico a standard modificando la proprietà name in sku nel file template.json:You can change the SKU of the external load balancer in the configuration from standard to basic or from basic to basic to standard by changing the **name** property under **sku** in the template.json file:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Per informazioni sulle differenze tra i servizi di bilanciamento del carico SKU di base e standard, vedere Panoramica di Azure Standard Load Balancer.For information on the differences between basic and standard SKU load balancers, see [Azure Standard Load Balancer overview](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Regole di bilanciamento del carico**. È possibile aggiungere o rimuovere regole di bilanciamento del carico nella configurazione aggiungendo o rimuovendo voci nella sezione **loadBalancingRules** del file template.json:

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
       Per informazioni sulle regole di bilanciamento del carico, vedere [Che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Sonde**. È possibile aggiungere o rimuovere un probe per il servizio di bilanciamento del carico nella configurazione aggiungendo o rimuovendo voci nella sezione **probes** del file template.json:

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
       Per altre informazioni, vedere Test di integrità di Load Balancer .For more information, see [Load Balancer health probes](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Regole NAT in ingresso**. È possibile aggiungere o rimuovere regole NAT in ingresso per il servizio di bilanciamento del carico aggiungendo o rimuovendo voci nella sezione **inboundNatRules** del file template.json:

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
        Per completare l'aggiunta o la rimozione di una regola NAT in ingresso, la regola deve essere presente o rimossa come proprietà **di tipo** alla fine del file template.json:

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
        Per informazioni sulle regole NAT in ingresso, vedere [Che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Regole in uscita**. È possibile aggiungere o rimuovere regole in uscita nella configurazione modificando la proprietà outboundRules nel file template.json:You can add or remove outbound rules in the configuration by editing the **outboundRules** property in the template.json file:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Per ulteriori informazioni, vedere [Regole in uscita di Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Selezionare **Salva** nell'editor online.

13. Selezionare**Sottoscrizione** **BASICS** > per scegliere la sottoscrizione in cui verrà distribuito il servizio di bilanciamento del carico esterno di destinazione.

15. Selezionare**Gruppo di risorse** **BASICS** > per scegliere il gruppo di risorse in cui verrà distribuito il servizio di bilanciamento del carico di destinazione. È possibile selezionare **Crea nuovo** per creare un nuovo gruppo di risorse per il servizio di bilanciamento del carico esterno di destinazione. In alternativa, è possibile scegliere il gruppo di risorse esistente creato in precedenza per l'IP pubblico. Assicurarsi che il nome non sia uguale al gruppo di risorse di origine del servizio di bilanciamento del carico esterno di origine esistente.

16. Verificare che **BasicS** > **Location** sia impostato sul percorso di destinazione in cui si vuole distribuire il servizio di bilanciamento del carico esterno.

17. In **IMPOSTAMENTI**verificare che il nome corrisponda al nome immesso in precedenza nell'editor dei parametri. Verificare che gli ID risorsa siano popolati per tutti gli indirizzi IP pubblici nella configurazione.

18. Selezionare la casella di controllo **TERMINI E CONDIZIONI.**

19. Selezionare **Acquista** per distribuire l'IP pubblico di destinazione.

## <a name="discard"></a>Discard

Se si desidera eliminare l'indirizzo IP pubblico di destinazione e il servizio di bilanciamento del carico esterno, eliminare il gruppo di risorse che li contiene. A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e quindi selezionare **Elimina** nella parte superiore della pagina di panoramica.

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento dell'IP pubblico e del servizio di bilanciamento del carico esterno, eliminare l'IP pubblico di origine e il servizio di bilanciamento del carico esterno o il gruppo di risorse. A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e quindi selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un servizio di bilanciamento del carico esterno di Azure da un'area a un'altra e sono stati puliti le risorse di origine. Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, see:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

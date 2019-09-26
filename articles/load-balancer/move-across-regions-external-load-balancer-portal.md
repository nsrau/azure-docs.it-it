---
title: Spostare un servizio di bilanciamento del carico esterno di Azure in un'altra area di Azure usando il portale di Azure
description: Usare un modello di Azure Resource Manager per spostare un servizio di bilanciamento del carico esterno da un'area di Azure a un'altra usando il portale di Azure.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 9358d99c66b3b8e3d6988b1881e51c11848ad97b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300627"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Spostare un servizio di bilanciamento del carico esterno in un'altra area usando il portale di Azure

Esistono diversi scenari in cui si vuole spostare un servizio di bilanciamento del carico esterno da un'area all'altra. Ad esempio, potrebbe essere necessario creare un altro servizio di bilanciamento del carico esterno con la stessa configurazione per il test. Potrebbe anche essere necessario spostare un servizio di bilanciamento del carico esterno in un'altra area nell'ambito della pianificazione del ripristino di emergenza.

In senso letterale, non è possibile spostare un servizio di bilanciamento del carico esterno di Azure da un'area a un'altra. È tuttavia possibile usare un modello di Azure Resource Manager per esportare la configurazione esistente e l'indirizzo IP pubblico di un servizio di bilanciamento del carico esterno. È quindi possibile organizzare la risorsa in un'altra area esportando il servizio di bilanciamento del carico e l'indirizzo IP pubblico in un modello, modificando i parametri in modo che corrispondano all'area di destinazione e quindi distribuendo il modello nella nuova area. Per altre informazioni su Gestione risorse e sui modelli, vedere [esportare gruppi di risorse nei modelli](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Prerequisiti

- Verificare che il servizio di bilanciamento del carico esterno di Azure si trovi nell'area di Azure da cui si vuole eseguire lo spostamento.

- I bilanciamenti del carico esterno di Azure non possono essere spostati tra le aree. È necessario associare il nuovo servizio di bilanciamento del carico alle risorse nell'area di destinazione.

- Per esportare una configurazione del servizio di bilanciamento del carico esterno e distribuire un modello per creare un servizio di bilanciamento del carico esterno in un'altra area, è necessario assegnare il ruolo Collaboratore rete o superiore.

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, Questo layout include ma non è limitato a bilanciamento del carico, gruppi di sicurezza di rete, indirizzi IP pubblici e reti virtuali.

- Verificare che la sottoscrizione di Azure consenta di creare bilanciamenti del carico esterni nell'area di destinazione. Contattare il supporto tecnico per abilitare la quota necessaria.

- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare l'aggiunta dei bilanciamenti del carico. Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Preparare e spostare
Le procedure seguenti illustrano come preparare il servizio di bilanciamento del carico esterno per lo spostamento usando un modello di Gestione risorse e spostare la configurazione del servizio di bilanciamento del carico esterno nell'area di destinazione usando il portale di Azure. È necessario prima esportare la configurazione IP pubblico del servizio di bilanciamento del carico esterno.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Esportare il modello IP pubblico e distribuire l'indirizzo IP pubblico dal portale

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.
2. Individuare il gruppo di risorse che contiene l'indirizzo IP pubblico di origine e selezionarlo.
3. Selezionare **Impostazioni** > **Esporta modello**.
4. Selezionare **Distribuisci** in **Esporta modello**.
5. Selezionare **modello** > **modifica parametri** per aprire il file Parameters. JSON nell'editor online.
8. Per modificare il parametro del nome IP pubblico, modificare la proprietà **value** in **Parameters** dal nome IP pubblico di origine al nome dell'indirizzo IP pubblico di destinazione. Racchiudere il nome tra virgolette.

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

    Selezionare **Save (Salva** ) nell'editor.

9.  Selezionare **modello** > **modifica modello** per aprire il file template. JSON nell'editor online.

10. Per modificare l'area di destinazione in cui verrà spostato l'IP pubblico, modificare la proprietà **location** in **Resources**:

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
  
    Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi. Ad esempio, il codice per gli Stati Uniti centrali è **centralus**.
    
12. È anche possibile modificare altri parametri nel modello, se si vuole o necessario, a seconda dei requisiti:

    * **SKU**. È possibile modificare lo SKU dell'indirizzo IP pubblico nella configurazione da standard a Basic o da Basic a standard modificando la proprietà **Name** in **SKU** nel file template. JSON:

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

        Per informazioni sulle differenze tra gli indirizzi IP pubblici con SKU Basic e standard, vedere [creare, modificare o eliminare un indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Metodo di allocazione IP pubblico** e **timeout di inattività**. È possibile modificare il metodo di allocazione dell'indirizzo IP pubblico modificando la proprietà **publicIPAllocationMethod** da **dinamico** a **statico** o da **statico** a **dinamico**. È possibile modificare il timeout di inattività modificando la proprietà **idleTimeoutInMinutes** con il valore desiderato. Il valore predefinito è **4**.

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

        Per informazioni sui metodi di allocazione e i valori di timeout di inattività, vedere [creare, modificare o eliminare un indirizzo IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Selezionare **Save (Salva** ) nell'editor online.

14. Selezionare**sottoscrizione** di **base** > per scegliere la sottoscrizione in cui verrà distribuito l'IP pubblico di destinazione.

15. Selezionare**gruppo di risorse** **nozioni di base** > per scegliere il gruppo di risorse in cui verrà distribuito l'IP pubblico di destinazione. È possibile selezionare **Crea nuovo** per creare un nuovo gruppo di risorse per l'indirizzo IP pubblico di destinazione. Verificare che il nome non sia uguale al gruppo di risorse di origine dell'IP pubblico di origine esistente.

16. Verificare che il**percorso** di **base** > sia impostato sul percorso di destinazione in cui si vuole distribuire l'indirizzo IP pubblico.

17. In **Impostazioni**, verificare che il nome corrisponda al nome immesso in precedenza nell'editor dei parametri.

18. Selezionare la casella di controllo **termini e condizioni** .

19. Selezionare **Acquista** per distribuire l'indirizzo IP pubblico di destinazione.

20. Se è presente un altro IP pubblico usato per la NAT in uscita per lo spostamento del servizio di bilanciamento del carico, ripetere i passaggi precedenti per esportare e distribuire il secondo indirizzo IP pubblico in uscita nell'area di destinazione.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Esportare il modello del servizio di bilanciamento del carico esterno e distribuire il servizio di bilanciamento del carico dalla portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.
2. Individuare il gruppo di risorse che contiene il servizio di bilanciamento del carico esterno di origine e selezionarlo.
3. Selezionare **Impostazioni** > **Esporta modello**.
4. Selezionare **Distribuisci** in **Esporta modello**.
5. Selezionare **modello** > **modifica parametri** per aprire il file Parameters. JSON nell'editor online.

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

6.  Per modificare il valore dell'indirizzo IP pubblico di destinazione spostato nei passaggi precedenti, è necessario prima ottenere l'ID risorsa e incollarlo nel file Parameters. JSON. Per ottenere l'ID:

    1. In un'altra scheda o finestra del browser accedere alla [portale di Azure](https://portal.azure.com) e selezionare **gruppi di risorse**.
    2. Individuare il gruppo di risorse di destinazione che contiene l'indirizzo IP pubblico che è stato spostato nei passaggi precedenti. Selezionarlo.
    3. Selezionare**Proprietà** **Impostazioni** > .
    4. Nel pannello a destra evidenziare l' **ID risorsa** e copiarlo negli Appunti. In alternativa, è possibile selezionare **copia negli Appunti** a destra del percorso dell' **ID risorsa** .
    5. Incollare l'ID risorsa nella proprietà **value** nell'editor **modifica parametri** aperto nell'altra finestra o scheda del browser:

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
    6. Selezionare **Save (Salva** ) nell'editor online.


7.  Se sono state configurate le regole NAT in uscita e in uscita per il servizio di bilanciamento del carico, verrà visualizzata una terza voce in questo file per l'ID esterno dell'IP pubblico in uscita. Ripetere i passaggi precedenti nell' **area di destinazione** per ottenere l'ID per l'indirizzo IP pubblico in uscita. Incollare l'ID nel file Parameters. JSON:

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

8.  Selezionare **modello** > **modifica modello** per aprire il file template. JSON nell'editor online.
9.  Per modificare l'area di destinazione in cui verrà spostata la configurazione del servizio di bilanciamento del carico esterno, modificare la proprietà **location** in **Resources** nel file template. JSON:

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

10. Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi. Ad esempio, il codice per gli Stati Uniti centrali è **centralus**.

11. È anche possibile modificare altri parametri nel modello, se si vuole o necessario, a seconda dei requisiti:

    * **SKU**. È possibile modificare lo SKU del servizio di bilanciamento del carico esterno nella configurazione da standard a Basic o da Basic a standard modificando la proprietà **Name** in **SKU** nel file template. JSON:

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
      Per informazioni sulle differenze tra i bilanciamenti del carico SKU Basic e standard, vedere [Panoramica di Azure Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Regole di bilanciamento del carico**. È possibile aggiungere o rimuovere le regole di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella sezione **loadBalancingRules** del file template. JSON:

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
       Per informazioni sulle regole di bilanciamento del carico, vedere [che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Probe**. È possibile aggiungere o rimuovere un probe per il servizio di bilanciamento del carico nella configurazione aggiungendo o rimuovendo le voci nella sezione **Probe** del file template. JSON:

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
       Per altre informazioni, vedere [Load Balancer Probe di integrità](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Regole NAT in ingresso**. È possibile aggiungere o rimuovere le regole NAT in ingresso per il servizio di bilanciamento del carico aggiungendo o rimuovendo le voci nella sezione **inboundNatRules** del file template. JSON:

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
        Per completare l'aggiunta o la rimozione di una regola NAT in ingresso, è necessario che la regola sia presente o rimossa come proprietà del **tipo** alla fine del file template. JSON:

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
        Per informazioni sulle regole NAT in ingresso, vedere [che cos'è Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Regole in uscita**. È possibile aggiungere o rimuovere regole in uscita nella configurazione modificando la proprietà **outboundRules** nel file template. JSON:

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

         Per ulteriori informazioni, vedere [Load Balancer regole in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Selezionare **Save (Salva** ) nell'editor online.

13. Selezionare**sottoscrizione** di **base** > per scegliere la sottoscrizione in cui verrà distribuito il servizio di bilanciamento del carico esterno di destinazione.

15. Selezionare**gruppo di risorse** **nozioni di base** > per scegliere il gruppo di risorse in cui verrà distribuito il servizio di bilanciamento del carico di destinazione. È possibile selezionare **Crea nuovo** per creare un nuovo gruppo di risorse per il servizio di bilanciamento del carico esterno di destinazione. In alternativa, è possibile scegliere il gruppo di risorse esistente creato in precedenza per l'indirizzo IP pubblico. Verificare che il nome non sia uguale al gruppo di risorse di origine del servizio di bilanciamento del carico esterno di origine esistente.

16. Verificare che il**percorso** di **base** > sia impostato sul percorso di destinazione in cui si desidera distribuire il servizio di bilanciamento del carico esterno.

17. In **Impostazioni**verificare che il nome corrisponda al nome immesso in precedenza nell'editor dei parametri. Verificare che gli ID risorsa siano popolati per tutti gli indirizzi IP pubblici nella configurazione.

18. Selezionare la casella di controllo **termini e condizioni** .

19. Selezionare **Acquista** per distribuire l'indirizzo IP pubblico di destinazione.

## <a name="discard"></a>Annulla

Se si vuole rimuovere l'indirizzo IP pubblico di destinazione e il servizio di bilanciamento del carico esterno, eliminare il gruppo di risorse che li contiene. A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e quindi fare clic su **Elimina** nella parte superiore della pagina Overview (panoramica).

## <a name="clean-up"></a>Eseguire la pulizia

Per eseguire il commit delle modifiche e completare lo spostamento dell'IP pubblico e del servizio di bilanciamento del carico esterno, eliminare l'indirizzo IP pubblico di origine e il servizio di bilanciamento del carico esterno o il gruppo di risorse. A tale scopo, selezionare il gruppo di risorse dal dashboard nel portale e quindi selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un servizio di bilanciamento del carico esterno di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine. Per ulteriori informazioni sullo stato di trasferimento delle risorse tra le aree e il ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

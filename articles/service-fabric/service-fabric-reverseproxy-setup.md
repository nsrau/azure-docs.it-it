---
title: Configurazione del proxy inverso di Azure Service Fabric | Microsoft Docs
description: Informazioni sulla configurazione del proxy inverso di Service Fabric.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: v-jamebr
ms.openlocfilehash: 7f1b6f955dd3f59f6c17403b536cf99d666aab08
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60392995"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Configurare il proxy inverso in Azure Service Fabric
Il proxy inverso è un servizio facoltativo di Azure Service Fabric che consente ai microservizi in esecuzione in un cluster di Service Fabric di rilevare e comunicare con altri servizi che hanno endpoint HTTP. Per altre informazioni, vedere [Proxy inverso in Azure Service Fabric](service-fabric-reverseproxy.md). Questo articolo illustra come configurare il proxy inverso nel cluster. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Abilitare il proxy inverso con il portale di Azure

Il portale di Azure fornisce un'opzione per abilitare il proxy inverso durante la creazione di un nuovo cluster di Service Fabric. Non è possibile aggiornare un cluster esistente per l'uso del proxy inverso tramite il portale. 

Per configurare il proxy inverso durante la [creazione di un cluster con il portale di Azure](./service-fabric-cluster-creation-via-portal.md), seguire questa procedura:

1. In **Passaggio 2: Configurazione cluster** in **Configurazione del tipo di nodo** selezionare **Abilita proxy inverso**.

   ![Abilitare il proxy inverso nel portale](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Facoltativo) Per configurare il proxy inverso sicuro, è necessario configurare un certificato SSL. In **Passaggio 3: Sicurezza** per **Configurare le impostazioni di sicurezza del cluster** in **Tipo configurazione** selezionare **Personalizzata**. In **Certificato SSL del proxy inverso** selezionare quindi **Includi un certificato SSL per il proxy inverso** e immettere i dettagli del certificato.

   ![Configurare il proxy inverso sicuro nel portale](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Se si sceglie di non configurare il proxy inverso con un certificato quando si crea il cluster, è possibile eseguire questa operazione in seguito tramite il modello di Resource Manager per il gruppo di risorse cluster. Per altre informazioni, vedere [Abilitare il proxy inverso tramite modelli di Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Abilitare il proxy inverso tramite modelli di Azure Resource Manager

Per i cluster in Azure è possibile usare il modello di Azure Resource Manager per abilitare il proxy inverso in Service Fabric. È possibile abilitare il proxy inverso durante la creazione del cluster oppure tramite l'aggiornamento del cluster in un secondo momento. 

Per un nuovo cluster è possibile [creare un modello di Resource Manager personalizzato](service-fabric-cluster-creation-via-arm.md) oppure usare un modello di esempio. 

I modelli di Resource Manager di esempio, che semplificano la configurazione del proxy inverso sicuro per un cluster di Azure, sono disponibili nella pagina relativa ai [modelli di esempio del proxy inverso sicuro](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample) in GitHub. Vedere [Configure HTTPS Reverse Proxy in a secure cluster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) (Configurare il proxy inverso HTTPS in un cluster sicuro) nel file LEGGIMI per istruzioni e per i modelli da usare per configurare il proxy inverso sicuro con un certificato e per gestire il rollover dei certificati.

Per un cluster esistente è possibile esportare il modello di Resource Manager per il gruppo di risorse cluster usando il [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Quando è disponibile un modello di Resource Manager, è possibile abilitare il proxy inverso seguendo questa procedura:

1. Definire una porta per il proxy inverso nella [sezione dei parametri](../azure-resource-manager/resource-group-authoring-templates.md) del modello.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Specificare la porta per ogni oggetto nodetype in [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) nella sezione [Tipo di risorsa](../azure-resource-manager/resource-group-authoring-templates.md).

    La porta è identificata dal nome del parametro reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Per configurare i certificati SSL sulla porta per il proxy inverso, aggiungere il certificato alla proprietà ***reverseProxyCertificate*** in **Microsoft.ServiceFabric/clusters** nella sezione [Tipo di risorsa](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Supporto di un certificato di proxy inverso diverso dal certificato di cluster
 Se il certificato di proxy inverso è diverso dal certificato che protegge il cluster, il certificato specificato in precedenza deve essere installato nella macchina virtuale e aggiunto all'elenco di controllo di accesso, in modo che Service Fabric possa accedervi. Questa operazione può essere eseguita in [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) nella sezione [Tipo di risorsa](../resource-group-authoring-templates.md). Per l'installazione, aggiungere il certificato a osProfile. La sezione del modello relativa all'estensione può aggiornare il certificato nell'elenco di controllo di accesso.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Quando si usano certificati diversi dal certificato del cluster per abilitare il proxy inverso in un cluster esistente, installare il certificato di proxy inverso e aggiornare l'elenco di controllo di accesso sul cluster prima di abilitare il proxy inverso. Completare la distribuzione del [modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) usando le impostazioni indicate in precedenza prima di avviare una distribuzione per abilitare il proxy inverso nei passaggi 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Abilitare il proxy inverso nei cluster autonomi

Per i cluster autonomi è necessario abilitare il proxy inverso nel file ClusterConfig.json. È possibile abilitare il proxy inverso durante la creazione del cluster oppure tramite l'aggiornamento della configurazione di un cluster esistente. Per altre informazioni sulle impostazioni disponibili nei file ClusterConfig.json, vedere [Impostazioni per un cluster autonomo](./service-fabric-cluster-manifest.md).

La procedura seguente illustra le impostazioni da usare per abilitare il proxy inverso e, facoltativamente, per proteggere il proxy inverso con un certificato X.509. 

1. Per abilitare il proxy inverso, impostare il valore **reverseProxyEndpointPort** per il tipo di nodo in **properties** nel file di configurazione del cluster. Il codice JSON seguente mostra l'impostazione della porta dell'endpoint del proxy inverso su 19081 per i nodi con tipo "NodeType0":

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Facoltativo) Per un proxy inverso sicuro è possibile configurare un certificato nella sezione **security** in **properties**. 
   - Per un ambiente di sviluppo o test è possibile usare l'impostazione **ReverseProxyCertificate**:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Per un ambiente di produzione è consigliabile usare l'impostazione **ReverseProxyCertificateCommonNames**:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Per altre informazioni sulla configurazione e sulla gestione dei certificati per un cluster autonomo e per altri dettagli sulla configurazione dei certificati usati per proteggere il proxy inverso, vedere [Sicurezza basata su certificati X509](./service-fabric-windows-cluster-x509-security.md).

Dopo avere modificato il file ClusterConfig.json per abilitare il proxy inverso, seguire le istruzioni disponibili in per eseguire il push [delle modifiche nel cluster](service-fabric-cluster-config-upgrade-windows-server.md).


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Esporre il proxy inverso su una porta pubblica tramite Azure Load Balancer

Per fare riferimento al proxy inverso dall'esterno di un cluster di Azure, configurare regole di Azure Load Balancer e un probe di Integrità di Azure per la porta del proxy inverso. Questa procedura può essere eseguita con il portale di Azure o il modello di Resource Manager in qualsiasi momento dopo la creazione del cluster. 

> [!WARNING]
> Quando si configura la porta del proxy inverso nel servizio Load Balancer, tutti i microservizi nel cluster che espone un endpoint HTTP sono indirizzabili dall'esterno del cluster. Ciò significa che i microservizi concepiti per essere interni potrebbero essere individuati da un utente malintenzionato. Questo presenta potenzialmente delle gravi vulnerabilità che possono essere sfruttate, ad esempio:
>
> * Un utente malintenzionato potrebbe attivare un attacco Denial of Service chiamando ripetutamente un servizio interno che non ha una superficie di attacco con protezione avanzata.
> * Un utente malintenzionato potrebbe distribuire dei pacchetti non validi a un servizio interno che potrebbe causare un comportamento indesiderato.
> * Un servizio concepito per essere interno potrebbe restituire informazioni private o riservate che non sono destinate a essere esposte ai servizi all'esterno del cluster esponendo quindi le informazioni riservate a un utente malintenzionato. 
>
> Assicurarsi di aver compreso completamente e di aver attenuato le potenziali ramificazioni di sicurezza per il cluster e le app in esecuzione su di esso prima di rendere pubblica la porta del proxy inverso. 
>

Se si vuole esporre il proxy inverso pubblicamente per un cluster autonomo, la modalità di esposizione dipenderà dal sistema che ospita il cluster e non rientra nell'ambito di questo articolo. L'avviso precedente sull'esposizione pubblica del proxy inverso è tuttavia comunque applicabile.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Esporre il proxy inverso con il portale di Azure 

1. Nel portale di Azure fare clic sul gruppo di risorse per il cluster e quindi fare clic sul servizio di bilanciamento del carico per il cluster.
2. Per aggiungere un probe di integrità per la porta del proxy inverso, nel riquadro sinistro della finestra di Load Balancer fare clic su **Probe integrità** in **IMPOSTAZIONI**. Fare quindi clic su **Aggiungi** nella parte superiore della finestra Probe integrità e immettere i dettagli relativi alla porta del proxy inverso, quindi fare clic su **OK**. Per impostazione predefinita, la porta del proxy inverso è 19081, a meno che non sia stata modificata durante la creazione del cluster.

   ![Configurare il probe del proxy inverso](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Per aggiungere una regola di Load Balancer per l'esposizione della porta del proxy inverso, nel riquadro sinistro della finestra di Load Balancer fare clic su **Regole di bilanciamento del carico** in **IMPOSTAZIONI**. Fare quindi clic su **Aggiungi** nella parte superiore della finestra delle regole del servizio di bilanciamento del carico e immettere i dettagli relativi alla porta del proxy inverso. Assicurarsi di impostare il valore **Porta** sulla porta in cui si vuole esporre il proxy inverso, il valore **Porta back-end** sulla porta configurata durante l'abilitazione del proxy inverso e il valore **Probe integrità** sul probe integrità configurato nel passaggio precedente. Impostare gli altri campi in base alle esigenze specifiche e fare clic su **OK**.

   ![Configurare una regola di Load Balancer per il proxy inverso](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Esporre il proxy inverso tramite i modelli di Resource Manager

Il codice JSON seguente fa riferimento allo stesso modello usato in [Abilitare il proxy inverso tramite modelli di Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Vedere tale sezione del documento per informazioni sulla creazione di un modello di Resource Manager o sull'esportazione di un modello per un cluster esistente.  Le modifiche vengono apportate a [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) nella sezione [Tipo di risorsa](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Personalizzare il comportamento del proxy con le impostazioni di infrastruttura

È possibile personalizzare il comportamento del proxy inverso tramite le impostazioni di infrastruttura nel modello di Resource Manager per i cluster ospitati in Azure o nel file ClusterConfig.json per i cluster autonomi. Le impostazioni che controllano il comportamento del proxy inverso sono disponibili nella sezione [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) della sezione **fabricSettings** sotto la sezione **properties** del cluster. 

È ad esempio possibile impostare il valore di **DefaultHttpRequestTimeout** per impostare il timeout per le richieste al proxy inverso su 180 secondi, come illustrato nel codice JSON seguente:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Per altre informazioni sull'aggiornamento delle impostazioni di infrastruttura per i cluster di Azure, vedere [Personalizzare le impostazioni del cluster usando i modelli di Resource Manager](service-fabric-cluster-config-upgrade-azure.md). Per i cluster autonomi vedere [Personalizzare le impostazioni del cluster per i cluster autonomi](service-fabric-cluster-config-upgrade-windows-server.md). 

Alcune impostazioni di infrastruttura vengono usate per stabilire comunicazioni sicure tra il proxy inverso e i servizi. Per informazioni dettagliate su queste impostazioni, vedere [Connetti a un servizio protetto con il proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Passaggi successivi
* [Configurare l'inoltro a un servizio HTTP sicuro con il proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md)
* Per informazioni sulle opzioni di configurazione del proxy inverso, vedere la sezione [ApplicationGateway/Http](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) in "Configurare le impostazioni del cluster di Service Fabric".

---
title: Creare una regola di Azure Load Balancer per un cluster
description: Configurare un'istanza di Azure Load Balancer per aprire le porte per un cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 71dbc2b5f6c3c37e432f5318169a088c5607f083
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Aprire le porte per un cluster di Service Fabric

Il servizio di bilanciamento del carico distribuito con il cluster di Azure Service Fabric indirizza il traffico all'app eseguita in un nodo. Se si modifica l'app per usare una porta diversa, è necessario esporre tale porta (o eseguire l'instradamento a un'altra porta) in Azure Load Balancer.

Al momento della distribuzione del cluster di Service Fabric in Azure, è stato creato automaticamente un servizio di bilanciamento del carico. Se non è disponibile un servizio di bilanciamento del carico, vedere l'articolo su come [configurare un servizio di bilanciamento del carico con connessione Internet](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Configurare Service Fabric

Il file di configurazione **ServiceManifest.xml** dell'applicazione di Service Fabric definisce gli endpoint che verranno usati dall'applicazione. Dopo l'aggiornamento del file di configurazione per definire un endpoint, è necessario aggiornare il servizio di bilanciamento del carico per esporre tale porta o un'altra. Per altre informazioni sulla creazione dell'endpoint di Service Fabric, vedere l'articolo su come [configurare un endpoint](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico apre una porta con connessione Internet e inoltra il traffico alla porta del nodo interno usata dall'applicazione. Se non è disponibile un servizio di bilanciamento del carico, vedere l'articolo su come [configurare un servizio di bilanciamento del carico con connessione Internet](..\load-balancer\load-balancer-get-started-internet-portal.md).

Per creare una regola di bilanciamento del carico, è necessario raccogliere le informazioni seguenti:

- Nome del servizio di bilanciamento del carico.
- Gruppo di risorse del servizio di bilanciamento del carico e del cluster di Service Fabric.
- Porta esterna.
- Porta interna.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Per creare una regola di bilanciamento del carico con l'**interfaccia della riga di comando di Azure** è sufficiente un solo comando. Per creare una nuova regola è necessario sapere solo il nome del servizio di bilanciamento del carico e quello del gruppo di risorse.

>[!NOTE]
>Se è necessario determinare il nome del servizio di bilanciamento del carico, usare questo comando per ottenere rapidamente un elenco di tutti i servizi di bilanciamento del carico e i gruppi di risorse associati.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Il comando dell'interfaccia della riga di comando di Azure include alcuni parametri descritti nella tabella seguente:

| Parametro | Descrizione |
| --------- | ----------- |
| `--backend-port`  | Porta su cui è in ascolto l'applicazione di Service Fabric. |
| `--frontend-port` | Porta esposta dal servizio di bilanciamento del carico per le connessioni esterne. |
| `-lb-name` | Nome del servizio di bilanciamento del carico da modificare. |
| `-g`       | Gruppo di risorse che include sia il servizio di bilanciamento del carico sia il cluster di Service Fabric. |
| `-n`       | Nome desiderato per la regola. |


>[!NOTE]
>Per altre informazioni in merito, vedere l'articolo su come [creare un servizio di bilanciamento del carico con l'interfaccia della riga di comando di Azure](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell è un po' più complesso rispetto all'interfaccia della riga di comando di Azure. Per creare una regola seguire questi passaggi concettuali:

1. Recuperare il servizio di bilanciamento del carico da Azure.
2. Creare una regola.
3. Aggiungere la regola al servizio di bilanciamento del carico.
4. Aggiornare il servizio di bilanciamento del carico.

>[!NOTE]
>Se è necessario determinare il nome del servizio di bilanciamento del carico, usare questo comando per ottenere rapidamente un elenco di tutti i servizi di bilanciamento del carico e i gruppi di risorse associati.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

Nel comando `New-AzureRmLoadBalancerRuleConfig`, `-FrontendPort` rappresenta la porta esposta dal servizio di bilanciamento del carico per le connessioni esterne, mentre `-BackendPort` rappresenta la porta su cui è in ascolto l'app di Service Fabric.

>[!NOTE]
>Per altre informazioni in merito, vedere l'articolo su come [creare un servizio di bilanciamento del carico con PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete in Service Fabric](service-fabric-patterns-networking.md).
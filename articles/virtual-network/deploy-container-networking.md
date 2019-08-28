---
title: Distribuire una rete di contenitori di rete virtuale di Azure | Microsoft Docs
description: Informazioni su come distribuire il plug-in CNI (Container Network Interface) della rete virtuale di Azure per i cluster Kubernetes distribuiti manualmente o tramite il motore ACS e per i contenitori Docker.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 657c23ad410d7aade17b3153f02ba0138edf4250
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104098"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Distribuire il plug-in CNI (Container Network Interface) della rete virtuale di Azure

Il plug-in CNI (Container Network Interface) della rete virtuale di Azure viene installato in una macchina virtuale di Azure e aggiunge funzionalità di rete virtuale ai pod Kubernetes e ai contenitori Docker. Per altre informazioni sul plug-in, vedere [Enable containers to use Azure Virtual Network capabilities](container-networking-overview.md) (Abilitare i contenitori all'uso delle funzionalità di rete virtuale di Azure). Il plug-in può inoltre essere usato con il servizio Azure Kubernetes scegliendo l'opzione [Rete avanzata](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), che inserisce automaticamente i contenitori del servizio Azure Kubernetes in una rete virtuale.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Distribuire il plug-in per un cluster Kubernetes del motore ACS

Il motore ACS distribuisce un cluster Kubernetes con un modello di Azure Resource Manager. La configurazione del cluster è specificata in un file JSON che viene passato allo strumento durante la generazione del modello. Per altre informazioni sull'intero elenco delle impostazioni di cluster supportate e le relative descrizioni, vedere [Microsoft Azure Container Service Engine - Cluster Definition](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md) (Motore del servizio Microsoft Azure Container - Definizione del cluster). Il plug-in corrisponde al plug-in di rete predefinito per i cluster creati con il motore ACS. Le seguenti impostazioni di configurazione di rete sono importanti per la configurazione del plug-in:

  | Impostazione                              | DESCRIZIONE                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | L'indirizzo IP allocato al nodo master. Si tratta di un'impostazione obbligatoria.                                     |
  | clusterSubnet sotto kubernetesConfig | CIDR della subnet della rete virtuale in cui viene distribuito il cluster e da cui gli indirizzi IP vengono allocati ai pod   |
  | vnetSubnetId sotto masterProfile     | Specifica l'ID risorsa di Azure Resource Manager della subnet in cui verrà distribuito il cluster                    |
  | vnetCidr                             | CIDR della rete virtuale in cui è distribuito il cluster                                                             |
  | max-Pods sotto kubeletConfig         | Numero massimo di pod in ogni macchina virtuale agente. Per il plug-in, il valore predefinito è 30. È possibile specificare fino a 250 pod  |

### <a name="example-configuration"></a>Configurazione di esempio

L'esempio JSON che segue è per un cluster con le proprietà seguenti:
-   1 nodo master e 2 nodi agente 
-   Distribuito in una subnet denominata *KubeClusterSubnet* (10.0.0.0/20), in cui risiedono i nodi sia master che agente.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Distribuire il plug-in per un cluster Kubernetes

Completare i passaggi seguenti per installare il plug-in ogni macchina virtuale di Azure in un cluster Kubernetes:

1. [Scaricare e installare il plug-in](#download-and-install-the-plug-in).
2. Preallocare un pool di indirizzi IP di rete virtuale in ogni macchina virtuale da cui gli indirizzi IP verranno assegnati ai pod. Ogni macchina virtuale di Azure è fornita con un indirizzo IP privato di rete virtuale primaria in ogni interfaccia di rete. Il pool di indirizzi IP per i pod viene aggiunto come indirizzi secondari (*ipconfigs*) nell'interfaccia di rete della macchina virtuale, usando una delle opzioni seguenti:

   - **Interfaccia della riga di comando**: [assegnare più indirizzi IP usando l'interfaccia della riga di comando di Azure](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [assegnare più indirizzi IP usando PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portale**: [assegnare più indirizzi IP usando il portale di Azure](virtual-network-multiple-ip-addresses-portal.md)
   - **Modello di Azure Resource Manager**: [assegnare più indirizzi IP usando modelli](virtual-network-multiple-ip-addresses-template.md)

   Assicurarsi di aggiungere indirizzi IP sufficienti per tutti i pod che si prevede di creare nella macchina virtuale.

3. Selezionare il plug-in per fornire funzionalità di rete per il cluster passando a Kubelet l'`–network-plugin=cni`opzione della riga di comando durante la creazione del cluster. Kubernetes, per impostazione predefinita, cerca il plug-in e il file di configurazione nella directory in cui sono già installati.
4. Se si desidera che i pod accedano a Internet, aggiungere la regola *iptables* seguente nelle macchine virtuali Linux per applicare la conversione Source NAT al traffico Internet. Nell'esempio seguente, l'intervallo IP specificato è 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   Le regole applicano la conversione NAT al traffico che non è destinato agli intervalli IP specificati. Il presupposto è che tutto il traffico esterno agli intervalli precedenti sia traffico Internet. È possibile specificare gli intervalli IP della rete virtuale della macchina virtuale, delle reti virtuali con peering e delle reti locali.

   Le macchine virtuali Windows applicano automaticamente la conversione Source NAT al traffico con una destinazione esterna alla subnet a cui appartiene la macchina virtuale. Non è possibile specificare intervalli IP personalizzati.

Al termine dei passaggi precedenti, ai pod creati nelle macchine virtuali dell'agente Kubernetes vengono automaticamente assegnati indirizzi IP privati dalla rete virtuale.

## <a name="deploy-plug-in-for-docker-containers"></a>Distribuire il plug-in per i contenitori Docker

1. [Scaricare e installare il plug-in](#download-and-install-the-plug-in).
2. Creare contenitori Docker con il comando seguente:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

I contenitori inizieranno automaticamente a ricevere gli indirizzi IP dal pool allocato. Se si desidera bilanciare il carico del traffico verso i contenitori Docker, questi devono essere posizionati dietro un software di bilanciamento del carico ed è necessario configurare una probe di bilanciamento del carico, allo stesso modo in cui si creano probe e un criterio per una macchina virtuale.

### <a name="cni-network-configuration-file"></a>File di configurazione di rete CNI

Il file di configurazione di rete CNI è descritto in formato JSON. Per impostazione predefinita, è presente in `/etc/cni/net.d` per Linux e in `c:\cni\netconf` per Windows. Il file specifica la configurazione del plug-in ed è diverso per Windows e Linux. Il codice JSON seguente è un file di configurazione Linux di esempio, seguito da una spiegazione per alcune delle impostazioni chiave. Non è necessario apportare modifiche al file:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Spiegazione delle impostazioni

- **cniVersion**: i plug-in CNI della rete virtuale di Azure supportano le versioni 0.3.0 e 0.3.1 della  [specifica CNI](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name**: nome della rete. Questa proprietà può essere impostata su qualsiasi valore univoco.
- **type**: nome del plug-in di rete. Impostare su *azure-vnet*.
- **mode**: modalità operativa. Questo campo è facoltativo. L'unica modalità supportata è "bridge". Per altre informazioni, vedere le [modalità operative](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md).
- **bridge**: nome del bridge che verrà usato per connettere i contenitori a una rete virtuale. Questo campo è facoltativo. Se omesso, il plug-in sceglie automaticamente un nome univoco, in base all'indice dell'interfaccia master.
- **ipam type**: nome del plug-in Gestione indirizzi IP. Impostare sempre su *azure-vnet-ipam*.

## <a name="download-and-install-the-plug-in"></a>Scaricare e installare il plug-in

Scaricare il plug-in da [GitHub](https://github.com/Azure/azure-container-networking/releases). Scaricare la versione più recente per la piattaforma in uso:

- **Linux**: [azure-vnet-cni-linux-amd64-\<n. versione\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [azure-vnet-cni-windows-amd64-\<n. versione\>.zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Copiare lo script di installazione per [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) o [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) nel computer in uso. Salvare lo script in una directory `scripts` nel computer e denominare il file `install-cni-plugin.sh` per Linux o `install-cni-plugin.ps1` per Windows. Per installare il plug-in, eseguire lo script appropriato per la piattaforma in uso, specificando la versione del plug-in usato. Ad esempio, si potrebbe specificare *v1.0.12-rc3*:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

Lo script installa il plug-in nel percorso `/opt/cni/bin` per Linux e `c:\cni\bin` per Windows. Nel plug-in installato è incluso un semplice file di configurazione di rete che funziona dopo l'installazione senza necessità di aggiornamenti. Per altre informazioni sulle impostazioni nel file, vedere [File di configurazione di rete CNI](#cni-network-configuration-file).
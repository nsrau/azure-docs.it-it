---
title: Accesso e sicurezza nei modelli di Azure per le macchine virtuali Linux | Documentazione Microsoft
description: Macchine virtuali di Azure - Esercitazione DotNet Core
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 07e47189-680e-4102-a8d4-5a8eb9c00213
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8dae248b5ac4059da4684336b22030c63546da15
ms.lasthandoff: 04/03/2017

---
# <a name="access-and-security-in-azure-resource-manager-templates-for-linux-vms"></a>Accesso e sicurezza nei modelli di Azure Resource Manager per macchine virtuali Linux

Le applicazioni ospitate in Azure devono in genere essere accessibili attraverso Internet o una connessione VPN/Express Route con Azure. Nel caso dell'applicazione Music Store di esempio, il sito Web è reso disponibile su Internet con un indirizzo IP pubblico. Una volta stabilito l'accesso, le connessioni all'applicazione e l'accesso alle risorse delle macchine virtuali deve essere protetto. La sicurezza dell'accesso è garantita da un gruppo di sicurezza di rete. 

Questo documento descrive in che modo è protetta l'applicazione Music Store nel modello di esempio di Azure Resource Manager. Tutte le dipendenze e le configurazioni univoche sono evidenziate. Per ottenere risultati ottimali, pre-distribuire un'istanza della soluzione alla propria sottoscrizione di Azure ed esercitarsi con il modello di Azure Resource Manager. Il modello completo è disponibile in [Music Store Deployment on Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)(Distribuzione di Music Store in Ubuntu). 

## <a name="public-ip-address"></a>Indirizzo IP pubblico
Per consentire l'accesso pubblico a una risorsa di Azure, è possibile usare una risorsa di indirizzo IP pubblico. L'indirizzo IP pubblico può essere configurato con un indirizzo IP statico o dinamico. Se viene usato un indirizzo dinamico e la macchina virtuale viene arrestata e deallocata, l'indirizzo viene rimosso. Al riavvio della macchina, è possibile che venga assegnato un indirizzo IP pubblico diverso. Per impedire l'assegnazione di un altro indirizzo IP, è possibile usare un indirizzo IP riservato. 

È possibile aggiungere un indirizzo IP pubblico a un modello di Azure Resource Manager usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON valida nel modello. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Indirizzo IP pubblico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Un indirizzo IP pubblico può essere associato a una scheda di rete virtuale o a un servizio di bilanciamento del carico. In questo esempio, il carico del sito Web di Music Store è bilanciato tra più macchine virtuali e quindi l'indirizzo IP pubblico è associato al servizio di bilanciamento del carico.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Associazione dell'indirizzo IP pubblico con il servizio di bilanciamento del carico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Di seguito è illustrato l'indirizzo IP pubblico come visualizzato nel portale di Azure. Si noti che l'indirizzo IP pubblico è associato a un servizio di bilanciamento del carico e non a una macchina virtuale. I servizi di bilanciamento del carico di rete sono descritti in dettaglio nel documento successivo di questa serie.

![Indirizzo IP pubblico](./media/dotnet-core-3-access-security/pubip.png)

Per altre informazioni sugli indirizzi IP pubblici di Azure, vedere [Indirizzi IP in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Gruppo di sicurezza di rete
Una volta stabilito l'accesso alle risorse di Azure, è necessario imporre alcune limitazioni. Per le macchine virtuali di Azure, la sicurezza dell'accesso è garantita da un gruppo di sicurezza di rete. Nel caso dell'applicazione Music Store di esempio, qualsiasi accesso alla macchina virtuale è limitato, ad eccezione della porta 80 per l'accesso HTTP e della porta 22 per l'accesso SSH. È possibile aggiungere un gruppo di sicurezza di rete a un modello di Azure Resource Manager usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON valida nel modello.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Gruppo di sicurezza di rete](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68).

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

In questo esempio il gruppo di sicurezza di rete è associato all'oggetto subnet dichiarato nella risorsa della rete virtuale. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Associazione del gruppo di sicurezza di rete con la rete virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158).

```json
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

Di seguito è illustrato il gruppo di sicurezza di rete come visualizzato nel portale di Azure. Si noti che un gruppo di sicurezza di rete può essere associato a una subnet e/o a un'interfaccia di rete. In questo caso, il gruppo di sicurezza di rete è associato a una subnet. In questa configurazione, le regole in ingresso si applicano a tutte le macchine virtuali connesse alla subnet.

![Gruppo di sicurezza di rete](./media/dotnet-core-3-access-security/nsg.png)

Per informazioni approfondite sui gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Passaggio successivo
<hr>

[Passaggio 3: Disponibilità e scalabilità nei modelli di Azure Resource Manager](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



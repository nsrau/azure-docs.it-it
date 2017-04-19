---
title: Distribuzione di risorse di calcolo Linux con modelli di Azure Resource Manager | Documentazione Microsoft
description: Macchine virtuali di Azure - Esercitazione DotNet Core
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 1c4d419e-ba0e-45e4-a9dd-7ee9975a86f9
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
ms.openlocfilehash: 1fd905b30c6624a21ed90ea75633d7ca1ea474a9
ms.lasthandoff: 04/03/2017


---
# <a name="application-architecture-with-azure-resource-manager-templates-for-linux-vms"></a>Architettura delle applicazioni con i modelli di Azure Resource Manager per macchine virtuali Linux

Quando si sviluppa una distribuzione di Azure Resource Manager, i requisiti di calcolo devono essere mappati ai servizi e alle risorse di Azure. Se un'applicazione è costituita da diversi endpoint HTTP, da un database e da un servizio di caching dei dati, le risorse di Azure che ospitano ognuno di questi componenti devono essere razionalizzate. Nel caso specifico, l'applicazione Music Store di esempio include un'applicazione Web, ospitata in una macchina virtuale, e un database SQL, ospitato in un database SQL di Azure. 

Questo documento descrive in che modo sono configurate le risorse di calcolo dell'applicazione Music Store nel modello di esempio di Azure Resource Manager. Tutte le dipendenze e le configurazioni univoche sono evidenziate. Per ottenere risultati ottimali, pre-distribuire un'istanza della soluzione alla propria sottoscrizione di Azure ed esercitarsi con il modello di Azure Resource Manager. Il modello completo è disponibile in [Music Store Deployment on Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)(Distribuzione di Music Store in Ubuntu). 

## <a name="virtual-machine"></a>Macchina virtuale
L'applicazione Music Store include un'applicazione Web in cui i clienti possono cercare e acquistare musica. Esistono vari servizi di Azure che possono ospitare applicazioni Web. In questo esempio viene usata una macchina virtuale. Con il modello Music Store di esempio, viene distribuita una macchina virtuale, viene installato un server Web e viene installato e configurato il sito Web di Music Store. Ai fini di questo articolo, viene illustrata solo la distribuzione della macchina virtuale. La configurazione del server Web e dell'applicazione è descritta in un articolo successivo.

È possibile aggiungere una macchina virtuale a un modello usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON valida nel modello di distribuzione. Quando si distribuisce una macchina virtuale, sono necessarie anche alcune risorse correlate. Se per creare il modello si usa Visual Studio, queste risorse vengono create automaticamente. Se invece si crea il modello manualmente, queste risorse devono essere inserite e configurate.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Esempio JSON di macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L295).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
      ........<truncated>  
    }
```

Dopo la distribuzione, le proprietà della macchina virtuale possono essere visualizzate nel portale di Azure.

![Macchina virtuale](./media/dotnet-core-2-architecture/vm.png)

## <a name="storage-account"></a>Account di archiviazione
Gli account di archiviazione presentano molte funzionalità e opzioni di archiviazione. Per il contesto delle macchine virtuali di Azure, un account di archiviazione contiene le unità disco rigido virtuali della macchina virtuale e altri dischi dati. L'esempio Music Store include un solo account di archiviazione per contenere l'unità disco rigido virtuale di ogni macchina virtuale nella distribuzione. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Account di archiviazione](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L109).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Un account di archiviazione è associato a una macchina virtuale all'interno della dichiarazione del modello di Resource Manager per la macchina virtuale. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Associazione della macchina virtuale con l'account di archiviazione](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L341).

```json
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Dopo la distribuzione, l'account di archiviazione può essere visualizzato nel portale di Azure.

![Account di archiviazione](./media/dotnet-core-2-architecture/storacct.png)

Facendo clic sul contenitore BLOB dell'account di archiviazione, è possibile visualizzare il file dell'unità disco rigido virtuale per ogni macchina virtuale distribuita con il modello.

![Unità disco rigido virtuali](./media/dotnet-core-2-architecture/vhd.png)

Per altre informazioni su Archiviazione di Azure, vedere [Documentazione su Archiviazione](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Rete virtuale
Se una macchina virtuale richiede una connessione di rete interna, ad esempio per avere la possibilità di comunicare con altre macchine virtuali e risorse di Azure, è necessario configurare una rete virtuale di Azure.  Una rete virtuale non rende accessibile la macchina virtuale attraverso Internet. La connettività pubblica richiede un indirizzo IP pubblico, come descritto più avanti in questa serie.

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Rete virtuale e subnet](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L136).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
    ]
  }
}
```

Nel portale di Azure la rete virtuale ha un aspetto simile all'immagine seguente. Si noti che tutte le macchine virtuali distribuite con il modello sono collegate alla rete virtuale.

![Rete virtuale](./media/dotnet-core-2-architecture/vnet.png)

## <a name="network-interface"></a>Interfaccia di rete
 Un'interfaccia di rete connette una macchina virtuale a una rete virtuale, in particolare a una subnet definita nella rete virtuale. 

 Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Interfaccia di rete](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L166).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceNamePrefix'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'SSH-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/SSH-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Ogni risorsa di macchina virtuale include un profilo di rete, in cui l'interfaccia di rete è associata alla macchina virtuale.  

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Profilo di rete della macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L350).

```json
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceNamePrefix'), copyindex()))]"
    }
  ]
}
```

Nel portale di Azure l'interfaccia di rete ha un aspetto simile all'immagine seguente. L'associazione dell'indirizzo IP interno con la macchina virtuale è visibile nella risorsa dell'interfaccia di rete.

![Interfaccia di rete](./media/dotnet-core-2-architecture/nic.png)

Per altre informazioni sulle reti virtuali di Azure, vedere [Documentazione su Rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Database SQL di Azure
Oltre a una macchina virtuale che ospita il sito Web di Music Store, viene distribuito un database SQL di Azure per ospitare il database di Music Store. L'uso di un database SQL di Azure è vantaggioso perché consente di evitare di usare un secondo set di macchine virtuali, integrando scalabilità e disponibilità nel servizio.

È possibile aggiungere un database SQL di Azure a un modello usando la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo una risorsa JSON valida nel modello. La risorsa di SQL Server include un nome utente e una password a cui sono concessi diritti amministrativi sull'istanza di SQL. Viene inoltre aggiunta una risorsa di firewall SQL. Per impostazione predefinita, le applicazioni ospitate in Azure sono in grado di connettersi all'istanza di SQL. Per consentire a un'applicazione esterna come SQL Server Management Studio di connettersi all'istanza di SQL, il firewall deve essere configurato. Ai fini della demo di Music Store, è sufficiente accettare la configurazione predefinita. 

Fare clic su questo collegamento per vedere l'esempio JSON incluso nel modello di Resource Manager: [Database SQL di Azure](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L401).

```json
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicStoreSqlName')]",
  "location": "[resourceGroup().location]",

  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('sqlAdminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicStoreSqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Di seguito è illustrato il database MusicStore di SQL Server come visualizzato nel portale di Azure.

![SQL Server](./media/dotnet-core-2-architecture/sql.png)

Per altre informazioni sulla distribuzione del database SQL di Azure, vedere [Documentazione su Database SQL](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Passaggio successivo
<hr>

[Passaggio 2: Accesso e sicurezza nei modelli di Azure Resource Manager](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



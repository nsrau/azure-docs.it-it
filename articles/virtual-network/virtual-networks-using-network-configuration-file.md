---
title: Configurare una rete virtuale di Azure (classica) usando un file di configurazione di rete | Microsoft Docs
description: Informazioni su come creare e modificare le reti virtuali (classiche) esportando, modificando e importando un file di configurazione di rete.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f1e3ae26b6525f2235a6b0d53546b334dc027b94
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configurare una rete virtuale (classica) usando un file di configurazione di rete
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo articolo illustra l'uso del modello di distribuzione classica. È consigliabile usare il modello di distribuzione di Resource Manager per le distribuzioni più recenti.

È possibile creare e configurare una rete virtuale (classica) con un file di configurazione di rete usando l'interfaccia della riga di comando di Azure 1.0 o Azure PowerShell. Non è possibile creare o modificare una rete virtuale mediante il modello di distribuzione di Azure Resource Manager usando un file di configurazione di rete. Non è possibile usare il portale di Azure per creare o modificare una rete virtuale (classica) con un file di configurazione di rete, ma è possibile usare il portale di Azure per creare una rete virtuale (classica) senza usare un file di configurazione di rete.

La creazione e la configurazione di una rete virtuale (classica) con un file di configurazione di rete prevede l'esportazione, la modifica e l'importazione del file.

## <a name="export"></a>Esportare un file di configurazione di rete

È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per esportare un file di configurazione di rete. PowerShell esporta un file XML, mentre l'interfaccia della riga di comando di Azure esporta un file json.

### <a name="powershell"></a>PowerShell
 
1. [Installare Azure PowerShell e accedere ad Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Modificare la directory (e verificare che sia presente) e il nome file nel comando seguente nel modo desiderato, quindi eseguire il comando per esportare il file di configurazione di rete:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

1. [Installare l'interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Completare i passaggi rimanenti dal prompt dei comandi dell'interfaccia della riga di comando di Azure 1.0.
2. Accedere ad Azure immettendo il comando `azure login`.
3. Verificare di essere in modalità asm immettendo il comando `azure config mode asm`.
4. Modificare la directory (e verificare che sia presente) e il nome file nel comando seguente nel modo desiderato, quindi eseguire il comando per esportare il file di configurazione di rete:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Creare o modificare un file di configurazione di rete

Un file di configurazione di rete è un file XML (quando si usa PowerShell) o un file json (quando si usa l'interfaccia della riga di comando di Azure). Il file può essere modificato in qualsiasi editor di testo o editor XML/json. L'articolo relativo alle [impostazioni dello schema del file di configurazione di rete](https://msdn.microsoft.com/library/azure/jj157100.aspx) include i dettagli di tutte le impostazioni. Per una spiegazione aggiuntiva delle impostazioni, vedere [View virtual networks and settings](virtual-network-manage-network.md#view-vnet) (Visualizzare le reti virtuali e le impostazioni). Le modifiche che vengono apportate al file:

- Devono essere conformi allo schema altrimenti l'importazione del file di configurazione di rete non riuscirà.
- Sovrascrivono le impostazioni di rete esistenti per la sottoscrizione, pertanto è necessario prestare attenzione alle modifiche che si apportano. Fare riferimento, ad esempio, ai file di configurazione di rete di esempio che seguono. Si supponga che il file di origine contenga due istanze di **VirtualNetworkSite** e che lo si modifichi come illustrato negli esempi. Quando si importa il file, Azure elimina la rete virtuale per l'istanza **VirtualNetworkSite** che è stata rimossa nel file. Questo scenario semplificato presuppone che non vi siano risorse nella rete virtuale, perché se fossero presenti, non sarebbe possibile eliminare la rete virtuale e l'importazione avrebbe esito negativo.

> [!IMPORTANT]
> Azure considera una subnet con un elemento distribuito come **in uso**. Quando una subnet è in uso, non può essere modificata. Prima di modificare le informazioni sulla subnet in un file di configurazione di rete, spostare tutti gli elementi distribuiti nella subnet in una subnet diversa che non deve essere modificata. Vedere [Spostare una VM o un'istanza del ruolo in un'altra subnet](virtual-networks-move-vm-role-to-subnet.md).

### <a name="example-xml-for-use-with-powershell"></a>XML di esempio da usare con PowerShell

L'esempio di file di configurazione di rete seguente illustra come creare una rete virtuale denominata *myVirtualNetwork* con uno spazio di indirizzi di *10.0.0.0/16* nell'area di Azure *Stati Uniti Orientali*. La rete virtuale contiene una subnet denominata *mySubnet* con un prefisso di indirizzo *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Se il file di configurazione di rete esportato non include alcun contenuto, è possibile copiare il codice XML dell'esempio precedente e incollarlo in un nuovo file.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>JSON di esempio da usare con l'interfaccia della riga di comando di Azure 1.0

L'esempio di file di configurazione di rete seguente illustra come creare una rete virtuale denominata *myVirtualNetwork* con uno spazio di indirizzi di *10.0.0.0/16* nell'area di Azure *Stati Uniti Orientali*. La rete virtuale contiene una subnet denominata *mySubnet* con un prefisso di indirizzo *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Se il file di configurazione di rete esportato non include alcun contenuto, è possibile copiare il codice json dell'esempio precedente e incollarlo in un nuovo file.

## <a name="import"></a>Importare un file di configurazione di rete

È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per importare un file di configurazione di rete. PowerShell importa un file XML, mentre l'interfaccia della riga di comando di Azure importa un file json. Se l'importazione non riesce, verificare che il file sia conforme allo [schema di configurazione di rete](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Installare Azure PowerShell e accedere ad Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Modificare la directory e il nome file nel comando seguente in base alle esigenze, quindi eseguire il comando per importare il file di configurazione di rete:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

1. [Installare l'interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Completare i passaggi rimanenti dal prompt dei comandi dell'interfaccia della riga di comando di Azure 1.0.
2. Accedere ad Azure immettendo il comando `azure login`.
3. Verificare di essere in modalità asm immettendo il comando `azure config mode asm`.
4. Modificare la directory e il nome file nel comando seguente in base alle esigenze, quindi eseguire il comando per importare il file di configurazione di rete:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```


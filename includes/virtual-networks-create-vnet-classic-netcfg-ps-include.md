---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116868"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Come creare una rete virtuale usando un file di configurazione di rete da PowerShell
Azure usa un file XML per definire tutte le reti virtuali disponibili per una sottoscrizione. È possibile scaricare questo file e modificarlo per eliminare o modificare le reti virtuali esistenti o per crearne di nuove. Questa esercitazione illustra come scaricare il file, chiamato anche file di configurazione di rete (o netcfg), e modificarlo per creare una nuova rete virtuale. Per altre informazioni sul file di configurazione di rete, vedere [Azure virtual network configuration schema](https://msdn.microsoft.com/library/azure/jj157100.aspx) (Schema di configurazione della rete virtuale di Azure).

Per creare una rete virtuale con un file netcfg usando PowerShell, seguire questa procedura:

1. Se si usa Azure PowerShell per la prima volta, completare la procedura descritta nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs), accedere ad Azure e selezionare la propria sottoscrizione.
2. Nella console di Azure PowerShell usare il cmdlet **Get-AzureVnetConfig** per scaricare il file di configurazione di rete in una directory del computer eseguendo il comando seguente: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Output previsto:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Aprire il file salvato nel passaggio 2 utilizzando qualsiasi editor XML o testo e cercare il  **\<VirtualNetworkSites >** elemento. Se si dispone di altre reti già create, ogni rete viene visualizzato con il relativo  **\<VirtualNetworkSite >** elemento.
4. Per creare la rete virtuale descritta in questo scenario, aggiungere il codice XML seguente sotto il  **\<VirtualNetworkSites >** elemento:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Salvare il file di configurazione di rete.
6. Nella console di Azure PowerShell usare il cmdlet **Set-AzureVnetConfig** per caricare il file di configurazione di rete con il comando seguente: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Output restituito:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Se nell'output restituito l'opzione **OperationStatus** non è impostata su *Succeded* (Operazione riuscita), controllare che nel file XML non siano presenti errori e ripetere il passaggio 6.

7. Nella console di Azure PowerShell usare il cmdlet **Get-AzureVnetSite** per verificare che la nuova rete sia stata aggiunta eseguendo il comando seguente: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   L'output restituito (abbreviato) include il testo seguente:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```

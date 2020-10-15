---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 594ad352d5fd8431ffaf6d681c891c967cf9d32a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606161"
---
Per stabilire correttamente un tunnel del dispositivo, è necessario soddisfare i requisiti seguenti:

* Il dispositivo deve essere un computer aggiunto a un dominio che esegue Windows 10 Enterprise o Education versione 1809 o successiva.
* Il tunnel è configurabile solo per la soluzione VPN incorporata di Windows e viene stabilito usando IKEv2 con l'autenticazione del certificato computer.
* È possibile configurare un solo tunnel del dispositivo per ogni dispositivo.

1. Installare i certificati client nel client Windows 10 usando l'articolo [client VPN da punto a sito](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) . Il certificato deve trovarsi nell'archivio del computer locale.
1. Creare un profilo VPN e configurare il tunnel del dispositivo nel contesto dell'account di sistema locale seguendo [queste istruzioni](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration).

### <a name="configuration-example-for-device-tunnel"></a>Esempio di configurazione per il tunnel del dispositivo

Dopo aver configurato il gateway di rete virtuale e installato il certificato client nell'archivio del computer locale nel client Windows 10, usare gli esempi seguenti per configurare un tunnel del dispositivo client:

1. Copiare il testo seguente e salvarlo come ***devicecert.ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Copiare il testo seguente e salvarlo come ***VPNProfile.xml*** nella stessa cartella **devicecert.ps1**. Modificare il testo seguente in modo che corrisponda all'ambiente.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Scaricare **PsExec** da [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) ed estrarre i file in **C:\PSTools**.
1. Da un prompt dei comandi amministratore avviare PowerShell eseguendo:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Screenshot mostra una finestra del prompt dei comandi con un comando per avviare la versione a 64 bit di PowerShell.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. In PowerShell passare alla cartella in cui si trovano **devicecert.ps1** e **VPNProfile.xml** ed eseguire il comando seguente:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![Screenshot mostra una finestra di PowerShell che ha eseguito MachineCertTest con lo script devicesert.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Eseguire **RASPHONE**.

   ![Screenshot mostra una finestra di dialogo Esegui con RASPHONE selezionato.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Cercare la voce **MachineCertTest** e fare clic su **Connetti**.

   ![Screenshot mostra una finestra di dialogo connessioni di rete con MachineCertTest selezionato e un pulsante Connetti.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Se la connessione ha esito positivo, riavviare il computer. Il tunnel si connetterà automaticamente.

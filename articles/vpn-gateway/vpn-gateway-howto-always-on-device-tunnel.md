---
title: Configurare un tunnel VPN always on
titleSuffix: Azure VPN Gateway
description: Procedura per configurare Always On tunnel VPN per il gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: f22b29cfcaf1d4c4ce28b2b0557d70b281b6891f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146391"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurare un tunnel di dispositivo VPN Always On

Una delle nuove funzionalità del client VPN (Virtual Private Network) di Windows 10 è la possibilità di mantenere una connessione VPN. Always On è una funzionalità di Windows 10 che consente al profilo VPN attivo di connettersi automaticamente e rimanere connesso in base ai trigger, ovvero l'accesso utente, la modifica dello stato della rete o lo schermo del dispositivo attivo.

I gateway di rete virtuale di Azure possono essere usati con Windows 10 Always On per stabilire tunnel utente persistenti e tunnel di dispositivi in Azure. Questo articolo consente di configurare un tunnel del dispositivo VPN Always ON.

Always On le connessioni VPN includono due tipi di tunnel:

* Il **tunnel del dispositivo** si connette ai server VPN specificati prima dell'accesso degli utenti al dispositivo. Gli scenari di connettività di pre-accesso e la gestione dei dispositivi usano il tunneling del dispositivo.

* Il **tunnel utente** si connette solo dopo che un utente ha eseguito l'accesso al dispositivo. Il tunnel utente consente agli utenti di accedere alle risorse dell'organizzazione tramite i server VPN.

Il tunnel del dispositivo e il tunnel utente operano in modo indipendente con i profili VPN. Possono essere connessi nello stesso momento e possono usare metodi di autenticazione diversi e altre impostazioni di configurazione VPN in base alle esigenze.

## <a name="1-configure-the-gateway"></a>1. configurare il gateway

Configurare il gateway VPN per l'uso di IKEv2 e l'autenticazione basata su certificati usando questo [articolo da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-device-tunnel"></a>2. configurare il tunnel del dispositivo

Per stabilire correttamente un tunnel del dispositivo, è necessario soddisfare i requisiti seguenti:

* Il dispositivo deve essere un computer aggiunto a un dominio che esegue Windows 10 Enterprise o Education versione 1709 o successiva.
* Il tunnel è configurabile solo per la soluzione VPN incorporata di Windows e viene stabilito usando IKEv2 con l'autenticazione del certificato computer. 
* È possibile configurare un solo tunnel del dispositivo per ogni dispositivo.

1. Installare i certificati client nel client Windows 10, come illustrato in questo [articolo del client VPN da punto a sito](point-to-site-how-to-vpn-client-install-azure-cert.md). Il certificato deve trovarsi nell'archivio del computer locale.
1. Usare [queste istruzioni](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) per creare un profilo VPN e configurare il tunnel del dispositivo nel contesto dell'account di sistema locale.

### <a name="configuration-example-for-device-tunnel"></a>Esempio di configurazione per il tunnel del dispositivo

Dopo aver configurato il gateway di rete virtuale e installato il certificato client nell'archivio del computer locale nel client Windows 10, usare gli esempi seguenti per configurare un tunnel del dispositivo client.

1. Copiare il testo seguente e salvarlo come ***devicecert. ps1***.

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
1. Copiare il testo seguente e salvarlo come ***la. XML*** nella stessa cartella di **devicecert. ps1**. Modificare il testo seguente in modo che corrisponda all'ambiente.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

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
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. In PowerShell passare alla cartella in cui si trovano **devicecert. ps1** e **la. XML** ed eseguire il comando seguente:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Eseguire **RASPHONE**.

   ![RASPHONE](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Cercare la voce **MachineCertTest** e fare clic su **Connetti**.

   ![Connetti](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Se la connessione ha esito positivo, riavviare il computer. Il tunnel si connetterà automaticamente.

## <a name="cleanup"></a>Pulizia

Per rimuovere il profilo, eseguire il comando seguente:

![Pulizia](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi, vedere [problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)

---
title: Configurare un tunnel VPN Always On per il Gateway VPN
description: Passaggi per la configurazione del tunnel VPN Always On per il Gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695743"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurare un tunnel di dispositivo VPN Always On

Una delle nuove funzionalità del client Windows 10 rete privata virtuale (VPN) è la possibilità di mantenere una connessione VPN. Always On è una funzionalità di Windows 10 che permette il profilo VPN attivo per connettersi automaticamente e mantenere la connessione basata su trigger, vale a dire, accesso dell'utente, modifica dello stato di rete o schermo del dispositivo attivo.

I gateway di rete virtuale di Azure è utilizzabile con Windows 10 Always On per stabilire i tunnel utente persistenti, nonché i tunnel di dispositivo in Azure. Questo articolo consente di configurare un tunnel del dispositivo VPN Always ON.

Le connessioni VPN Always On includono due tipi di tunnel:

* **Tunnel dispositivo** si connette al server VPN specificati prima che gli utenti accedono i dispositivi. Scenari di connettività di pre-accesso e gestione dei dispositivi Usa tunnel di dispositivo.

* **Utente tunnel** si connette solo dopo un accesso degli utenti del dispositivo. Tunnel utente consente agli utenti di accedere alle risorse dell'organizzazione tramite i server VPN.

Entrambi i tunnel di dispositivo e utente tunnel di operare in modo indipendente i profili VPN. Essi possono essere connesse allo stesso tempo e possono usare diversi metodi di autenticazione e altre impostazioni di configurazione VPN come appropriato.

## <a name="1-configure-the-gateway"></a>1. Configurare il gateway

Configurare il gateway VPN per l'utilizzo di IKEv2 e l'autenticazione basata su certificati usando questa [point-to-site articolo](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Configurare il tunnel di utente

1. Installare i certificati client sul client Windows 10, come illustrato in questo [articolo di point-to-site VPN client](point-to-site-how-to-vpn-client-install-azure-cert.md). Il certificato deve essere in Store l'utente corrente
2. Configurare il client VPN Always On tramite PowerShell, SCCM o Intune usando [queste istruzioni](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Configurare il tunnel di dispositivo

I requisiti seguenti devono essere soddisfatti per stabilire un tunnel di dispositivo:

* Il dispositivo deve essere un computer aggiunto a un dominio che eseguono Windows 10 Enterprise o Education versione 1709 o successiva.
* Il tunnel è possibile configurare per la soluzione VPN incorporata di Windows e viene stabilito con l'autenticazione del certificato computer IKEv2. 
* Tunnel di un solo dispositivo possono essere configurati per ogni dispositivo.

1. Installare i certificati client sul client Windows 10, come illustrato in questo [articolo di point-to-site VPN client](point-to-site-how-to-vpn-client-install-azure-cert.md). Il certificato deve trovarsi nell'archivio computer locale.
1. Uso [queste istruzioni](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) per creare un profilo VPN e configurare il tunnel di dispositivo nel contesto dell'account di sistema locale.

### <a name="configuration-example-for-device-tunnel"></a>Esempio di configurazione per il tunnel di dispositivo

Dopo aver configurato il gateway di rete virtuale e installare il certificato client nell'archivio del computer locale nel client Windows 10, è possibile usare gli esempi seguenti per configurare un tunnel di dispositivo client.

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
1. Copiare il testo seguente e salvarlo come ***VPNProfile.xml*** nella stessa cartella **devicecert.ps1**. Modificare il testo seguente per adattarli all'ambiente.

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
1. Scaricare **PsExec** dalla [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) ed estrarre i file da **C:\PSTools**.
1. Da un prompt dei comandi CMD Admin, avviare PowerShell eseguendo:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. In PowerShell passare alla cartella in cui **devicecert.ps1** e **VPNProfile.xml** si trovano, ed eseguire il comando seguente:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Eseguire **rasphone**.

   ![RASPHONE](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Cercare il **MachineCertTest** voce e fare clic su **Connect**.

   ![Connettere](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Se la connessione riesce, riavviare il computer. Il tunnel si connetterà automaticamente.

## <a name="cleanup"></a>Pulizia

Per rimuovere il profilo, eseguire il comando seguente:

![Pulizia](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

Per risolvere il problema, vedere [problemi di connessione di Azure da punto a sito](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)

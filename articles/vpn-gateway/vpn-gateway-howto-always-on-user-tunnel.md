---
title: Configurare un tunnel utente VPN Always On per il gateway VPN
description: Questo articolo descrive come configurare un tunnel utente VPN Always On per il gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: 9aa1f951add5b79eab12f4957be05a42bbdd4434
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299918"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurare un tunnel utente VPN Always On

Una nuova funzionalità del client VPN di Windows 10, Always On, è la possibilità di mantenere una connessione VPN. Con Always On, il profilo VPN attivo può connettersi automaticamente e rimanere connesso in base ai trigger, ad esempio l'accesso utente, la modifica dello stato della rete o lo schermo del dispositivo attivo.

È possibile usare i gateway di rete virtuale di Azure con Windows 10 Always On per stabilire tunnel utente persistenti e tunnel di dispositivo in Azure. Questo articolo consente di configurare un tunnel utente Always On VPN.

Always On connessioni VPN includono uno dei due tipi di tunnel:

* **Tunnel del dispositivo**: Si connette ai server VPN specificati prima che gli utenti accedino al dispositivo. Gli scenari di connettività di pre-accesso e la gestione dei dispositivi usano un tunnel del dispositivo.

* **Tunnel utente**: Si connette solo dopo che gli utenti hanno eseguito l'accesso al dispositivo. Usando i tunnel utente è possibile accedere alle risorse dell'organizzazione tramite i server VPN.

I tunnel e i tunnel utente del dispositivo operano indipendentemente dai profili VPN. Possono essere connesse contemporaneamente e possono usare metodi di autenticazione diversi e altre impostazioni di configurazione VPN, in base alle esigenze.

Nelle sezioni seguenti vengono configurati un gateway VPN e un tunnel utente.

## <a name="step-1-configure-a-vpn-gateway"></a>Passaggio 1: Configurare un gateway VPN

Il gateway VPN viene configurato per l'uso di IKEv2 e l'autenticazione basata su certificati seguendo le istruzioni riportate in questo articolo da [punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="step-2-configure-a-user-tunnel"></a>Passaggio 2: Configurare un tunnel utente

1. Installare i certificati client nel client Windows 10, come illustrato in questo articolo del [client VPN da punto a sito](point-to-site-how-to-vpn-client-install-azure-cert.md) . Il certificato deve trovarsi nell'archivio utente corrente.

1. Configurare il client VPN Always On tramite PowerShell, System Center Configuration Manager o Intune seguendo le istruzioni riportate in [configurare il client Windows 10 always on le connessioni VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="example-configuration-for-the-user-tunnel"></a>Configurazione di esempio per il tunnel utente

Dopo aver configurato il gateway di rete virtuale e installato il certificato client nell'archivio del computer locale nel client Windows 10, configurare un tunnel del dispositivo client usando gli esempi seguenti:

1. Copiare il testo seguente e salvarlo come *userCert. ps1*:

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
1. Copiare il testo seguente e salvarlo come *la. XML* nella stessa cartella di *userCert. ps1*. Modificare il testo seguente in modo che corrisponda all'ambiente:

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Eseguire PowerShell come amministratore.

1. In PowerShell passare alla cartella in cui si trovano *userCert. ps1* e *la. XML* ed eseguire il comando seguente:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. In **Impostazioni VPN**cercare la voce **UserTest** , quindi selezionare **Connetti**.

1. Se la connessione ha esito positivo, il tunnel utente Always On è stato configurato correttamente.

## <a name="clean-up-your-resources"></a>Pulire le risorse

Per rimuovere il profilo, eseguire le operazioni seguenti:

1. Eseguire il comando seguente:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Disconnettere la connessione e deselezionare la casella di controllo **Connetti automaticamente** .

![Pulizia](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi di connessione che possono verificarsi, vedere [problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

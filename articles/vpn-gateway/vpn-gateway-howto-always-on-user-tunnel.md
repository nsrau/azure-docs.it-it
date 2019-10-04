---
title: Configurare un tunnel VPN Always On per il gateway VPN
description: Procedura per configurare il tunnel VPN utente Always On per il gateway VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846472"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurare un tunnel utente VPN Always On

Una delle nuove funzionalità del client VPN (Virtual Private Network) di Windows 10 è la possibilità di mantenere una connessione VPN. Always On è una funzionalità di Windows 10 che consente al profilo VPN attivo di connettersi automaticamente e rimanere connesso in base ai trigger, ovvero l'accesso utente, la modifica dello stato della rete o lo schermo del dispositivo attivo.

I gateway di rete virtuale di Azure possono essere usati con Windows 10 Always On per stabilire tunnel utente persistenti e tunnel di dispositivi in Azure. Questo articolo consente di configurare un tunnel utente Always On VPN.

Always On le connessioni VPN includono due tipi di tunnel:

* Il **tunnel del dispositivo** si connette ai server VPN specificati prima dell'accesso degli utenti al dispositivo. Gli scenari di connettività di pre-accesso e la gestione dei dispositivi usano il tunneling del dispositivo.

* Il **tunnel utente** si connette solo dopo che un utente ha eseguito l'accesso al dispositivo. Il tunnel utente consente agli utenti di accedere alle risorse dell'organizzazione tramite i server VPN.

Il tunnel del dispositivo e il tunnel utente operano in modo indipendente con i profili VPN. Possono essere connessi nello stesso momento e possono usare metodi di autenticazione diversi e altre impostazioni di configurazione VPN in base alle esigenze.

## <a name="1-configure-the-gateway"></a>1. Configurare il gateway

Configurare il gateway VPN per l'uso di IKEv2 e l'autenticazione basata su certificati usando questo [articolo da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Configurare il tunnel utente

1. Installare i certificati client nel client Windows 10, come illustrato in questo [articolo del client VPN da punto a sito](point-to-site-how-to-vpn-client-install-azure-cert.md). Il certificato deve trovarsi nell'archivio utente corrente
2. Configurare il client VPN Always On tramite PowerShell, SCCM o Intune seguendo [queste istruzioni](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="configuration-example-for-user-tunnel"></a>Esempio di configurazione per il tunnel utente

Dopo aver configurato il gateway di rete virtuale e installato il certificato client nell'archivio del computer locale nel client Windows 10, usare gli esempi seguenti per configurare un tunnel del dispositivo client.

1. Copiare il testo seguente e salvarlo come ***userCert. ps1***.

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
1. Copiare il testo seguente e salvarlo come ***la. XML*** nella stessa cartella di **userCert. ps1**. Modificare il testo seguente in modo che corrisponda all'ambiente.

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

1. In PowerShell passare alla cartella in cui si trovano **userCert. ps1** e **la. XML** ed eseguire il comando seguente:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Controllare le impostazioni VPN.

1. Cercare la voce **UserTest** e fare clic su **Connetti**.

1. Se la connessione ha esito positivo, è stato configurato correttamente un tunnel utente always on.

## <a name="cleanup"></a>Pulizia

Per rimuovere il profilo, eseguire il comando seguente:

1. Disconnettere la connessione e deselezionare "Connetti automaticamente"

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Pulizia](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Passaggi successivi

Per la risoluzione dei problemi, vedere [problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)

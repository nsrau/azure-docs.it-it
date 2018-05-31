---
title: Creare un gateway applicazione di Azure con ridondanza della zona
description: Informazioni su come creare un gateway applicazione con ridondanza della zona che non richiede un gateway separato in ogni zona.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937704"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>Creare un gateway applicazione di Azure con ridondanza della zona - Anteprima privata

La piattaforma con ridondanza della zona del gateway applicazione è un nuovo SKU che rispetto all'attuale SKU di gateway applicazione offre numerosi miglioramenti, tra cui:
- **Resilienza della zona**: una distribuzione di gateway applicazione può estendersi ora a più zone di disponibilità, eliminando la necessità di eseguire il provisioning e aggiungere istanze del gateway applicazione separate in ogni zona tramite un'utilità di gestione del traffico. È possibile scegliere una o più zone in cui distribuire le istanze del gateway applicazione, assicurando così la resilienza dagli errori delle zone. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.
- **Miglioramenti delle prestazioni**
- **VIP statico**: l'indirizzo VIP del gateway applicazione supporta ora esclusivamente il tipo di indirizzo VIP statico. Questa funzionalità garantisce che l'indirizzo VIP associato al gateway applicazione non cambi dopo il riavvio.
- **Integrazione dell'insieme di credenziali delle chiavi per i certificati SSL del cliente**
- **Tempi di distribuzione e di aggiornamento più veloci**

> [!NOTE]
> Il gateway applicazione con ridondanza della zona è attualmente disponibile nell'anteprima privata. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Aree supportate

I gateway applicazione con ridondanza della zona sono attualmente supportati nell'area Stati Uniti orientali 2. Presto verranno aggiunte altre aree.

## <a name="topologies"></a>Topologie

Con la versione corrente non è più necessario creare gateway applicazione aggiunti alla zona per ottenere la ridondanza di zona. È ora possibile estendere la distribuzione dello stesso gateway applicazione su più zone.

Sono necessarie almeno tre istanze per garantire che vengano estese in tutte le tre aree. Il gateway applicazione distribuisce le istanze tra le zone man mano che si aggiungono istanze.

Le versioni precedenti delle topologie con ridondanza della zona si presentano in modo simile a quanto illustrato nel diagramma seguente:

![Topologia con ridondanza precedente](media/create-zone-redundant/old-redundant.png)

La nuova topologia con ridondanza della zona è simile a questo diagramma:

![Nuova topologia con ridondanza della zona](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>Distribuzione

### <a name="prerequisites"></a>prerequisiti

La funzionalità di ridondanza della zona è disponibile al momento solo nell'anteprima privata. Per ottenere il consenso per accedere all'anteprima, è necessario inviare un'e-mail a appgwxzone@microsoft.com. Dopo aver ricevuto il messaggio di conferma, è possibile procedere con i passaggi successivi. Nell'e-mail di richiesta di registrazione includere le informazioni seguenti:

- ID sottoscrizione
- Nome area
- Numero approssimativo di gateway applicazione richiesto

### <a name="resource-manager-template-deployment"></a>Distribuzione del modello di Resource Manager

1. Assicurarsi che la sottoscrizione in uso sia consentita come indicato in precedenza.
2. Dopo aver ricevuto il messaggio di conferma, accedere all'account di Azure e selezionare la sottoscrizione appropriata, se sono presenti più sottoscrizioni. Assicurarsi di selezionare la sottoscrizione che ha ricevuto il consenso.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. Creare un nuovo gruppo di risorse

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. Scaricare i modelli da [GitHub](https://github.com/amitsriva/CrossZonePreview) e prendere nota della cartella in cui li si salva.
5. Creare una nuova distribuzione nel gruppo di risorse che è stato creato. Modificare il file di modello e dei parametri secondo le proprie esigenze prima della distribuzione. 

   Il diagramma seguente illustra dove recuperare l'ID tenant nel portale di Azure:

   ![ID tenant dal portale](media/create-zone-redundant/tenant-id.png)

Il modello crea le risorse seguenti:

- **Identità assegnata dall'utente**: viene usata per consentire alle istanze del gateway applicazione di accedere all'insieme di credenziali delle chiavi e di recuperare i certificati archiviati dall'utente.
- **KeyVault**: insieme di credenziali delle chiavi in cui è archiviato il certificato dell'utente. Può anche essere un insieme di credenziali delle chiavi preesistente.
- **Segreto**: chiave privata archiviata nell'insieme di credenziali delle chiavi.
- **Criteri di accesso**: criteri di accesso applicati all'insieme di credenziali delle chiavi che autorizzano all'utilizzo dell'identità assegnata dall'utente in modo che le distribuzioni del gateway applicazione possano recuperare i certificati dell'utente.
- **Indirizzo IP pubblico** : indirizzo IP riservato usato per accedere al gateway applicazione. Questo indirizzo IP non cambia mai durante il ciclo di vita del gateway applicazione.
- **Gruppi di sicurezza di rete**: gruppo di sicurezza di rete creato automaticamente nella subnet del gateway applicazione che apre il traffico di porta nel listener configurato. Questo gruppo viene creato e gestito esplicitamente nel nuovo SKU, a differenza dello SKU precedente in cui il gruppo di sicurezza di rete è implicito.
- **Rete virtuale**: rete virtuale in cui vengono distribuiti i gateway applicazione e le applicazioni.
- **Gateway applicazione** : crea un gateway applicazione con istanze nelle zone richieste. Per impostazione predefinita, tutte le zone (1, 2, 3) sono selezionate. Il nome dello SKU viene modificato in *Standard_v2* ed è soggetto a modifiche. La configurazione della scalabilità automatica ha attualmente i valori minimo e massimo impostati sul numero di istanze richiesto. Dopo che la scalabilità automatica viene attivata, questa impostazione può essere modificata.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>Distribuzione PowerShell

1. Assicurarsi che la sottoscrizione usata sia consentita come indicato in precedenza nei prerequisiti.
2. Scaricare e installare il file MSI PowerShell privato da [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi).
3. Scaricare il file zip di PowerShell privato dal percorso menzionato nell'e-mail di conferma della registrazione all'anteprima. Decomprimere il file nell'unità e prendere nota del percorso.
4. Dopo avere attivato l'anteprima, caricarne i moduli prima di accedere all'account:

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Accedere all'account di Azure e selezionare la sottoscrizione appropriata, se sono presenti più sottoscrizioni. Assicurarsi di selezionare la sottoscrizione che ha ricevuto il consenso.
5. Eseguire i comandi seguenti per stabilire le costanti comuni che includono i nomi per la maggior parte delle entità da creare. 

   Modificare le voci in base alle proprie preferenze di denominazione.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. Creare il gruppo di risorse:

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Creare l'identità assegnata dall'utente usata per consentire l'accesso al gateway applicazione per recuperare i certificati dall'insieme di credenziali delle chiavi.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. Selezionare l'insieme di credenziali delle chiavi usato per archiviare i certificati:

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Caricare un certificato nell'insieme di credenziali delle chiavi come segreto:

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. Assegnare i criteri di accesso all'insieme di credenziali delle chiavi usando l'identità assegnata dall'utente. Le istanze del gateway applicazione possono così accedere al segreto contenuto nell'insieme di credenziali delle chiavi:

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. Creare il gruppo di sicurezza di rete (NSG) per consentire l'accesso alla subnet del gateway applicazione sulle porte in cui vengono creati i nuovi listener.

    Per HTTP/HTTPS su porte predefinite, ad esempio, il gruppo di sicurezza di rete consente l'accesso in ingresso alle porte 80, 443 e da 65200 a 65535 per le operazioni di gestione.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. Creare una rete virtuale e le subnet:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. Creare un indirizzo IP pubblico di tipo riservato/statico:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. Creare il gateway applicazione:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. Recuperare l'indirizzo IP pubblico del gateway applicazione creato:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>Domande frequenti

-  L'uso del gateway applicazione disponibile in anteprima prevede l'addebito di costi?

   Durante l'anteprima, non è previsto alcun addebito. Viene fatturato l'uso di risorse diverse dal gateway applicazione, ad esempio Key Vault, macchine virtuali e così via.
- In quali aree è disponibile l'anteprima?

   L'anteprima è attualmente disponibile nell'area Stati Uniti orientali 2. Presto verranno aggiunte altre aree.
- L'anteprima supporta il portale?

   No, nell'anteprima privata il supporto è limitato a un modulo di PowerShell privato e a un modello di Resource Manager.

- L'anteprima supporta i carichi di lavoro di produzione?

   No, non sono previsti contratti di servizio o supporto durante l'anteprima privata. Non è consigliabile usare carichi di lavoro di produzione durante le anteprime. Il supporto è limitato all'interazione diretta con il gruppo di prodotti tramite l'alias di posta elettronica per l'anteprima.

- Come si segnalano eventuali problemi?

   L'anteprima privata può contenere bug ed essere soggetta a frequenti distribuzioni di codice. Usare l'alias di supporto appgwxzone@microsoft.com per segnalare eventuali problemi e richiedere assistenza.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni


|Problema  |Dettagli  |
|---------|---------|---------|
|Fatturazione     |Nessun addebito attualmente|
|Log di diagnostica (non metrica)     |I log di richieste/risposte e delle prestazioni non vengono attualmente visualizzati.|
|Portale/CLI/SDK     |Nessun supporto per portale, CLI o SDK Il portale non deve essere usato per rilasciare aggiornamenti ai gateway dell'anteprima.|
|L'aggiornamento tramite modello talvolta non ha esito positivo.     |Ciò è dovuto a una race condition con i criteri di accesso nell'insieme di credenziali delle chiavi.|Dopo avere creato l'insieme di credenziali delle chiavi e l'identità assegnata dall'utente, questi possono essere rimossi dal modello in quanto nel modello sono richiesti solo i riferimenti al segreto e all'identità.|
|Scalabilità automatica     |Nessun supporto per la scalabilità automatica attualmente|
|WAF     |Il WAF non è attualmente supportato.|
|Indirizzi VIP dinamici e certificati dell'utente     |Non sono supportati nel nuovo modello. Usare Key Vault per archiviare i certificati e gli indirizzi VIP statici.|
|Stessa subnet per versione precedente e di anteprima del gateway applicazione     |Per la versione di anteprima privata non è possibile usare una subnet con un gateway applicazione esistente (modello precedente).|
|HTTP/2, modalità FIPS, WebSocket, App Web di Azure come back-end     |Non sono supportati attualmente. |


## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Per supporto, commenti e suggerimenti, inviare un'e-mail all'indirizzo appgwxzone@microsoft.com. Il team che si occupa del gruppo di prodotti gateway applicazione è lieto di ricevere commenti e suggerimenti su come migliorare i prodotti e di mettere a disposizione materiale sussidiario ove richiesto.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altre funzionalità del gateway applicazione:

- [Cos'è il gateway applicazione di Azure?](overview.md)
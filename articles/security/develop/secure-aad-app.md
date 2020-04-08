---
title: Sviluppare un'applicazione Web di Azure AD sicura Documenti Microsoft
description: Questa semplice app di esempio implementa procedure consigliate per la sicurezza che migliorano l'applicazione e la sicurezza dell'organizzazione durante lo sviluppo in Azure.This simple sample app implements security best practices that improve your application and your organization's security posture when you develop on Azure.
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810542"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Sviluppare un'app sicura per un'app Azure ADDevelop secure app for an Azure AD app
## <a name="overview"></a>Panoramica

Questo esempio è un semplice Azure Active Directory con app Web che si collega alle risorse di sicurezza per lo sviluppo di app in Azure.This sample is a simple Azure Active Directory with web app that links to security resources for developing apps on Azure. L'app implementa le procedure consigliate per la sicurezza che consentono di migliorare l'applicazione e la sicurezza dell'organizzazione quando si sviluppano app in Azure.The app implements security best practices that can help improve your application and your organization's security posture when you develop apps on Azure.

Gli script di distribuzione configurano l'infrastruttura. Dopo aver eseguito gli script di distribuzione, è necessario eseguire alcune operazioni di configurazione manuale nel portale di Azure per collegare i componenti e i servizi. Questo esempio è destinato a sviluppatori esperti in Azure che lavorano nel settore retail e desiderano creare un Azure Active Directory protetto con un'infrastruttura di Azure sicura. 


Nello sviluppo e nella distribuzione di questa app, imparerai come 
- Creare un'istanza dell'insieme di credenziali delle chiavi di Azure, archiviare e recuperare segreti da essa.
- Distribuire l'app Web di Azure, dedicata isolata con accesso al firewall front-end. 
- Creare e configurare un'istanza del gateway applicazione di Azure con un firewall che usa Set di regole OWASP Top 10.Create and configure an Azure Application Gateway instance with a firewall that uses OWASP Top 10 Ruleset. 
- Abilitare la crittografia dei dati in transito e inattivi usando i servizi di Azure.Enable encryption of data in transit and at rest by using Azure services. 
- Configurare i criteri e il centro sicurezza di Azure per valutare le funzionalità. 

Dopo aver sviluppato e distribuito questa app, sarà stata configurata l'app Web di esempio seguente insieme alle misure di configurazione e sicurezza descritte.

## <a name="architecture"></a>Architecture
L'app è una tipica applicazione a più livelli con tre livelli. Il livello front-end, back-end e database con componenti di monitoraggio e gestione segreta integrati sono illustrati di seguito:

![Architettura dell'app](./media/secure-aad-app/architecture.png)

Questa soluzione usa i servizi di Azure seguenti. I dettagli dell'architettura di distribuzione sono disponibili nella sezione Architettura di distribuzione. 

L'architettura è costituita da questi componenti

- [Gateway applicazione di Azure](../../application-gateway/index.yml). Fornisce il gateway e il firewall per l'architettura dell'applicazione.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Fornisce un servizio APM (Application Performance Management) estensibile su più piattaforme.
- [Archivio chiavi di Azure](../../key-vault/index.yml). Archivia e crittografa i segreti della nostra app e gestisce la creazione di criteri di accesso che li circondano.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Fornisce il servizio di gestione di identità e accessi basato su cloud, accesso e accesso alle risorse.
- [Azure Domain Name System](../../dns/dns-overview.md). Fornire il servizio per ospitare il dominio.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Fornisce per scalare le applicazioni e creare disponibilità elevata per i servizi.
- [App Web di Azure](../../app-service/overview.md).  Fornisce un servizio basato su HTTP per l'hosting di applicazioni Web.
- [Centro sicurezza di Azure](../../security-center/index.yml). fornisce una protezione avanzata dalle minacce tra i carichi di lavoro ibridi nel cloud.
- [Criteri di Azure](../../governance/policy/overview.md). Fornisce la valutazione delle risorse per la non conformità con i criteri assegnati.

## <a name="threat-model"></a>Modello di minaccia
La modellazione delle minacce è il processo di identificazione di potenziali minacce alla sicurezza per l'azienda e l'applicazione e quindi garantire che sia in atto un piano di attenuazione appropriato.

In questo esempio è stato usato [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) per implementare la modellazione delle minacce per l'app di esempio sicura. Con la creazione di diagrammi dei componenti e dei flussi di dati, è possibile identificare i problemi e le minacce nelle prime fasi del processo di sviluppo. Il tempo e il denaro saranno risparmiati in un secondo momento utilizzando questo.

Ecco il modello di minaccia per l'app di esempioHere is the threat model for the sample app

![Modello di minaccia](./media/secure-aad-app/threat-model.png)

Alcune minacce di esempio e potenziali vulnerabilità generate dallo strumento di modellazione delle minacce sono illustrate nella schermata seguente. Il modello di minaccia fornisce una panoramica della superficie di attacco esposta e richiede agli sviluppatori di pensare a come mitigare i problemi.

![Output del modello di minaccia](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Prerequisiti
Per mettere in funzione l'applicazione, è necessario installare questi strumenti:

- Editor di codice per modificare e visualizzare il codice dell'applicazione. [Visual Studio Code](https://code.visualstudio.com/) è un'opzione open source.
- [Interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) di Azure nel computer di sviluppo.
- [Git](https://git-scm.com/) sul vostro sistema. Git viene utilizzato per clonare il codice sorgente in locale.
- [jq](https://stedolan.github.io/jq/), uno strumento UNIX per l'esecuzione di query su JSON in modo semplice.

È necessaria una sottoscrizione di Azure per distribuire le risorse dell'app di esempio. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/) per testare l'app di esempio.

Dopo aver installato questi strumenti, è possibile distribuire l'app in Azure.After installing these tools, you're ready to deploy the app on Azure.

### <a name="implementation-guidance"></a>Indicazioni relative all'implementazione
Lo script di distribuzione è uno script che può essere suddiviso in quattro fasi. Ogni fase distribuisce e configura una risorsa di Azure presente nel [diagramma dell'architettura.](#architecture)

Le quattro fasi sono

- Distribuire l'insieme di credenziali delle chiavi di Azure.Deploy Azure Key Vault.
- Distribuire app Web di Azure.Deploy Azure Web Apps.
- Distribuire il gateway applicazione con il firewall dell'applicazione Web.
- Configurare azure AD con l'app distribuita.

Ogni fase si basa su quella precedente utilizzando la configurazione delle risorse distribuite in precedenza.

Per completare la procedura di implementazione, assicurarsi di aver installato gli strumenti elencati in [Prerequisiti](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Distribuire l'insieme di credenziali delle chiavi di AzureDeploy Azure Key Vault
In questa sezione viene creata e distribuita un'istanza di Archiviazione delle chiavi di Azure usata per archiviare segreti e certificati.

Dopo aver completato la distribuzione, si dispone di un'istanza di Archiviazione delle chiavi di Azure distribuita in Azure.After you complete the deployment, you have an Azure Key Vault instance deployed on Azure.

Per distribuire l'insieme di credenziali delle chiavi di Azure tramite PowershellTo deploy Azure Key Vault by using Powershell
 
1. Dichiarare le variabili per L'insieme di credenziali delle chiavi di Azure.Declare the variables for Azure Key Vault.
2. Registrare il provider dell'insieme di credenziali delle chiavi di Azure.Register the Azure Key Vault provider.
3. Creare il gruppo di risorse per l'istanza.
4. Creare l'istanza di Azure Key Vault nel gruppo di risorse creato nel passaggio 3.Create the Azure Key Vault instance in the resource group created in step 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>The below Azure AD user will have admin permissions to the Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registrare i provider Az
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Creare l'istanza di Azure Key VaultCreate the Azure Key Vault instance
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Aggiungere i criteri di amministrazione all'insieme di credenziali delle chiavi
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Per creare criteri di accesso per consentire a un utente di ottenere ed elencare chiavi crittografiche, certificati e segreti se si conosce il nome dell'entità utente:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

È consigliabile usare le identità gestite per le risorse di Azure nelle app che usano l'insieme di credenziali delle chiavi per accedere alle risorse. La sicurezza aumenta quando le chiavi di accesso all'insieme di credenziali delle chiavi non vengono archiviate nel codice o nella configurazione.

Nel contenitore è incluso un certificato radice. Le misure adottate per ottenere il certificato sono

1. Scaricare il file del certificato [dall'autorità di certificazione](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Decodificare il file del certificato:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Questo script crea un'identità assegnata per l'istanza del servizio app che può essere usata con MSI per interagire con l'insieme di credenziali delle chiavi di Azure senza segreti di codifica rigida nel codice o nella configurazione.

Passare all'istanza dell'insieme di **credenziali**delle chiavi di Azure nel portale per autorizzare l'identità assegnata nella scheda dei criteri di accesso. In **Seleziona entità**cercare il nome dell'applicazione simile al nome dell'istanza del servizio app creata.
Un'entità servizio collegata all'applicazione deve essere visibile. Selezionarlo e salvare la pagina dei criteri di accesso, come illustrato nella schermata seguente.

Poiché l'applicazione deve solo recuperare le chiavi, selezionare l'autorizzazione **Get** nelle opzioni dei segreti, consentendo l'accesso riducendo i privilegi concessi.

![Criteri di accesso insieme di credenziali delle chiavi](./media/secure-aad-app/kv-access-policy.png)

*Creare un criterio di accesso dell'insieme di credenziali delle chiaviCreate a Key Vault access policy*

Salvare i criteri di accesso e quindi salvare la nuova modifica nella scheda Criteri di **accesso** per aggiornare i criteri.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuire il gateway applicazione con il web application firewall abilitato
Nelle app Web, non è consigliabile esporre i servizi direttamente al mondo esterno su Internet.
Il bilanciamento del carico e le regole del firewall forniscono maggiore sicurezza e controllo sul traffico in ingresso e consentono di gestirlo.

Per distribuire un'istanza del gateway applicazioneTo deploy an Application Gateway instance

1. Creare il gruppo di risorse per ospitare il gateway applicazione.
2. Effettuare il provisioning di una rete virtuale da collegare al gateway.
3. Creare una subnet per il gateway nella rete virtuale.
4. Effettuare il provisioning di un indirizzo IP pubblico.
5. Effettuare il provisioning del gateway applicazione.
6. Abilitare il firewall dell'applicazione Web sul gateway.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Distribuire app Web di AzureDeploy Azure Web Apps
Il servizio app di Azure consente di creare e ospitare app Web usando linguaggi come Python, Ruby, C'è e Java. Azure supporta anche contenitori personalizzati, che consentono praticamente l'esecuzione di tutti i linguaggi di programmazione nella piattaforma del servizio app di Azure.Azure also supports custom containers, which can allow virtually all programming languages to run on the Azure App Service platform.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Creare un piano di servizio app nel livello GratuitoCreate an App Service plan in Free tier
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Creare un'app Web
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Prima di continuare, passare all'interfaccia utente di configurazione del https://aka.ms/appservicecustomdns sistema dei nomi di dominio di Azure per il dominio personalizzato e seguire le istruzioni per configurare un record CNAME per il nome host "www" e puntare il nome di dominio predefinito dell'app Web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Aggiornare il piano del servizio app al livello condiviso (minimo richiesto dai domini personalizzati)Upgrade App Service plan to Shared tier (minimum required by custom domains)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Aggiungere un nome di dominio personalizzato all'app Web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="network"></a>Rete
Dopo aver completato la distribuzione, è possibile avere un gateway applicazione con il firewall dell'applicazione Web abilitato.

L'istanza del gateway espone la porta 443 per HTTPS. Questa configurazione garantisce che l'app sia accessibile solo sulla porta 443 tramite HTTPS.

Bloccare le porte inutilizzate e limitare l'esposizione alla superficie di attacco è una procedura consigliata per la sicurezza.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Aggiungere gruppi di sicurezza di rete all'istanza del servizio appAdd network security groups to the App Service instance

Le istanze del servizio app possono essere integrate con reti virtuali. Questa integrazione consente di configurarli con criteri di gruppo di sicurezza di rete che gestiscono il traffico in ingresso e in uscita dell'app.

1. Per abilitare questa funzionalità, nel pannello Dell'istanza del servizio app di Azure, in **Impostazioni,** selezionare **Rete**. Nel riquadro destro configurare in **Integrazione rete virtuale**.

   ![Nuova integrazione di rete virtuale](./media/secure-web-app/app-vnet-menu.png)

    *Nuova integrazione di rete virtuale per il servizio app*
1. Nella pagina successiva selezionare **Aggiungi vNET (anteprima)**.

1. Nel menu successivo selezionare la rete virtuale creata `aad-vnet`nella distribuzione che inizia con . È possibile creare una nuova subnet o selezionarne una esistente.
   In questo caso, creare una nuova subnet. Impostare **l'intervallo** di indirizzi su **10.0.3.0/24** e denominare la subnet **dell'app**subnet della subnet.

   ![Configurazione della rete virtuale del servizio app](./media/secure-web-app/app-vnet-config.png)

    *Configurazione della rete virtuale per il servizio appVirtual network configuration for App Service*

Dopo aver abilitato l'integrazione della rete virtuale, è possibile aggiungere gruppi di sicurezza di rete all'app.

1. Utilizzare la casella di ricerca , cercare i gruppi di sicurezza di **rete**. Selezionare Gruppi di sicurezza di **rete** nei risultati.

    ![Cercare gruppi di sicurezza di reteSearch for network security groups](./media/secure-web-app/nsg-search-menu.png)

    *Cercare gruppi di sicurezza di reteSearch for network security groups*

2. Nel menu successivo selezionare **Aggiungi**. Immettere il **nome** del gruppo di sicurezza di rete e il **gruppo di** risorse in cui deve essere posizionato. Questo gruppo di sicurezza di rete verrà applicato alla subnet del gateway applicazione.

    ![Creare un gruppo di sicurezza di baseCreate an NSG](./media/secure-web-app/nsg-create-new.png)

    *Creare un gruppo di sicurezza di baseCreate an NSG*

3. Dopo aver creato il gruppo di sicurezza di gruppo, selezionarlo. Nel pannello selezionare Regole di **sicurezza in ingresso**in **Impostazioni**. Configurare queste impostazioni per consentire le connessioni in ingresso nel gateway applicazione tramite la porta 443.Configure these settings to allow connections in the application gateway over port 443.

   ![Configurare il gruppo di sicurezza di gruppo](./media/secure-web-app/nsg-gateway-config.png)

   *Configurare il gruppo di sicurezza di gruppo*

4. Nelle regole in uscita per il gruppo di sicurezza di rete del gateway aggiungere una regola che consenta le connessioni in uscita all'istanza del servizio app creando una regola destinata al tag del servizio`AppService`

   ![Aggiungere regole in uscita per il gruppo di sicurezza di gruppoAdd outbound rules for the NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Aggiungere regole in uscita per il gruppo di sicurezza di gruppoAdd outbound rules for the NSG*

    Aggiungere un'altra regola in uscita per consentire al gateway di inviare regole in uscita a una rete virtuale.

   ![Aggiungere un'altra regola in uscita](./media/secure-web-app/nsg-outbound-vnet.png)

    *Aggiungere un'altra regola in uscita*

5. Nel pannello subnet del gruppo di sicurezza di rete selezionare **Associa**, selezionare la rete virtuale creata nella distribuzione e selezionare la subnet del gateway denominata **gw-subnet**. Il gruppo di sicurezza di rete viene applicato alla subnet.

6. Creare un altro gruppo di sicurezza di rete come nel passaggio precedente, questa volta per l'istanza del servizio app. Dagli un nome. Aggiungere la regola in ingresso per la porta 443 come per il gruppo di sicurezza di rete del gateway applicazione.

   Se si dispone di un'istanza del servizio app distribuita in un'istanza dell'ambiente del servizio app, che non è il caso di questa app, è possibile aggiungere regole in ingresso per consentire i probe di integrità del servizio di Azure aprendo le porte 454-455 nei gruppi di sicurezza in ingresso del gruppo di sicurezza del servizio app. Di seguito è riportata la configurazione:

   ![Aggiungere regole per i probe di Integrità dei servizi di AzureAdd rules for Azure Service Health probes](./media/secure-web-app/nsg-create-healthprobes.png)

    *Aggiungere regole per i probe di integrità del servizio di Azure (solo ambiente del servizio app)Add rules for Azure Service Health probes (App Service Environment only)*

Per limitare la superficie di attacco, modificare le impostazioni di rete del servizio app per consentire solo al gateway applicazione di accedere all'applicazione.
Per applicare le impostazioni, passare alla scheda Rete del servizio app, selezionare la scheda **Restrizioni IP** e creare una regola di autorizzazione che consenta solo all'IP del gateway applicazione di accedere direttamente al servizio. È possibile recuperare l'indirizzo IP del gateway dalla relativa pagina di panoramica. Nella scheda **CIDR Indirizzo IP** immettere l'indirizzo IP nel seguente formato: `<GATEWAY_IP_ADDRESS>/32`.

![Consenti solo il gateway](./media/secure-web-app/app-allow-gw-only.png)

*Consentire solo all'IP del gateway di accedere al servizio appAllow only the gateway IP to access the App Service*

### <a name="azure-domain-name-system"></a>Sistema di nomi di dominio di AzureAzure Domain Name System 
Il sistema dei nomi di dominio di Azure o Azure Domain Name System è responsabile della traduzione (o risoluzione) del nome di un sito Web o di un servizio nel relativo indirizzo IP. Azure Domain Namehttps://docs.microsoft.com/azure/dns/dns-overview) System( è un servizio di hosting per i domini del sistema dei nomi di dominio che fornisce la risoluzione dei nomi usando l'infrastruttura di Azure.Azure Domain Name System( is a hosting service for Domain Name System domains that provides name resolution using Azure infrastructure. Ospitando domini in Azure, gli utenti possono gestire i record del sistema dei nomi di dominio usando le stesse credenziali, API, strumenti e fatturazione di altri servizi di Azure.By hosting domains in Azure, users can manage Domain Name System records using the same credentials, APIs, tools, and billing as other Azure services. Azure Domain Name System supporta anche domini privati di Domain Name System.Azure Domain Name System also supports private Domain Name System domains.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Crittografia dischi di Azure si avvale della funzionalità BitLocker di Windows per abilitare la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti offrono funzionalità per gestire l'accesso ai dati del titolare della carta nell'ambiente AzureThe following technologies provide capabilities to manage access to cardholder data in the Azure environment
- Azure Active Directory  è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Tutti gli utenti per questa soluzione vengono creati in Azure Active Directory, inclusi gli utenti che accedono ad Azure WebApp.All users for this solution are created in Azure Active Directory, including users accessing the Azure WebApp.
- Il controllo degli accessi in base al ruolo di Azure consente agli amministratori di definire autorizzazioni di accesso con granularità fine per concedere solo il livello di accesso necessario agli utenti per il proprio lavoro. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati dei possessori di carte. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- Azure Active Directory Privileged Identity Management consente ai clienti di ridurre al minimo il numero di utenti autorizzati ad accedere a determinate informazioni, ad esempio i dati dei possessori di carte. Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- Azure Active Directory Identity Protection rileva potenziali vulnerabilità che interessano le identità di un'organizzazione, configura risposte automatiche alle azioni sospette rilevate relative alle identità di un'organizzazione e analizza gli eventi imprevisti sospetti per intraprendere le azioni appropriate per risolverli.
### <a name="secrets-management"></a>Gestione dei segreti
la soluzione usa Azure Key Vault per la gestione delle chiavi e dei segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le seguenti funzionalità di Azure Key Vault consentono ai clienti di proteggere e accedere a tali dati
   - I criteri di accesso avanzati vengono configurati in base alle necessità.
   - I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
   - Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
   - Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Il tipo di chiave è una chiave RSA a 2048 bit protetta da modulo di sicurezza hardware (HSM).
   - Con Key Vault è possibile crittografare chiavi e segreti, ad esempio chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dei dati, . PFX) utilizzando chiavi protette da moduli di sicurezza hardware (HSM). 
   - Utilizzare il controllo degli accessi in base al ruolo per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito.     
   - Utilizzare Vault chiave per gestire i certificati TLS con il rinnovo automatico. 
   - I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
   - Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.
### <a name="azure-security-center"></a>Centro sicurezza di Azure
con il Centro sicurezza di Azure i clienti possono applicare e gestire centralmente i criteri di sicurezza nei carichi di lavoro, limitare l'esposizione alle minacce e rilevare e rispondere agli attacchi. Inoltre 
   - Il Centro sicurezza di Azure accede alle configurazioni esistenti dei servizi di Azure per fornire consigli di configurazione e di servizio per migliorare la sicurezza e proteggere i dati.
   - Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di avvisi di sicurezza predefiniti che vengono attivati in caso di minaccia o di attività sospetta. Le regole di avviso personalizzate nel Centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente.
   - Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un report di intelligence per le minacce per ogni minaccia rilevata per supportare i team di risposta agli eventi imprevisti a livello di indagine e reazione alle minacce.
### <a name="azure-application-gateway"></a>Gateway applicazione di Azure 
   L'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione di Azure con un web application firewall configurato e il set di regole OWASP abilitato. Le funzionalità aggiuntive includono
   - TLS end-to-end.
   - Disabilitazione di TLS v1.0 e v1.1.
   - Abilitare TLSv1.2.
   - Firewall applicazione Web (modalità prevenzione).
   - Modalità di prevenzione con set di regole OWASP 3.0.
   - Abilitare la registrazione diagnostica.
   - Probe di integrità personalizzati.
   - Il Centro sicurezza di Azure e Azure Advisor forniscono protezione e notifiche aggiuntive. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.
### <a name="logging-and-auditing"></a>Registrazione e controllo
I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
   - Log attività: [i log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) forniscono informazioni dettagliate sulle operazioni eseguite sulle risorse in una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
   - Log di diagnostica: [i log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. Questi log includono i log del sistema di eventi di Windows, i log di Archiviazione di Azure, i log di controllo dell'insieme di credenziali delle chiavi e i log di accesso al gateway applicazione e Firewall.These logs include Windows event system logs, Azure Storage logs, Key Vault audit logs, and Application Gateway access and Firewall logs. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.
### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
   Questi log vengono consolidati nei log di Monitoraggio di [Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per tipo nelle aree di lavoro di Log Analytics, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Inoltre, Il Centro sicurezza di Azure si integra con i log di Monitoraggio di Azure, consentendo ai clienti di usare le query Kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

   Le seguenti [soluzioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di monitoraggio di Azure sono incluse come parte di questa architettura

   - [Valutazione di Active Directory:](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)la soluzione di controllo dell'integrità di Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco con priorità di consigli specifici per l'infrastruttura server distribuita.
   - [Integrità agente:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)la soluzione Integrità agenti segnala il numero di agenti distribuiti e la relativa distribuzione geografica, nonché il numero di agenti che non rispondono e il numero di agenti che inviano dati operativi.
   - [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): questa soluzione fornisce assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.
### <a name="azure-monitor"></a>Monitoraggio di Azure
   [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)consente agli utenti di tenere traccia delle prestazioni, mantenere la sicurezza e identificare le tendenze consentendo alle organizzazioni di controllare, creare avvisi e archiviare dati, incluso il rilevamento delle chiamate API nelle risorse di Azure.Azure Monitor helps users track performance, maintain security, and identify trends by enablingorganizations to audit, create alerts, and archive data, including tracking API calls in their Azure resources.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. Application Insights rileva eventuali anomalie nelle prestazioni e i clienti possono usarlo per monitorare l'applicazione Web in tempo reale. Include avanzati strumenti di analisi che consentono ai clienti di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità da parte dei clienti.

### <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure
   Creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e mantenere la responsabilità per le attività operative, come indicato di seguito

   - I dati archiviati in Key Vault includono   
   - Chiave di Application Insights
   - Chiave di accesso all'archiviazione datiData Storage Access key
   - Stringa di connessione
   - Data table name
   - Credenziali dell'utente
   - I criteri di accesso avanzati vengono configurati in base alle necessità
   - I criteri di accesso dell'Insieme di credenziali delle chiavi sono definiti con le autorizzazioni minime necessarie per chiavi e segretiKey Vault access policies are defined with minimum required permissions to keys and secrets
   - Tutti i segreti e le chiavi in Key Vault hanno date di scadenza
   - Tutte le chiavi in Key Vault sono protette dal modulo di sicurezza hardware (HSM) [Key Type - hardware security module (HSM) Protected       
     Chiave RSA a 2048 bit]
   - A tutti gli utenti/identità vengono concesse le autorizzazioni minime necessarie tramite il controllo degli accessi in base al ruolo
   - Le applicazioni non condividono un'istanza di Key Vault a meno che non si considerino attendibili reciprocamente e non debbano accedere agli stessi segreti in fase di esecuzione
   - I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
   - Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
   Un tunnel VPN sicuro o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doveva essere configurato stabilendo in modo sicuro una connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web PaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

   L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione avviene tramite Internet e consente ai clienti di "tunnel" in modo sicuro le informazioni all'interno di un collegamento crittografato tra la rete del cliente e Azure.This connection takes place over the Internet and allows customers to securely "tunnel" information inside an encrypted link between the customer's network and Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La modalità tunnel IPsec viene usata in questa opzione come meccanismo di crittografia.

   Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

   Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

#### <a name="implement-azure-active-directory-oidc"></a>Implementare OIDC di Azure Active DirectoryImplement Azure Active Directory OIDC

1. Per clonare il repository del codice sorgente, utilizzare questo comando GitTo clone the source code repository, use this Git command

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Aggiornare gli URL di reindirizzamento
1.  Ritornare al portale di Azure. Nel riquadro di spostamento a sinistra selezionare il servizio Azure Active Directory e quindi selezionare Registrazioni app.
2.  Nella schermata risultante, selezionare l'applicazione WebApp-OpenIDConnect-DotNet-code-v2.
3.  Nella scheda Autenticazione o Nella sezione URI di reindirizzamento selezionare Web nella casella combinata e aggiungere i seguenti URI di reindirizzamento.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o Nella sezione Impostazioni avanzate impostare URL di disconnessione suhttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Nella scheda Personalizzazione o Aggiornare l'URL della home https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netpage all'indirizzo del servizio app, ad esempio .
        o Salvare la configurazione.
5.  Se l'applicazione chiama un'API Web, assicurarsi di applicare le modifiche necessarie al progetto appsettings.json, in modo da chiamare l'URL dell'API pubblicata anziché localhost.
Pubblicazione dell'esempio
    1.  Nella scheda Panoramica del servizio app, scaricare il profilo di pubblicazione facendo clic sul collegamento Recupera profilo di pubblicazione, quindi salvarlo. È anche possibile usare altri meccanismi di distribuzione, ad esempio dal controllo del codice sorgente.
    2.  Passare a Visual Studio e passare al progetto WebApp-OpenIDConnect-DotNet-code-v2. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Pubblica. Fare clic su Importa profilo sulla barra inferiore e importare il profilo di pubblicazione scaricato in precedenza.
    3.  Fare clic su Configura e nella scheda Connessione aggiornare l'URL di destinazione https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netin modo che sia un https nell'URL della home page, ad esempio . Scegliere Avanti.
    4.  Nella scheda Impostazioni verificare che l'opzione Abilita autenticazione organizzativa NON sia selezionata. Fare clic su Salva. Fare clic su Pubblica nella schermata principale.
    5.  Visual Studio pubblicherà il progetto e aprirà automaticamente un browser all'URL corrispondente. Se viene visualizzata la pagina Web predefinita del progetto, la pubblicazione ha avuto esito positivo.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementare Multi-Factor Authentication per Azure Active Directory
   Gli amministratori devono assicurarsi che gli account di sottoscrizione nel portale siano protetti. La sottoscrizione è vulnerabile agli attacchi perché gestisce le risorse create. Per proteggere la sottoscrizione, abilitare Multi-Factor Authentication nella scheda **Azure Active Directory** della sottoscrizione.

   Un Azure AD funziona in base ai criteri applicati a un utente o a gruppi di utenti che soddisfano determinati criteri.
Azure crea un criterio predefinito che specifica che gli amministratori devono utilizzare l'autenticazione a due fattori per accedere al portale.
Dopo aver abilitato questo criterio, potrebbe essere richiesto di disconnettersi e accedere di nuovo al portale di Azure.After enabling this policy, you might be prompted to sign out and sign back into the Azure portal.

Per abilitare l'autenticazione a più fattori per gli accessi amministratoreTo enable MFA for admin sign-ins

   1. Passare alla scheda Azure Active Directory nel portale di AzureGo to the **Azure Active Directory** tab in the Azure portal
   2. Nella categoria di sicurezza selezionare accesso condizionale. Viene visualizzata questa schermata

       ![Accesso condizionale - Criteri](./media/secure-aad-app/ad-mfa-conditional-add.png)

Se non è possibile creare un nuovo criterio

   1. Passare alla scheda **MFA.**
   2. Selezionare un collegamento di versione di **valutazione gratuita** di Azure AD Premium per sottoscrivere la versione di valutazione gratuita.

   ![Una versione di valutazione gratuita di Azure AD PremiumAn Azure AD Premium free trial](./media/secure-aad-app/ad-trial-premium.png)

Tornare alla schermata di accesso condizionale.

   1. Selezionare la nuova scheda dei criteri.
   2. Immettere il nome del criterio.
   3. Selezionare gli utenti o i gruppi per cui si desidera abilitare l'autenticazione a più fattori.
   4. In **Controlli di accesso**selezionare la scheda **Concedi** e quindi selezionare **Richiedi autenticazione** a più fattori (e altre impostazioni, se lo si desidera).

   ![Richiedere l'autenticazione MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   È possibile abilitare il criterio selezionando la casella di controllo nella parte superiore dello schermo o eseguire questa operazione nella scheda **Accesso condizionale.** Quando il criterio è abilitato, gli utenti devono eseguire l'autenticazione a più fattori per accedere al portale.

   Esiste un criterio di base che richiede l'autenticazione a più fattori per tutti gli amministratori di Azure.There's a baseline policy that requires MFA for all Azure administrators. È possibile abilitarlo immediatamente nel portale. L'abilitazione di questo criterio potrebbe invalidare la sessione corrente e forzare l'accesso.

   Se il criterio di base non è abilitato
   1.   Selezionare **Richiedi autenticazione a più fattori per gli amministratori**.
   2.   Selezionare **Usa criterio immediatamente**.

   ![Selezionare Usa criterio immediatamente](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Usare Azure Sentinel per monitorare app e risorseUse Azure Sentinel to monitor apps and resources

   Man mano che un'applicazione cresce, diventa difficile aggregare tutti i segnali di sicurezza e le metriche ricevute dalle risorse e renderli utili in modo orientato all'azione.

   Azure Sentinel è progettato per raccogliere dati, rilevare i tipi di minacce possibili e fornire visibilità sugli incidenti di sicurezza.
Durante l'attesa dell'intervento manuale, Azure Sentinel può fare affidamento su playbook prescritti per avviare avvisi e processi di gestione degli incidenti.

   L'app di esempio è composta da diverse risorse che Azure Sentinel può monitorare.
Per configurare Azure Sentinel, è innanzitutto necessario creare un'area di lavoro di Log Analytics in cui sono archiviati tutti i dati raccolti dalle varie risorse.

Per creare questa area di lavoro

   1. Nella casella di ricerca nel portale di Azure cercare **Log Analytics**. Selezionare **Aree di lavoro di Log Analytics**.

   ![Cercare aree di lavoro di Log AnalyticsSearch for Log Analytics workspaces](./media/secure-aad-app/sentinel-log-analytics.png)

   *Cercare aree di lavoro di Log AnalyticsSearch for Log Analytics workspaces*

   2. Nella pagina successiva selezionare **Aggiungi** e quindi specificare un nome, un gruppo di risorse e un percorso per l'area di lavoro.
   ![Creare un'area di lavoro Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Creare un'area di lavoro di Log AnalyticsCreate a Log Analytics workspace*

   3. Usare la casella di ricerca per cercare **Azure Sentinel.**

   ![Cercare Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Cercare Azure SentinelSearch for Azure Sentinel*

   4. Selezionare **Aggiungi** e quindi selezionare l'area di lavoro di Log Analytics creata in precedenza.

   ![Aggiungere un'area di lavoro di Log AnalyticsAdd a Log Analytics workspace](./media/secure-aad-app/sentinel-workspace-add.png)

   *Aggiungere un'area di lavoro di Log AnalyticsAdd a Log Analytics workspace*

   5. Nella pagina **Azure Sentinel - Connettori dati,** in **Configurazione**, selezionare **Connettori dati**. You see an array of Azure services that you can link to the Log Analytics storage instance for analysis in Azure Sentinel.

   ![Connettori dati di Log AnalyticsLog Analytics data connectors](./media/secure-aad-app/sentinel-connectors.png)

      *Aggiungere un connettore dati ad Azure SentinelAdd a data connector to Azure Sentinel*

   Ad esempio, per connettere il gateway applicazione, eseguire la procedura seguente:For example, to connect the application gateway, take these steps:

   1. Aprire il pannello dell'istanza del gateway applicazione di Azure.Open the Azure Application Gateway instance blade.
   2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
   3. Selezionare **Aggiungi impostazione diagnostica**.

   ![Aggiungere la diagnostica del gateway applicazioneAdd Application Gateway diagnostics](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Aggiungere la diagnostica del gateway applicazioneAdd Application Gateway diagnostics*

   4. Nella pagina Impostazioni di diagnostica selezionare l'area di lavoro Log Analytics creata e quindi selezionare tutte le metriche che si desidera raccogliere e inviare ad Azure Sentinel.On the **Diagnostic settings** page, select the Log Analytics workspace that you created and then select all the metrics that you want to collect and send to Azure Sentinel. Selezionare **Salva**.

   ![Impostazioni del connettore di Azure SentinelAzure Sentinel connector settings](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Considerazioni sul costo
   Se non si dispone già di un account Azure, è possibile crearne uno gratuito. Vai alla [pagina dell'account gratuito](https://azure.microsoft.com/free/) per iniziare, scopri cosa puoi fare con un account Azure gratuito e scopri quali prodotti sono gratuiti per 12 mesi.

   Per distribuire le risorse nell'app di esempio con le funzionalità di sicurezza, devi pagare alcune funzionalità premium. Man mano che l'app viene ridimensionata e i livelli e le versioni di valutazione gratuiti offerti da Azure devono essere aggiornati per soddisfare i requisiti dell'applicazione, i costi potrebbero aumentare. Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure per stimare i costi.

## <a name="next-steps"></a>Passaggi successivi
   Gli articoli seguenti consentono di progettare, sviluppare e distribuire applicazioni protette.

- [Progettazione](secure-design.md)
- [Sviluppo](secure-develop.md)
- [Distribuire](secure-deploy.md)

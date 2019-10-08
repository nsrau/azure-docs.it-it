---
title: Sviluppare un'applicazione Web di Azure AD protetta | Microsoft Docs
description: Questa semplice app di esempio implementa le procedure consigliate per la sicurezza che consentono di migliorare l'applicazione e il comportamento di sicurezza dell'organizzazione quando si sviluppa in Azure.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: v-fehase
ms.openlocfilehash: 87df7824a182e68d849fdf967f96b2974b7e0c16
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148172"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Sviluppare un'app sicura per un'app Azure AD
## <a name="overview"></a>Panoramica

Questo esempio è una semplice Azure Active Directory con app Web che si collega a risorse di sicurezza per lo sviluppo di app in Azure. L'app implementa le procedure consigliate per la sicurezza che consentono di migliorare l'applicazione e il comportamento di sicurezza dell'organizzazione quando si sviluppano app in Azure.

Gli script di distribuzione configurano l'infrastruttura. Dopo aver eseguito gli script di distribuzione, è necessario eseguire alcune operazioni di configurazione manuale nel portale di Azure per collegare insieme i componenti e i servizi. Questo esempio è destinato a sviluppatori esperti di Azure che operano nel settore della vendita al dettaglio e vogliono creare un Azure Active Directory protetto con l'infrastruttura di Azure sicura. 


Durante lo sviluppo e la distribuzione di questa app, si apprenderà come 
- Creare un'istanza Azure Key Vault, archiviare e recuperare i relativi segreti.
- Distribuire l'app Web di Azure, dedicata isolata con accesso al firewall front-end. 
- Creare e configurare un'istanza di applicazione Azure gateway con un firewall che usa OWASP primi 10 RuleSet. 
- Abilitare la crittografia dei dati in transito e a riposo usando i servizi di Azure. 
- Configurare i criteri e il Centro sicurezza di Azure per valutare la compliancies. 

Dopo aver sviluppato e distribuito l'app, è necessario configurare l'app Web di esempio seguente insieme alle misure di configurazione e sicurezza descritte.

## <a name="architecture"></a>Architettura
L'app è una tipica applicazione a più livelli con tre livelli. Il front-end, il back-end e il livello di database con componenti di monitoraggio e di gestione segreta integrati sono illustrati di seguito:

![Architettura dell'app](./media/secure-aad-app/architecture.png)

Questa soluzione usa i servizi di Azure seguenti. I dettagli dell'architettura di distribuzione sono disponibili nella sezione architettura di distribuzione. 

L'architettura è costituita da questi componenti

- [Gateway applicazione di Azure](../../application-gateway/index.yml). Fornisce il gateway e il firewall per l'architettura dell'applicazione.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Fornisce un servizio estendibile di gestione delle prestazioni delle applicazioni (APM) su più piattaforme.
- [Azure Key Vault](../../key-vault/index.yml). Archivia e crittografa i segreti dell'app e gestisce la creazione di criteri di accesso.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Fornisce servizi di gestione delle identità e degli accessi basati sul cloud, accesso e risorse.
- [Domain Name System di Azure](../../dns/dns-overview.md). Fornire il servizio per ospitare il dominio.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Consente di ridimensionare le applicazioni e di creare disponibilità elevata per i servizi.
- [App Web di Azure](../../app-service/overview.md).  Fornisce un servizio basato su HTTP per l'hosting di applicazioni Web.
- [Centro sicurezza di Azure](../../security-center/index.yml). offre protezione avanzata dalle minacce nei carichi di lavoro ibridi nel cloud.
- [Criteri di Azure](../../governance/policy/overview.md). Fornisce la valutazione delle risorse per la mancata conformità con i criteri assegnati.

## <a name="threat-model"></a>Modello di minaccia
La modellazione delle minacce è il processo di identificazione delle potenziali minacce per la sicurezza per l'azienda e l'applicazione e quindi per assicurarsi che sia presente un piano di mitigazione appropriato.

Questo esempio ha usato le [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) per implementare la modellazione delle minacce per l'app di esempio protetta. Grazie alla creazione di diagrammi dei componenti e dei flussi di dati, è possibile identificare i problemi e le minacce nelle fasi iniziali del processo di sviluppo. Il tempo e il denaro verranno salvati in seguito utilizzando questo.

Ecco il modello di minaccia per l'app di esempio

![Modello di minaccia](./media/secure-aad-app/threat-model.png)

Nella schermata seguente vengono illustrate alcune minacce di esempio e potenziali vulnerabilità generate dallo strumento di modellazione delle minacce. Il modello di minaccia fornisce una panoramica della superficie di attacco esposta e chiede agli sviluppatori di considerare come mitigare i problemi.

![Output del modello di minaccia](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Prerequisiti
Per fare in modo che l'applicazione sia in esecuzione, è necessario installare gli strumenti seguenti:

- Un editor di codice per modificare e visualizzare il codice dell'applicazione. [Visual Studio Code](https://code.visualstudio.com/) è un'opzione open source.
- [Interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) della riga di comando di Azure nel computer di sviluppo.
- [Git](https://git-scm.com/) nel sistema. Git viene usato per clonare il codice sorgente localmente.
- [JQ](https://stedolan.github.io/jq/), uno strumento UNIX per l'esecuzione di query su JSON in modo semplice e intuitivo.

Per distribuire le risorse dell'app di esempio, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/) per testare l'app di esempio.

Dopo l'installazione di questi strumenti, si è pronti per distribuire l'app in Azure.

### <a name="implementation-guidance"></a>Indicazioni relative all'implementazione
Lo script di distribuzione è uno script che può essere suddiviso in quattro fasi. Ogni fase distribuisce e configura una risorsa di Azure che si trova nel [diagramma dell'architettura](#architecture).

Le quattro fasi sono

- Distribuire Azure Key Vault.
- Distribuire app Web di Azure.
- Distribuire il gateway applicazione con web application firewall.
- Configurare un Azure AD con l'app distribuita.

Ogni fase si basa su quella precedente usando la configurazione dalle risorse distribuite in precedenza.

Per completare i passaggi di implementazione, verificare di aver installato gli strumenti elencati in [prerequisiti](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Distribuisci Azure Key Vault
In questa sezione viene creata e distribuita un'istanza di Azure Key Vault utilizzata per archiviare segreti e certificati.

Dopo aver completato la distribuzione, è presente un'istanza di Azure Key Vault distribuita in Azure.

Per distribuire Azure Key Vault tramite PowerShell
 
1. Dichiarare le variabili per Azure Key Vault.
2. Registrare il provider di Azure Key Vault.
3. Creare il gruppo di risorse per l'istanza.
4. Creare l'istanza Azure Key Vault nel gruppo di risorse creato nel passaggio 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Il seguente Azure AD utente avrà le autorizzazioni di amministratore per il Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Registrare i provider AZ
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Creare l'istanza di Azure Key Vault
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Aggiungere i criteri di amministratore al Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Per creare un criterio di accesso per consentire a un utente di ottenere ed elencare le chiavi crittografiche, i certificati e i segreti se si conosce il nome dell'entità utente:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

È consigliabile usare identità gestite per le risorse di Azure nelle app che usano Key Vault per accedere alle risorse. Il comportamento di sicurezza aumenta quando le chiavi di accesso Key Vault non vengono archiviate nel codice o nella configurazione.

Un certificato radice è incluso nel contenitore. I passaggi necessari per ottenere il certificato sono

1. Scaricare il file del certificato dall' [autorità di certificazione](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. Decodificare il file di certificato:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Questo script crea un'identità assegnata per l'istanza del servizio app che può essere usata con MSI per interagire con Azure Key Vault senza segreti di codifica rigidi nel codice o nella configurazione.

Passare all'istanza di Azure Key Vault nel portale per autorizzare l'identità assegnata nella scheda criteri di accesso. Selezionare **Aggiungi nuovi criteri di accesso**. In **Seleziona entità**cercare il nome dell'applicazione simile al nome dell'istanza del servizio app creata.
Un'entità servizio associata all'applicazione deve essere visibile. Selezionare la pagina e salvare i criteri di accesso, come illustrato nello screenshot seguente.

Poiché l'applicazione deve solo recuperare le chiavi, selezionare l'autorizzazione **Get** nelle opzioni Secrets, consentendo l'accesso, riducendo al contempo i privilegi concessi.

![Criteri di accesso insieme di credenziali delle chiavi](./media/secure-aad-app/kv-access-policy.png)

*Creare un criterio di accesso Key Vault*

Salvare i criteri di accesso, quindi salvare la nuova modifica nella scheda **criteri di accesso** per aggiornare i criteri.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuire il gateway applicazione con web application firewall abilitato
Nelle app Web non è consigliabile esporre i servizi direttamente al mondo esterno su Internet.
Il bilanciamento del carico e le regole del firewall forniscono maggiore sicurezza e controllo sul traffico in ingresso e consentono di gestirlo.

Per distribuire un'istanza del gateway applicazione

1. Creare il gruppo di risorse per ospitare il gateway applicazione.
2. Eseguire il provisioning di una rete virtuale per connettersi al gateway.
3. Creare una subnet per il gateway nella rete virtuale.
4. Effettuare il provisioning di un indirizzo IP pubblico.
5. Eseguire il provisioning del gateway applicazione.
6. Abilitare web application firewall sul gateway.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.0.0/24

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

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.0.0

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 

#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Distribuire app Web di Azure
App Azure servizio ti permette di creare e ospitare app Web usando linguaggi come Python, Ruby, C#e Java. Azure supporta anche i contenitori personalizzati, che possono consentire l'esecuzione praticamente di tutti i linguaggi di programmazione sulla piattaforma del servizio app Azure.

#### <a name="create-an-app-service-plan-in-free-tier"></a>Creare un piano di servizio app nel livello gratuito
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Crea un'app Web
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>Prima di continuare, passare all'interfaccia utente di configurazione di Azure Domain Name System per il dominio personalizzato e seguire https://aka.ms/appservicecustomdns le istruzioni in per configurare un record CNAME per il nome host "www" e puntare al nome di dominio predefinito dell'app Web

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Aggiornare il piano di servizio app al livello condiviso (minimo richiesto dai domini personalizzati)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Aggiungere un nome di dominio personalizzato all'app Web
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Indicazioni e consigli

### <a name="network"></a>Rete
Dopo aver completato la distribuzione, è disponibile un gateway applicazione con web application firewall abilitata.

L'istanza del gateway espone la porta 443 per HTTPS. Questa configurazione garantisce che l'app sia accessibile solo sulla porta 443 tramite HTTPS.

Bloccare le porte inutilizzate e limitare l'esposizione alla superficie di attacco è una procedura di sicurezza consigliata.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Aggiungere gruppi di sicurezza di rete all'istanza del servizio app

Le istanze del servizio app possono essere integrate con le reti virtuali. Questa integrazione consente di configurare i criteri del gruppo di sicurezza di rete che gestiscono il traffico in ingresso e in uscita dell'app.

1. Per abilitare questa funzionalità, nel pannello istanza servizio app Azure, in **Impostazioni**, selezionare **rete**. Nel riquadro destro configurare in **integrazione VNet**.

   ![Nuova integrazione della rete virtuale](./media/secure-web-app/app-vnet-menu.png)

    *Nuova integrazione della rete virtuale per il servizio app*
1. Nella pagina successiva selezionare **Aggiungi VNET (anteprima)** .

1. Nel menu successivo selezionare la rete virtuale creata nella distribuzione che inizia con `aad-vnet`. È possibile creare una nuova subnet o selezionarne una esistente.
   In tal caso, creare una nuova subnet. Impostare l' **intervallo di indirizzi** su **10.0.3.0/24** e assegnare un nome alla subnet **app-subnet**.

   ![Configurazione della rete virtuale del servizio app](./media/secure-web-app/app-vnet-config.png)

    *Configurazione della rete virtuale per il servizio app*

Ora che è stata abilitata l'integrazione della rete virtuale, è possibile aggiungere gruppi di sicurezza di rete all'app.

1. Utilizzare la casella di ricerca per cercare **gruppi di sicurezza di rete**. Nei risultati selezionare **gruppi di sicurezza di rete** .

    ![Cercare gruppi di sicurezza di rete](./media/secure-web-app/nsg-search-menu.png)

    *Cercare gruppi di sicurezza di rete*

2. Nel menu successivo selezionare **Aggiungi**. Immettere il **nome** del NSG e il **gruppo di risorse** in cui deve essere individuato. Questo NSG verrà applicato alla subnet del gateway applicazione.

    ![Creare un NSG](./media/secure-web-app/nsg-create-new.png)

    *Creare un NSG*

3. Dopo aver creato il NSG, selezionarlo. Nel pannello in **Impostazioni**selezionare **regole di sicurezza in ingresso**. Configurare queste impostazioni per consentire le connessioni in ingresso nel gateway applicazione sulla porta 443.

   ![Configurare NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configurare NSG*

4. Nelle regole in uscita per il gateway NSG aggiungere una regola che consenta le connessioni in uscita all'istanza del servizio app creando una regola destinata al tag di servizio`AppService`

   ![Aggiungere regole in uscita per NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Aggiungere regole in uscita per NSG*

    Aggiungere un'altra regola in uscita per consentire al gateway di inviare regole in uscita a una rete virtuale.

   ![Aggiungere un'altra regola in uscita](./media/secure-web-app/nsg-outbound-vnet.png)

    *Aggiungere un'altra regola in uscita*

5. Nel pannello subnet del NSG selezionare **associa**, selezionare la rete virtuale creata nella distribuzione e selezionare la subnet del gateway denominata **GW-Subnet**. NSG viene applicato alla subnet.

6. Creare un'altra NSG come nel passaggio precedente, questa volta per l'istanza del servizio app. Assegnargli un nome. Aggiungere la regola in ingresso per la porta 443 come per il gateway applicazione NSG.

   Se è stata distribuita un'istanza del servizio app in un'istanza di ambiente del servizio app, che non è il caso di questa app, è possibile aggiungere regole in ingresso per consentire i probe di integrità dei servizi di Azure aprendo le porte 454-455 nei gruppi di sicurezza in ingresso del servizio app NSG. Ecco la configurazione:

   ![Aggiungere regole per i probe di integrità dei servizi di Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Aggiungere regole per i probe di integrità dei servizi di Azure (solo ambiente del servizio app)*

Per limitare la superficie di attacco, modificare le impostazioni di rete del servizio app per consentire solo al gateway applicazione di accedere all'applicazione.
Per applicare le impostazioni, passare alla scheda rete del servizio app, selezionare la scheda **restrizioni IP** e creare una regola di autorizzazione che consenta solo all'indirizzo IP del gateway applicazione di accedere direttamente al servizio. È possibile recuperare l'indirizzo IP del gateway dalla relativa pagina di panoramica. Nella scheda **CIDR indirizzo IP** immettere l'indirizzo IP nel formato seguente: `<GATEWAY_IP_ADDRESS>/32`.

![Consenti solo il gateway](./media/secure-web-app/app-allow-gw-only.png)

*Consenti solo all'indirizzo IP del gateway di accedere al servizio app*

### <a name="azure-domain-name-system"></a>Domain Name System di Azure 
Il Domain Name System di Azure, o Domain Name System di Azure, è responsabile della conversione (o risoluzione) del nome di un sito Web o di un nome di servizio nel relativo indirizzo IP. Azure Domain Name System (https://docs.microsoft.com/azure/dns/dns-overview) è un servizio di hosting per domini Domain Name System che fornisce la risoluzione dei nomi usando l'infrastruttura di Azure. Ospitando i domini in Azure, gli utenti possono gestire i record Domain Name System usando le stesse credenziali, le stesse API, gli strumenti e la fatturazione degli altri servizi di Azure. Azure Domain Name System supporta anche domini Domain Name System privati.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Crittografia dischi di Azure sfrutta la funzionalità BitLocker di Windows per fornire la crittografia del volume per i dischi dati. La soluzione si integra con Azure Key Vault per semplificare il controllo e la gestione delle chiavi di crittografia dei dischi.

### <a name="identity-management"></a>Gestione delle identità
Le tecnologie seguenti forniscono funzionalità per gestire l'accesso ai dati dei titolari di carte nell'ambiente Azure
- Azure Active Directory è il servizio Microsoft di gestione di identità e Directory basato sul cloud multi-tenant. Tutti gli utenti per questa soluzione vengono creati in Azure Active Directory, inclusi gli utenti che accedono alla WebApp di Azure.
- Il controllo degli accessi in base al ruolo di Azure consente agli amministratori di definire le autorizzazioni di accesso con granularità fine per concedere solo la quantità di accesso necessario agli utenti per eseguire i propri processi. Invece di concedere a ogni utente autorizzazioni senza limiti per le risorse di Azure, gli amministratori possono consentire solo determinate azioni per l'accesso ai dati dei possessori di carte. L'accesso alla sottoscrizione è limitato all'amministratore della sottoscrizione.
- Azure Active Directory Privileged Identity Management consente ai clienti di ridurre al minimo il numero di utenti che hanno accesso a determinate informazioni, ad esempio i dati di titolari di carte. Gli amministratori possono usare Azure Active Directory Privileged Identity Management per individuare, limitare e monitorare le identità con privilegi e il rispettivo accesso alle risorse. Questa funzionalità può essere usata anche per applicare l'accesso amministrativo on demand e Just-In-Time quando necessario.
- Azure Active Directory Identity Protection rileva le potenziali vulnerabilità che interessano le identità di un'organizzazione, configura le risposte automatiche per rilevare azioni sospette correlate alle identità di un'organizzazione ed esamina sospette eventi imprevisti per eseguire le azioni appropriate per risolverli.
### <a name="secrets-management"></a>Gestione dei segreti
La soluzione USA Azure Key Vault per la gestione di chiavi e segreti. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Le seguenti funzionalità di Azure Key Vault consentono ai clienti di proteggere e accedere a tali dati
   - I criteri di accesso avanzati vengono configurati in base alle necessità.
   - I criteri di accesso di Key Vault sono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti.
   - Tutti i segreti e le chiavi in Key Vault hanno date di scadenza.
   - Tutte le chiavi in Key Vault sono protette da moduli di protezione hardware specializzati. Il tipo di chiave è un modulo di protezione hardware (HSM) protetto da una chiave RSA a 2048 bit.
   - Con Key Vault, è possibile crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dei dati). File PFX e password) usando chiavi protette da moduli di protezione hardware (HSM). 
   - Usare il controllo degli accessi in base al ruolo (RBAC) per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito.     
   - Usare Key Vault per gestire i certificati TLS con il rinnovo automatico. 
   - I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
   - Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie.
### <a name="azure-security-center"></a>Centro sicurezza di Azure
Con il Centro sicurezza di Azure, i clienti possono applicare e gestire centralmente i criteri di sicurezza tra i carichi di lavoro, limitare l'esposizione alle minacce e rilevare e rispondere agli attacchi. Inoltre 
   - Il Centro sicurezza di Azure accede alle configurazioni esistenti dei servizi di Azure per fornire consigli per la configurazione e il servizio per migliorare le posture della sicurezza e proteggere i dati.
   - Il Centro sicurezza di Azure usa una serie di funzionalità di rilevamento per avvisare i clienti riguardo a potenziali attacchi contro gli ambienti in cui operano. Questi avvisi contengono informazioni importanti relative a cosa ha attivato l'avviso, alle risorse interessate e all'origine dell'attacco. Il Centro sicurezza di Azure include un set di avvisi di sicurezza predefiniti, che vengono attivati quando si verifica una minaccia o un'attività sospetta. Le regole di avviso personalizzate nel centro sicurezza di Azure consentono ai clienti di definire nuovi avvisi di sicurezza in base ai dati già raccolti dal proprio ambiente.
   - Il Centro sicurezza di Azure offre avvisi di sicurezza e imprevisti classificati in ordine di priorità semplificando l'individuazione e gestione di potenziali problemi di sicurezza per i clienti. Viene generato un report di intelligence per le minacce per ogni minaccia rilevata, che consente ai team di risposta agli eventi imprevisti di analizzare e correggere le minacce.
### <a name="azure-application-gateway"></a>Gateway applicazione di Azure 
   L'architettura riduce il rischio di vulnerabilità della sicurezza tramite un gateway applicazione di Azure con un web application firewall configurato e il set di regole OWASP abilitato. Funzionalità aggiuntive includono
   - SSL end-to-end.
   - Disabilitare TLS v 1.0 e v 1.1.
   - Abilitare TLSv 1.2.
   - Web Application Firewall (modalità di prevenzione).
   - Modalità di prevenzione con RuleSet OWASP 3,0.
   - Abilitare la registrazione diagnostica.
   - Probe di integrità personalizzati.
   - Il Centro sicurezza di Azure e un Azure Advisor forniscono protezione e notifiche aggiuntive. Centro sicurezza di Azure mette a disposizione anche un sistema di reputazione.
### <a name="logging-and-auditing"></a>Registrazione e controllo
I servizi di Azure registrano in modo completo le attività di sistema e degli utenti e l'integrità del sistema:
   - Log attività: i [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse di una sottoscrizione. I log attività possono essere utili per determinare l'iniziatore di un'operazione, l'ora in cui si è verificata e lo stato.
   - Log di diagnostica: i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) includono tutti i log generati da ogni risorsa. Questi log includono i registri eventi del sistema di Windows, i log di archiviazione di Azure, i log di controllo Key Vault e l'accesso del gateway applicazione e i log del firewall. Tutti i log di diagnostica eseguono operazioni di scrittura in un account di archiviazione di Azure centralizzato e crittografato per finalità di archiviazione. La conservazione può essere configurata dall'utente per un massimo di 730 giorni per soddisfare i requisiti di conservazione specifici dell'organizzazione.
### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
   Questi log vengono consolidati nei [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/) per l'elaborazione, l'archiviazione e la creazione di report del dashboard. Dopo la raccolta, i dati vengono organizzati in tabelle separate per tipo nelle aree di lavoro di Log Analytics, in modo che sia possibile analizzare tutti i dati insieme, indipendentemente dalla rispettiva origine. Il Centro sicurezza di Azure si integra inoltre con i log di monitoraggio di Azure, consentendo ai clienti di usare query kusto per accedere ai dati degli eventi di sicurezza e combinarli con i dati di altri servizi.

   Le soluzioni di [monitoraggio](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) di Azure seguenti sono incluse come parte di questa architettura

   - [Valutazione Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la soluzione Controllo integrità Active Directory valuta il rischio e l'integrità degli ambienti server a intervalli regolari e fornisce un elenco di elementi consigliati specifici per l'infrastruttura di server distribuita, classificati in ordine di priorità.
   - [Integrità agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): La soluzione Integrità agente segnala il numero di agenti distribuiti e la relativa distribuzione geografica, nonché il numero di agenti che non rispondono e il numero di agenti, che inviano dati operativi.
   - [Analisi log attività](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la soluzione Analisi log attività offre assistenza per l'analisi dei log attività di Azure in tutte le sottoscrizioni di Azure per un cliente.
### <a name="azure-monitor"></a>Monitoraggio di Azure
   [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)consente agli utenti di tenere traccia delle prestazioni, mantenere la sicurezza e identificare le tendenze consentendo alle organizzazioni di controllare, creare avvisi e archiviare dati, incluso il rilevamento delle chiamate API nelle risorse di Azure.
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. Application Insights rileva eventuali anomalie nelle prestazioni e i clienti possono usarlo per monitorare l'applicazione Web in tempo reale. Include avanzati strumenti di analisi che consentono ai clienti di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità da parte dei clienti.

### <a name="azure-key-vault"></a>Azure Key Vault
   Creare un insieme di credenziali per l'organizzazione in cui archiviare le chiavi e mantenere la responsabilità per le attività operative, come indicato di seguito

   - I dati archiviati in Key Vault includono   
   - Chiave di Application Insights
   - Chiave di accesso all'archiviazione dati
   - Stringa di connessione
   - Nome tabella dati
   - Credenziali utente
   - I criteri di accesso avanzati vengono configurati in base alle necessità
   - I criteri di accesso Key Vault vengono definiti con le autorizzazioni minime necessarie per le chiavi e i segreti
   - Tutti i segreti e le chiavi in Key Vault hanno date di scadenza
   - Tutte le chiavi nel Key Vault sono protette dal modulo di protezione hardware (HSM) [chiave Type = hardware Security Module (HSM) protetto       
     Chiave RSA a 2048 bit]
   - A tutti gli utenti e le identità vengono concesse le autorizzazioni minime necessarie tramite il controllo degli accessi in base al ruolo (RBAC)
   - Le applicazioni non condividono un'istanza di Key Vault a meno che non si considerino attendibili reciprocamente e non debbano accedere agli stessi segreti in fase di esecuzione
   - I log di diagnostica per Key Vault sono abilitati con un periodo di conservazione di almeno 365 giorni
   - Le operazioni di crittografia consentite per le chiavi sono limitate a quelle necessarie

### <a name="vpn-and-expressroute"></a>VPN ed ExpressRoute
   È necessario configurare un tunnel VPN o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) sicuro per stabilire una connessione alle risorse distribuite come parte di questa architettura di riferimento dell'applicazione Web PaaS. La configurazione corretta di una VPN o di una connessione ExpressRoute consente ai clienti di aggiungere un livello di protezione per i dati in transito.

   L'implementazione di un tunnel VPN sicuro con Azure consente di creare una connessione virtuale privata tra una rete locale e una rete virtuale di Azure. Questa connessione viene eseguita su Internet e consente ai clienti di "effettuare il tunneling" in modo sicuro all'interno di un collegamento crittografato tra la rete del cliente e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La modalità tunnel IPsec viene utilizzata in questa opzione come meccanismo di crittografia.

   Poiché il traffico entro il tunnel VPN attraversa Internet con una connessione VPN da sito a sito, Microsoft offre un'altra opzione di connessione ancora più sicura. Azure ExpressRoute è un collegamento WAN dedicato tra Azure e una posizione locale o un provider di hosting di Exchange. Poiché le connessioni ExpressRoute non sfruttano la rete Internet, queste connessioni offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. Poiché inoltre si tratta di una connessione diretta del provider di telecomunicazioni del cliente, i dati non vengono trasmessi su Internet e quindi non vengono esposti a Internet.

   Sono [disponibili](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedure consigliate per l'implementazione di una rete ibrida protetta che estende una rete locale in Azure.

#### <a name="implement-azure-active-directory-oidc"></a>Implementare Azure Active Directory OIDC

1. Per clonare il repository del codice sorgente, usare questo comando git

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Aggiornare gli URL di Reindirizzamento
1.  Tornare al portale di Azure. Nel riquadro di spostamento a sinistra selezionare il servizio Azure Active Directory, quindi selezionare Registrazioni app.
2.  Nella schermata risultante selezionare l'applicazione WebApp-OpenIDConnect-DotNet-code-V2.
3.  Nella scheda autenticazione nella sezione URI di reindirizzamento selezionare Web nella casella combinata e aggiungere gli URI di reindirizzamento seguenti.
    [https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net](https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net ) https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o nella sezione Impostazioni avanzate impostare Disconnetti URL su https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  Nella scheda personalizzazione o aggiornare l'URL della Home page all'indirizzo del servizio app, ad esempio https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o salvare la configurazione.
5.  Se l'applicazione chiama un'API Web, assicurarsi di applicare le modifiche necessarie nel progetto appSettings. JSON, in modo che chiami l'URL dell'API pubblicata invece di localhost.
Pubblicazione dell'esempio
    1.  Dalla scheda Panoramica del servizio app scaricare il profilo di pubblicazione facendo clic sul collegamento Ottieni profilo di pubblicazione e salvarlo. È inoltre possibile utilizzare altri meccanismi di distribuzione, ad esempio dal controllo del codice sorgente.
    2.  Passare a Visual Studio e passare al progetto WebApp-OpenIDConnect-DotNet-code-V2. Fare clic con il pulsante destro del mouse sul progetto nella Esplora soluzioni e scegliere pubblica. Fare clic su Importa profilo nella barra inferiore e importare il profilo di pubblicazione scaricato in precedenza.
    3.  Fare clic su Configura e nella scheda connessione aggiornare l'URL di destinazione in modo che sia un HTTPS nell'URL home page, ad esempio https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. Fare clic su Avanti.
    4.  Nella scheda Impostazioni verificare che l'opzione Abilita autenticazione organizzativa non sia selezionata. Fare clic su Save. Fare clic su pubblica nella schermata principale.
    5.  Visual Studio pubblicherà il progetto e aprirà automaticamente un browser per l'URL del progetto. Se viene visualizzata la pagina Web predefinita del progetto, la pubblicazione ha avuto esito positivo.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementare Multi-Factor Authentication per Azure Active Directory
   Gli amministratori devono assicurarsi che gli account di sottoscrizione nel portale siano protetti. La sottoscrizione è vulnerabile agli attacchi perché gestisce le risorse create. Per proteggere la sottoscrizione, abilitare Multi-Factor Authentication nella scheda **Azure Active Directory** della sottoscrizione.

   Un Azure AD funziona in base ai criteri applicati a un utente o a gruppi di utenti che soddisfano determinati criteri.
Azure crea un criterio predefinito che specifica che gli amministratori devono eseguire l'autenticazione a due fattori per accedere al portale.
Dopo aver abilitato questo criterio, potrebbe essere richiesto di disconnettersi e accedere nuovamente al portale di Azure.

Per abilitare l'autenticazione a più fattori per gli accessi amministrativi

   1. Passare alla scheda **Azure Active Directory** nella portale di Azure
   2. Nella categoria sicurezza selezionare accesso condizionale. Viene visualizzata questa schermata

       ![Accesso condizionale-criteri](./media/secure-aad-app/ad-mfa-conditional-add.png)

Se non è possibile creare un nuovo criterio

   1. Passare **alla scheda autenticazione** a più fattori.
   2. Selezionare un collegamento alla **versione di valutazione gratuita** di Azure ad Premium per sottoscrivere la versione di valutazione gratuita.

   ![Una versione di valutazione gratuita di Azure AD Premium](./media/secure-aad-app/ad-trial-premium.png)

Tornare alla schermata di accesso condizionale.

   1. Selezionare la scheda nuovo criterio.
   2. Immettere il nome del criterio.
   3. Selezionare gli utenti o i gruppi per i quali si desidera abilitare l'autenticazione a più fattori.
   4. In **controlli di accesso**selezionare la scheda **Concedi** , quindi selezionare **Richiedi autenticazione** a più fattori e altre impostazioni, se lo si desidera.

   ![Richiedere l'autenticazione MFA](./media/secure-aad-app/ad-mfa-conditional-add.png)

   È possibile abilitare il criterio selezionando la casella di controllo nella parte superiore della schermata oppure facendo clic sulla scheda **accesso condizionale** . Quando il criterio è abilitato, gli utenti devono avere l'autenticazione a più fattori per accedere al portale.

   Sono disponibili criteri di base che richiedono l'autenticazione a più fattori per tutti gli amministratori di Azure. È possibile abilitarlo immediatamente nel portale. L'abilitazione di questo criterio potrebbe invalidare la sessione corrente e forzare l'accesso.

   Se il criterio di base non è abilitato
   1.   Selezionare **Richiedi autenticazione a**più fattori per gli amministratori.
   2.   Selezionare **usa immediatamente i criteri**.

   ![Selezionare Usa immediatamente il criterio](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Usare Sentinel di Azure per monitorare le app e le risorse

   Man mano che un'applicazione cresce, diventa difficile aggregare tutti i segnali di sicurezza e le metriche ricevute dalle risorse e renderle utili in modo orientato alle azioni.

   Azure Sentinel è progettato per raccogliere dati, rilevare i tipi di minacce possibili e fornire visibilità agli eventi imprevisti della sicurezza.
Sebbene attenda l'intervento manuale, Azure Sentinel può basarsi su PlayBook pre-scritti per avviare gli avvisi e i processi di gestione degli eventi imprevisti.

   L'app di esempio è costituita da diverse risorse che possono essere monitorate da Azure Sentinel.
Per configurare Sentinel di Azure, è prima di tutto necessario creare un'area di lavoro di Log Analytics che archivia tutti i dati raccolti dalle varie risorse.

Per creare questa area di lavoro

   1. Nella casella di ricerca del portale di Azure cercare **log Analytics**. Selezionare **Aree di lavoro di Log Analytics**.

   ![Cerca aree di lavoro Log Analytics](./media/secure-aad-app/sentinel-log-analytics.png)

   *Cerca aree di lavoro Log Analytics*

   2. Nella pagina successiva selezionare **Aggiungi** e quindi specificare un nome, un gruppo di risorse e un percorso per l'area di lavoro.
   ![Creare un'area di lavoro Log Analytics](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Creare un'area di lavoro Log Analytics*

   3. Usare la casella di ricerca per cercare **Azure Sentinel**.

   ![Cercare Azure Sentinel](./media/secure-aad-app/sentinel-add.png)

   *Cercare Azure Sentinel*

   4. Selezionare **Aggiungi** e quindi selezionare l'area di lavoro log Analytics creata in precedenza.

   ![Aggiungere un'area di lavoro Log Analytics](./media/secure-aad-app/sentinel-workspace-add.png)

   *Aggiungere un'area di lavoro Log Analytics*

   5. Nella pagina **Sentinel di Azure-connettori dati** , in **configurazione**, selezionare **connettori dati**. Viene visualizzata una matrice di servizi di Azure che è possibile collegare all'istanza di archiviazione Log Analytics per l'analisi in Sentinel di Azure.

   ![Connettori di dati Log Analytics](./media/secure-aad-app/sentinel-connectors.png)

      *Aggiungere un connettore dati ad Azure Sentinel*

   Ad esempio, per connettere il gateway applicazione, seguire questa procedura:

   1. Aprire il pannello dell'istanza di applicazione Azure gateway.
   2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
   3. Selezionare **Aggiungi impostazioni di diagnostica**.

   ![Aggiungere la diagnostica del gateway applicazione](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Aggiungere la diagnostica del gateway applicazione*

   4. Nella pagina **impostazioni di diagnostica** selezionare l'area di lavoro log Analytics creata e quindi selezionare tutte le metriche che si desidera raccogliere e inviare a Sentinel di Azure. Selezionare **Salva**.

   ![Impostazioni del connettore Sentinel di Azure](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Considerazioni sul costo
   Se non si ha già un account Azure, è possibile crearne uno gratuito. Vai alla [pagina account gratuito](https://azure.microsoft.com/free/) per iniziare, Scopri cosa puoi fare con un account Azure gratuito e Scopri quali prodotti sono gratuiti per 12 mesi.

   Per distribuire le risorse nell'app di esempio con le funzionalità di sicurezza, è necessario pagare per alcune funzionalità Premium. Quando l'app viene ridimensionata e i livelli gratuiti e le versioni di valutazione offerte da Azure devono essere aggiornati per soddisfare i requisiti dell'applicazione, i costi potrebbero aumentare. Usa il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure per stimare i costi.

## <a name="next-steps"></a>Passaggi successivi
   Gli articoli seguenti possono essere utili per progettare, sviluppare e distribuire applicazioni protette.

- [Progettazione](secure-design.md)
- [Sviluppo](secure-develop.md)
- [Distribuire](secure-deploy.md)

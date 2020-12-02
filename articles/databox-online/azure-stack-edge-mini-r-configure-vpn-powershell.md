---
title: Configurare la VPN sul dispositivo Mini R Azure Stack Edge usando Azure PowerShell
description: Viene descritto come configurare una VPN nel dispositivo Mini R Azure Stack Edge usando uno script Azure PowerShell per creare risorse di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 763ccd397d8cd704ca161032e65f17979bccb53b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467894"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Configurare la VPN sul dispositivo Mini R Azure Stack Edge tramite Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

L'opzione VPN fornisce un secondo livello di crittografia per i dati in transito su *TLS* dal dispositivo mini-r Azure stack Edge o da Azure stack Edge Pro r in Azure. È possibile configurare VPN sul dispositivo Mini R Azure Stack Edge tramite il portale di Azure o tramite il Azure PowerShell. 

Questo articolo descrive i passaggi necessari per configurare una VPN da punto a sito (P2S) nel dispositivo Azure Stack Edge Mini R usando uno script Azure PowerShell per creare la configurazione nel cloud. La configurazione nel dispositivo Azure Stack Edge viene eseguita tramite l'interfaccia utente locale.

## <a name="about-vpn-setup"></a>Informazioni sulla configurazione VPN

Una connessione gateway VPN P2S consente di creare una connessione sicura alla rete virtuale da un singolo computer client o dal dispositivo Mini R Azure Stack Edge. Si avvia la connessione P2S dal computer client o dal dispositivo. La connessione P2S in questo caso usa la VPN IKEv2, una soluzione VPN IPsec basata su standard.

Il flusso di lavoro tipico è costituito dai passaggi seguenti:

1. Configurare i prerequisiti.
2. Configurare le risorse necessarie in Azure.
    1. Creare e configurare una rete virtuale e le subnet obbligatorie. 
    2. Creare e configurare un gateway VPN di Azure (gateway di rete virtuale).
    3. Configurare il firewall di Azure e aggiungere le regole di rete e app.
    4. Creare tabelle di routing di Azure e aggiungere route.
    5. Abilitare la connessione da punto a sito nel gateway VPN.
        1. Aggiungere il pool di indirizzi client.
        2. Configurare il tipo di tunnel.
        3. Configurare il tipo di autenticazione.
        4. Creare un certificato.
        5. Caricare il certificato.
    6. Scarica Rubrica telefonica.
3. Configurare la VPN nell'interfaccia utente Web locale del dispositivo. 
    1. Fornire una rubrica telefonica.
    2. Fornire il file di tag di servizio (JSON).


I passaggi dettagliati sono disponibili nelle sezioni seguenti.

## <a name="configure-prerequisites"></a>Configurazione dei prerequisiti

- È necessario avere accesso a un dispositivo Mini R Azure Stack Edge installato in base alle istruzioni riportate in [installare il dispositivo mini r di Azure stack Edge](azure-stack-edge-mini-r-deploy-install.md). Questo dispositivo stabilirà una connessione P2S con Azure. 

- È necessario avere accesso a una sottoscrizione di Azure valida abilitata per il servizio Azure Stack Edge in Azure. Usare questa sottoscrizione per creare una risorsa corrispondente in Azure per gestire il dispositivo Mini R Azure Stack Edge.  

- Si ha accesso a un client Windows che verrà usato per accedere al dispositivo Mini R di Azure Stack Edge. Questo client verrà usato per creare la configurazione nel cloud a livello di codice.

    1. Per installare la versione richiesta di PowerShell nel client Windows, eseguire i comandi seguenti:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Per connettersi all'account e alla sottoscrizione di Azure, eseguire i comandi seguenti:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Fornire il nome della sottoscrizione di Azure in uso con il dispositivo Mini R Azure Stack Edge per configurare la VPN.

    3. [Scaricare lo script](https://aka.ms/ase-vpn-deployment) necessario per creare la configurazione nel cloud. Lo script:
        
        - Creare una rete virtuale di Azure e le subnet seguenti: *GatewaySubnet* e *AzureFirewallSubnet*.
        - Creare e configurare un gateway VPN di Azure.
        - Creare e configurare un gateway di rete locale di Azure.
        - Creare e configurare una connessione VPN di Azure tra il gateway VPN di Azure e il gateway di rete locale.
        - Creare un firewall di Azure e aggiungere le regole di rete e le regole dell'app.
        - Creare una tabella di routing di Azure e aggiungervi le route.

    4. Creare il gruppo di risorse nel portale di Azure in cui si vogliono creare le risorse di Azure. Passare all'elenco dei servizi in portale di Azure, selezionare **gruppo di risorse** e quindi selezionare **+ Aggiungi**. Specificare le informazioni sulla sottoscrizione e il nome per il gruppo di risorse e quindi selezionare **Crea**. Se si passa a questo gruppo di risorse, al momento non dovrebbe avere risorse al suo interno.

        ![Gruppo di risorse di Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. È necessario disporre di un certificato con codifica 64 in base al `.cer` formato per il dispositivo Mini R Azure stack Edge. Il certificato deve essere caricato nel dispositivo Azure Stack Edge come `pfx` con una chiave privata. Questo certificato deve anche essere installato nella radice attendibile dell'archivio nel client che sta tentando di stabilire la connessione P2S.

## <a name="use-the-script"></a>USA lo script

Prima di tutto, modificare il `parameters-p2s.json` file per immettere i parametri. Eseguire quindi lo script usando il file JSON modificato.

Ognuno di questi passaggi è descritto nelle sezioni seguenti.

### <a name="download-service-tags-file"></a>Scarica file tag servizio

È possibile che si disponga già `ServiceTags.json` di un file nella cartella in cui è stato scaricato lo script. In caso contrario, è possibile scaricare il file dei tag del servizio.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Modificare il file dei parametri

Il primo passaggio consiste nel modificare il `parameters-p2s.json` file e salvare le modifiche. 

Per le risorse di Azure create, verranno forniti i nomi seguenti:

|Nome parametro  |Descrizione  |
|---------|---------|
|virtualNetworks_vnet_name    | Nome rete virtuale di Azure        |
|azureFirewalls_firewall_name     | Nome del firewall di Azure        |
|routeTables_routetable_name     | Nome della tabella di route di Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Nome dell'indirizzo IP pubblico per il gateway di rete virtuale       |
|virtualNetworkGateways_VNGW_name    | Nome del gateway VPN di Azure (gateway di rete virtuale)        |
|publicIPAddresses_firewall_public_ip_name     | Nome dell'indirizzo IP pubblico per il firewall di Azure         |
|posizione     |Si tratta dell'area in cui si vuole creare la rete virtuale. Selezionare la stessa area di quella associata al dispositivo.         |
|RouteTables_routetable_onprem_name| Si tratta del nome della tabella di route aggiuntiva per consentire al firewall di riportare i pacchetti al dispositivo Azure Stack Edge. Lo script crea due route aggiuntive e associa *default* e *FirewallSubnet* a questa tabella di route.|

Fornire gli indirizzi IP e gli spazi di indirizzi seguenti per le risorse di Azure create, incluse la rete virtuale e le subnet associate (*impostazione predefinita*, *Firewall*, *GatewaySubnet*).

|Nome parametro  |Descrizione  |
|---------|---------|
|VnetIPv4AddressSpace    | Si tratta dello spazio degli indirizzi associato alla rete virtuale. Specificare l'intervallo IP di VNET come intervallo IP privato ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Si tratta dello spazio degli indirizzi associato alla `Default` subnet per la rete virtuale.         |
|FirewallSubnetIPv4AddressSpace    |Si tratta dello spazio degli indirizzi associato alla `Firewall` subnet per la rete virtuale.          |
|GatewaySubnetIPv4AddressSpace    |Si tratta dello spazio degli indirizzi associato al `GatewaySubnet` per la rete virtuale.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Si tratta dell'indirizzo IP riservato per la comunicazione BGP ed è basato sullo spazio degli indirizzi associato al per la `GatewaySubnet` rete virtuale.          |
|ClientAddressPool    | Questo indirizzo IP viene usato per il pool di indirizzi nella configurazione di P2S in portale di Azure.         |
|PublicCertData     | I dati del certificato pubblico vengono usati dal gateway VPN per autenticare i client P2S che si connettono alla connessione. Per ottenere i dati del certificato, installare il certificato radice. Verificare che il certificato sia codificato in base 64 con estensione cer. Aprire il certificato e copiare il testo nel certificato compreso tra = = BEGIN CERTIFICAte = = e = = END CERTIFICAte = = in una riga continua.     |



### <a name="run-the-script"></a>Eseguire lo script

Seguire questa procedura per usare il modificato `parameters-p2s.json` ed eseguire lo script per creare le risorse di Azure.

1. Eseguire PowerShell. Passare alla directory in cui si trova lo script.

3. Eseguire lo script.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > In questa versione, lo script funziona solo nell'area Stati Uniti orientali.

    Quando si esegue lo script, sarà necessario immettere le informazioni seguenti:

    
    |Parametro  |Descrizione  |
    |---------|---------|
    |Location     |Si tratta dell'area in cui è necessario creare le risorse di Azure.         |
    |AzureAppRuleFilePath     | Si tratta del percorso del file per `appRule.json` .       |
    |AzureIPRangesFilePath     |Si tratta del file JSON del tag di servizio scaricato nel passaggio precedente.         |
    |ResourceGroupName     | Si tratta del nome del gruppo di risorse in cui vengono create tutte le risorse di Azure.        |
    |AzureDeploymentName    |Questo è il nome per la distribuzione di Azure.         |
    |NetworkRuleCollectionName            | Questo è il nome per la raccolta di tutte le regole di rete create e aggiunte al firewall di Azure.             |
    |Priorità            | Si tratta della priorità assegnata a tutte le regole di rete e di applicazione create.              |
    |AppRuleCollectionName            |Questo è il nome per la raccolta di tutte le regole dell'applicazione create e aggiunte al firewall di Azure.                |


    Di seguito è riportato un output di esempio.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - L'esecuzione dello script richiede circa 90 minuti. Assicurarsi di accedere alla rete immediatamente prima dell'avvio dello script.
    > - Se per qualsiasi motivo è presente una sessione non riuscita con lo script, assicurarsi di eliminare il gruppo di risorse per eliminare tutte le risorse create al suo interno.
  
    
    Al termine dello script, viene generato un log di distribuzione nella stessa cartella in cui risiede lo script.


## <a name="verify-the-azure-resources"></a>Verificare le risorse di Azure

Dopo aver eseguito correttamente lo script, verificare che tutte le risorse siano state create in Azure. Passare al gruppo di risorse creato. Verranno visualizzate le risorse seguenti:

![Risorse di Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Scarica Rubrica telefonica per profilo VPN

In questo passaggio verrà scaricato il profilo VPN per il dispositivo.

1. Nel portale di Azure andare al gruppo di risorse e quindi selezionare il gateway di rete virtuale creato nel passaggio precedente.

    ![Gateway di rete virtuale di Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Passare a **impostazioni > configurazione da punto a sito**. Selezionare **Scarica client VPN**.

    ![Abilita configurazione P2S 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Salvare il profilo compresso ed estrarlo nel client Windows.

    ![Abilitare la configurazione di P2S 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Passare alla cartella *WindowsAmd64* , quindi estrarre il `.exe` : *VpnClientSetupAmd64.exe*.

    ![Abilitare la configurazione di P2S 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Creare un percorso temporaneo. Ad esempio:

    `C:\NewTemp\vnet\tmp`

4. Eseguire PowerShell e passare alla directory in cui `.exe` si trova. Per eseguire `.exe` , digitare:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Il percorso temporaneo avrà nuovi file. Di seguito è riportato un output di esempio:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. Il file con *estensione pbk* è la rubrica del profilo VPN. Questa operazione verrà utilizzata nell'interfaccia utente locale.


## <a name="vpn-configuration-on-the-device"></a>Configurazione VPN nel dispositivo

Seguire questa procedura nell'interfaccia utente locale del dispositivo Azure Stack Edge.

1. Nell'interfaccia utente locale passare alla pagina **VPN** . In stato VPN selezionare **Configura**.

    ![Configurare VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. Nel pannello **Configura VPN** :
    
    1. Nel caricare il file della rubrica telefonica, puntare al file con estensione pbk creato nel passaggio precedente.
    2. Nel file di configurazione upload Public IP list immettere il file JSON dell'intervallo IP del Data Center di Azure come input. Il file è stato scaricato in un passaggio precedente da: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Selezionare **eastus** come area e selezionare **applica**.

    ![Configurare VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. Nella sezione **intervalli di indirizzi IP a cui è possibile accedere usando solo VPN** , immettere l'intervallo IPv4 VNET scelto per la rete virtuale di Azure.

    ![Configurare VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Verifica connessione client

1. Nel portale di Azure passare al gateway VPN.
2. Passare a **impostazioni > configurazione da punto a sito**. In **indirizzi IP allocati** verrà visualizzato l'indirizzo IP del dispositivo Azure stack Edge.

## <a name="validate-data-transfer-through-vpn"></a>Convalidare il trasferimento dei dati tramite VPN

Per verificare che la VPN funzioni, copiare i dati in una condivisione SMB. Seguire i passaggi in [aggiungere una condivisione](azure-stack-edge-j-series-manage-shares.md#add-a-share) nel dispositivo Azure stack Edge. 

1. Copiare un file, ad esempio \data\pictures\waterfall.jpg alla condivisione SMB montata nel sistema client. 
2. Per convalidare la connessione dei dati tramite VPN, durante la copia dei dati:

    1. Passare al gateway VPN nella portale di Azure. 

    2. Passare a **monitoraggio > metrica**.

    3. Nel riquadro di destra scegliere l' **ambito** come gateway VPN, **metrica** come gateway P2S bandwidth e **aggregazione** come media.

    4. Quando i dati vengono copiati, si noterà un aumento dell'utilizzo della larghezza di banda e, al termine della copia dei dati, l'utilizzo della larghezza di banda viene eliminato.

        ![Metriche VPN di Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Verificare che il file venga visualizzato nell'account di archiviazione nel cloud.
 
## <a name="debug-issues"></a>Eseguire il debug dei problemi

Per eseguire il debug di eventuali problemi, usare i comandi seguenti:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Di seguito è riportato l'output di esempio:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Passaggi successivi

[Configurare VPN tramite l'interfaccia utente locale nel dispositivo Azure stack Edge](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).
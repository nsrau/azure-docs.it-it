---
title: Configurare la VPN sul dispositivo R Azure Stack Edge Pro usando Azure PowerShell
description: Viene descritto come configurare una VPN nel dispositivo R Azure Stack Edge Pro usando uno script Azure PowerShell per creare le risorse di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 2139080367cdce9a5f018afab0970a7bd0e7504c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467309"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Configurare la VPN sul dispositivo R Azure Stack Edge Pro tramite Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

L'opzione VPN fornisce un secondo livello di crittografia per i dati in movimento su *TLS* dal dispositivo R di Azure stack Edge Pro ad Azure. È possibile configurare la VPN sul dispositivo R Azure Stack Edge Pro tramite il portale di Azure o tramite Azure PowerShell.

Questo articolo descrive i passaggi necessari per configurare la VPN nel dispositivo R Azure Stack Edge Pro usando un Azure PowerShell per creare la configurazione nel cloud.

## <a name="about-vpn-setup"></a>Informazioni sulla configurazione VPN

Una connessione VPN locale è costituita da un gateway VPN di Azure, un dispositivo VPN locale e un tunnel VPN S2S IPsec che li connette. Il flusso di lavoro tipico è costituito dai passaggi seguenti:

- Configurare i prerequisiti.
- Configurare le risorse necessarie in Azure.
    - Creare e configurare un gateway VPN di Azure (gateway di rete virtuale).
    - Creare e configurare un gateway di rete locale di Azure che rappresenti la rete locale e il dispositivo VPN.
    - Creare e configurare una connessione VPN di Azure tra il gateway VPN di Azure e il gateway di rete locale.
    - Configurare il firewall di Azure e aggiungere le regole di rete e app.
    - Creare una tabella di routing di Azure e aggiungere le route.
- Configurare la VPN nell'interfaccia utente Web locale del dispositivo. Configurare il dispositivo VPN locale rappresentato dal gateway di rete locale per stabilire il tunnel VPN S2S effettivo con il gateway VPN di Azure.

I passaggi dettagliati sono disponibili nelle sezioni seguenti.

## <a name="configure-prerequisites"></a>Configurazione dei prerequisiti

1. È necessario avere accesso a un dispositivo R Azure Stack Edge Pro installato in base alle istruzioni riportate in [installare il dispositivo r Azure stack Edge Pro](azure-stack-edge-pro-r-deploy-install.md). Questo dispositivo fungerà da dispositivo VPN locale per creare la connessione VPN con Azure. 

2. Il dispositivo VPN deve avere un indirizzo IP pubblico statico (esterno). Questo indirizzo non deve essere NAT.

3. È necessario avere accesso a una sottoscrizione di Azure valida abilitata per il servizio Azure Stack Edge in Azure. Usare questa sottoscrizione per creare una risorsa corrispondente in Azure per gestire il dispositivo R di Azure Stack Edge Pro.  

4. Si ha accesso a un client Windows che verrà usato per accedere al dispositivo R di Azure Stack Edge Pro. Questo client verrà usato per creare la configurazione nel cloud a livello di codice.

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
        Fornire il nome della sottoscrizione di Azure in uso con il dispositivo R Azure Stack Edge Pro per configurare la VPN.

    3. [Scaricare lo script](https://aka.ms/ase-vpn-deployment) necessario per creare la configurazione nel cloud. Lo script:
        
        - Creare una rete virtuale di Azure e le subnet seguenti: *GatewaySubnet* e *AzureFirewallSubnet*.
        - Creare e configurare un gateway VPN di Azure.
        - Creare e configurare un gateway di rete locale di Azure.
        - Creare e configurare una connessione VPN di Azure tra il gateway VPN di Azure e il gateway di rete locale.
        - Creare un firewall di Azure e aggiungere le regole di rete e le regole dell'app.
        - Creare una tabella di routing di Azure e aggiungervi le route.


## <a name="use-the-script"></a>USA lo script

Prima di tutto, modificare il `parameters.json` file per immettere i parametri. Eseguire quindi lo script usando il file JSON modificato.

Ognuno di questi passaggi è descritto nelle sezioni seguenti.

### <a name="download-service-tags-file"></a>Scarica file tag servizio

È possibile che si disponga già `ServiceTags.json` di un file nella cartella in cui è stato scaricato lo script. In caso contrario, è possibile scaricare il file dei tag del servizio.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Modificare il file dei parametri

Il primo passaggio consiste nel modificare il `parameters.json` file e salvare le modifiche. 


Per le risorse di Azure create, verranno forniti i nomi seguenti:

|Nome parametro  |Descrizione  |
|---------|---------|
|virtualNetworks_vnet_name    | Nome rete virtuale di Azure        |
|azureFirewalls_firewall_name     | Nome del firewall di Azure        |
|routeTables_routetable_name     | Nome della tabella di route di Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Nome dell'indirizzo IP pubblico per il gateway di rete virtuale       |
|virtualNetworkGateways_VNGW_name    | Nome del gateway VPN di Azure (gateway di rete virtuale)        |
|publicIPAddresses_firewall_public_ip_name     | Nome dell'indirizzo IP pubblico per il firewall di Azure         |
|localNetworkGateways_LNGW_name    |Nome del gateway di rete locale di Azure          |
|connections_vngw_lngw_name    | Nome della connessione VPN di Azure. Si tratta della connessione tra il gateway di rete virtuale e il gateway di rete locale.       |
|posizione     |Si tratta dell'area in cui si vuole creare la rete virtuale. Selezionare la stessa area di quella associata al dispositivo.         |

Gli indirizzi IP e gli spazi di indirizzi seguenti riguardano le risorse di Azure create, incluse la rete virtuale e le subnet associate (impostazione predefinita, firewall, GatewaySubnet).

|Nome parametro  |Descrizione  |
|---------|---------|
|VnetIPv4AddressSpace    | Si tratta dello spazio degli indirizzi associato alla rete virtuale.       |
|DefaultSubnetIPv4AddressSpace    |Si tratta dello spazio degli indirizzi associato alla `Default` subnet per la rete virtuale.         |
|FirewallSubnetIPv4AddressSpace    |Si tratta dello spazio degli indirizzi associato alla `Firewall` subnet per la rete virtuale.          |
|GatewaySubnetIPv4AddressSpace    |Si tratta dello spazio degli indirizzi associato al `GatewaySubnet` per la rete virtuale.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Si tratta dell'indirizzo IP riservato per la comunicazione BGP ed è basato sullo spazio degli indirizzi associato al per la `GatewaySubnet` rete virtuale.          |

Gli indirizzi IP e gli spazi di indirizzi seguenti riguardano la rete locale, in cui viene distribuito il dispositivo R Azure Stack Edge Pro.

|Nome parametro  |Descrizione  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Si tratta dello spazio degli indirizzi per l'indirizzo IP privato.       |
|CustomerPublicNetworkAddressSpace    |  Si tratta dello spazio degli indirizzi per l'indirizzo IP pubblico.       |
|DbeIOTNetworkAddressSpace    |Questo indirizzo IP è riservato dal servizio Internet delle cose. Non modificare questo parametro.        |
|AzureVPNsharedKey    |Questa chiave condivisa viene usata durante la creazione della risorsa di connessione VPN di Azure. Questa chiave viene fornita anche come segreto condiviso VPN durante la configurazione VPN dell'interfaccia utente Web locale.         |
|DBE-gateway-IPAddress   | Indirizzo IP pubblico per il dispositivo R Azure Stack Edge Pro. Questa operazione potrebbe non essere nota ed è possibile eseguire lo script con un indirizzo IP segnaposto. Modificare il gateway di rete locale in un secondo momento con l'indirizzo IP effettivo.     |

#### <a name="caveats-to-keep-in-mind"></a>Considerazioni da tenere presente:

- Non si avrà l'indirizzo IP del dispositivo R Azure Stack Edge Pro. È possibile usare un indirizzo IP segnaposto per creare la risorsa e successivamente modificare il gateway di rete locale di Azure per assegnare l'indirizzo IP effettivo del dispositivo e lo spazio degli indirizzi della rete locale per il dispositivo.
- In base alla direzione di IETF in Internet Assigned Numbers Authority (IANA), usare qualsiasi subnet da 172.16. x. y a 172.24. z.a. Si riservano gli intervalli di indirizzi IPv4 172,24 per la rete di Azure.

### <a name="run-the-script"></a>Eseguire lo script

Seguire questa procedura per usare il modificato `parameters.json` ed eseguire lo script per creare le risorse di Azure.

1. Eseguire PowerShell come amministratore.

2. Passare alla directory in cui si trova lo script.

3. Eseguire lo script.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Di seguito è riportato un output di esempio.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    L'esecuzione dello script richiede circa 90 minuti. Al termine dello script, viene generato un log di distribuzione nella stessa cartella in cui risiede lo script.


## <a name="verify-the-azure-resources"></a>Verificare le risorse di Azure

Dopo aver eseguito correttamente lo script, verificare che tutte le risorse siano state create in Azure.

Si configurerà quindi la VPN nell'interfaccia utente Web locale del dispositivo.


## <a name="vpn-configuration-on-the-device"></a>Configurazione VPN nel dispositivo

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>Verifica VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Convalidare il trasferimento dei dati tramite VPN

Per verificare che la VPN funzioni, copiare i dati in una condivisione SMB. Seguire i passaggi in [aggiungere una condivisione](azure-stack-edge-j-series-manage-shares.md#add-a-share) sul dispositivo R Azure stack Edge Pro. 

1. Copiare un file, ad esempio \data\pictures\waterfall.jpg alla condivisione SMB montata nel sistema client. 
2. Verificare che il file venga visualizzato nell'account di archiviazione nel cloud.

Per convalidare la connessione dei dati tramite VPN:

1. Aprire la risorsa di connessione presente nel gruppo di risorse. 

2. Controllare i dati in e il valore dei dati.
 
3. Aprire il gateway di rete virtuale nel gruppo di risorse. Visualizzare i grafici per l' **ingresso del tunnel totale** e l' **uscita del tunnel totale**.
 
## <a name="debug-issues"></a>Eseguire il debug dei problemi

Per eseguire il debug di eventuali problemi, usare i comandi seguenti:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Di seguito è riportato l'output di esempio:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
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

[Configurare VPN tramite l'interfaccia utente locale sul dispositivo R Azure Stack Edge Pro](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)

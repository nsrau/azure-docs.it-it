---
title: Configurare una VPN da punto a sito in Linux per l'uso con File di Azure | Microsoft Docs
description: Come configurare una VPN da punto a sito in Linux per l'uso con File di Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 685373203da14a6aa83c608d90d6416ab2b30ae4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515307"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Configurare una VPN da punto a sito in Linux per l'uso con File di Azure
È possibile usare una connessione VPN da punto a sito per montare le condivisioni file di Azure su SMB dall'esterno di Azure, senza aprire la porta 445. Una connessione VPN da punto a sito è una connessione VPN tra Azure e un singolo client. Per usare una connessione VPN da punto a sito con File di Azure, è necessario configurarla per ogni client da connettere. Se è necessario connettere molti client alle condivisioni file di Azure dalla rete locale, è possibile usare una connessione VPN da sito a sito invece di una da punto a sito per ogni client. Per altre informazioni, vedere [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md).

Per una descrizione completa delle opzioni di rete disponibili per File di Azure, è consigliabile leggere l'articolo [Panoramica della rete per File di Azure](storage-files-networking-overview.md) prima di procedere con questo articolo.

Questo articolo illustra i passaggi per configurare una VPN da punto a sito in Linux per montare le condivisioni file di Azure direttamente in locale. Se si vuole instradare il traffico di Sincronizzazione file di Azure tramite una VPN, vedere come [configurare le impostazioni di proxy e firewall di Sincronizzazione file di Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Prerequisiti
- La versione più recente dell'interfaccia della riga di comando di Azure. Per altre informazioni su come installare l'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure PowerShell](https://docs.microsoft.com/cli/azure/install-azure-cli) e selezionare il sistema operativo in uso. Se si preferisce, è anche possibile usare il modulo Azure PowerShell in Linux, ma le istruzioni seguenti vengono presentate per l'interfaccia della riga di comando di Azure.

- Una condivisione file di Azure che si vuole montare in locale. Le condivisioni file di Azure vengono distribuite all'interno degli account di archiviazione, ovvero costrutti di gestione che rappresentano un pool di archiviazione condiviso in cui è possibile distribuire più condivisione file, oltra ad altre risorse come contenitori BLOB o code. Per altre informazioni su come distribuire condivisioni file di Azure e account di archiviazione, vedere [Creare una condivisione file di Azure](storage-how-to-create-file-share.md).

- Un endpoint privato per l'account di archiviazione contenente l'archiviazione file di Azure da montare in locale. Per altre informazioni su come creare un endpoint privato, vedere [Configurazione degli endpoint di rete di File di Azure](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Installare il software necessario
Il gateway di rete virtuale di Azure può fornire connessioni VPN con diversi protocolli VPN, tra cui IPsec e OpenVPN. Questa guida illustra come usare IPsec e usa il pacchetto strongSwan per fornire il supporto in Linux. 

> La procedura è verificata con Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Distribuire una rete virtuale 
Per accedere alla condivisione file di Azure e ad altre risorse di Azure dall'ambiente locale tramite una VPN da punto a sito, è necessario creare una rete virtuale. La connessione VPN da punto a sito che verrà creata automaticamente è un bridge tra il computer Linux locale e questa rete virtuale di Azure.

Lo script seguente creerà una rete virtuale di Azure con tre subnet: una per l'endpoint di servizio dell'account di archiviazione, una per l'endpoint privato dell'account di archiviazione (necessario per accedere all'account di archiviazione in locale senza creare un routing personalizzato per l'IP pubblico dell'account di archiviazione, che potrebbe cambiare), e una per il gateway di rete virtuale che fornisce il servizio VPN. 

Ricordarsi di sostituire `<region>`, `<resource-group>` e `<desired-vnet-name>` con i valori appropriati per l'ambiente.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Creare i certificati per l'autenticazione della VPN
Per consentire l'autenticazione delle connessioni VPN da computer Linux locali per l'accesso alla rete virtuale, è necessario creare due certificati: un certificato radice, che verrà fornito al gateway della macchina virtuale, e un certificato client, che verrà firmato con il certificato radice. Lo script seguente crea i certificati richiesti.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Distribuire il gateway di rete virtuale
Il gateway di rete virtuale di Azure è il servizio a cui si connetteranno i computer Linux locali. La distribuzione di questo servizio richiede due componenti di base: un indirizzo IP pubblico che identificherà il gateway per i client ovunque si trovino nel mondo e un certificato radice creato in precedenza, che verrà usato per autenticare i client.

Ricordarsi di sostituire `<desired-vpn-name-here>` con il nome che si vuole usare per queste risorse.

> [!Note]  
> La distribuzione del gateway di rete virtuale di Azure può richiedere fino a 45 minuti. Durante la distribuzione di questa risorsa, questo script bash verrà bloccato per consentire il completamento della distribuzione.
>
> Le connessioni P2S IKEv2/OpenVPN non sono supportate con lo SKU **Basic** . Questo script usa lo SKU **VpnGw1** per il gateway di rete virtuale, di conseguenza.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Configurare il client VPN
Il gateway di rete virtuale di Azure creerà un pacchetto scaricabile con i file di configurazione necessari per inizializzare la connessione VPN nel computer Linux locale. Lo script seguente inserirà i certificati creati nel punto corretto e configurerà il file `ipsec.conf` con i valori corretti del file di configurazione nel pacchetto scaricabile.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Montare la condivisione file di Azure
Dopo aver configurato la VPN da punto a sito, è possibile montare la condivisione file di Azure. L'esempio seguente monterà la condivisione in modo non persistente. Per montarla in modo persistente, vedere [Usare una condivisione file di Azure con Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Vedere anche
- [Panoramica della rete per File di Azure](storage-files-networking-overview.md)
- [Configurare una VPN da punto a sito in Windows per l'uso con File di Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configurare una VPN da sito a sito per l'uso con File di Azure](storage-files-configure-s2s-vpn.md)
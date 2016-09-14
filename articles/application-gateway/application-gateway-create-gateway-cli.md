<properties
   pageTitle="Creare un gateway applicazione con l'interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Informazioni su come creare un gateway applicazione usando l'interfaccia della riga di comando di Azure in Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="gwallace" />

# Creare un gateway applicazione con l'interfaccia della riga di comando di Azure

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre le seguenti funzionalità di distribuzione delle applicazioni: bilanciamento del carico HTTP, affinità di sessione basata sui cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati e supporto per più siti.

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-gateway-portal.md)
- [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [PowerShell per Azure classico](application-gateway-create-gateway.md)
- [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
- [Interfaccia della riga di comando di Azure](application-gateway-create-gateway-cli.md)

<BR>

## Prerequisito: installare l'interfaccia della riga di comando di Azure

Per eseguire i passaggi in questo articolo, è necessario [installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows (interfaccia della riga di comando di Azure CLI)](../xplat-cli-install.md) ed è necessario [accedere a Azure](../xplat-cli-connect.md).

> [AZURE.NOTE] Se non si dispone di un account Azure, è necessario procurarsene uno. Usare la [versione di valutazione gratuita](../active-directory/sign-up-organization.md).

## Scenario

Questo scenario mostra come creare un gateway applicazione usando il portale di Azure.

Questo scenario illustrerà come:

- Creare un gateway applicazione Medium con due istanze.
- Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato 10.0.0.0/16.
- Creare una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.
- Configurare un certificato per l'offload SSL.

![Esempio dello scenario][scenario]

>[AZURE.NOTE] La configurazione aggiuntiva del gateway applicazione, che include i probe di integrità personalizzati, gli indirizzi del pool back-end e le regole aggiuntive, viene definita dopo la configurazione del gateway applicazione e non durante la distribuzione iniziale.

## Prima di iniziare

Il gateway applicazione di Azure richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo che un gateway applicazione è stato distribuito in una subnet, alla subnet possono essere aggiunti solo altri gateway applicazione.

## Creare il gruppo di risorse.

Prima di creare il gateway applicazione viene creato un gruppo di risorse che contenga il gateway applicazione. Di seguito sono riportati il comando e l'output previsto.

    azure group create -n AdatumAppGatewayRG -l eastus

    info:    Executing command group create
    + Getting resource group AdatumAppGatewayRG
    + Creating resource group AdatumAppGatewayRG
    info:    Created resource group AdatumAppGatewayRG
    data:    Id:                  /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG
    data:    Name:                AdatumAppGatewayRG
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

## Crea rete virtuale

Dopo aver creato il gruppo di risorse, viene creata una rete virtuale per il gateway applicazione. Nell'esempio seguente, lo spazio degli indirizzi era 10.0.0.0/16 come definito nelle note sullo scenario precedente.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

    info:    Executing command network vnet create
    + Looking up the virtual network "AdatumAppGatewayVNET"
    + Creating virtual network "AdatumAppGatewayVNET"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET
    data:    Name                            : AdatumAppGatewayVNET
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Address prefixes:
    data:      10.0.0.0/16
    info:    network vnet create command OK

## Creare una subnet

Dopo aver creato la rete virtuale, viene aggiunta una subnet per il gateway applicazione. Se si intende usare il gateway applicazione con un'app Web ospitata nella stessa rete virtuale del gateway applicazione, assicurarsi di lasciare spazio sufficiente per un'altra subnet.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

    info:    Executing command network vnet subnet create
    verbose: Looking up the virtual network "AdatumAppGatewayVNET"
    verbose: Looking up the subnet "Appgatewaysubnet"
    verbose: Creating subnet "Appgatewaysubnet"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet
    data:    Name                            : Appgatewaysubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.0.0.0/28
    info:    network vnet subnet create command OK

## Creare il gateway applicazione

Dopo aver creato la rete virtuale e la subnet, i prerequisiti per il gateway applicazione sono soddisfatti. Per il passaggio successivo sono anche necessari un certificato PFX esportato in precedenza e la password del certificato. Gli indirizzi IP usati per il back-end sono gli indirizzi IP per il server back-end. Può trattarsi di indirizzi IP privati nella rete virtuale, di indirizzi IP pubblici o di nomi di dominio completi per i server back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd

    info:    Executing command network application-gateway create
    + Looking up an application gateway "AdatumAppGateway"
    + Looking up the subnet "Appgatewaysubnet"
    warn:    Using default http listener protocol: https
    warn:    Using default gateway ip name: ipConfig01
    warn:    Using default sku name: Standard_Medium
    warn:    Using default sku tier: Standard
    warn:    Using default sku capacity: 2
    warn:    Using default frontend ip name: frontendIp01
    warn:    Using default frontend port name: frontendPort01
    warn:    Using default frontend port: 443
    warn:    Using default address pool name: pool01
    warn:    Using default http settings name: httpSettings01
    warn:    Using default http settings protocol: http
    warn:    Using default http settings port: 80
    warn:    Using default http settings cookie based affinity: Disabled
    warn:    Using default http listener name: listener01
    warn:    Using default request routing rule name: rule01
    warn:    Using default request routing rule type: Basic
    + Looking up the subnet "Appgatewaysubnet"
    + Creating configuration for an application gateway "AdatumAppGateway"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway
    data:    Name                            : AdatumAppGateway
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Sku                             : Standard_Medium
    data:    Resource Group                  : AdatumAppGatewayRG
    data:    Gateway IP configations         : [ipConfig01]
    data:    SSL cerificates                 : [cert01]
    data:    Frontend ip configurations      : [frontendIp01]
    data:    Frontend ports                  : [frontendPort01]
    data:    Backend address pools           : [pool01]
    data:    Backend http settings           : [httpSettings01]
    data:    Http listeners                  : [listener01]
    data:    Request routing rules           : [rule01]
    data:    Probes                          : []
    data:    Url Path Maps                   : []
    data:    GatewayIpConfigurationText      : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/gatewayIPConfigurations/ipConfig01",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "ipConfig01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SslCertificateText              : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01",
            "publicCertData": "MIIDHjCCAgagAwIBAgIQE8ZZuidyb59IscYNJPYTvDANBgkqhkiG9w0BAQsFADA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wHhcNMTYwNzI4MTUyNzIwWhcNMTcwNzI4MDAwMDAwWjA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDC+pkArA1y9C10AOPWISHEThbNqvuRA+MaVDFUOu15NB9M7+0PUx5pyGlbW+1MuXb9acia/KXF9WxpYUMbQt8t8p1S5HsV4oKGdEOpdR0d7dozyPrkNgBgYvqRzDQ6R5VuK/uLq9oWpjPkqNoQeYR1wr7f/SNsIA4YsaDqqAi62ET6cvg1wN/VRXbWyi9wLeon7g6fZiCrFZspTUiSyqrRQx7sO0e/bqV7nKgSWmaqo4jLoUAqJBBCUJryDaTNfkFO4VEdnsQLN+PSGO8HwSZPJOzG1V6+MynmaGCKaTJE2UCxtLIJQHBhmES+X/BoinrIsjNVxsKqWFMv/mV7M2GBAgMBAAGjJDAiMAsGA1UdDwQEAwIEMDATBgNVHSUEDDAKBggrBgEFBQcDATANBgkqhkiG9w0BAQsFAAOCAQEAa2XLrwQJwX2ZmVN0MR+/+jWTED134wgoIKw6Ni30ukF9U936FsuvFcEjPr4vBp82cjnz76BjLNhyw/MxAHP7tTaguxzHgUHP9X9fmtcsLEUD74/D5BPmnpl+4cJ/BZMdyzIsuyyPSsDxkVN/W70ykOVTJeAb1ycwfJCllgLgkZcLVgTcMMAJYSttfWn9e1dhTUIlTIYKzD669emFvdBHi+sdTT1HGrZenpkT5oK+H6/5wIV7/DW+C+pqvXCsK0XSeYWW7KuBk5MpD8829HeCvV0rBSf538nYLwUUVUUMNHuTp5QXzouAtHOWyvo00/xRi+aDeq0NfUvTv2iS2BS/Ow==",
            "provisioningState": "Succeeded",
            "name": "cert01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendIpConfigurationText     : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01",
            "privateIPAddress": "10.0.0.6",
            "privateIPAllocationMethod": "Dynamic",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "frontendIp01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendPortText                : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01",
            "port": 443,
            "provisioningState": "Succeeded",
            "name": "frontendPort01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendAddressPoolText          : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01",
            "backendAddresses": [
                {
                    "ipAddress": "134.170.185.46"
                },
                {
                    "ipAddress": "134.170.188.221"
                },
                {
                    "ipAddress": "134.170.185.50"
                }
            ],
            "provisioningState": "Succeeded",
            "name": "pool01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendHttpSettingsText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01",
            "port": 80,
            "protocol": "Http",
            "cookieBasedAffinity": "Disabled",
            "requestTimeout": 30,
            "provisioningState": "Succeeded",
            "name": "httpSettings01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    HttpListenersText               : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01",
            "frontendIPConfiguration": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01"
            },
            "frontendPort": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01"
            },
            "protocol": "Https",
            "sslCertificate": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01"
            },
            "requireServerNameIndication": false,
            "provisioningState": "Succeeded",
            "name": "listener01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    RequestRoutingRulesText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/requestRoutingRules/rule01",
            "ruleType": "Basic",
            "backendAddressPool": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01"
            },
            "httpListener": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01"
            },
            "provisioningState": "Succeeded",
            "name": "rule01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SkuText                         : {
        "name": "Standard_Medium",
        "tier": "Standard",
        "capacity": 2
    }
    data:    ProbesText                      : []
    data:    UrlPathMapsText                 : []
    info:    network application-gateway create command OK

Viene così creato un gateway applicazione di base con le impostazioni predefinite per il listener, il pool back-end, le impostazioni HTTP back-end e le regole. Viene anche configurato l'offload SSL. Queste impostazioni possono essere modificate in base alla propria distribuzione dopo che è stato completato il provisioning. Se si ha già un'applicazione Web definita con gli indirizzi IP per il pool di back-end definito nei passaggi precedenti, dopo il provisioning e l'avvio del gateway applicazione verrà avviato il bilanciamento del carico.

## Passaggi successivi

Per informazioni su come creare probe di integrità personalizzati, vedere [Creare un probe personalizzato per un gateway applicazione con il portale](application-gateway-create-probe-portal.md)

Per informazioni su come configurare l'offload SSL ed evitare costose attività di decrittografia SSL nei server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png

<!---HONumber=AcomDC_0831_2016-->
---
title: Creare un gateway applicazione di Azure - Interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come creare un gateway applicazione usando l'interfaccia della riga di comando di Azure 2.0 in Resource Manager
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 14dab2197ff7c1eaff012066e321ef1b99f05bb3
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Creare un gateway applicazione con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell per Azure classico](application-gateway-create-gateway.md)
> * [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interfaccia della riga di comando di Azure 1.0](application-gateway-create-gateway-cli.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-gateway-cli.md)

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre le seguenti funzionalità di distribuzione delle applicazioni: bilanciamento del carico HTTP, affinità di sessione basata sui cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati e supporto per più siti.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure 1.0](application-gateway-create-gateway-cli-nodejs.md): l'interfaccia della riga di comando per il modello di distribuzione classico e di gestione delle risorse.
* [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-gateway-cli.md): interfaccia avanzata per il modello di distribuzione di gestione delle risorse

## <a name="prerequisite-install-the-azure-cli-20"></a>Prerequisito: installare l'interfaccia della riga di comando di Azure 2.0

Per eseguire i passaggi indicati in questo articolo è necessario [installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows (interfaccia della riga di comando di Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Se non si dispone di un account Azure, è necessario procurarsene uno. Usare la [versione di valutazione gratuita](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenario

Questo scenario mostra come creare un gateway applicazione usando il portale di Azure.

Questo scenario illustrerà come:

* Creare un gateway applicazione Medium con due istanze.
* Creare una rete virtuale denominata AdatumAppGatewayVNET con un blocco CIDR riservato 10.0.0.0/16.
* Creare una subnet denominata Appgatewaysubnet che usa 10.0.0.0/28 come blocco CIDR.
* Configurare un certificato per l'offload SSL.

![Esempio dello scenario][scenario]

> [!NOTE]
> La configurazione aggiuntiva del gateway applicazione, che include i probe di integrità personalizzati, gli indirizzi del pool back-end e le regole aggiuntive, viene definita dopo la configurazione del gateway applicazione e non durante la distribuzione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

Il gateway applicazione di Azure richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo che un gateway applicazione è stato distribuito in una subnet, alla subnet possono essere aggiunti solo altri gateway applicazione.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Aprire il **prompt dei comandi di Microsoft Azure**ed effettuare l'accesso. 

```azurecli
az login -u "username"
```

> [!NOTE]
> È possibile anche usare `az login` senza l'opzione per l'accesso del dispositivo che richiederà l'inserimento di un codice in aka.ms/devicelogin.

Dopo avere digitato l'esempio precedente, viene fornito un codice. Passare a https://aka.ms/devicelogin in un browser per continuare il processo di accesso.

![Comando che illustra l'accesso al dispositivo][1]

Nel browser immettere il codice ricevuto. Si verrà reindirizzati a una pagina di accesso.

![Browser in cui immettere il codice][2]

Dopo avere immesso il codice ed effettuato l'accesso, chiudere il browser per continuare con lo scenario.

![Accesso eseguito][3]

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare il gateway applicazione viene creato un gruppo di risorse che contenga il gateway applicazione. Di seguito è riportato il comando.

```azurecli
az resource group create --name myresourcegroup --location "West US"
```

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Dopo aver creato il gruppo di risorse, viene creata una rete virtuale per il gateway applicazione.  Nell'esempio seguente lo spazio degli indirizzi definito per la rete virtuale è 10.0.0.0/16 e per la subnet è 10.0.0.0/28, come illustrato nelle note relative allo scenario precedente.

```azurecli
az network vnet create \
--name AdatumAppGatewayVNET \
--address-prefix 10.0.0.0/16 \
--subnet-name Appgatewaysubnet \
--subnet-prefix 10.0.0.0/28 \
--resource-group AdatumAppGateway \
--location eastus
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Dopo aver creato la rete virtuale e la subnet, i prerequisiti per il gateway applicazione sono soddisfatti. Per il passaggio seguente sono necessari anche un certificato PFX esportato prima e la password del certificato. Gli indirizzi IP usati per il back-end sono gli indirizzi IP per il server back-end. Questi valori possono essere indirizzi IP privati nella rete virtuale, indirizzi IP pubblici o nomi di dominio completi per i server back-end.

```azurecli
az network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group AdatumAppGatewayRG \
--vnet-name AdatumAppGatewayVNET \
---vnet-address-prefix 10.0.0.0/16 \
--subnet Appgatewaysubnet \
---subnet-address-prefix 10.0.0.0/28 \
--servers 10.0.0.4 10.0.0.5 \
--cert-file /mnt/c/Users/username/Desktop/application-gateway/fabrikam.pfx \
--cert-password P@ssw0rd \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--public-ip-address AdatumAppGatewayPIP \
--frontend-port 443 \
--routing-rule-type Basic \
--http-settings-port 80

```

> [!NOTE]
> Per un elenco dei parametri che possono essere specificati durante la creazione, eseguire questo comando: **az network application-gateway create --help**.

Questo esempio crea un gateway applicazione di base con le impostazioni predefinite per il listener, il pool back-end, le impostazioni HTTP back-end e le regole. Viene anche configurato l'offload SSL. Queste impostazioni possono essere modificate in base alla propria distribuzione dopo che è stato completato il provisioning.
Se l'applicazione Web è già stata definita con il pool back-end nei passaggi precedenti, dopo la creazione, inizia il bilanciamento del carico.

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create nell'esecuzione dell'esercizio, seguire questa procedura:

```azurecli
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare probe di integrità personalizzati, vedere [Creare un probe personalizzato per un gateway applicazione con il portale](application-gateway-create-probe-portal.md)

Per informazioni su come configurare l'offload SSL ed evitare costose attività di decrittografia SSL nei server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png


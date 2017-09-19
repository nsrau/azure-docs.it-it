---
title: Creare un gateway applicazione di Azure - Interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come creare un gateway applicazione usando l'interfaccia della riga di comando di Azure 1.0 in Resource Manager
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: e7b16e789e0f241aa8ca2292aacb2bccde8777ee
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Creare un gateway applicazione con l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell per Azure classico](application-gateway-create-gateway.md)
> * [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interfaccia della riga di comando di Azure 1.0](application-gateway-create-gateway-cli.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-gateway-cli.md)
> 
> 

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre le seguenti funzionalità di distribuzione delle applicazioni: bilanciamento del carico HTTP, affinità di sessione basata sui cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati e supporto per più siti.

## <a name="prerequisite-install-the-azure-cli"></a>Prerequisito: installare l'interfaccia della riga di comando di Azure

Per eseguire i passaggi in questo articolo, è necessario [installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows (interfaccia della riga di comando di Azure)](../xplat-cli-install.md) ed è necessario [accedere ad Azure](../xplat-cli-connect.md). 

> [!NOTE]
> Se non si dispone di un account Azure, è necessario procurarsene uno. Usare la [versione di valutazione gratuita](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scenario

Questo scenario mostra come creare un gateway applicazione usando il portale di Azure.

Questo scenario illustrerà come:

* Creare un gateway applicazione Medium con due istanze.
* Creare una rete virtuale denominata ContosoVNET con un blocco CIDR riservato di 10.0.0.0/16.
* Creare una subnet denominata subnet01 che usa 10.0.0.0/28 come blocco CIDR.

> [!NOTE]
> La configurazione aggiuntiva del gateway applicazione, che include i probe di integrità personalizzati, gli indirizzi del pool back-end e le regole aggiuntive, viene definita dopo la configurazione del gateway applicazione e non durante la distribuzione iniziale.

## <a name="before-you-begin"></a>Prima di iniziare

Il gateway applicazione di Azure richiede una propria subnet. Quando si crea una rete virtuale, assicurarsi di lasciare uno spazio indirizzi sufficiente per più subnet. Dopo che un gateway applicazione è stato distribuito in una subnet, alla subnet possono essere aggiunti solo altri gateway applicazione.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Aprire il **prompt dei comandi di Microsoft Azure**ed effettuare l'accesso. 

```azurecli-interactive
azure login
```

Dopo avere digitato l'esempio precedente, viene fornito un codice. Passare a https://aka.ms/devicelogin in un browser per continuare il processo di accesso.

![Comando che illustra l'accesso al dispositivo][1]

Nel browser immettere il codice ricevuto. Si verrà reindirizzati a una pagina di accesso.

![Browser in cui immettere il codice][2]

Dopo avere immesso il codice ed effettuato l'accesso, chiudere il browser per continuare con lo scenario.

![Accesso eseguito][3]

## <a name="switch-to-resource-manager-mode"></a>Passare alla modalità Resource Manager

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Prima di creare il gateway applicazione viene creato un gruppo di risorse che contenga il gateway applicazione. Di seguito è riportato il comando.

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Dopo aver creato il gruppo di risorse, viene creata una rete virtuale per il gateway applicazione.  Nell'esempio seguente, lo spazio degli indirizzi era 10.0.0.0/16 come definito nelle note sullo scenario precedente.

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>Creare una subnet

Dopo aver creato la rete virtuale, viene aggiunta una subnet per il gateway applicazione.  Se si intende usare il gateway applicazione con un'app Web ospitata nella stessa rete virtuale del gateway applicazione, assicurarsi di lasciare spazio sufficiente per un'altra subnet.

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Dopo aver creato la rete virtuale e la subnet, i prerequisiti per il gateway applicazione sono soddisfatti. Per il passaggio seguente sono necessari anche un certificato PFX esportato prima e la password del certificato. Gli indirizzi IP usati per il back-end sono gli indirizzi IP per il server back-end. Questi valori possono essere indirizzi IP privati nella rete virtuale, indirizzi IP pubblici o nomi di dominio completi per i server back-end.

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> Per un elenco di parametri che possono essere specificati durante la creazione, eseguire questo comando: **azure network application-gateway create --help**.

Questo esempio crea un gateway applicazione di base con le impostazioni predefinite per il listener, il pool back-end, le impostazioni HTTP back-end e le regole. Queste impostazioni possono essere modificate in base alla propria distribuzione dopo che è stato completato il provisioning.
Se l'applicazione Web è già stata definita con il pool back-end nei passaggi precedenti, dopo la creazione, inizia il bilanciamento del carico.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare probe di integrità personalizzati, vedere [Creare un probe personalizzato per un gateway applicazione con il portale](application-gateway-create-probe-portal.md)

Per informazioni su come configurare l'offload SSL ed evitare costose attività di decrittografia SSL nei server Web, vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png


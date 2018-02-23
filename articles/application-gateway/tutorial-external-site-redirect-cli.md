---
title: Creare un gateway applicazione con reindirizzamento del traffico esterno - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare un gateway applicazione che reindirizza il traffico Web interno al pool appropriato usando l'interfaccia della riga di comando di Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: fed23c7138516b3af8af170383948b14c49a61a9
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Creare un gateway applicazione con reindirizzamento esterno tramite l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per configurare il [reindirizzamento del traffico Web](application-gateway-multi-site-overview.md) quando si crea un [gateway applicazione](application-gateway-introduction.md). In questa esercitazione si configura un listener e una regola che reindirizza il traffico Web che arriva al gateway applicazione a un sito esterno.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Configurare la rete
> * Creare un listener e una regola di reindirizzamento
> * Creare un gateway applicazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse usando [az group create](/cli/azure/group#create).

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupAG* nella posizione *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete 

Creare la rete virtuale denominata *myVNet* e la subnet denominata *myAGSubnet* usando [az network vnet create](/cli/azure/network/vnet#az_net). Creare l'indirizzo IP pubblico denominato *myAGPublicIPAddress* tramite [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create). Queste risorse vengono usate per fornire la connettività di rete al gateway applicazione e alle risorse associate.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

È possibile usare [az network application-gateway create](/cli/azure/application-gateway#create) per creare il gateway applicazione denominato *myAppGateway*. Quando si crea un gateway applicazione usando l'interfaccia della riga di comando di Azure, specificare le informazioni di configurazione, ad esempio le impostazioni relative a capacità, SKU e HTTP. Il gateway applicazione viene assegnato alla subnet *myAGSubnet* e all'indirizzo IP pubblico *myPublicIPSddress* creati in precedenza. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 8080 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Il processo di creazione del gateway applicazione può richiedere alcuni minuti. Dopo aver creato il gateway applicazione, saranno disponibili le nuove funzionalità seguenti:

- *appGatewayBackendPool*: un gateway applicazione deve avere almeno un pool di indirizzi back-end.
- *appGatewayBackendHttpSettings*: specifica che per le comunicazioni vengono usati la porta 80 e il protocollo HTTP.
- *appGatewayHttpListener*: il listener predefinito associato ad *appGatewayBackendPool*.
- *appGatewayFrontendIP*: assegna *myAGPublicIPAddress* ad *appGatewayHttpListener*.
- *rule1*: la regola di routing predefinita associata ad *appGatewayHttpListener*.

### <a name="add-the-redirection-configuration"></a>Aggiungere la configurazione di reindirizzamento

Aggiungere la configurazione di reindirizzamento che invia il traffico dal gateway applicazione a *bing.com* usando [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "http://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Aggiungere un listener e una regola di gestione

È necessario un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato. Creare il listener usando [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create) con la porta front-end creata con [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). È necessaria una regola per comunicare al listener dove inviare il traffico in ingresso. Creare una regola di base denominata *redirectRule* usando [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create) con la configurazione di reindirizzamento.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Per ottenere l'indirizzo IP pubblico del gateway applicazione, è possibile usare [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser.

Nel browser dovrebbe essere visualizzato *bing.com*.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> * Configurare la rete
> * Creare un listener e una regola di reindirizzamento
> * Creare un gateway applicazione

> [!div class="nextstepaction"]
> [Altre informazioni sulle operazioni che è possibile eseguire con il gateway applicazione](./application-gateway-introduction.md)
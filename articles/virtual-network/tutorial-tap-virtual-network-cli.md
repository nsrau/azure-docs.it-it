---
title: Creare, modificare o eliminare un TAP di rete virtuale - Interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare, modificare o eliminare un TAP di rete virtuale con l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kaanan
ms.openlocfilehash: 36de5ec6f7384663106bfb88ee9f236cced6930a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997948"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Usare un TAP di rete virtuale con l'interfaccia della riga di comando di Azure

Un TAP (Terminal Access Point) di rete virtuale di Azure consente di trasmettere il traffico di rete della macchina virtuale come flusso continuo a un agente di raccolta di pacchetti di rete o a uno strumento di analisi. L'agente di raccolta o lo strumento di analisi viene fornito da un partner [appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/). Per un elenco delle soluzioni dei partner convalidate per l'uso con un TAP di rete virtuale, vedere le [soluzioni dei partner](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Creare una risorsa TAP di rete virtuale

Vedere i [prerequisiti](virtual-network-tap-overview.md#prerequisites) prima di creare una risorsa TAP di rete virtuale. È possibile eseguire questi comandi in [Azure Cloud Shell](https://shell.azure.com/bash) oppure con l'interfaccia della riga di comando di Azure nel computer. Azure Cloud Shell è una shell interattiva gratuita che non richiede l'installazione dell'interfaccia della riga di comando di Azure nel computer. È necessario accedere ad Azure con un account che abbia le [autorizzazioni](virtual-network-tap-overview.md#permissions) appropriate. Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0.46 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

1. Recuperare l'ID sottoscrizione in una variabile che verrà usata in un passaggio successivo:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Impostare l'ID sottoscrizione che verrà usato per creare una risorsa TAP di rete virtuale.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Registrare di nuovo l'ID sottoscrizione che verrà usato per creare una risorsa TAP di rete virtuale. Se si verifica un errore di registrazione quando si crea una risorsa TAP, eseguire questo comando:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Se la destinazione del TAP di rete virtuale è l'interfaccia di rete nell'appliance virtuale di rete per l'agente di raccolta o lo strumento di analisi:

   - Recuperare la configurazione IP dell'interfaccia di rete dell'appliance virtuale di rete in una variabile che verrà usata in un passaggio successivo. L'ID è l'endpoint che aggregherà il traffico TAP. L'esempio seguente recupera l'ID della configurazione IP *ipconfig1* per un'interfaccia di rete denominata *myNetworkInterface*, in un gruppo di risorse denominato *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Creare il TAP di rete virtuale nell'area westcentralus di Azure usando l'ID della configurazione IP come destinazione e una proprietà facoltativa per la porta. La proprietà specifica la porta di destinazione nella configurazione IP dell'interfaccia di rete in cui verrà ricevuto il traffico TAP:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Se la destinazione del TAP di rete virtuale è un servizio di bilanciamento del carico interno di Azure:
  
   - Recuperare la configurazione IP front-end del servizio di bilanciamento del carico interno di Azure in una variabile che verrà usata in un passaggio successivo. L'ID è l'endpoint che aggregherà il traffico TAP. L'esempio seguente recupera l'ID della configurazione IP front-end *frontendipconfig1* per un servizio di bilanciamento del carico denominato *myInternalLoadBalancer* in un gruppo di risorse denominato *myResourceGroup*:

      ```azurecli-interactive
      FrondendIpConfigId=$(az network lb fronend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - Creare il TAP di rete virtuale usando l'ID della configurazione IP front-end come destinazione e una proprietà facoltativa per la porta. La proprietà specifica la porta di destinazione nella configurazione IP front-end in cui verrà ricevuto il traffico TAP:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Verificare la creazione del TAP di rete virtuale:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Aggiungere una configurazione TAP a un'interfaccia di rete

1. Recuperare l'ID di una risorsa TAP di rete virtuale esistente. L'esempio seguente recupera un TAP di rete virtuale denominato *myTap* in un gruppo di risorse denominato *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network tap show show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Creare una configurazione TAP nell'interfaccia di rete della macchina virtuale monitorata. L'esempio seguente crea una configurazione TAP per un'interfaccia di rete denominata *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Verificare la creazione della configurazione TAP:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Eliminare la configurazione TAP in un'interfaccia di rete

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --tap-configuration-name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Elencare i TAP di rete virtuale in una sottoscrizione

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Eliminare un TAP di rete virtuale in un gruppo di risorse

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
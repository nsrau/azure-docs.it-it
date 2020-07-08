---
title: Disabilitare e riabilitare il componente aggiuntivo del controller di ingresso del gateway applicazione per il cluster del servizio Kubernetes di Azure
description: Questo articolo fornisce informazioni su come disabilitare e riabilitare il componente aggiuntivo AGIC per il cluster AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807945"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>Disabilitare e riabilitare il componente aggiuntivo AGIC per il cluster AKS
Il controller di ingresso del gateway applicazione (AGIC) distribuito come componente aggiuntivo AKS consente di abilitare e disabilitare il componente aggiuntivo con una riga nell'interfaccia della riga di comando di Azure. Il ciclo di vita del gateway applicazione sarà diverso quando si disabilita il componente aggiuntivo AGIC, a seconda che il gateway applicazione sia stato creato dal componente aggiuntivo AGIC o se è stato distribuito separatamente dal componente aggiuntivo AGIC. È possibile eseguire lo stesso comando per riabilitare il componente aggiuntivo AGIC se lo si disabilita o si Abilita il componente aggiuntivo AGIC usando un cluster AKS esistente e un gateway applicazione.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>Disabilitazione del componente aggiuntivo AGIC con il gateway applicazione associato 
Se il componente aggiuntivo AGIC distribuisce automaticamente il gateway applicazione per la prima volta, la disattivazione del componente aggiuntivo AGIC eliminerà il gateway applicazione per impostazione predefinita in base a un paio di criteri. Sono disponibili due criteri che il componente aggiuntivo AGIC Cerca per determinare se eliminare il gateway applicazione associato quando lo si disabilita:
- Il gateway applicazione a cui è associato il componente aggiuntivo AGIC è stato distribuito nel gruppo di risorse MC_ * node? 
- Il gateway applicazione a cui è associato il componente aggiuntivo AGIC ha il tag "created-by: Ingress-appgw"? Il tag viene usato da AGIC per determinare se il gateway applicazione è stato distribuito dal componente aggiuntivo. 

Se vengono soddisfatti entrambi i criteri, il componente aggiuntivo AGIC eliminerà il gateway applicazione creato quando il componente aggiuntivo è disabilitato; Tuttavia, non eliminerà l'indirizzo IP pubblico o la subnet in cui è stato distribuito il gateway applicazione con/in. Se il primo criterio non viene soddisfatto, non è importante se il gateway applicazione ha il tag "created-by: Ingress-appgw". la disabilitazione del componente aggiuntivo non comporta l'eliminazione del gateway applicazione. Analogamente, se il secondo criterio non viene soddisfatto, ovvero se il gateway applicazione non dispone di tale tag, la disabilitazione del componente aggiuntivo non eliminerà il gateway applicazione nel gruppo di risorse del nodo MC_ *. 

> [!TIP] 
> Se non si vuole che il gateway applicazione venga eliminato quando si disabilita il componente aggiuntivo, ma soddisfa entrambi i criteri, rimuovere il tag "created-by: Ingress-appgw" per impedire che il componente aggiuntivo elimini il gateway applicazione. 

Per disabilitare il componente aggiuntivo AGIC, eseguire il comando seguente: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>Abilitare il componente aggiuntivo AGIC nel gateway applicazione esistente e nel cluster AKS
Se si disattiva il componente aggiuntivo AGIC ed è necessario riabilitare il componente aggiuntivo oppure si vuole abilitare il componente aggiuntivo usando un gateway applicazione e un cluster AKS esistenti, eseguire il comando seguente:

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come abilitare il componente aggiuntivo AGIC usando un gateway applicazione esistente e un cluster AKS, vedere la pagina relativa alla [distribuzione del componente aggiuntivo AGIC Brownfield](tutorial-ingress-controller-add-on-existing.md).
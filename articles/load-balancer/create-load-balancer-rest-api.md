---
title: Creare un'istanza di Azure Load Balancer con API REST
titlesuffix: Azure Load Balancer
description: Informazioni sulle modalità di creazione di Azure Load Balancer con API REST.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: kumud
ms.openlocfilehash: 159fe9d6a891858d8d2cc2315e9544b79eb44cff
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079722"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Creare Azure Load Balancer Basic con API REST

Azure Load Balancer del carico distribuisce i nuovi flussi in ingresso che arrivano sul front-end di bilanciamento del carico nelle istanze del pool back-end in base alle regole e ai probe di integrità. Il Load Balancer è disponibile in due SKU: Di base e standard. Per comprendere la differenza tra le due versioni, SKU, vedere [Confronto tra gli SKU di Load Balancer](load-balancer-overview.md#skus).
 
Questa sezione illustra come creare Azure Load Balancer Basic usando [API REST di Azure](/rest/api/azure/) per consentire la richiesta in ingresso di bilanciamento del carico tra più macchine virtuali all'interno di una rete virtuale di Azure. La documentazione di riferimento completa e altri esempi relativi sono disponibili nelle [informazioni di riferimento REST di Azure Load Balancer](/rest/api/load-balancer/).
 
## <a name="build-the-request"></a>Compilare la richiesta
Usare la seguente richiesta HTTP PUT per creare un nuovo Azure Load Balancer Basic.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>Parametri URI

|NOME  |In ingresso  |Obbligatoria |type |DESCRIZIONE |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  True        |   stringa      |  Le credenziali della sottoscrizione che identificano in modo univoco la sottoscrizione a Microsoft Azure. L'ID sottoscrizione fa parte dell'URI per ogni chiamata di servizio.      |
|resourceGroupName     |     path    | True         |  stringa       |   Nome del gruppo di risorse.     |
|loadBalancerName     |  path       |      True    |    stringa     |    Nome del bilanciamento del carico.    |
|api-version    |   query     |  True        |     stringa    |  Versione API client.      |



### <a name="request-body"></a>Corpo della richiesta

L'unico parametro obbligatorio è `location`. Se non si definisce la versione *SKU*, per impostazione predefinita viene creato Load Balancer Basic.  Usare [parametri facoltativi](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) per personalizzare il servizio di bilanciamento del carico.

| NOME | type | DESCRIZIONE |
| :--- | :--- | :---------- |
| location | stringa | Percorso della risorsa. Ottenere un elenco aggiornato delle località usando l'operazione [List Locations](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations). |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Esempio: creare e aggiornare un Load Balancer di base

In questo esempio, è innanzitutto necessario creare Load Balancer Basic e le relative risorse. Successivamente, configurare le risorse di bilanciamento del carico che includono una configurazione IP front-end, un pool di indirizzi back-end, una regola di bilanciamento del carico, un probe di integrità e una regola NAT in ingresso.

Prima di creare un servizio di bilanciamento del carico usando l'esempio seguente, creare una rete virtuale denominata *vnetlb* con una subnet denominata *subnetlb* in un gruppo di risorse denominato *rg1* in località **Stati Uniti orientali**.

### <a name="step-1-create-a-basic-load-balancer"></a>PASSAGGIO 1. Creare un servizio di bilanciamento del carico di base
In questo passaggio si crea Load Balancer Basic, denominato *lb* in località **Stati Uniti ORIENTALI** all'interno di un gruppo di risorse *rg1*.
#### <a name="sample-request"></a>Richiesta di esempio

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Corpo della richiesta

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>PASSAGGIO 2. Configurare le risorse di bilanciamento del carico
Successivamente, configurare le risorse di bilanciamento del carico *lb* che includono una configurazione IP front-end (*fe-lb*), un pool di indirizzi back-end (*be-lb*), una regola di bilanciamento del carico (*rulelb*), un probe di integrità (*probe-lb*) e una regola NAT in ingresso (*in-nat-rule*).
#### <a name="sample-request"></a>Richiesta di esempio

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Corpo della richiesta

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```

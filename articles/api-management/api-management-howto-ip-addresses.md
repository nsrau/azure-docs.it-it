---
title: Indirizzi IP del servizio Gestione API di Azure Documenti Microsoft
description: Informazioni su come recuperare gli indirizzi IP di un servizio Gestione API di Azure e quando cambiano.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047735"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP addresses of Azure API Management

In questo articolo viene descritto come recuperare gli indirizzi IP del servizio Gestione API di Azure.In this article we describe how to retrieve the IP addresses of Azure API Management service. Gli indirizzi IP possono essere pubblici o privati se il servizio si trova in una rete virtuale.

È possibile utilizzare gli indirizzi IP per creare regole del firewall, filtrare il traffico in ingresso verso i servizi back-end o limitare il traffico in uscita.

## <a name="ip-addresses-of-api-management-service"></a>Indirizzi IP del servizio Gestione API

Ogni istanza del servizio Gestione API nel livello Sviluppatore, Basic, Standard o Premium dispone di indirizzi IP pubblici, che sono esclusivi solo per tale istanza del servizio (non sono condivisi con altre risorse). 

È possibile recuperare gli indirizzi IP dal dashboard di panoramica della risorsa nel portale di Azure.You can retrieve the IP addresses from the overview dashboard of your resource in the Azure portal.

![Indirizzo IP di Gestione API](media/api-management-howto-ip-addresses/public-ip.png)

È anche possibile recuperarli a livello di codice con la chiamata API seguente:You can also fetch them programmatically with the following API call:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Gli indirizzi IP pubblici faranno parte della risposta:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Nelle [distribuzioni multiregionale,](api-management-howto-deploy-multi-region.md)ogni distribuzione regionale dispone di un indirizzo IP pubblico.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP addresses of API Management service in VNet

Se il servizio Gestione API si trova all'interno di una rete virtuale, diverrà costituito da due tipi di indirizzi IP: pubblico e privato.

Gli indirizzi IP pubblici vengono `3443` usati per le comunicazioni interne sulla porta, ad esempio per la gestione della configurazione, ad esempio tramite Azure Resource Manager.Public IP addresses are used for internal communication on port - for managing configuration (for example, through Azure Resource Manager). Nella configurazione della rete virtuale esterna, vengono utilizzati anche per il traffico delle API di runtime. Quando una richiesta viene inviata da Gestione API a un back-end pubblico (con connessione Internet), un indirizzo IP pubblico sarà visibile come origine della richiesta.

Gli indirizzi IP virtuali privati (VIP), disponibili **solo** nella [modalità rete virtuale interna,](api-management-using-with-internal-vnet.md)vengono utilizzati per connettersi dall'interno della rete agli endpoint di Gestione API: gateway, portale per sviluppatori e piano di gestione per l'accesso diretto alle API. È possibile utilizzarli per configurare i record DNS all'interno della rete.

Verranno visualizzati gli indirizzi di entrambi i tipi nel portale di Azure e nella risposta della chiamata API:You will see addresses of both types in the Azure portal and in response of the API call:

![Gestione API nell'indirizzo IP della rete virtuale](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

Gestione API utilizza un indirizzo IP pubblico per le connessioni esterne alla rete virtuale e un indirizzo IP privato per le connessioni all'interno della rete virtuale.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Indirizzi IP del servizio Gestione API del livello di consumo

Se il servizio Gestione API è un servizio di livello Consumo, non dispone di un indirizzo IP dedicato. Il servizio del livello di consumo viene eseguito in un'infrastruttura condivisa e senza un indirizzo IP deterministico. 

Ai fini della restrizione del traffico, è possibile usare l'intervallo di indirizzi IP dei data center di Azure.For traffic restriction purposes, you can use the range of IP addresses of Azure data centers. Fare riferimento [all'articolo della documentazione](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) di Funzioni di Azure per la procedura precisa.

## <a name="changes-to-the-ip-addresses"></a>Modifiche agli indirizzi IP

Nei livelli Sviluppatore, Base, Standard e Premium di Gestione API, gli indirizzi IP pubblici (VIP) sono statici per la durata di un servizio, con le eccezioni seguenti:

* Il servizio viene eliminato e quindi ricreato.
* La sottoscrizione al servizio viene [sospesa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) o [avvisata](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states), ad esempio per mancato pagamento, e quindi ripristinata.
* Rete virtuale di Azure viene aggiunto o rimosso dal servizio.
* Il servizio Gestione API passa dalla modalità di distribuzione della rete virtuale esterna e interna.

Nelle [distribuzioni multiregionale](api-management-howto-deploy-multi-region.md), l'indirizzo IP regionale cambia se un'area viene liberata e quindi ripristinata.

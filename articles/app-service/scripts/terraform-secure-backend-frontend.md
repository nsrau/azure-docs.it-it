---
title: "Terraform: Distribuire un'app Web front-end e un'app Web back-end connesse in modo sicuro con l'integrazione rete virtuale e l'endpoint privato"
description: Informazioni su come usare il provider di Terraform per il servizio app per distribuire due app Web connesse in modo sicuro con l'endpoint privato e l'integrazione rete virtuale
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739834"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Creare due app Web connesse in modo sicuro con l'endpoint privato e l'integrazione rete virtuale

Questo articolo illustra un esempio dell'uso dell'[endpoint privato](../networking/private-endpoint.md) e dell'[integrazione rete virtuale](../web-sites-integrate-with-vnet.md) a livello di area per connettere in modo sicuro due app Web (front-end e back-end) attenendosi alla procedura seguente:
- Distribuire una rete virtuale
- Creare la prima subnet per l'integrazione
- Creare la seconda subnet per l'endpoint privato; è necessario impostare un parametro specifico per disabilitare i criteri di rete
- Distribuire un piano di servizio app di tipo PremiumV2 o Premium V3, necessario per la funzionalità Endpoint privato
- Creare l'app Web front-end con impostazioni specifiche dell'app per utilizzare la zona DNS privata, [altri dettagli](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Connettere l'app Web front-end alla subnet di integrazione
- Creare l'app Web di back-end
- Creare la zona DNS privata con il nome della zona del collegamento privato per l'app Web privatelink.azurewebsites.net
- Collegare questa zona alla rete virtuale
- Creare l'endpoint privato per l'app Web back-end nella subnet dell'endpoint e registrare i nomi DNS (sito Web e Gestione controllo servizi) nella zona DNS privata creata in precedenza

## <a name="how-to-use-terraform-in-azure"></a>Come usare Terraform in Azure

Passare alla [documentazione di Azure](/azure/developer/terraform/) per informazioni su come usare Terraform con Azure.

## <a name="the-complete-terraform-file"></a>Il file Terraform completo

Per usare questo file è necessario modificare la proprietà Name per le risorse frontwebapp e backwebapp (il nome webapp deve essere un nome DNS univoco in tutto il mondo). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Passaggi successivi


> [Vedere altre informazioni sull'uso di Terraform in Azure](/azure/developer/terraform/)
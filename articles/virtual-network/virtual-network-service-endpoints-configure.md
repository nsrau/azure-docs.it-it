---
title: Configurare gli endpoint del servizio Rete virtuale di Azure | Microsoft Docs
description: Informazioni su come abilitare e disabilitare gli endpoint di servizio dalla rete virtuale
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: ee01b83101d4ab4ee8665c32aca22a793b41ac0d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="configure-virtual-network-service-endpoints"></a>Configurare gli endpoint del servizio Rete virtuale

Gli endpoint del servizio Rete virtuale consentono di associare le risorse dei servizi di Azure alla rete virtuale di Azure, rimuovendo completamente l'accesso a Internet a queste risorse. Gli endpoint di servizio forniscono la connessione diretta dalla rete virtuale a un servizio di Azure, consentendo di usare lo spazio di indirizzi privato per accedere ai servizi di Azure. Il traffico destinato ai servizi di Azure tramite gli endpoint di servizio rimane sempre nella rete backbone di Microsoft Azure. Per altre informazioni, vedere [Endpoint del servizio di rete virtuale](virtual-network-service-endpoints-overview.md)

Questo articolo descrive la procedura per abilitare e disabilitare gli endpoint di servizio. Dopo che gli endpoint sono stati abilitati in una subnet per un servizio di Azure, è possibile associare specifiche risorse del servizio a una rete virtuale.

Gli endpoint di servizio possono essere configurati usando il [portale di Azure](#azure-portal), [Azure PowerShell](#azure-powershell), l'[interfaccia della riga di comando di Azure](#azure-cli) o un [modello](#resource-manager-template) di Azure Resource Manager.

>[!NOTE]
Durante l'anteprima, la funzionalità degli endpoint del servizio Rete virtuale è supportata per aree specifiche. Per l'elenco delle aree supportate, vedere la pagina [Aggiornamenti di Rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Panoramica della configurazione degli endpoint di servizio

- Gli endpoint di servizio possono essere configurati solo nelle reti virtuali distribuite tramite il modello di distribuzione Azure Resource Manager.

- Gli endpoint di servizio vengono impostati in ogni subnet di una rete virtuale.

- Per una subnet, è possibile configurare un solo endpoint di servizio per un servizio. È possibile configurare più endpoint di servizio per servizi diversi (ad esempio, Archiviazione di Azure, Azure SQL).

- È possibile abilitare gli endpoint in una subnet nuova o esistente.

- La località viene configurata automaticamente per un endpoint. Per impostazione predefinita, gli endpoint di servizio vengono configurati per l'area della rete virtuale. Per Archiviazione di Azure, per supportare scenari di failover a livello di area, vengono configurati automaticamente endpoint per [aree abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

  >[!NOTE]
  A seconda delle dimensioni della rete virtuale/subnet, il completamento dell'abilitazione dell'endpoint di servizio può richiedere del tempo. Assicurarsi che non siano in corso attività critiche quando si abilitano gli endpoint di servizio. Gli endpoint di servizio cambiano le route in ogni scheda di interfaccia di rete della subnet e possono terminare le connessioni TCP aperte. 

- La chiamata a un endpoint di servizio restituisce "operazione riuscita" dopo che i flussi di traffico verso il servizio in tutte le schede di interfaccia di rete della subnet sono passati agli indirizzi IP privati della rete virtuale.

- Route valide per convalidare la configurazione di un endpoint:

   Per verificare se l'endpoint di servizio è configurato correttamente, "route valide" (route valide) in una scheda di interfaccia di rete della subnet visualizza una nuova route predefinita con nextHopType: VirtualNetworkServiceEndpoint, per servizio e per area. Per altre informazioni, vedere [Uso di regole efficaci per risolvere i problemi](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   Le route valide possono essere visualizzate solo se una o più interfacce di rete (schede di interfaccia di rete) sono configurate e associate con una macchina virtuale in esecuzione nella subnet.

## <a name="azure-portal"></a>Portale di Azure

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Configurazione dell'endpoint di servizio in una subnet durante la creazione della rete virtuale

1. Aprire il [portale di Azure](https://portal.azure.com/).
Accedere ad Azure usando l'account Azure. Se non si ha un account Azure, è possibile iscriversi per ottenere una versione di valutazione gratuita. L'account deve avere le [autorizzazioni](#provisioning) necessarie per la creazione di una rete virtuale e di un endpoint di servizio.
2. Fare clic su **Crea una risorsa** > **Rete** > **Rete virtuale** > **+Aggiungi**.
3. In "Crea rete virtuale" immettere i valori seguenti e quindi fare clic su **Crea**:

Impostazione | Valore
------- | -----
NOME    | myVnet
Spazio degli indirizzi | 10.0.0.0/16
Nome della subnet|mySubnet
Intervallo di indirizzi subnet|10.0.0.0/24
Gruppo di risorse|Lasciare selezionata l'opzione Crea nuovo e quindi immettere un nome.
Località|Un'area supportata, ad esempio, Australia orientale
Sottoscrizione|Selezionare la propria sottoscrizione.
__Endpoint servizio__|Attivato
__Servizi__ | Selezionare uno o tutti i servizi disponibili. Servizi supportati: __"Microsoft.Storage", "Microsoft.Sql"__.

Selezionare i servizi per gli endpoint: ![Selezionare i servizi per gli endpoint di servizio](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Verificare che tutte le impostazioni siano corrette e fare clic su "Crea".

![Impostare l'endpoint di servizio](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Per finire di associare le risorse dei servizi di Azure alla rete virtuale, fare clic sulla documentazione del servizio in [Passaggi successivi](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Convalida della configurazione degli endpoint di servizio

Per verificare che gli endpoint di servizio siano configurati, seguire questa procedura:

- Nelle risorse fare clic su "Reti virtuali". Cercare la rete virtuale.
- Fare clic sul nome della rete virtuale e passare a "Endpoint di servizio"
- Gli endpoint configurati vengono visualizzati come "Riuscito". Possono essere visualizzate anche le località configurate automaticamente

![Verificare la configurazione degli endpoint di servizio](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Route valide per convalidare la configurazione di un endpoint

Per visualizzare le route valide in un'interfaccia di rete (scheda di interfaccia di rete) della subnet, fare clic su una scheda di interfaccia di rete di tale subnet. In "Supporto e risoluzione dei problemi" fare clic su "Route valide". Se l'endpoint è configurato, verrà visualizzata una nuova route predefinita con i prefissi degli indirizzi del servizio come destinazione e nextHopType come "VirtualNetworkServiceEndpoint".

![Route valide per gli endpoint di servizio](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Configurazione di endpoint di servizio per le subnet esistenti in una rete virtuale

1. Nelle risorse fare clic su "Reti virtuali" e cercare una rete virtuale esistente
2. Fare clic sul nome della rete virtuale e passare a "Endpoint di servizio"
3. Fare clic su "Aggiungi". Selezionare "Servizio". È possibile creare un endpoint per un solo servizio alla volta. 
4. Selezionare tutte le subnet in cui si vuole applicare l'endpoint. Fare clic su "Aggiungi"

![Configurazione degli endpoint di servizio delle subnet](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Eliminazione di endpoint di servizio
1. Nelle risorse fare clic su "Reti virtuali". Cercare una rete virtuale esistente filtrando il nome della rete virtuale.
2. Fare clic sul nome della rete virtuale e passare a "Endpoint di servizio"
3. Fare clic sul nome del servizio e fare clic con il pulsante destro del mouse sulla voce dell'endpoint di servizio
4. Selezionare "Elimina"

![Eliminazione di un endpoint di servizio](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure Powershell

Configurare i prerequisiti:

- Installare la versione più recente del modulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) di PowerShell. Se non si ha familiarità con Azure PowerShell, vedere [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Panoramica di Azure PowerShell).
- Per avviare una sessione di PowerShell, passare a **Start**, immettere **powershell** e quindi fare clic su **PowerShell**.
- In PowerShell accedere ad Azure immettendo il comando `login-azurermaccount`. L'account deve avere le [autorizzazioni](#provisioning) necessarie per la creazione di una rete virtuale e di un endpoint di servizio.

### <a name="get-available-service-endpoints-for-azure-region"></a>Ottenere gli endpoint di servizio disponibili per l'area di Azure

Usare il comando seguente per ottenere l'elenco di servizi supportati per gli endpoint, per un'area di Azure.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Output: 
NOME | ID | type
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Aggiungere l'endpoint del servizio Archiviazione di Azure a una subnet *mySubnet* durante la creazione della rete virtuale *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
È possibile abilitare più servizi usando un elenco di nomi di servizio delimitati da virgole.
Esempio: "Microsoft.Storage", "Microsoft.Sql"

Output previsto:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Per finire di associare le risorse dei servizi di Azure alla rete virtuale, fare clic sulla documentazione del servizio in [Passaggi successivi](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Aggiungere più endpoint di servizio a una subnet esistente

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Output previsto: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Visualizzare gli endpoint di servizio configurati in una subnet

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Output:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Eliminare gli endpoint di servizio in una subnet
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Configurare i prerequisiti:
- Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/reference-index#az_login) e seguire le istruzioni visualizzate. Per altre informazioni sulla registrazione, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
 - L'account deve avere le [autorizzazioni](#provisioning) necessarie per la creazione di una rete virtuale e di un endpoint di servizio.

 Per l'elenco completo dei comandi per le rete virtuali, vedere [Azure CLI Virtual Network commands](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) (Comandi per le reti virtuali dell'interfaccia della riga di comando di Azure)

### <a name="get-available-service-endpoints-for-azure-region"></a>Ottenere gli endpoint di servizio disponibili per l'area di Azure

Usare il comando seguente per ottenere l'elenco di servizi supportati per gli endpoint, per un'area di Azure, ad esempio "EastUS".
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Output:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Aggiungere l'endpoint del servizio Archiviazione di Azure a una subnet *mySubnet* durante la creazione della rete virtuale *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Per aggiungere più endpoint: --service-endpoints Microsoft.Storage Microsoft.Sql

Output:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Per finire di associare le risorse dei servizi di Azure alla rete virtuale, fare clic sulla documentazione del servizio in [Passaggi successivi](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Aggiungere più endpoint di servizio a una subnet esistente

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Output previsto:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Visualizzare gli endpoint di servizio configurati in una subnet

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Eliminare gli endpoint di servizio in una subnet
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Output: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Modello di Resource Manager

### <a name="securing-azure-service-resources-to-vnets"></a>Associazione delle risorse dei servizi di Azure alle reti virtuali

È possibile associare risorse di Azure specifiche alla rete virtuale tramite endpoint di servizio.

Scaricare il [modello di Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) di esempio per associare un account di archiviazione a una subnet in una rete virtuale.

Il modello crea una rete virtuale con 2 subnet e una VM con una scheda di interfaccia di rete in ogni subnet. Abilita gli endpoint in una subnet e associa un account di archiviazione a tale subnet.

È possibile scaricare il modello e modificarne le varie parti per adattarlo allo scenario.

Insieme al modello vengono fornite le istruzioni per distribuirlo tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Assicurarsi di avere le [autorizzazioni](#provisioning) necessarie per configurare l'endpoint e proteggere l'account.

Per associare le risorse di Azure a una subnet:

- Un endpoint di servizio deve essere configurato in tale subnet.
- La risorsa deve essere associata alla rete virtuale aggiungendo una regola della rete virtuale per la risorsa.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Eliminazione di endpoint di servizio con risorse associate alla subnet
Se alla subnet sono associate risorse dei servizi di Azure e l'endpoint di servizio viene eliminato, non è più possibile accedere alla risorsa dalla subnet.
Abilitando di nuovo il solo endpoint, non viene ripristinato l'accesso alle risorse prima associate alla subnet.

Per associare nuovamente la risorsa del servizio a questa subnet, è necessario:

- Abilitare nuovamente l'endpoint
- Rimuovere la regola di rete virtuale precedente per la risorsa
- Aggiungere una nuova regola che associa la risorsa alla subnet

## <a name="provisioning"></a>Provisioning

Un utente con accesso in scrittura alla rete virtuale può configurare endpoint di servizio indipendenti nelle reti virtuali.

Per associare le risorse dei servizi di Azure a una rete virtuale, l'utente deve avere l'autorizzazione "Microsoft.Network/JoinServicetoaSubnet" per le subnet da aggiungere. Per impostazione predefinita, questa autorizzazione è inclusa nei ruoli di amministratore del servizio predefiniti e può essere modificata creando ruoli personalizzati.

Altre informazioni sui [ruoli predefiniti](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e sull'assegnazione di autorizzazioni specifiche ai [ruoli personalizzati](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Le reti virtuali e le risorse dei servizi di Azure possono essere nella stessa sottoscrizione o in sottoscrizioni diverse. Se sono in sottoscrizioni diverse, le risorse devono essere nello stesso tenant di Active Directory (AD).

## <a name="next-steps"></a>Passaggi successivi

Per altre istruzioni per associare una risorsa di servizio alle reti virtuali, vedere i collegamenti seguenti:

[Associazione di account di archiviazione di Azure a reti virtuali](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Associazione di Azure SQL a reti virtuali](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

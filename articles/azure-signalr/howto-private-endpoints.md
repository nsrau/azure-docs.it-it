---
title: Usare endpoint privati
titleSuffix: Azure SignalR Service
description: Panoramica degli endpoint privati per l'accesso sicuro al servizio Azure SignalR dalle reti virtuali.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 645b2c643c1c1d4fe82eb5998a35ccc48536603e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84302143"
---
# <a name="use-private-endpoints-for-azure-signalr-service"></a>Usare endpoint privati per il servizio Azure SignalR

È possibile usare [endpoint privati](../private-link/private-endpoint-overview.md) per il servizio Azure SignalR per consentire ai client in una rete virtuale (VNet) di accedere in modo sicuro ai dati tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello spazio di indirizzi VNet per il servizio Azure SignalR. Il traffico di rete tra i client nel servizio VNet e Azure SignalR attraversa un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

L'uso di endpoint privati per il servizio Azure SignalR consente di:

- Proteggere il servizio Azure SignalR usando il controllo di accesso alla rete per bloccare tutte le connessioni nell'endpoint pubblico per il servizio Azure SignalR.
- Aumentare la sicurezza per la rete virtuale (VNet), consentendo di bloccare exfiltration di dati da VNet.
- Connettersi in modo sicuro ai servizi di Azure SignalR da reti locali che si connettono alla VNet tramite [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [delle expressroute](../expressroute/expressroute-locations.md) con peering privato.

## <a name="conceptual-overview"></a>Panoramica dei concetti

![Panoramica degli endpoint privati per il servizio Azure SignalR](media/howto-private-endpoints/private-endpoint-overview.png)

Un endpoint privato è un'interfaccia di rete speciale per un servizio di Azure nella [rete virtuale](../virtual-network/virtual-networks-overview.md) (VNet). Quando si crea un endpoint privato per il servizio Azure SignalR, fornisce connettività sicura tra i client di VNet e il servizio. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet. La connessione tra l'endpoint privato e il servizio Azure SignalR usa un collegamento privato protetto.

Le applicazioni in VNet possono connettersi al servizio Azure SignalR tramite l'endpoint privato senza interruzioni, **usando le stesse stringhe di connessione e i meccanismi di autorizzazione che verrebbero usati in altro modo**. Gli endpoint privati possono essere usati con tutti i protocolli supportati dal servizio Azure SignalR, inclusa l'API REST.

Quando si crea un endpoint privato per un servizio Azure SignalR in VNet, viene inviata una richiesta di consenso per l'approvazione al proprietario del servizio Azure SignalR. Se l'utente che richiede la creazione dell'endpoint privato è anche un proprietario del servizio Azure SignalR, questa richiesta di consenso viene approvata automaticamente.

I proprietari del servizio Azure SignalR possono gestire le richieste di consenso e gli endpoint privati, tramite la scheda "*endpoint privati*" per il servizio Azure SignalR nella [portale di Azure](https://portal.azure.com).

> [!TIP]
> Se si vuole limitare l'accesso al servizio Azure SignalR solo tramite l'endpoint privato, [configurare il controllo di accesso alla rete](howto-network-access-control.md#managing-network-access-control) per negare o controllare l'accesso tramite l'endpoint pubblico.

### <a name="connecting-to-private-endpoints"></a>Connessione agli endpoint privati

I client in un VNet che usa l'endpoint privato devono usare la stessa stringa di connessione per il servizio Azure SignalR, perché i client si connettono all'endpoint pubblico. Si fa affidamento sulla risoluzione DNS per instradare automaticamente le connessioni da VNet al servizio Azure SignalR tramite un collegamento privato.

> [!IMPORTANT]
> Usare la stessa stringa di connessione per connettersi al servizio Azure SignalR usando endpoint privati, come si farebbe diversamente. Non connettersi al servizio Azure SignalR usando il relativo `privatelink` URL di sottodominio.

Per impostazione predefinita, viene creata una [zona DNS privata](../dns/private-dns-overview.md) collegata a VNet con gli aggiornamenti necessari per gli endpoint privati. Tuttavia, se si usa il proprio server DNS, potrebbe essere necessario apportare altre modifiche alla configurazione DNS. La sezione sulle [modifiche DNS](#dns-changes-for-private-endpoints) riportata di seguito descrive gli aggiornamenti necessari per gli endpoint privati.

## <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati

Quando si crea un endpoint privato, il record di risorse DNS CNAME per il servizio Azure SignalR viene aggiornato a un alias in un sottodominio con il prefisso `privatelink` . Per impostazione predefinita, si crea anche una [zona DNS privata](../dns/private-dns-overview.md), corrispondente al `privatelink` sottodominio, con i record di risorse DNS a per gli endpoint privati.

Quando si risolve il nome di dominio del servizio SignalR di Azure dall'esterno di VNet con l'endpoint privato, questo viene risolto nell'endpoint pubblico del servizio Azure SignalR. Quando viene risolto da VNet che ospita l'endpoint privato, il nome di dominio viene risolto nell'indirizzo IP dell'endpoint privato.

Per l'esempio illustrato in precedenza, i record di risorse DNS per il servizio Azure SignalR ' foobar ', quando risolti dall'esterno della VNet che ospita l'endpoint privato, saranno:

| Nome                                                  | Type  | valore                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | Una     | \<Azure SignalR Service public IP address\>           |

Come indicato in precedenza, è possibile negare o controllare l'accesso per i client esterni a VNet tramite l'endpoint pubblico usando il controllo di accesso alla rete.

I record di risorse DNS per ' foobar ', quando vengono risolti da un client in VNet che ospita l'endpoint privato, saranno:

| Nome                                                  | Type  | valore                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.service.signalr.net``                        | CNAME | ``foobar.privatelink.service.signalr.net``            |
| ``foobar.privatelink.service.signalr.net``            | Una     | 10.1.1.5                                              |

Questo approccio consente di accedere al servizio Azure SignalR **usando la stessa stringa di connessione** per i client in VNet che ospitano gli endpoint privati, nonché i client esterni al VNet.

Se si usa un server DNS personalizzato nella rete, i client devono essere in grado di risolvere il nome di dominio completo per l'endpoint di servizio di Azure SignalR nell'indirizzo IP dell'endpoint privato. È necessario configurare il server DNS per delegare il sottodominio di collegamento privato alla zona DNS privata per la VNet o configurare i record A per `foobar.privatelink.service.signalr.net` con l'indirizzo IP dell'endpoint privato.

> [!TIP]
> Quando si usa un server DNS personalizzato o locale, è necessario configurare il server DNS per risolvere il nome del servizio Azure SignalR nel `privatelink` sottodominio per l'indirizzo IP dell'endpoint privato. A tale scopo, è possibile delegare il `privatelink` sottodominio alla zona DNS privata del VNet o configurare la zona DNS nel server DNS e aggiungere i record A DNS.

Il nome della zona DNS consigliata per gli endpoint privati per il servizio Azure SignalR è: `privatelink.service.signalr.net` .

Per ulteriori informazioni sulla configurazione del server DNS per supportare endpoint privati, fare riferimento agli articoli seguenti:

- [Risoluzione dei nomi per le risorse in reti virtuali di Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configurazione DNS per endpoint privati](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

### <a name="create-a-private-endpoint-along-with-a-new-azure-signalr-service-in-the-azure-portal"></a>Creare un endpoint privato insieme a un nuovo servizio Azure SignalR nella portale di Azure

1. Quando si crea un nuovo servizio Azure SignalR, selezionare scheda **rete** . Scegliere **endpoint privato** come metodo di connettività.

    ![Creare il servizio Azure SignalR-scheda rete](media/howto-private-endpoints/portal-create-blade-networking-tab.png)

1. Scegliere **Aggiungi**. Compilare la sottoscrizione, il gruppo di risorse, la località e il nome per il nuovo endpoint privato. Scegliere una rete virtuale e una subnet.

    ![Creare un servizio Azure SignalR-Aggiungi endpoint privato](media/howto-private-endpoints/portal-create-blade-add-private-endpoint.png)

1. Fare clic su **Rivedi e crea**.

### <a name="create-a-private-endpoint-for-an-existing-azure-signalr-service-in-the-azure-portal"></a>Creare un endpoint privato per un servizio Azure SignalR esistente nell'portale di Azure

1. Passare al servizio Azure SignalR.

1. Fare clic sul menu impostazioni denominato **connessioni endpoint privato**.

1. Fare clic sul pulsante **+ endpoint privato** nella parte superiore.

    ![Pannello connessioni endpoint privato](media/howto-private-endpoints/portal-private-endpoint-connections-blade.png)

1. Compilare la sottoscrizione, il gruppo di risorse, il nome della risorsa e l'area per il nuovo endpoint privato.
    
    ![Crea endpoint privato-nozioni di base](media/howto-private-endpoints/portal-create-private-endpoint-basics.png)

1. Scegliere la risorsa del servizio Azure SignalR di destinazione.

    ![Crea endpoint privato-risorsa](media/howto-private-endpoints/portal-create-private-endpoint-resource.png)

1. Scegliere la rete virtuale di destinazione

    ![Crea endpoint privato-configurazione](media/howto-private-endpoints/portal-create-private-endpoint-configuration.png)

1. Fare clic su **Rivedi e crea**.

### <a name="create-a-private-endpoint-using-azure-cli"></a>Creare un endpoint privato con l'interfaccia della riga di comando di Azure

1. Accedere all'interfaccia della riga di comando di Azure
    ```console
    az login
    ```
1. Selezionare la sottoscrizione di Azure
    ```console
    az account set --subscription {AZURE SUBSCRIPTION ID}
    ```
1. Creare un nuovo gruppo di risorse
    ```console
    az group create -n {RG} -l {AZURE REGION}
    ```
1. Registrare Microsoft. SignalRService come provider
    ```console
    az provider register -n Microsoft.SignalRService
    ```
1. Creare un nuovo servizio Azure SignalR
    ```console
    az signalr create --name {NAME} --resource-group {RG} --location {AZURE REGION} --sku Standard_S1
    ```
1. Creare una rete virtuale
    ```console
    az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
    ```
1. Aggiungere una subnet
    ```console
    az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
    ```
1. Disabilitare i criteri della rete virtuale
    ```console
    az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
    ```
1. Creare una zona DNS privato
    ```console
    az network private-dns zone create --resource-group {RG} --name privatelink.service.signalr.net
    ```
1. Collegare la zona DNS privato alla rete virtuale
    ```console
    az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.service.signalr.net --name {dnsZoneLinkName} --registration-enabled true
    ```
1. Creare un endpoint privato (approvazione automatica)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION}
    ```
1. Creare un endpoint privato (richiesta manuale di approvazione)
    ```console
    az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.SignalRService/SignalR/{NAME}" --group-ids signalr --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
    ```
1. Visualizzare lo stato di connessione
    ```console
    az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
    ```

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Problemi noti

Tenere presente i seguenti problemi noti sugli endpoint privati per il servizio Azure SignalR

### <a name="free-tier"></a>Livello gratuito

Non è possibile creare un endpoint privato per il servizio Azure SignalR di livello gratuito.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Vincoli di accesso per i client in reti virtuali con endpoint privati

I client in reti virtuali con endpoint privati esistenti devono affrontare vincoli per l'accesso ad altre istanze del servizio SignalR di Azure con endpoint privati. Si supponga, ad esempio, che un VNet N1 disponga di un endpoint privato per un'istanza del servizio Azure SignalR S1. Se il servizio Azure SignalR S2 ha un endpoint privato in un VNet N2, i client in VNet N1 devono accedere anche al servizio Azure SignalR S2 usando un endpoint privato. Se il servizio Azure SignalR S2 non ha endpoint privati, i client in VNet N1 possono accedere al servizio Azure SignalR in tale account senza un endpoint privato.

Questo vincolo è il risultato delle modifiche DNS apportate quando il servizio Azure SignalR S2 crea un endpoint privato.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regole del gruppo di sicurezza di rete per le subnet con endpoint privati

Attualmente, non è possibile configurare le regole del [gruppo di sicurezza di rete](../virtual-network/security-overview.md) (NSG) e le route definite dall'utente per gli endpoint privati. Le regole NSG applicate alla subnet che ospita l'endpoint privato vengono applicate all'endpoint privato. Una soluzione alternativa limitata a questo problema consiste nell'implementare le regole di accesso per gli endpoint privati nelle subnet di origine, sebbene questo approccio potrebbe richiedere un sovraccarico di gestione superiore.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare il controllo di accesso alla rete](howto-network-access-control.md)

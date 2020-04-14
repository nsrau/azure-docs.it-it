---
title: Connettere Azure ExpressRoute con l'infrastruttura cloud Oracle Documenti Microsoft
description: Connettere Azure ExpressRoute a Oracle Cloud Infrastructure (OCI) FastConnect per abilitare soluzioni di applicazioni Oracle tra cloud
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: d85c0fc8986adfa00559eab1c49a79daacdeb33f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263183"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Configurare un'interconnessione diretta tra Azure e Oracle Cloud InfrastructureSet up a direct interconnection between Azure and Oracle Cloud Infrastructure  

Per creare [un'esperienza multi-cloud integrata,](oracle-oci-overview.md)Microsoft e Oracle offrono l'interconnessione diretta tra Azure e Oracle Cloud Infrastructure (OCI) tramite [ExpressRoute](../../../expressroute/expressroute-introduction.md) e [FastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Tramite l'interconnessione ExpressRoute e FastConnect, i clienti possono riscontrare bassa latenza, velocità effettiva elevata e connettività diretta privata tra i due cloud.

> [!IMPORTANT]
> Oracle certificherà queste applicazioni per l'esecuzione in Azure quando si usa la soluzione di interconnessione Azure / Oracle Cloud entro maggio 2020.
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Applicazioni Oracle Retail
> * Oracle Hyperion Financial Management

L'immagine seguente mostra una panoramica generale dell'interconnessione:

![Connessione di rete tra cloud](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Prerequisiti

* Per stabilire la connettività tra Azure e OCI, è necessario disporre di una sottoscrizione di Azure attiva e di una tenancy OCI attiva.

* La connettività è possibile solo quando una posizione di peering di Azure ExpressRoute si trova in prossimità o nella stessa posizione di peering di OCI FastConnect.Connectivity is only possible where an Azure ExpressRoute peering location is in proximity to or in the same peering location as the OCI FastConnect. Vedere [Disponibilità dell'area](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configurare la connettività diretta tra ExpressRoute e FastConnectConfigure direct connectivity between ExpressRoute and FastConnect

1. Creare un circuito ExpressRoute standard nella sottoscrizione di Azure in un gruppo di risorse. 
    * Durante la creazione di ExpressRoute, scegliere **Oracle Cloud FastConnect** come provider di servizi. Per creare un circuito ExpressRoute, vedere la [guida dettagliata](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Un circuito di Azure ExpressRoute offre opzioni di larghezza di banda granulari, mentre FastConnect supporta 1, 2, 5 o 10 Gbps. Pertanto, è consigliabile scegliere una di queste opzioni di larghezza di banda corrispondente in ExpressRoute.Therefore, it is recommended to choose one of these matching bandwidth options under ExpressRoute.

    ![Crea circuito ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Prendere nota della **chiave del servizio**ExpressRoute . È necessario fornire la chiave durante la configurazione del circuito FastConnect.

    ![Chiave del servizio ExpressRouteExpressRoute Service key](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Ti verranno addebitati i costi ExpressRoute non appena viene eseguito il provisioning del circuito ExpressRoute (anche se non viene **eseguito**il provisioning dello stato del **provider).**

1. Eseguire l'individuazione di due spazi di indirizzi IP privati di /30 ciascuno che non si sovrappongono alla rete virtuale di Azure o allo spazio di indirizzi IP della rete virtuale OCI. Si farà riferimento al primo spazio di indirizzi IP come spazio di indirizzi primario e al secondo spazio di indirizzi IP come spazio di indirizzi secondari. Annotare gli indirizzi necessari per la configurazione del circuito FastConnect.
1. Creare un gateway di routing dinamico (DRG). Questo sarà necessario durante la creazione del circuito FastConnect. Per altre informazioni, vedere la documentazione di [Gateway di routing dinamico.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Creare un circuito FastConnect nel tenant Oracle.Create a FastConnect circuit under your Oracle tenant. Per ulteriori informazioni, vedere la [documentazione](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)di Oracle .
  
    * In Configurazione FastConnect selezionare **Microsoft Azure: ExpressRoute** come provider.
    * Selezionare il Gateway di routing dinamico di cui è stato eseguito il provisioning nel passaggio precedente.
    * Selezionare la larghezza di banda di cui eseguire il provisioning. Per ottenere prestazioni ottimali, la larghezza di banda deve corrispondere alla larghezza di banda selezionata durante la creazione del circuito ExpressRoute.For optimal performance, the bandwidth must match the bandwidth selected when creating the ExpressRoute circuit.
    * In Chiave servizio provider incollare la chiave del servizio ExpressRoute.In **Provider Service Key**, paste the ExpressRoute service key.
    * Utilizzare il primo spazio di indirizzi IP privati /30 ritagliato in un passaggio precedente per **l'indirizzo IP BGP primario** e il secondo spazio di indirizzi IP privati /30 per l'indirizzo **IP BGP secondario.**
        * Assegnare il primo indirizzo utilizzabile dei due intervalli per l'indirizzo IP BGP Oracle (primario e secondario) e il secondo indirizzo all'indirizzo IP BGP del cliente (dal punto di vista di FastConnect). Il primo indirizzo IP utilizzabile è il secondo indirizzo IP nello spazio di indirizzi /30 (il primo indirizzo IP è riservato da Microsoft).
    * Fare clic su **Crea**.
1. Completare il collegamento di FastConnect alla rete cloud virtuale nel tenant Oracle tramite il gateway di routing dinamico tramite route Table.Complete linking the FastConnect to virtual cloud network under your Oracle tenant via Dynamic Routing Gateway, using Route Table.
1. Passare ad Azure e verificare che **lo stato** del provider per il circuito ExpressRoute sia stato modificato in Provisioning e che sia stato eseguito il provisioning di un peering di tipo Azure private.Navigate to Azure and ensure that the Provider Status for your ExpressRoute circuit has changed to **Provisioned** and that a peering of type **Azure private** has been provisioned. Si tratta di un prerequisito per i passaggi seguenti.

    ![Stato del provider ExpressRouteExpressRoute provider status](media/configure-azure-oci-networking/exr-provider-status.png)
1. Fare clic sul peering privato di Azure.Click on **the Azure private** peering. Vedrai che i dettagli del peering sono stati configurati automaticamente in base alle informazioni immesse durante la configurazione del circuito FastConnect.

    ![Impostazioni di peering privato](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Connettere una rete virtuale a ExpressRouteConnect virtual network to ExpressRoute

1. Creare una rete virtuale e un gateway di rete virtuale, se non è già stato fatto. Per informazioni dettagliate, vedere la [guida dettagliata](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configurare la connessione tra il gateway di rete virtuale e il circuito ExpressRoute eseguendo [lo script Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) o eseguendo il comando PowerShell per [configurare ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Dopo aver completato la configurazione di rete, è possibile verificare la validità della configurazione facendo clic su **Ottieni record ARP** e tabella di route Get nel pannello di peering privato ExpressRoute nel portale di Azure.Once you have completed the network configuration, you can verify the validity of your configuration by clicking on Get ARP Records and Get route **table** under the ExpressRoute Private peering blade in the Azure portal.

## <a name="automation"></a>Automazione

Microsoft ha creato script Terraform per consentire la distribuzione automatica dell'interconnessione di rete. Gli script Terraform devono eseguire l'autenticazione con Azure prima dell'esecuzione, perché richiedono autorizzazioni adeguate per la sottoscrizione di Azure.The Terraform scripts need to authenticate with Azure before execution, because they require adequate permissions on the Azure subscription. L'autenticazione può essere eseguita usando un'entità servizio di Azure Active Directory o l'interfaccia della riga di comando di Azure.Authentication can be performed using an [Azure Active Directory service principal](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) or using the Azure CLI. Per ulteriori informazioni, vedere la [documentazione di Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Gli script Terraform e la relativa documentazione per distribuire l'inter-connect sono disponibili in questo [repository GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitoraggio

Installando agenti in entrambi i cloud, è possibile sfruttare Monitoraggio [prestazioni rete di](../../../expressroute/how-to-npm.md) Azure per monitorare le prestazioni della rete end-to-end. NPM consente di identificare facilmente i problemi di rete e di eliminarli.

## <a name="delete-the-interconnect-link"></a>Eliminare il collegamento di interconnessione

Per eliminare l'interconnessione, è necessario seguire i passaggi seguenti, nell'ordine specifico indicato. In caso contrario, verrà creato un circuito ExpressRoute "stato non riuscito".

1. Eliminare la connessione ExpressRoute.Delete the ExpressRoute connection. Eliminare la connessione facendo clic sull'icona **Elimina** nella pagina della connessione. Per altre informazioni, vedere la documentazione di [ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Eliminare Oracle FastConnect da Oracle Cloud Console.
1. Dopo aver eliminato il circuito Oracle FastConnect, è possibile eliminare il circuito Azure ExpressRoute.Once the Oracle FastConnect circuit has been deleted, you can delete the Azure ExpressRoute circuit.

A questo punto, il processo di eliminazione e deprovisioning è completo.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla connessione cross-cloud tra OCI e Azure, vedere la [documentazione](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)di Oracle .
* Usare [gli script Terraform](https://aka.ms/azureociinterconnecttf) per distribuire l'infrastruttura per le applicazioni Oracle di destinazione su Azure e configurare l'interconnessione di rete. 

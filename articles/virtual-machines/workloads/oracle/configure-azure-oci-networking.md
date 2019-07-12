---
title: La connessione ExpressRoute di Azure con l'infrastruttura Cloud Oracle | Microsoft Docs
description: La connessione Azure ExpressRoute con FastConnect Oracle Cloud dell'infrastruttura (OCI) per abilitare soluzioni di applicazioni tra cloud Oracle
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707623"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Configurare un'interconnessione diretta tra Azure e dell'infrastruttura Cloud Oracle  

Per creare un [multi-cloud esperienza integrata](oracle-oci-overview.md) (anteprima), Microsoft e Oracle offrono interconnessione diretta tra Azure e dell'infrastruttura Cloud Oracle (OCI) attraverso [ExpressRoute](../../../expressroute/expressroute-introduction.md) e[ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Tramite l'interconnessione FastConnect ed ExpressRoute, ai clienti di provare a bassa latenza, velocità effettiva elevata, connettività diretta privata tra due cloud.

> [!IMPORTANT]
> La connessione tra Microsoft Azure e OCI è in fase di anteprima. Per abilitare la connettività a bassa latenza tra Azure e OCI, la sottoscrizione di Azure e la tenancy OCI deve essere innanzitutto inserito nella whitelist per questa funzionalità.

L'immagine seguente mostra una panoramica generale di interconnessione:

![Connessione di rete tra cloud](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Prerequisiti

* Per stabilire la connettività tra Azure e OCI, è necessario disporre di una sottoscrizione Azure attiva e una tenancy OCI attivo.

* La connettività è possibile solo in una località di peering ExpressRoute di Azure è nelle vicinanze o nella stessa località di peering di FastConnect OCI. Visualizzare [limitazioni dell'anteprima](oracle-oci-overview.md#preview-limitations).

* Sottoscrizione di Azure deve essere nell'elenco elementi consentiti per questa funzionalità in anteprima. Contattare il rappresentante Microsoft per abilitare questa funzionalità per la sottoscrizione.

* Alla tenancy OCI deve essere nell'elenco elementi consentiti per questa funzionalità in anteprima. Per altre informazioni, contattare il rappresentante di Oracle.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configurare la connettività diretta tra FastConnect ed ExpressRoute

1. Creare un circuito ExpressRoute standard nella sottoscrizione di Azure in un gruppo di risorse. 
    * Durante la creazione di ExpressRoute, scegliere **Oracle Cloud FastConnect** come provider di servizi. Per creare un circuito ExpressRoute, vedere la [Guida dettagliata](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Un circuito Azure ExpressRoute offre larghezze di banda granulari, mentre FastConnect supporta 1, 2, 5 o 10 Gbps. Pertanto, è consigliabile scegliere una di queste opzioni di larghezza di banda corrispondente in ExpressRoute.

    ![Creare il circuito ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Annotare ExpressRoute **chiave di servizio**. È necessario fornire la chiave durante la configurazione del circuito FastConnect.

    ![Chiave di ExpressRoute Service](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Ti verrà addebitato gli addebiti per ExpressRoute, non appena viene eseguito il provisioning del circuito ExpressRoute (anche se il **Provider di stato** viene **senza provisioning**).

1. Per consentire due spazi di indirizzi IP privati di /30 ogni che non si sovrapponga con la rete virtuale di Azure o rete virtuale cloud OCI spazio di indirizzi IP. Si farà riferimento il primo spazio di indirizzi IP come spazio di indirizzi primari e lo spazio di indirizzi IP secondo come spazio di indirizzi secondario. Annotare gli indirizzi, che è necessario quando si configura il circuito FastConnect.
1. Creare un Gateway con Routing dinamico (Rod). Sarà necessario durante la creazione del circuito FastConnect. Per altre informazioni, vedere la [Gateway con Routing dinamico](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) documentazione.
1. Creare un circuito FastConnect nel tenant di Oracle. Per altre informazioni, vedere la [documentazione di Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Nella sezione Configurazione FastConnect selezionare **Microsoft Azure: ExpressRoute** come provider.
    * Selezionare il Gateway di Routing dinamico sottoposta a provisioning nel passaggio precedente.
    * Selezionare la larghezza di banda per eseguire il provisioning. Per ottenere prestazioni ottimali, la larghezza di banda deve corrispondere alla larghezza di banda selezionata durante la creazione del circuito ExpressRoute.
    * Nelle **chiave di Service Provider**, incollare la chiave del servizio ExpressRoute.
    * Utilizzo/30 prima ritagliare spazio di indirizzi IP privato in un passaggio precedente per il **indirizzo IP BGP primario** secondo/30 e spazio di indirizzi IP privato per il **secondario IP BGP** indirizzo.
        * Assegnare il primo indirizzo utilizzabile di due intervalli per l'indirizzo IP BGP Oracle (primaria e secondaria) e il secondo indirizzo per il cliente indirizzo IP BGP (dal punto di vista FastConnect). Il primo indirizzo IP usato è il secondo indirizzo IP in/30 addressspace (il primo indirizzo IP è riservato da Microsoft).
    * Fare clic su **Create**(Crea).
1. Completare il collegamento di FastConnect alla rete virtuale cloud nel tenant di Oracle tramite il Gateway dinamico Routing, usando una tabella di Route.
1. Passare ad Azure e verificare che il **Provider di stato** per ExpressRoute circuito è stato modificato per **provisioning eseguito** e che un peering di tipo **privato di Azure** è stata il provisioning. Questo è un prerequisito per i passaggi seguenti.

    ![Stato provider ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Fare clic sui **di Azure privato** peering. Verranno visualizzati che i dettagli del peering sono stati configurati automaticamente sulla base delle informazioni che immesse durante l'impostazione di un circuito FastConnect.

    ![Impostazioni peer private](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>La connessione di rete virtuale al circuito ExpressRoute

1. Se già stato fatto, creare una rete virtuale e un gateway di rete virtuale. Per informazioni dettagliate, vedere la [Guida dettagliata](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configurare la connessione tra il gateway di rete virtuale e il circuito ExpressRoute eseguendo il [lo script Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) o eseguendo il comando PowerShell per [configurare ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Dopo aver completato la configurazione di rete, è possibile verificare la validità della configurazione, fare clic sul **ottenere i record ARP** e **Get RouteTable** sotto il pannello di peering privato di ExpressRoute in il portale di Azure.

## <a name="automation"></a>Automazione

Microsoft ha creato gli script di Terraform per consentire la distribuzione automatica dell'interconnessione di rete. Gli script Terraform è necessario eseguire l'autenticazione con Azure prima dell'esecuzione, poiché richiedono le autorizzazioni adeguate per la sottoscrizione di Azure. L'autenticazione può essere eseguita usando un [entità servizio Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) o tramite la CLI di Azure. Per altre informazioni, vedere la [documentazione di Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Gli script Terraform e la documentazione correlata per distribuire il inter-connect è reperibile in questo [repository GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitoraggio

Installare gli agenti in entrambi i cloud, è possibile sfruttare Azure [Monitoraggio prestazioni rete (NPM)](../../../expressroute/how-to-npm.md) per monitorare le prestazioni della rete end-to-end. NPM consente di identificare immediatamente i problemi di rete e consente loro eliminazione.

## <a name="delete-the-interconnect-link"></a>Eliminare il collegamento di interconnessione

Per eliminare l'interconnessione, devono essere seguiti i passaggi seguenti nell'ordine specifico indicato. Errore eseguire questa operazione comporterà un circuito ExpressRoute "stato di errore".

1. Eliminare la connessione ExpressRoute. Eliminare la connessione facendo clic la **eliminare** sull'icona della pagina per la connessione. Per altre informazioni, vedere la [documentazione di ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Eliminare il FastConnect Oracle dalla Console di Cloud Oracle.
1. Una volta eliminato il circuito FastConnect Oracle, è possibile eliminare il circuito ExpressRoute di Azure.

A questo punto, l'eliminazione e deprovisioning processo è stata completata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla connessione tra cloud tra OCI e Azure, vedere la [documentazione di Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Uso [script Terraform](https://aka.ms/azureociinterconnecttf) per distribuire l'infrastruttura per applicazioni di destinazione Oracle in Azure e configurare l'interconnessione di rete. 
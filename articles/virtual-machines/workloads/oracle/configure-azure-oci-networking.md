---
title: Connettere Azure ExpressRoute con l'infrastruttura cloud Oracle | Microsoft Docs
description: Connettere Azure ExpressRoute con Oracle Cloud Infrastructure (OCI) FastConnect per abilitare soluzioni di applicazioni Oracle tra cloud
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
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 3b901f7aba40f3548a259d36b83fedca0ff2a5c2
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781300"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Configurare un'interconnessione diretta tra Azure e l'infrastruttura cloud Oracle  

Per creare un' [esperienza multicloud integrata](oracle-oci-overview.md) (anteprima), Microsoft e Oracle offrono l'interconnessione diretta tra Azure e Oracle Cloud Infrastructure (OCI) tramite [ExpressRoute](../../../expressroute/expressroute-introduction.md) e [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Attraverso l'interconnessione ExpressRoute e FastConnect, i clienti possono riscontrare bassa latenza, velocità effettiva elevata e connettività diretta privata tra i due cloud.

> [!IMPORTANT]
> La connessione tra Microsoft Azure e OCI si trova nella fase di anteprima. Per abilitare la connettività a bassa latenza tra Azure e OCI, è necessario che la sottoscrizione di Azure sia consentita per questa funzionalità.

Nell'immagine seguente viene illustrata una panoramica di alto livello dell'interconnessione:

![Connessione di rete tra cloud](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Prerequisiti

* Per stabilire la connettività tra Azure e OCI, è necessario avere una sottoscrizione di Azure attiva e un tenant attivo OCI.

* La connettività è possibile solo quando una località di peering di Azure ExpressRoute è in prossimità o nella stessa località di peering del FastConnect OCI. Vedere [limitazioni](oracle-oci-overview.md#preview-limitations)della versione di anteprima.

* Per questa funzionalità di anteprima è necessario che la sottoscrizione di Azure sia consentita. Contattare il rappresentante Microsoft per abilitare questa funzionalità nella sottoscrizione.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configurare la connettività diretta tra ExpressRoute e FastConnect

1. Creare un circuito ExpressRoute standard nella sottoscrizione di Azure in un gruppo di risorse. 
    * Durante la creazione del ExpressRoute, scegliere **Oracle Cloud FastConnect** come provider di servizi. Per creare un circuito ExpressRoute, vedere la [Guida dettagliata](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Un circuito ExpressRoute di Azure offre opzioni di larghezza di banda granulari, mentre FastConnect supporta 1, 2, 5 o 10 Gbps. È quindi consigliabile scegliere una di queste opzioni di larghezza di banda corrispondente in ExpressRoute.

    ![Crea un circuito ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Prendere nota della **chiave del servizio**ExpressRoute. È necessario specificare la chiave durante la configurazione del circuito FastConnect.

    ![Chiave del servizio ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Verranno addebitati gli addebiti per ExpressRoute non appena viene effettuato il provisioning del circuito ExpressRoute (anche se **non**è stato effettuato il provisioning dello **stato del provider** ).

1. Suddividere due spazi di indirizzi IP privati di/30 ognuno che non si sovrappongono alla rete virtuale di Azure o allo spazio di indirizzi IP della rete del cloud virtuale OCI. Si fa riferimento al primo spazio degli indirizzi IP come spazio di indirizzi primario e al secondo spazio degli indirizzi IP come spazio degli indirizzi secondario. Prendere nota degli indirizzi necessari durante la configurazione del circuito FastConnect.
1. Creare un gateway di routing dinamico (DRG). Questa operazione sarà necessaria per la creazione del circuito FastConnect. Per ulteriori informazioni, vedere la documentazione del [gateway di routing dinamico](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) .
1. Creare un circuito FastConnect nel tenant Oracle. Per ulteriori informazioni, vedere la [documentazione di Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * In configurazione di FastConnect selezionare **Microsoft Azure: ExpressRoute** come provider.
    * Selezionare il gateway di routing dinamico di cui è stato effettuato il provisioning nel passaggio precedente.
    * Selezionare la larghezza di banda di cui effettuare il provisioning. Per ottenere prestazioni ottimali, la larghezza di banda deve corrispondere alla larghezza di banda selezionata durante la creazione del circuito ExpressRoute.
    * Nella **chiave del servizio del provider**incollare la chiave del servizio ExpressRoute.
    * Usare il primo/30 spazio di indirizzi IP privati suddiviso in un passaggio precedente per l' **indirizzo IP BGP primario** e il secondo/30 spazio di indirizzi IP privato per l'indirizzo **IP BGP secondario** .
        * Assegnare il primo indirizzo utilizzabile dei due intervalli per l'indirizzo IP BGP Oracle (primario e secondario) e il secondo indirizzo all'indirizzo IP BGP del cliente (dal punto di vista della FastConnect). Il primo indirizzo IP utilizzabile è il secondo indirizzo IP nello spazio degli indirizzi/30 (il primo indirizzo IP è riservato da Microsoft).
    * Fare clic su **Create**(Crea).
1. Completare il collegamento di FastConnect alla rete cloud virtuale nel tenant Oracle tramite il gateway di routing dinamico, usando la tabella di route.
1. Passare ad Azure e assicurarsi che lo **stato del provider** per il circuito ExpressRoute sia stato modificato in provisioning eseguito e che sia stato effettuato il provisioning di un peering di tipo **privato di Azure** . Questo è un prerequisito per i passaggi seguenti.

    ![Stato provider ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Fare clic sul peering **privato di Azure** . Si noterà che i dettagli del peering sono stati configurati automaticamente in base alle informazioni immesse durante la configurazione del circuito FastConnect.

    ![Impostazioni peering privato](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Connetti rete virtuale a ExpressRoute

1. Creare una rete virtuale e un gateway di rete virtuale, se non è già stato fatto. Per informazioni dettagliate, vedere la [Guida dettagliata](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configurare la connessione tra il gateway di rete virtuale e il circuito ExpressRoute eseguendo lo script di [bonifica](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) oppure eseguendo il comando di PowerShell per [configurare ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Dopo aver completato la configurazione di rete, è possibile verificare la validità della configurazione facendo clic su **ottenere i record ARP** e **ottenere la tabella di route** sotto il pannello peering privato ExpressRoute nel portale di Azure.

## <a name="automation"></a>Automazione

Microsoft ha creato script di bonifica per consentire la distribuzione automatica dell'interconnessione di rete. Gli script di bonifica devono eseguire l'autenticazione con Azure prima dell'esecuzione, perché richiedono autorizzazioni adeguate per la sottoscrizione di Azure. L'autenticazione può essere eseguita tramite un' [entità servizio Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) o tramite l'interfaccia della riga di comando di Azure. Per ulteriori informazioni, vedere la [documentazione relativa alla bonifica](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Gli script di bonifica e la documentazione correlata per la distribuzione di Inter-Connect sono disponibili in questo [repository GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitoraggio

Installando gli agenti in entrambi i cloud, è possibile sfruttare Azure [monitoraggio prestazioni rete (NPM)](../../../expressroute/how-to-npm.md) per monitorare le prestazioni della rete end-to-end. NPM consente di identificare facilmente i problemi di rete e di eliminarli.

## <a name="delete-the-interconnect-link"></a>Elimina il collegamento di interconnessione

Per eliminare l'interconnessione, è necessario seguire i passaggi seguenti nell'ordine specifico specificato. In caso contrario, viene generato un circuito ExpressRoute "stato di errore".

1. Eliminare la connessione ExpressRoute. Per eliminare la connessione, fare clic sull'icona **Elimina** nella pagina della connessione. Per ulteriori informazioni, vedere la [documentazione di ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Eliminare il FastConnect Oracle dalla console cloud Oracle.
1. Una volta eliminato il circuito Oracle FastConnect, è possibile eliminare il circuito ExpressRoute di Azure.

A questo punto, il processo di eliminazione e deprovisioning è completo.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulla connessione tra cloud tra OCI e Azure, vedere la documentazione di [Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Usare gli [script di bonifica](https://aka.ms/azureociinterconnecttf) per distribuire l'infrastruttura per le applicazioni Oracle di destinazione in Azure e configurare l'interconnessione di rete. 
---
title: Informazioni sulla rete in ripristino di emergenza per macchine virtuali di Azure con Azure Site Recovery
description: Viene fornita una panoramica delle reti per la replica delle macchine virtuali di Azure tramite Azure Site Recovery.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: f0a3ac0c81291a1231ef660481d8e31b38c0e212
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631342"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Informazioni sulle reti in ripristino di emergenza per macchine virtuali di Azure



In questo articolo viene illustrato in dettaglio il materiale sussidiario del servizio di rete quando si esegue la replica e il ripristino di macchine virtuali di Azure da un'area ad un'altra tramite [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infrastruttura di rete tipica

Il diagramma seguente illustra un ambiente di Azure tipico per applicazioni in esecuzione in macchine virtuali di Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se si usa Azure ExpressRoute o una connessione VPN da una rete locale ad Azure, l'ambiente è il seguente:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Le reti sono in genere protette tramite firewall e gruppi di sicurezza di rete (NSG). I tag di servizio devono essere usati per controllare la connettività di rete. Gruppi deve consentire a diversi tag di servizio di controllare la connettività in uscita.

>[!IMPORTANT]
> Se si usa un proxy autenticato per controllare la connettività di rete, questo non è supportato da Site Recovery e non è possibile abilitare la replica.


## <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di consentire gli URL di Site Recovery seguenti:

>[!NOTE]
> Non è necessario eseguire il filtro basato su indirizzi IP per controllare la connettività in uscita.

**URL** | **Dettagli**
--- | ---
*.blob.core.windows.net | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale. Se si conoscono tutti gli account di archiviazione della cache per le macchine virtuali, è possibile consentire l'accesso agli URL specifici dell'account di archiviazione, ad esempio cache1.blob.core.windows.net e cache2.blob.core.windows.net, anziché *. blob.core.windows.net
login.microsoftonline.com | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale.
*.servicebus.windows.net | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale.
*.vault.azure.net | Consente l'accesso per abilitare la replica per le macchine virtuali abilitate per ADE tramite il portale
*. automation.ext.azure.com | Consente l'abilitazione dell'aggiornamento automatico dell'agente di mobilità per un elemento replicato tramite il portale

## <a name="outbound-connectivity-using-service-tags"></a>Connettività in uscita tramite tag di servizio

Se si usa un NSG per controllare la connettività in uscita, questi tag del servizio devono essere consentiti.

- Per gli account di archiviazione nell'area di origine:
    - Creare una regola NSG basata su [tag del servizio di archiviazione](../virtual-network/security-overview.md#service-tags) per l'area di origine.
    - Consentire questi indirizzi in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.
- Creare una regola NSG basata su [tag del servizio Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) per consentire l'accesso a tutti gli indirizzi IP corrispondenti ad AAD
- Creare una regola NSG basata su tag del servizio EventsHub per l'area di destinazione, consentendo l'accesso al monitoraggio Site Recovery.
- Creare una regola NSG basata su tag del servizio AzureSiteRecovery per consentire l'accesso al servizio Site Recovery in qualsiasi area.
- Creare una regola NSG basata su tag del servizio AzureKeyVault. Questa operazione è necessaria solo per abilitare la replica delle macchine virtuali abilitate per ADE tramite il portale.
- Creare una regola NSG basata su tag del servizio GuestAndHybridManagement. Questa operazione è necessaria solo per abilitare l'aggiornamento automatico dell'agente di mobilità per un elemento replicato tramite il portale.
- Prima di creare le regole in un gruppo di sicurezza di rete di produzione, è consigliabile creare le regole del gruppo di sicurezza di rete necessarie in un NSG di test e verificare che non siano presenti problemi.

## <a name="example-nsg-configuration"></a>Esempio di configurazione del gruppo di sicurezza di rete

In questo esempio viene illustrato come configurare le regole NSG per una macchina virtuale da replicare.

- Se si usano regole NSG per controllare la connettività in uscita, usare regole che consentano HTTPS in uscita per la porta 443 per tutti gli intervalli di indirizzi IP necessari.
- Nell'esempio si presuppone che il percorso di origine della macchina virtuale sia "Stati Uniti orientali" e che il percorso di destinazione sia "Stati Uniti centrali".

### <a name="nsg-rules---east-us"></a>Regole NSG - Stati Uniti orientali

1. Creare una regola di sicurezza HTTPS in uscita (443) per "Storage.EastUS" nel gruppo di sicurezza di rete, come illustrato nello screenshot seguente.

      ![Screenshot mostra aggiungere una regola di sicurezza in uscita per un gruppo di sicurezza di rete per l'archiviazione punto est U S.](./media/azure-to-azure-about-networking/storage-tag.png)

2. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureActiveDirectory" nel gruppo di sicurezza di rete, come illustrato nello screenshot seguente.

      ![Screenshot mostra la regola di sicurezza in uscita aggiunta per un gruppo di sicurezza di rete per Azure A D.](./media/azure-to-azure-about-networking/aad-tag.png)

3. Analogamente alle regole di sicurezza sopra riportate, creare una regola di sicurezza HTTPS in uscita (443) per "EventHub. Centralus" in NSG che corrisponde al percorso di destinazione. In questo modo è possibile accedere al monitoraggio Site Recovery.

4. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureSiteRecovery" in NSG. In questo modo è possibile accedere al servizio Site Recovery in qualsiasi area.

### <a name="nsg-rules---central-us"></a>Regole NSG - Stati Uniti centrali

Queste regole sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:

1. Creare una regola di sicurezza HTTPS in uscita (443) per "Storage.CentralUS" nel gruppo di sicurezza di rete.

2. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureActiveDirectory" nel gruppo di sicurezza di rete.

3. Analogamente alle regole di sicurezza sopra riportate, creare una regola di sicurezza HTTPS in uscita (443) per "EventHub. Eastus" in NSG che corrisponde alla posizione di origine. In questo modo è possibile accedere al monitoraggio Site Recovery.

4. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureSiteRecovery" in NSG. In questo modo è possibile accedere al servizio Site Recovery in qualsiasi area.

## <a name="network-virtual-appliance-configuration"></a>Configurazione di appliance virtuali di rete

Se si usano appliance virtuali di rete per controllare il traffico di rete in uscita dalle VM, l'appliance potrebbe essere soggetta a limitazioni se tutto il traffico di replica passa attraverso l'appliance virtuale di rete. È consigliabile creare un endpoint del servizio di rete nella rete virtuale per "Archiviazione" in modo che il traffico di replica non venga indirizzato all'appliance virtuale di rete.

### <a name="create-network-service-endpoint-for-storage"></a>Creare un endpoint servizio di rete per Storage
È possibile creare un endpoint servizio di rete nella rete virtuale per "Storage" in modo che il traffico di replica non lasci il limite di Azure.

- Selezionare la rete virtuale di Azure e fare clic su "Endpoint servizio"

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Fare clic su "Aggiungi" per aprire la scheda "Aggiungi endpoint del servizio"
- Selezionare "Microsoft.Storage" in "Servizio" e le subnet obbligatorie nel campo "Subnet" e fare clic su "Aggiungi"

>[!NOTE]
>Non limitare agli account di archiviazione usati per ASR l'accesso alla rete virtuale. È consigliabile consentire l'accesso da "Tutte le reti"

### <a name="forced-tunneling"></a>Tunneling forzato

È possibile eseguire l'override della route di sistema predefinita di Azure per il prefisso dell'indirizzo 0.0.0.0/0 con una [route personalizzata](../virtual-network/virtual-networks-udr-overview.md#custom-routes) e deviare il traffico delle macchine virtuali a un'appliance virtuale di rete locale (NVA), ma questa configurazione non è consigliata per la replica Site Recovery. Se si usano route personalizzate, [creare un endpoint servizio di rete virtuale](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) nella rete virtuale per "Archiviazione" in modo che il traffico di replica non lasci il limite di Azure.

## <a name="next-steps"></a>Passaggi successivi
- Iniziare a proteggere i carichi di lavoro [eseguendo la replica di macchine virtuali di Azure](./azure-to-azure-quickstart.md).
- Altre informazioni sul [Mantenimento degli indirizzi IP](site-recovery-retain-ip-azure-vm-failover.md) per il failover delle macchine virtuali di Azure.
- Scopri di più sul ripristino di emergenza di [macchine virtuali di Azure con ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).

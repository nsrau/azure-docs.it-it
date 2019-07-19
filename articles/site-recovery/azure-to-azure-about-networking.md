---
title: Informazioni sulle reti da Azure a ripristino di emergenza di Azure tramite Azure Site Recovery | Microsoft Docs
description: Viene fornita una panoramica delle reti per la replica delle macchine virtuali di Azure tramite Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 844563e03529e472624b35d2b545c3e432e4ea17
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876289"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Informazioni sulle reti per la replica da Azure ad Azure



In questo articolo viene illustrato in dettaglio il materiale sussidiario del servizio di rete quando si esegue la replica e il ripristino di macchine virtuali di Azure da un'area ad un'altra tramite [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infrastruttura di rete tipica

Il diagramma seguente illustra un ambiente di Azure tipico per applicazioni in esecuzione in macchine virtuali di Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se si usa Azure ExpressRoute o una connessione VPN da una rete locale ad Azure, l'ambiente è il seguente:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Le reti sono in genere protette tramite firewall e gruppi di sicurezza di rete (NSG). I firewall usano URL o whitelist basate su IP per controllare la connettività di rete. I gruppi di sicurezza di rete forniscono regole che usano intervalli di indirizzi IP per controllare la connettività di rete.

>[!IMPORTANT]
> Se si usa un proxy autenticato per controllare la connettività di rete, questo non è supportato da Site Recovery e non è possibile abilitare la replica.


## <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di consentire gli URL di Site Recovery seguenti:


**URL** | **Dettagli**  
--- | ---
*.blob.core.windows.net | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale. Se si conoscono tutti gli account di archiviazione della cache per le macchine virtuali, è possibile aggiungere all'elenco elementi consentiti gli URL specifici dell'account di archiviazione (ad esempio, cache1.blob.core.windows.net e cache2.blob.core.windows.net) anziché *. blob.core.windows.net
login.microsoftonline.com | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale. È possibile usare il ' Site Recovery IP ' corrispondente se il proxy del firewall supporta gli IP.
*.servicebus.windows.net | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale. Se il proxy del firewall supporta gli IP, è possibile usare il ' Site Recovery IP di monitoraggio ' corrispondente.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Connettività in uscita per gli intervalli di indirizzi IP

Se si usa un proxy firewall basato su IP o regole NSG per controllare la connettività in uscita, è necessario consentire i seguenti intervalli IP.

- Tutti gli intervalli di indirizzi IP che corrispondono agli account di archiviazione nell'area di origine
    - Creare una regola NSG basata su [tag del servizio di archiviazione](../virtual-network/security-overview.md#service-tags) per l'area di origine.
    - Consentire questi indirizzi in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.
- Creare una regola NSG basata su [tag del servizio Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) per consentire l'accesso a tutti gli indirizzi IP corrispondenti ad AAD
    - Se in futuro vengono aggiunti nuovi indirizzi ad Azure Active Directory (AAD), è necessario creare nuove regole NSG.
- Indirizzi IP dell'endpoint di servizio di Site Recovery, disponibili in un [file XML](https://aka.ms/site-recovery-public-ips) e che variano a seconda della posizione di destinazione.
- Prima di creare le regole in un gruppo di sicurezza di rete di produzione, è consigliabile creare le regole del gruppo di sicurezza di rete necessarie in un NSG di test e verificare che non siano presenti problemi.


Gli intervalli di indirizzi IP di Site Recovery sono i seguenti:

   **Destinazione** | **IP di Site Recovery** |  **IP di monitoraggio di Site Recovery**
   --- | --- | ---
   Asia orientale | 52.175.17.132 | 13.94.47.61
   Asia sud-orientale | 52.187.58.193 | 13.76.179.223
   India centrale | 52.172.187.37 | 104.211.98.185
   India meridionale | 52.172.46.220 | 104.211.224.190
   Stati Uniti centro-settentrionali | 23.96.195.247 | 168.62.249.226
   Europa settentrionale | 40.69.212.238 | 52.169.18.8
   Europa occidentale | 52.166.13.64 | 40.68.93.145
   East US | 13.82.88.226 | 104.45.147.24
   Stati Uniti occidentali | 40.83.179.48 | 104.40.26.199
   Stati Uniti centro-meridionali | 13.84.148.14 | 104.210.146.250
   Stati Uniti centrali | 40.69.144.231 | 52.165.34.144
   Stati Uniti orientali 2 | 52.184.158.163 | 40.79.44.59
   Giappone orientale | 52.185.150.140 | 138.91.1.105
   Giappone occidentale | 52.175.146.69 | 138.91.17.38
   Brasile meridionale | 191.234.185.172 | 23.97.97.36
   Australia orientale | 104.210.113.114 | 191.239.64.144
   Australia sud-orientale | 13.70.159.158 | 191.239.160.45
   Canada centrale | 52.228.36.192 | 40.85.226.62
   Canada orientale | 52.229.125.98 | 40.86.225.142
   Stati Uniti centro-occidentali | 52.161.20.168 | 13.78.149.209
   Stati Uniti occidentali 2 | 52.183.45.166 | 13.66.228.204
   Regno Unito occidentale | 51.141.3.203 | 51.141.14.113
   Regno Unito meridionale | 51.140.43.158 | 51.140.189.52
   Regno Unito meridionale 2 | 13.87.37.4| 13.87.34.139
   Regno Unito settentrionale | 51.142.209.167 | 13.87.102.68
   Corea del Sud centrale | 52.231.28.253 | 52.231.32.85
   Corea del Sud meridionale | 52.231.198.185 | 52.231.200.144
   Francia centrale | 52.143.138.106 | 52.143.136.55
   Francia meridionale | 52.136.139.227 |52.136.136.62
   Australia centrale| 20.36.34.70 | 20.36.46.142
   Australia centrale 2| 20.36.69.62 | 20.36.74.130
   Sudafrica occidentale | 102.133.72.51 | 102.133.26.128
   Sudafrica settentrionale | 102.133.160.44 | 102.133.154.128
   US Gov Virginia | 52.227.178.114 | 23.97.0.197
   Governo degli Stati Uniti - Iowa | 13.72.184.23 | 23.97.16.186
   US Gov Arizona | 52.244.205.45 | 52.244.48.85
   US Gov Texas | 52.238.119.218 | 52.238.116.60
   US DoD (area orientale) | 52.181.164.103 | 52.181.162.129
   US DoD (area centrale) | 52.182.95.237 | 52.182.90.133
## <a name="example-nsg-configuration"></a>Esempio di configurazione del gruppo di sicurezza di rete

In questo esempio viene illustrato come configurare le regole NSG per una macchina virtuale da replicare.

- Se si usano regole NSG per controllare la connettività in uscita, usare regole che consentano HTTPS in uscita per la porta 443 per tutti gli intervalli di indirizzi IP necessari.
- Nell'esempio si presuppone che il percorso di origine della macchina virtuale sia "Stati Uniti orientali" e che il percorso di destinazione sia "Stati Uniti centrali".

### <a name="nsg-rules---east-us"></a>Regole NSG - Stati Uniti orientali

1. Creare una regola di sicurezza HTTPS in uscita (443) per "Storage.EastUS" nel gruppo di sicurezza di rete, come illustrato nello screenshot seguente.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureActiveDirectory" nel gruppo di sicurezza di rete, come illustrato nello screenshot seguente.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Creare regole HTTPS in uscita (443) per gli IP di Site Recovery che corrispondono alla località di destinazione:

   **Location** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regole NSG - Stati Uniti centrali

Queste regole sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:

1. Creare una regola di sicurezza HTTPS in uscita (443) per "Storage.CentralUS" nel gruppo di sicurezza di rete.

2. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureActiveDirectory" nel gruppo di sicurezza di rete.

3. Creare regole HTTPS in uscita (443) per gli IP di Site Recovery che corrispondono alla località di origine:

   **Location** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 13.82.88.226 | 104.45.147.24

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
- Iniziare a proteggere i carichi di lavoro [eseguendo la replica di macchine virtuali di Azure](site-recovery-azure-to-azure.md).
- Altre informazioni sul [Mantenimento degli indirizzi IP](site-recovery-retain-ip-azure-vm-failover.md) per il failover delle macchine virtuali di Azure.
- Scopri di più sul ripristino di emergenza di [macchine virtuali di Azure con ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).

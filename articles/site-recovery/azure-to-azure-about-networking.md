---
title: Informazioni sulle reti da Azure a ripristino di emergenza di Azure tramite Azure Site Recovery | Microsoft Docs
description: Viene fornita una panoramica delle reti per la replica delle macchine virtuali di Azure tramite Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/08/2018
ms.author: sujayt
ms.openlocfilehash: 5ce85761df4e0ad62c22a829f67464a3145fd827
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Informazioni sulle reti per la replica da Azure ad Azure

>[!NOTE]
> La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

In questo articolo viene illustrato in dettaglio il materiale sussidiario del servizio di rete quando si esegue la replica e il ripristino di macchine virtuali di Azure da un'area ad un'altra tramite [Azure Site Recovery](site-recovery-overview.md). 

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infrastruttura di rete tipica

Il diagramma seguente illustra un ambiente di Azure tipico per applicazioni in esecuzione in macchine virtuali di Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se si usa Azure ExpressRoute o una connessione VPN da una rete locale ad Azure, l'ambiente è simile al seguente:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Le reti sono in genere protette tramite firewall e gruppi di sicurezza di rete (NSG). I firewall usano URL o whitelist basate su IP per controllare la connettività di rete. I gruppi di sicurezza di rete forniscono regole che usano intervalli di indirizzi IP per controllare la connettività di rete.

>[!IMPORTANT]
> Se si usa un proxy autenticato per controllare la connettività di rete, questo non è supportato da Site Recovery e non è possibile abilitare la replica.


## <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di consentire gli URL di Site Recovery seguenti:


**URL** | **Dettagli**  
--- | ---
*.blob.core.windows.net | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale.
login.microsoftonline.com | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale.
*.servicebus.windows.net | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Connettività in uscita per gli intervalli di indirizzi IP

Se si usa un proxy firewall basato su IP o regole NSG per controllare la connettività in uscita, è necessario consentire i seguenti intervalli IP.

- Tutti gli intervalli di indirizzi IP che corrispondono alla località di origine.
    - È possibile scaricare gli [intervalli di indirizzi IP](https://www.microsoft.com/download/confirmation.aspx?id=41653).
    - Questi indirizzi devono essere consentiti in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.
- Tutti gli intervalli di indirizzi IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) di Office 365.
    - Se in futuro vengono aggiunti nuovi indirizzi agli intervalli di Office 365, è necessario creare nuove regole NSG.
- Indirizzi IP dell'endpoint di servizio di Site Recovery. Questi sono disponibili in un [file XML](https://aka.ms/site-recovery-public-ips)e variano a seconda del percorso di destinazione.
-  Per creare automaticamente le regole richieste nel gruppo di sicurezza di rete, è possibile [scaricare e usare questo script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702). 
- Prima di creare le regole in un gruppo di sicurezza di rete di produzione, è consigliabile creare le regole del gruppo di sicurezza di rete necessarie in un NSG di test e verificare che non siano presenti problemi.
- Per creare il numero di regole NSG richiesto, verificare che la sottoscrizione sia consentita. Contattare il supporto tecnico di Azure per aumentare il limite delle regole NSG nella sottoscrizione.

Gli intervalli di indirizzi IP sono i seguenti:

>
   **Destinazione** | **IP di Site Recovery** |  **IP di monitoraggio di Site Recovery**
   --- | --- | ---
   Asia orientale | 52.175.17.132 | 13.94.47.61
   Asia sudorientale | 52.187.58.193 | 13.76.179.223
   India centrale | 52.172.187.37 | 104.211.98.185
   India meridionale | 52.172.46.220 | 104.211.224.190
   Stati Uniti centro-settentrionali | 23.96.195.247 | 168.62.249.226
   Europa settentrionale | 40.69.212.238 | 52.169.18.8
   Europa occidentale | 52.166.13.64 | 40.68.93.145
   Stati Uniti orientali | 13.82.88.226 | 104.45.147.24
   Stati Uniti occidentali | 40.83.179.48 | 104.40.26.199
   Stati Uniti centro-meridionali | 13.84.148.14 | 104.210.146.250
   Stati Uniti centrali | 40.69.144.231 | 52.165.34.144
   Stati Uniti orientali 2 | 52.184.158.163 | 40.79.44.59
   Giappone orientale | 52.185.150.140 | 138.91.1.105
   Giappone occidentale | 52.175.146.69 | 138.91.17.38
   Brasile meridionale | 191.234.185.172 | 23.97.97.36
   Australia orientale | 104.210.113.114 | 191.239.64.144
   Australia sudorientale | 13.70.159.158 | 191.239.160.45
   Canada centrale | 52.228.36.192 | 40.85.226.62
   Canada orientale | 52.229.125.98 | 40.86.225.142
   Stati Uniti centro-occidentali | 52.161.20.168 | 13.78.149.209
   Stati Uniti occidentali 2 | 52.183.45.166 | 13.66.228.204
   Regno Unito occidentale | 51.141.3.203 | 51.141.14.113
   Regno Unito meridionale | 51.140.43.158 | 51.140.189.52
   Regno Unito meridionale 2 | 13.87.37.4| 13.87.34.139
   Regno Unito settentrionale | 51.142.209.167 | 13.87.102.68
   Corea centrale | 52.231.28.253 | 52.231.32.85
   Corea meridionale | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>Esempio di configurazione del gruppo di sicurezza di rete

In questo esempio viene illustrato come configurare le regole NSG per una macchina virtuale da replicare. 

- Se si usano regole NSG per controllare la connettività in uscita, usare regole che consentano HTTPS in uscita per tutti gli intervalli di indirizzi IP necessari.
- Nell'esempio si presuppone che il percorso di origine della macchina virtuale sia "Stati Uniti orientali" e che il percorso di destinazione sia "Stati Uniti centrali".

### <a name="nsg-rules---east-us"></a>Regole NSG - Stati Uniti orientali

1. Creare regole che corrispondano agli [intervalli di indirizzi IP degli Stati Uniti orientali](https://www.microsoft.com/download/confirmation.aspx?id=41653). Queste regole sono richieste in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.
2. Creare regole per tutti gli intervalli di indirizzi IP che corrispondano agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) di Office 365.
3. Creare regole che corrispondano alla posizione di destinazione:

   **Posizione** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regole NSG - Stati Uniti centrali 

Queste regole sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:

* Regole che corrispondono agli [intervalli IP degli Stati Uniti centrali](https://www.microsoft.com/download/confirmation.aspx?id=41653). Queste regole sono richieste in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.

* Regole per tutti gli intervalli IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) di Office 365.

* Regole che corrispondono alla posizione di origine:
    - Stati Uniti orientali
    - Indirizzo IP di Site Recovery: 13.82.88.226
    - Indirizzo IP di monitoraggio di Site Recovery: 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

Se si dispone di una connessione VPN o ExpressRoute tra la posizione locale e quella di Azure, seguire le linee guida in questa sezione.

### <a name="forced-tunneling"></a>Tunneling forzato

In genere si stabilisce una route predefinita (0.0.0.0/0) che forza il flusso del traffico Internet in uscita attraverso il percorso locale. Ciò non è consigliabile. Il traffico della replica e la comunicazione del servizio Site Recovery non devono allontanarsi dal limite di Azure. La soluzione consiste nell'aggiungere route definite dall'utente (UDR) per [questi intervalli IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) in modo che il traffico della replica non finisca locale.

### <a name="connectivity"></a>Connettività 

Seguire queste linee guida per le connessioni tra la posizione di destinazione e la posizione locale:
- Se l'applicazione deve connettersi alle macchine locali o se sono presenti client che si connettono all'applicazione dal locale tramite VPN/ExpressRoute, verificare di disporre di almeno una [connessione da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) tra l'area di Azure di destinazione e il data center locale.

- Se si prevede molto traffico tra l'area di Azure di destinazione e il data center locale, è necessario creare un'altra [connessione ExpressRoute](../expressroute/expressroute-introduction.md) tra l'area di Azure di destinazione e il data center locale.

- Se si desidera mantenere gli IP per le macchine virtuali dopo il failover, mantenere la connessione da sito a sito/ExpressRoute dell'area di destinazione in uno stato di disconnessione. Ciò garantisce che non si generi conflitto di intervalli tra gli intervalli IP dell'area di origine e gli intervalli IP dell'area di destinazione.

### <a name="expressroute-configuration"></a>Configurazione di ExpressRoute
Seguire queste procedure consigliate per la configurazione di ExpressRoute:

- È necessario creare un circuito ExpressRoute nelle aree di origine e in quelle di destinazione. È quindi necessario creare una connessione tra:
  - La rete virtuale di origine e il circuito ExpressRoute.
  - La rete virtuale di destinazione e il circuito ExpressRoute.

- Come parte dello standard ExpressRoute, è possibile creare circuiti nella stessa area geopolitica. Per creare i circuiti ExpressRoute in diverse aree geopolitiche, è necessaria la presenza di Azure ExpressRoute Premium, il che comporta un costo incrementale (se si sta già usando ExpressRoute Premium, non sono previsti costi aggiuntivi). Per altre informazioni, vedere il [documento sulle posizioni di ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e i [prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

- È consigliabile usare diversi intervalli IP nelle aree di origine e di destinazione. Il circuito ExpressRoute non sarà in grado di connettersi a due reti virtuali di Azure degli stessi intervalli IP nello stesso momento.

- È possibile creare reti virtuali con gli stessi intervalli IP in entrambe le aree e quindi creare circuiti ExpressRoute in entrambe le aree. Nel caso di un evento di failover, disconnettere il circuito dalla rete virtuale di origine e connettersi al circuito nella rete virtuale di destinazione.

 >[!IMPORTANT]
 > Se l'area primaria è completamente inattiva, l'operazione di disconnessione può avere esito negativo. Ciò impedirà alla rete virtuale di destinazione di ottenere la connettività di ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi
Iniziare a proteggere i carichi di lavoro [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).

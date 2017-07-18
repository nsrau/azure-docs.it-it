---
title: Materiale sussidiario del servizio di rete di Azure Site Recovery per la replica delle macchine virtuali da Azure ad Azure | Microsoft Docs
description: Materiale sussidiario del servizio di rete per la replica delle macchine virtuali di Azure
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/13/2017
ms.author: sujayt
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: eb7b6d606d1a7455710be5e1cf0298c368fc8b1e
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Materiale sussidiario del servizio di rete per la replica delle macchine virtuali di Azure

>[!NOTE]
> La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

In questo articolo si illustra nel dettaglio il materiale sussidiario del servizio di rete per Azure Site Recovery quando si esegue la replica e il ripristino delle macchine virtuali di Azure da un'area ad un'altra area. Per altre informazioni sui requisiti di Azure Site Recovery, vedere l'articolo sui [prerequisiti](site-recovery-prereq.md).

## <a name="site-recovery-architecture"></a>Architettura di Site Recovery

Site Recovery fornisce un modo semplice per replicare le applicazioni in esecuzione nelle macchine virtuali di Azure in un'altra area di Azure in modo che possano essere ripristinate in caso di interruzione nell'area primaria. Altre informazioni su [questo scenario e sull'architettura di Site Recovery](site-recovery-azure-to-azure-architecture.md).

## <a name="your-network-infrastructure"></a>Infrastruttura della rete in uso

Il diagramma seguente illustra l'ambiente tipico di Azure per un'applicazione in esecuzione nelle macchine virtuali di Azure:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Se si usa Azure ExpressRoute o una connessione VPN da una rete locale ad Azure, l'ambiente è simile al seguente:

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

In genere, i clienti proteggono le proprie reti usando firewall e/o gruppi di sicurezza di rete. I firewall possono usare elenchi basati su URL o su IP consentiti per controllare la connettività di rete. I NSGs consentono le regole per usare intervalli IP per controllare la connettività di rete.

>[!IMPORTANT]
> Se si usa un proxy autenticato per controllare la connettività di rete, esso non è supportato e non è possibile abilitare la replica di Site Recovery. 

Le sezioni seguenti illustrano le modifiche alla connettività in uscita di rete che sono richieste dalle macchine virtuali di Azure per consentire alla replica di Site Recovery di funzionare.

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Connettività in uscita per gli URL di Azure Site Recovery

Se si usa qualsiasi proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di inserire nell'elenco dei consentiti questi URL del servizio di Azure Site Recovery richiesti:


**URL** | **Scopo**  
--- | ---
*.blob.core.windows.net | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale.
login.microsoftonline.com | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale.
*.servicebus.windows.net | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale.

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Connettività in uscita per gli intervalli IP di Azure Site Recovery

>[!NOTE]
> Per creare automaticamente le regole NSG richieste nel gruppo di sicurezza di rete, è possibile [scaricare e usare questo script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

>[!IMPORTANT]
> * È consigliabile creare le regole NSG richieste in un gruppo di sicurezza di rete di test e verificare che non siano presenti problemi prima di creare le regole in un gruppo di sicurezza di rete di produzione.
> * Per creare il numero di regole NSG richiesto, verificare che la sottoscrizione sia consentita. Contattare il supporto tecnico per aumentare il limite delle regole NSG nella sottoscrizione.

Se si usa qualsiasi proxy firewall basato su IP o le regole NSG per controllare la connettività in uscita, è necessario che i seguenti intervalli IP siano consentiti, a seconda dei percorsi di origine e destinazione delle macchine virtuali:

- Tutti gli intervalli IP che corrispondono alla posizione di origine. (è possibile scaricare gli [intervalli IP](https://www.microsoft.com/download/confirmation.aspx?id=41653)). L'elenco consentito è richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.

- Tutti gli intervalli IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) di Office 365.

    >[!NOTE]
    > Se in futuro i nuovi IP vengono aggiunti agli intervalli IP di Office 365, è necessario creare nuove regole NSG.
    
- IP dell'endpoint di servizio di Site Recovery ([disponibili in un file XML](https://aka.ms/site-recovery-public-ips)), che variano a seconda della posizione di destinazione: 

   **Posizione di destinazione** | **IP del servizio Site Recovery** |  **IP di monitoraggio di Site Recovery**
   --- | --- | ---
   Asia orientale | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   Asia sudorientale | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   India centrale | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   India meridionale | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   Stati Uniti centro-settentrionali | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   Europa settentrionale | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   Europa occidentale | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   Stati Uniti orientali | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   Stati Uniti occidentali | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   Stati Uniti centro-meridionali | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   Stati Uniti centrali | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   Stati Uniti orientali 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   Giappone orientale | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   Giappone occidentale | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   Brasile meridionale | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   Australia orientale | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   Australia sudorientale | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   Canada centrale | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   Canada orientale | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   Stati Uniti centro-occidentali | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   Stati Uniti occidentali 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   Regno Unito occidentale | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   Regno Unito meridionale | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="sample-nsg-configuration"></a>Configurazione NSG di esempio
In questa sezione vengono illustrati i passaggi per configurare regole NSG in modo che la replica di Site Recovery possa funzionare in una macchina virtuale. Se si usano regole NSG per controllare la connettività in uscita, usare le regole "Allow HTTPS outbound" (Consenti HTTPS in uscita) per tutti gli intervalli IP richiesti.

>[!Note]
> Per creare automaticamente le regole NSG richieste nel gruppo di sicurezza di rete, è possibile [scaricare e usare questo script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

Ad esempio, se la posizione di origine della macchina virtuale è "Stati Uniti orientali" e la posizione di destinazione della replica è "Stati Uniti centrali", seguire i passaggi descritti nelle due sezioni successive.

>[!IMPORTANT]
> * È consigliabile creare le regole NSG richieste in un gruppo di sicurezza di rete di test e verificare che non siano presenti problemi prima di creare le regole in un gruppo di sicurezza di rete di produzione.
> * Per creare il numero di regole NSG richiesto, verificare che la sottoscrizione sia consentita. Contattare il supporto tecnico per aumentare il limite delle regole NSG nella sottoscrizione. 

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>Regole NSG nel gruppo di sicurezza di rete degli Stati Uniti orientali

* Creare regole che corrispondano agli [intervalli IP degli Stati Uniti orientali](https://www.microsoft.com/download/confirmation.aspx?id=41653). Queste regole sono richieste in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.

* Creare regole per tutti gli intervalli IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.

* Creare regole che corrispondano alla posizione di destinazione:

   **Posizione** | **IP del servizio Site Recovery** |  **IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>Regole NSG nel gruppo di sicurezza di rete degli Stati Uniti centrali

Queste regole sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:

* Regole che corrispondono agli [intervalli IP degli Stati Uniti centrali](https://www.microsoft.com/download/confirmation.aspx?id=41653). Queste regole sono richieste in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.

* Regole per tutti gli intervalli IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) di Office 365.

* Regole che corrispondono alla posizione di origine:

   **Posizione** | **IP del servizio Site Recovery** |  **IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti orientali | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>Linee guida per la configurazione di ExpressRoute/VPN esistente da Azure in locale

Se si dispone di una connessione VPN o ExpressRoute tra il locale e la posizione di origine in Azure, seguire le linee guida in questa sezione.

### <a name="forced-tunneling-configuration"></a>Configurazione del tunneling forzato

Secondo una diffusa configurazione personalizzata si definisce una route predefinita (0.0.0.0/0) che forza il traffico Internet in uscita a fluire attraverso la posizione locale. Ciò non è consigliabile. Il traffico della replica e la comunicazione del servizio Site Recovery non devono allontanarsi dal limite di Azure. La soluzione consiste nell'aggiungere route definite dall'utente (UDR) per [questi intervalli IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) in modo che il traffico della replica non finisca locale.

### <a name="connectivity-between-the-target-and-on-premises-location"></a>Connettività tra la posizione di destinazione e quella locale

Seguire queste linee guida per le connessioni tra la posizione di destinazione e la posizione locale:
- Se l'applicazione deve connettersi alle macchine locali o se sono presenti client che si connettono all'applicazione dal locale tramite VPN/ExpressRoute, verificare di disporre di almeno una [connessione da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) tra l'area di Azure di destinazione e il data center locale.

- Se si prevede molto traffico tra l'area di Azure di destinazione e il data center locale, è necessario creare un'altra [connessione ExpressRoute](../expressroute/expressroute-introduction.md) tra l'area di Azure di destinazione e il data center locale.

- Se si desidera mantenere gli IP per le macchine virtuali dopo il failover, mantenere la connessione da sito a sito/ExpressRoute dell'area di destinazione in uno stato di disconnessione. Ciò garantisce che non si generi conflitto di intervalli tra gli intervalli IP dell'area di origine e gli intervalli IP dell'area di destinazione.

### <a name="best-practices-for-expressroute-configuration"></a>Procedure consigliate per la configurazione di ExpressRoute
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


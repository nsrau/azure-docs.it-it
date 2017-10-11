---
title: Pianificare la rete per la replica VMware in Azure con Site Recovery | Microsoft Docs
description: Questo articolo illustra la pianificazione di rete necessaria per la replica di macchine virtuali di Azure con Azure Site Recovery
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
ms.date: 08/01/2017
ms.author: sujayt
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>Passaggio 3: Pianificare la rete per la replica di macchine virtuali di Azure

Dopo aver verificato i [requisiti di distribuzione](azure-to-azure-walkthrough-prerequisites.md), questo articolo illustra le considerazioni sulla rete necessarie per la replica e il ripristino di macchine virtuali (VM) di Azure da un'area di Azure a un'altra con il servizio Azure Site Recovery. 

- Dopo aver letto l'articolo si dovrebbe comprendere come configurare l'accesso in uscita per le macchine virtuali di Azure da replicare e come connettersi a tali macchine virtuali dal sito locale.
- È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
> La replica di macchine virtuali di Azure con il servizio Site Recovery è attualmente disponibile in anteprima.



## <a name="network-overview"></a>Panoramica della rete

Normalmente, le macchine virtuali di Azure si trovano in una rete/subnet virtuale di Azure ed è disponibile una connessione dal sito locale ad Azure tramite Azure ExpressRoute o una connessione VPN.

Le reti sono in genere protette tramite firewall e/o gruppi di sicurezza rete. I firewall possono usare elenchi basati su URL o su IP per controllare la connettività di rete. I gruppi di sicurezza di rete fanno uso di regole basate su intervalli IP. 


Per un corretto funzionamento di Site Recovery, è necessario apportare alcune modifiche alla connettività di rete in uscita, dalle macchine virtuali che si vuole replicare. Site Recovery non supporta l'uso di un proxy di autenticazione per controllare la connettività di rete. Se si usa un proxy di autenticazione, non è possibile abilitare la replica. 


Il diagramma seguente illustra un ambiente tipico per un'applicazione in esecuzione in macchine virtuali di Azure.

![customer-environment](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Ambiente di macchine virtuali di Azure**

È anche possibile configurare una connessione ad Azure dal sito locale, usando Azure ExpressRoute o una connessione VPN. 

![customer-environment](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**Connessione locale ad Azure**



## <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, assicurarsi di consentire gli URL seguenti usati da Site Recovery

**URL** | **Dettagli**  
--- | ---
*.blob.core.windows.net | Consente la scrittura dei dati dalla macchina virtuale nell'account di archiviazione della cache nell'area di origine.
login.microsoftonline.com | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Consente la comunicazione con il servizio Site Recovery dalla macchina virtuale.
*.servicebus.windows.net | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Connettività in uscita per gli intervalli di indirizzi IP

- Per creare automaticamente le regole necessarie nel gruppo di sicurezza di rete, è possibile scaricare ed eseguire [questo script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).
- Prima di creare le regole in un gruppo di sicurezza di rete di produzione, è consigliabile creare le regole del gruppo di sicurezza di rete necessarie in un NSG di test e verificare che non siano presenti problemi.
- Per creare il numero di regole NSG richiesto, verificare che la sottoscrizione sia consentita. Contattare il supporto tecnico per aumentare il limite delle regole NSG nella sottoscrizione.
Se si usa qualsiasi proxy firewall basato su IP o le regole NSG per controllare la connettività in uscita, è necessario che i seguenti intervalli IP siano consentiti, a seconda dei percorsi di origine e destinazione delle macchine virtuali:

    - Tutti gli intervalli di indirizzi IP che corrispondono alla località di origine. Scaricare gli [intervalli](https://www.microsoft.com/download/confirmation.aspx?id=41653). L'elenco elementi consentiti è necessario per poter scrivere i dati nell'account di archiviazione della cache dalla macchina virtuale.
    - Tutti gli intervalli IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) di Office 365. Se vengono aggiunti nuovi indirizzi IP agli intervalli IP di Office 365, è necessario creare nuove regole del gruppo di sicurezza di rete.
-     Indirizzi IP dell'endpoint di servizio di Site Recovery, [disponibili in un file XML](https://aka.ms/site-recovery-public-ips), che variano a seconda della località di destinazione: 

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

## <a name="example-nsg-configuration"></a>Esempio di configurazione del gruppo di sicurezza di rete

Questa sezione illustra come configurare le regole del gruppo di sicurezza di rete, in modo che le repliche funzionino per una macchina virtuale. Se si usano regole del gruppo di sicurezza di rete per controllare la connettività in uscita, usare regole che consentono HTTPS in uscita per tutti gli intervalli IP necessari.

In questo esempio la località di origine della macchina virtuale è Stati Uniti orientali. La località di destinazione della replica è Stati Uniti centrali.


### <a name="example"></a>Esempio

#### <a name="east-us"></a>Stati Uniti orientali

1. Creare regole che corrispondano agli [intervalli IP degli Stati Uniti orientali](https://www.microsoft.com/download/confirmation.aspx?id=41653). Queste regole sono richieste in modo che i dati possano essere scritti nell'account di archiviazione della cache dalla macchina virtuale.
2. Creare regole per tutti gli intervalli IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
3. Creare regole che corrispondano alla posizione di destinazione:

   **Posizione** | **IP del servizio Site Recovery** |  **IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>Stati Uniti centrali

Queste regole sono necessarie per abilitare la replica dall'area di destinazione all'area di origine dopo il failover.

1. Creare regole che corrispondono agli [intervalli IP degli Stati Uniti centrali](https://www.microsoft.com/download/confirmation.aspx?id=41653).
2. Creare regole per tutti gli intervalli IP che corrispondono agli [endpoint di autenticazione e identità IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
3. Creare regole che corrispondono alla località di origine:

   **Posizione** | **IP del servizio Site Recovery** |  **IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti orientali | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>Connessione locale esistente

Se è presente una connessione VPN o ExpressRoute tra il sito locale e la località di origine in Azure, seguire queste linee guida:

**Configurazione** | **Dettagli**
--- | ---
**Tunneling forzato** | In genere, una route predefinita (0.0.0.0/0) forza il flusso del traffico Internet in uscita attraverso il percorso locale, ma non è una scelta consigliata. Il traffico di replica e le comunicazioni di Site Recovery devono rimanere all'interno di Azure.<br/><br/> La soluzione consiste nell'aggiungere route definite dall'utente per [questi intervalli IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges), in modo che il traffico non vada in locale.
**Connettività** | Se le app devono connettersi a computer locali oppure i client locali si connettono all'app locale tramite VPN o ExpressRoute, accertarsi di avere almeno una [connessione da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) tra l'area di Azure di destinazione e il sito locale.<br/><br/> Se i volumi di traffico tra l'area di Azure di destinazione e il sito locale sono elevati, creare un'altra [connessione ExpressRoute](../expressroute/expressroute-introduction.md) tra l'area di destinazione e il sito locale.<br/><br/> Se si desidera mantenere gli IP per le macchine virtuali dopo il failover, tenere la connessione da sito a sito o ExpressRoute dell'area di destinazione in uno stato di disconnessione. In questo modo è possibile evitare conflitti tra gli intervalli di indirizzi IP di origine e di destinazione.
**ExpressRoute** | Creare un circuito ExpressRoute nelle aree di origine e di destinazione.<br/><br/> Creare una connessione tra la rete di origine e il circuito ExpressRoute e tra la rete di destinazione e il circuito.<br/><br/> È consigliabile usare diversi intervalli IP nelle aree di origine e di destinazione. Il circuito non potrà connettersi a più di una rete di Azure con gli stessi intervalli IP nello stesso momento.<br/><br/> È possibile creare reti virtuali con gli stessi intervalli IP in entrambe le aree e quindi creare circuiti ExpressRoute in entrambe le aree. Per il failover, disconnettere il circuito dalla rete virtuale di origine e connetterlo alla rete virtuale di destinazione.<br/><br/> Se l'area primaria è completamente inattiva, l'operazione di disconnessione può avere esito negativo. In tal caso la rete virtuale di destinazione non avrà connettività a ExpressRoute.
**ExpressRoute Premium** | È possibile creare circuiti nella stessa area geopolitica.<br/><br/> Per creare circuiti in aree geopolitiche diverse, è necessario Azure ExpressRoute Premium.<br/><br/> Con la versione Premium, il costo è incrementale. Per coloro che la usano già non sono previsti costi aggiuntivi.<br/><br/> Altre informazioni sulle [località](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e i [prezzi](https://azure.microsoft.com/pricing/details/expressroute/).



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 4: Creare un insieme di credenziali](azure-to-azure-walkthrough-vault.md).


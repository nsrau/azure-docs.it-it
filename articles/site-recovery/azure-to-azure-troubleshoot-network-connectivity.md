---
title: Risolvere i problemi di connettività per il ripristino di emergenza da Azure ad Azure con Azure Site Recovery
description: Risolvere gli errori e i problemi relativi alla replica di macchine virtuali di Azure per il ripristino di emergenza
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 4995a4086c471a06fe859febfd2d1af7fbb22a76
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622444"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Risolvere i problemi di connettività di rete delle macchine virtuali da Azure ad Azure

Questo articolo descrive i problemi comuni relativi alla connettività di rete quando si esegue la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra. Per altre informazioni sui requisiti di rete, vedere [requisiti di connettività per la replica di macchine virtuali di Azure](azure-to-azure-about-networking.md).

Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi.

**URL** | **Dettagli**  
--- | ---
*.blob.core.windows.net | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale. Se si conoscono tutti gli account di archiviazione della cache per le macchine virtuali, è possibile consentire l'elenco degli URL specifici dell'account di archiviazione, ad esempio cache1.blob.core.windows.net e cache2.blob.core.windows.net, anziché *. blob.core.windows.net
login.microsoftonline.com | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale. È possibile usare il ' Site Recovery IP ' corrispondente se il proxy del firewall supporta gli IP.
*.servicebus.windows.net | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale. Se il proxy del firewall supporta gli IP, è possibile usare il ' Site Recovery IP di monitoraggio ' corrispondente.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connettività in uscita per gli intervalli IP o gli URL di Site Recovery (codice errore 151037 o 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: non è stato possibile registrare la macchina virtuale di Azure con Site Recovery (151195) </br>
- **Causa possibile** </br>
  - Non è possibile stabilire la connessione per Site Recovery endpoint a causa di un errore di risoluzione DNS.
  - Questo problema si verifica più spesso durante la riprotezione dopo il failover della macchina virtuale, quando il server DNS non è raggiungibile dall'area di ripristino di emergenza.

- **Risoluzione**
   - Se si usa il DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area di ripristino di emergenza. Per verificare se si usa un DNS personalizzato, passare alla macchina virtuale > Rete di ripristino di emergenza > Server DNS. Provare ad accedere al server DNS dalla macchina virtuale. Se non è accessibile, renderlo accessibile eseguendo il failover del server DNS o creando la riga di sito tra la rete di ripristino di emergenza e il DNS.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: La configurazione di Site Recovery non è riuscita (151196)

> [!NOTE]
> Se le macchine virtuali sono dietro il servizio di bilanciamento del carico interno **standard** , non avrà accesso agli indirizzi IP O365 (ovvero login.microsoftonline.com) per impostazione predefinita. Impostarlo sul tipo di servizio di bilanciamento del carico interno di **base** o creare un accesso in uscita come indicato nell' [articolo](https://aka.ms/lboutboundrulescli).

- **Causa possibile** </br>
  - Non è possibile connettersi agli endpoint IP4 di identità e autenticazione di Office 365.

- **Risoluzione**
  - Azure Site Recovery deve accedere agli intervalli IP di Office 365 per l'autenticazione.
    Se si usano regole del gruppo di sicurezza di rete di Azure o un proxy firewall per controllare la connettività di rete in uscita della macchina virtuale, assicurarsi di consentire la comunicazione con gli intervalli IP di Office 365. Creare Azure Active Directory una regola NSG [(Azure ad)](../virtual-network/security-overview.md#service-tags) basata su tag di servizio per consentire l'accesso a tutti gli indirizzi IP corrispondenti a Azure ad
      - Se vengono aggiunti nuovi indirizzi a Azure AD in futuro, è necessario creare nuove regole NSG.

### <a name="example-nsg-configuration"></a>Esempio di configurazione del gruppo di sicurezza di rete

In questo esempio viene illustrato come configurare le regole NSG per una macchina virtuale da replicare.

- Se si usano regole NSG per controllare la connettività in uscita, usare regole che consentano HTTPS in uscita per la porta 443 per tutti gli intervalli di indirizzi IP necessari.
- Nell'esempio si presuppone che il percorso di origine della macchina virtuale sia "Stati Uniti orientali" e che il percorso di destinazione sia "Stati Uniti centrali".

### <a name="nsg-rules---east-us"></a>Regole NSG - Stati Uniti orientali

1. Creare una regola di sicurezza HTTPS in uscita (443) per "Storage.EastUS" nel gruppo di sicurezza di rete, come illustrato nello screenshot seguente.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureActiveDirectory" nel gruppo di sicurezza di rete, come illustrato nello screenshot seguente.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Creare regole HTTPS in uscita (443) per gli IP di Site Recovery che corrispondono alla località di destinazione:

   **Posizione** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regole NSG - Stati Uniti centrali

Queste regole sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:

1. Creare una regola di sicurezza HTTPS in uscita (443) per "Storage.CentralUS" nel gruppo di sicurezza di rete.

2. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureActiveDirectory" nel gruppo di sicurezza di rete.

3. Creare regole HTTPS in uscita (443) per gli IP di Site Recovery che corrispondono alla località di origine:

   **Posizione** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: La configurazione di Site Recovery non è riuscita (151197)
- **Causa possibile** </br>
  - Non è possibile stabilire la connessione agli endpoint di servizio di Azure Site Recovery.

- **Risoluzione**
  - Azure Site Recovery deve accedere agli [intervalli IP di Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) a seconda dell'area. Assicurarsi che gli intervalli IP necessari siano accessibili dalla macchina virtuale.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: replica di A2A non riuscita quando il traffico di rete passa attraverso il server proxy locale (151072)
- **Causa possibile** </br>
  - Le impostazioni proxy personalizzate non sono valide e Azure Site Recovery agente del servizio Mobility non ha rilevato automaticamente le impostazioni proxy da Internet Explorer


- **Risoluzione**
  1. L'agente del servizio Mobility rileva le impostazioni proxy da Internet Explorer in Windows e da /etc/environment in Linux.
  2. Se si preferisce impostare il proxy solo per Azure Site Recovery servizio Mobility, è possibile specificare i dettagli del proxy in ProxyInfo. conf disponibile all'indirizzo:</br>
     - ``/usr/local/InMage/config/`` su ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` su ***Windows***
  3. Il file ProxyInfo.conf deve includere le impostazioni proxy nel formato INI seguente.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery agente del servizio Mobility supporta solo ***proxy non autenticati***.

### <a name="fix-the-problem"></a>Risolvere il problema
Per consentire [gli URL richiesti](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP richiesti](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), attenersi alla procedura descritta nel [documento sulle linee guida](site-recovery-azure-to-azure-networking-guidance.md)per la rete.


## <a name="next-steps"></a>Passaggi successivi
[Replicare le macchine virtuali di Azure](site-recovery-replicate-azure-to-azure.md)

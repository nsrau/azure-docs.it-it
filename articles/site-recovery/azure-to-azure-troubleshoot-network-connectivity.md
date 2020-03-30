---
title: Risolvere i problemi di connettività per il ripristino di emergenza da Azure ad Azure con Site Recovery di AzureTroubleshoot connectivity for Azure to Azure disaster recovery with Azure Site Recovery
description: Risolvere i problemi di connettività nel ripristino di emergenza della macchina virtuale di AzureTroubleshoot connectivity issues in Azure VM disaster recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292017"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Risolvere i problemi di connettività di rete da macchina virtuale azure ad AzureTroubleshoot Azure-to-Azure VM network connectivity issues

Questo articolo descrive i problemi comuni relativi alla connettività di rete quando si replicano e si ripristinano macchine virtuali di Azure da un'area a un'altra. Per altre informazioni sui requisiti di rete, vedere requisiti di connettività per la replica delle macchine virtuali di Azure.For more information about networking requirements, see [the connectivity requirements for replicating Azure VMs](azure-to-azure-about-networking.md).

Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi.

**Url** | **Dettagli**  
--- | ---
*.blob.core.windows.net | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale. Se si conoscono tutti gli account di archiviazione della cache per le macchine virtuali, è possibile elencare gli URL degli account di archiviazione specifici (ad esempio, cache1.blob.core.windows.net e cache2.blob.core.windows.net) anziché blob.core.windows.net
login.microsoftonline.com | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale. È possibile utilizzare l'IP di ripristino del sito corrispondente se il proxy del firewall supporta gli indirizzi IP.
*.servicebus.windows.net | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale. È possibile utilizzare il corrispondente 'IP di monitoraggio di Site Recovery' se il proxy del firewall supporta gli IP.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connettività in uscita per gli intervalli IP o gli URL di Site Recovery (codice errore 151037 o 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: Non è stato possibile registrare la macchina virtuale di Azure con Site Recovery (151195) </br>
- **Causa possibile** </br>
  - Impossibile stabilire la connessione agli endpoint di Site Recovery a causa di un errore di risoluzione DNS.
  - Questo problema si verifica più spesso durante la riprotezione dopo il failover della macchina virtuale, quando il server DNS non è raggiungibile dall'area di ripristino di emergenza.

- **Risoluzione**
   - Se si utilizza DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area ripristino di emergenza. Per verificare se si usa un DNS personalizzato, passare alla macchina virtuale > Rete di ripristino di emergenza > Server DNS. Provare ad accedere al server DNS dalla macchina virtuale. Se non è accessibile, renderlo accessibile eseguendo il failover del server DNS o creando la linea di sito tra la rete di ripristino di emergenza e DNS.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: La configurazione di Site Recovery non è riuscita (151196)

> [!NOTE]
> Se le macchine virtuali sono dietro il servizio di bilanciamento del carico interno **standard,** non avrebbe accesso agli indirizzi IP di O365 (vale a dire, login.microsoftonline.com) per impostazione predefinita. Impostarlo sul tipo di servizio di bilanciamento del carico interno **di base** o creare l'accesso in uscita come indicato [nell'articolo](https://aka.ms/lboutboundrulescli).

- **Causa possibile** </br>
  - Non è possibile connettersi agli endpoint IP4 di identità e autenticazione di Office 365.

- **Risoluzione**
  - Azure Site Recovery deve accedere agli intervalli IP di Office 365 per l'autenticazione.
    Se si usano regole del gruppo di sicurezza di rete di Azure o un proxy firewall per controllare la connettività di rete in uscita della macchina virtuale, assicurarsi di consentire la comunicazione con gli intervalli IP di Office 365. Creare una regola del gruppo di sicurezza di rete basato su [tag di servizio di Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags) per consentire l'accesso a tutti gli indirizzi IP corrispondenti ad Azure ADCreate an Azure Active Directory (Azure AD) service tag based NSG rule for allowing access to all IP addresses corresponding to Azure AD
      - Se in futuro vengono aggiunti nuovi indirizzi ad Azure AD, è necessario creare nuove regole del gruppo di sicurezza di rete.

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

   **Percorso** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Regole NSG - Stati Uniti centrali

Queste regole sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:

1. Creare una regola di sicurezza HTTPS in uscita (443) per "Storage.CentralUS" nel gruppo di sicurezza di rete.

2. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureActiveDirectory" nel gruppo di sicurezza di rete.

3. Creare regole HTTPS in uscita (443) per gli IP di Site Recovery che corrispondono alla località di origine:

   **Percorso** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery**
    --- | --- | ---
   Stati Uniti centrali | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: La configurazione di Site Recovery non è riuscita (151197)
- **Causa possibile** </br>
  - Non è possibile stabilire la connessione agli endpoint di servizio di Azure Site Recovery.

- **Risoluzione**
  - Azure Site Recovery deve accedere agli [intervalli IP di Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags) a seconda dell'area. Assicurarsi che gli intervalli IP necessari siano accessibili dalla macchina virtuale.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: Replica A2A non riuscita quando il traffico di rete passa attraverso il server proxy locale (151072)
- **Causa possibile** </br>
  - Le impostazioni proxy personalizzate non sono valide e l'agente del servizio per dispositivi mobili di Azure Site Recovery non ha rilevato automaticamente le impostazioni proxy da Internet Explorer


- **Risoluzione**
  1. L'agente del servizio Mobility rileva le impostazioni proxy da Internet Explorer in Windows e da /etc/environment in Linux.
  2. Se si preferisce impostare il proxy solo per il servizio per dispositivi mobili Azure Site Recovery, è possibile fornire i dettagli del proxy in ProxyInfo.conf disponibile in:If you prefer to set proxy only for Azure Site Recovery Mobility Service, you can provide the proxy details in ProxyInfo.conf located at:</br>
     - ``/usr/local/InMage/config/`` su ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``su ***Windows***
  3. Il file ProxyInfo.conf deve includere le impostazioni proxy nel formato INI seguente.</br>
                *[proxy]*</br>
                *Indirizzo :http://1.2.3.4*</br>
                *Port=567*</br>
  4. L'agente del servizio per dispositivi mobili di Azure Site Recovery supporta solo ***proxy non autenticati.***

### <a name="fix-the-problem"></a>Risolvere il problema
Per consentire [gli URL necessari](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP necessari,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)seguire i passaggi nel documento di linee guida sulla [rete.](site-recovery-azure-to-azure-networking-guidance.md)


## <a name="next-steps"></a>Passaggi successivi
[Replicare le macchine virtuali di Azure](site-recovery-replicate-azure-to-azure.md)

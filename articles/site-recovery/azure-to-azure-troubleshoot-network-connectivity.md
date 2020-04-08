---
title: Risolvere i problemi di connettività per il ripristino di emergenza da Azure ad Azure con Site Recovery di AzureTroubleshoot connectivity for Azure to Azure disaster recovery with Azure Site Recovery
description: Risolvere i problemi di connettività nel ripristino di emergenza della macchina virtuale di AzureTroubleshoot connectivity issues in Azure VM disaster recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 49d2d3d3e8829198a57aaf2feb40e89f105667bd
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804861"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Risolvere i problemi di connettività di rete da macchina virtuale azure ad AzureTroubleshoot Azure-to-Azure VM network connectivity issues

Questo articolo descrive i problemi comuni relativi alla connettività di rete quando si replicano e si ripristinano macchine virtuali di Azure (VM) da un'area a un'altra. Per altre informazioni sui requisiti di rete, vedere requisiti di connettività per la replica delle macchine virtuali di Azure.For more information about networking requirements, see [the connectivity requirements for replicating Azure VMs](azure-to-azure-about-networking.md).

Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi.

| **URL** | **Dettagli** |
| --- | --- |
| `*.blob.core.windows.net` | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale. Se si conoscono tutti gli account di archiviazione della cache per le macchine virtuali, è possibile usare un elenco consenti per gli URL degli account di archiviazione specifici. Ad `cache1.blob.core.windows.net` esempio, `cache2.blob.core.windows.net` e `*.blob.core.windows.net`anziché . |
| `login.microsoftonline.com` | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale. È possibile utilizzare _l'IP di Site Recovery_ corrispondente se il proxy del firewall supporta gli indirizzi IP. |
| `*.servicebus.windows.net` | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale. È possibile utilizzare l'IP di _monitoraggio di Site Recovery_ corrispondente se il proxy del firewall supporta gli indirizzi IP. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connettività in uscita per gli intervalli IP o gli URL di Site Recovery (codice errore 151037 o 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problema 1: Non è stato possibile registrare la macchina virtuale di Azure con Site Recovery (151195)

#### <a name="possible-cause"></a>Causa possibile

Impossibile stabilire una connessione agli endpoint di Site Recovery a causa di un errore di risoluzione DNS (Domain Name System). Questo problema è più comune durante la riprotezione quando è stato eseguito il failover della macchina virtuale, ma il server DNS non è raggiungibile dall'area di ripristino di emergenza (DR).

#### <a name="resolution"></a>Risoluzione

Se si utilizza DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area di ripristino di emergenza.

Per verificare se la macchina virtuale usa un'impostazione DNS personalizzata:To check if the VM uses a custom DNS setting:

1. Aprire **Macchine virtuali** e selezionare la macchina virtuale.
1. Passare alle **impostazioni** delle macchine virtuali e selezionare **Rete**.
1. In **Rete virtuale/subnet**selezionare il collegamento per aprire la pagina delle risorse della rete virtuale.
1. Passare a **Impostazioni** e selezionare **Server DNS**.

Provare ad accedere al server DNS dalla macchina virtuale. Se il server DNS non è accessibile, renderlo accessibile eseguendo il failover del server DNS o creando la linea di sito tra rete DR e DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="Errore COM":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: La configurazione di Site Recovery non è riuscita (151196)

> [!NOTE]
> Se le macchine virtuali sono dietro un servizio di bilanciamento del carico interno **Standard,** per `login.microsoftonline.com`impostazione predefinita, non avrebbe accesso agli indirizzi IP di Office 365, ad esempio . Cambiarlo nel tipo di servizio di bilanciamento del carico interno di base o creare l'accesso in uscita come indicato nell'articolo Configurare il [bilanciamento del carico e le regole in uscita in Load Balancer Standard usando l'interfaccia della riga di comando](/azure/load-balancer/configure-load-balancer-outbound-cli)di Azure.Either change it to **Basic** internal load balancer type or create outbound access as mentioned in the article Configure load balancing and outbound rules in Standard Load Balancer using Azure CLI.

#### <a name="possible-cause"></a>Causa possibile

Impossibile stabilire una connessione agli endpoint IP4 di autenticazione e identità di Office 365.

#### <a name="resolution"></a>Risoluzione

- Azure Site Recovery richiede l'accesso agli intervalli IP di Office 365 per l'autenticazione.
- Se si usano le regole del gruppo di sicurezza di rete di Azure/proxy del firewall per controllare la connettività di rete in uscita nella macchina virtuale, assicurarsi di consentire la comunicazione con gli intervalli IP di Office 365.If you're using Azure Network security group (NSG) rules/firewall proxy to control outbound network connectivity on the VM, ensure you allow communication to the Office 365 IP ranges. Creare una regola del gruppo di sicurezza di rete basato su [tag di servizio di Azure Active Directory (Azure AD)](/azure/virtual-network/security-overview#service-tags) che consente l'accesso a tutti gli indirizzi IP corrispondenti ad Azure AD.
- Se in futuro vengono aggiunti nuovi indirizzi ad Azure AD, è necessario creare nuove regole del gruppo di sicurezza di rete.

### <a name="example-nsg-configuration"></a>Esempio di configurazione del gruppo di sicurezza di rete

In questo esempio viene illustrato come configurare le regole NSG per una macchina virtuale da replicare.

- Se si usano le regole del gruppo di sicurezza di rete per controllare la connettività in uscita, usare Consenti regole **in uscita HTTPS** alla porta 443 per tutti gli intervalli di indirizzi IP necessari.
- Nell'esempio si presuppone che il percorso di origine della macchina virtuale sia **Stati Uniti orientali** e che il percorso di destinazione sia Stati Uniti **centrali**.

#### <a name="nsg-rules---east-us"></a>Regole NSG - Stati Uniti orientali

1. Creare una regola di sicurezza HTTPS in uscita per il gruppo di sicurezza di gruppo di sicurezza di gruppo, come illustrato nella schermata seguente. In questo esempio viene utilizzato il **tag Del servizio Destination**: _Storage.EastUS_ e **Destination port ranges**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. Creare una regola di sicurezza HTTPS in uscita per il gruppo di sicurezza di gruppo di sicurezza di gruppo, come illustrato nella schermata seguente. In questo esempio viene utilizzato il **tag del servizio Destination**: _AzureActiveDirectory_ e **Destination port ranges**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. Creare regole in uscita della porta HTTPS 443 per gli indirizzi IP di Site Recovery che corrispondono al percorso di destinazione:Create HTTPS port 443 outbound rules for the Site Recovery IPs that correspond to the target location:

   | **Posizione** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery** |
   | --- | --- | --- |
   | Stati Uniti centrali | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>Regole NSG - Stati Uniti centrali

Per questo esempio, queste regole del gruppo di sicurezza di rete sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:For this example, these NSG rules are required so that replication can be enabled from the target region to the source region post-failover:

1. Creare una regola di sicurezza HTTPS in uscita per _Storage.CentralUS_:

   - **Tag del servizio**di destinazione : _Storage.CentralUS_
   - **Intervalli di porte**di destinazione : _443_

1. Creare una regola di sicurezza HTTPS in uscita per _AzureActiveDirectory._

   - **Tag del servizio**di destinazione: _AzureActiveDirectoryDestination_ service tag : AzureActiveDirectory
   - **Intervalli di porte**di destinazione : _443_

1. Creare regole in uscita della porta HTTPS 443 per gli indirizzi IP di Site Recovery che corrispondono al percorso di origine:Create HTTPS port 443 outbound rules for the Site Recovery IPs that correspond to the source location:

   |**Posizione** | **Indirizzo IP di Site Recovery** |  **Indirizzo IP di monitoraggio di Site Recovery** |
   | --- | --- | --- |
   | Stati Uniti orientali | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: La configurazione di Site Recovery non è riuscita (151197)

#### <a name="possible-cause"></a>Causa possibile

Non è possibile stabilire una connessione agli endpoint del servizio Azure Site Recovery.A connection can't create to Azure Site Recovery service endpoints.

#### <a name="resolution"></a>Risoluzione

Azure Site Recovery deve accedere agli [intervalli IP di Site Recovery](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) a seconda dell'area. Assicurarsi che gli intervalli IP necessari siano accessibili dalla macchina virtuale.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: Replica da Azure ad Azure non riuscita quando il traffico di rete passa attraverso il server proxy locale (151072)Issue 4: Azure-to-Azure replication failed when the network traffic goes through on-premises proxy server (151072)

#### <a name="possible-cause"></a>Causa possibile

Le impostazioni proxy personalizzate non sono valide e l'agente del servizio per dispositivi mobili di Azure Site Recovery non ha rilevato automaticamente le impostazioni proxy da Internet Explorer (IE).

#### <a name="resolution"></a>Risoluzione

1. L'agente del servizio Mobility rileva le impostazioni `/etc/environment` proxy da Internet Explorer in Windows e in Linux.
1. Se si preferisce impostare il proxy solo per il servizio Azure Site Recovery Mobility, è possibile fornire i dettagli del proxy in ProxyInfo.conf disponibile in:If you prefer to set proxy only for Azure Site Recovery Mobility service, you can provide the proxy details in _ProxyInfo.conf_ located at:

   - **Linux**:`/usr/local/InMage/config/`
   - **Finestre di windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. Il file _ProxyInfo.conf_ deve avere le impostazioni proxy nel seguente formato _INI:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

1. L'agente del servizio Per dispositivi mobili di Azure Site RECOVERY supporta solo **proxy non autenticati.**

### <a name="fix-the-problem"></a>Risolvere il problema

Per consentire [gli URL necessari](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP necessari,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)seguire i passaggi nel documento di linee guida sulla [rete.](site-recovery-azure-to-azure-networking-guidance.md)

## <a name="next-steps"></a>Passaggi successivi

[Replicare le macchine virtuali di Azure](site-recovery-replicate-azure-to-azure.md)

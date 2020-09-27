---
title: Risolvere i problemi di connettività per il ripristino di emergenza da Azure ad Azure con Azure Site Recovery
description: Risolvere i problemi di connettività nel ripristino di emergenza delle macchine virtuali di Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 59bbca9461ff174ebe2451a6c01d84dee404cf56
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398307"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Risolvere i problemi di connettività di rete delle macchine virtuali da Azure ad Azure

Questo articolo descrive i problemi comuni relativi alla connettività di rete quando si esegue la replica e il ripristino di macchine virtuali (VM) di Azure da un'area a un'altra. Per altre informazioni sui requisiti di rete, vedere [requisiti di connettività per la replica di macchine virtuali di Azure](azure-to-azure-about-networking.md).

Per il funzionamento della replica di Site Recovery, è necessaria la connettività in uscita dalla VM a intervalli IP o URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi.

| **Nome**                  | **Commerciale**                               | **Enti pubblici**                                 | **Descrizione** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Archiviazione                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Richiesto in modo che i dati possano essere scritti nell'account di archiviazione della cache nell'area di origine dalla macchina virtuale. Se si conoscono tutti gli account di archiviazione della cache per le macchine virtuali, è possibile usare un elenco Consenti per gli URL specifici dell'account di archiviazione. Ad esempio, `cache1.blob.core.windows.net` e `cache2.blob.core.windows.net` anziché `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Richiesto per l'autorizzazione e l'autenticazione negli URL del servizio Site Recovery. |
| Replica               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Richiesto in modo che la comunicazione del servizio di Site Recovery possa verificarsi dalla macchina virtuale. È possibile usare l' _IP Site Recovery_ corrispondente se il proxy del firewall supporta gli IP. |
| Bus di servizio               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Richiesto in modo che il monitoraggio e i dati di diagnostica di Site Recovery possano essere scritti dalla macchina virtuale. È possibile usare l' _indirizzo IP di monitoraggio Site Recovery_ corrispondente se il proxy del firewall supporta gli IP. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connettività in uscita per gli intervalli IP o gli URL di Site Recovery (codice errore 151037 o 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problema 1: Non è stato possibile registrare la macchina virtuale di Azure con Site Recovery (151195)

#### <a name="possible-cause"></a>Possibile causa

Non è possibile stabilire una connessione per Site Recovery endpoint a causa di un errore di risoluzione Domain Name System (DNS). Questo problema è più comune durante la riprotezione quando è stato eseguito il failover della macchina virtuale, ma il server DNS non è raggiungibile dall'area di ripristino di emergenza.

#### <a name="resolution"></a>Soluzione

Se si usa il DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area di ripristino di emergenza.

Per verificare se la macchina virtuale usa un'impostazione DNS personalizzata:

1. Aprire **macchine virtuali** e selezionare la macchina virtuale.
1. Passare alle **Impostazioni** della macchina virtuale e selezionare **rete**.
1. In **rete virtuale/subnet**selezionare il collegamento per aprire la pagina delle risorse della rete virtuale.
1. Passare a **Impostazioni** e selezionare **server DNS**.

Provare ad accedere al server DNS dalla macchina virtuale. Se il server DNS non è accessibile, renderlo accessibile eseguendo il failover del server DNS o creando la riga di sito tra la rete di ripristino di emergenza e il DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="Errore COM":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: La configurazione di Site Recovery non è riuscita (151196)

> [!NOTE]
> Se le macchine virtuali si trovano dietro un servizio di bilanciamento del carico interno **standard** , per impostazione predefinita non avrebbe accesso ai Microsoft 365 IP, ad esempio `login.microsoftonline.com` . Impostarlo sul tipo di servizio di bilanciamento del carico interno di **base** o creare un accesso in uscita come indicato nell'articolo [configurare le regole di bilanciamento del carico e in uscita in Load Balancer standard usando l'interfaccia della](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration)riga di comando di Azure.

#### <a name="possible-cause"></a>Possibile causa

Non è possibile stabilire una connessione per Microsoft 365 gli endpoint IP4 di autenticazione e identità.

#### <a name="resolution"></a>Soluzione

- Azure Site Recovery richiede l'accesso agli intervalli IP Microsoft 365 per l'autenticazione.
- Se si usano regole del gruppo di sicurezza di rete di Azure (NSG)/proxy firewall per controllare la connettività di rete in uscita nella macchina virtuale, assicurarsi di consentire la comunicazione con gli intervalli di indirizzi IP Microsoft 365. Creare Azure Active Directory una regola NSG [(Azure ad)](../virtual-network/security-overview.md#service-tags) basata su tag di servizio che consenta l'accesso a tutti gli indirizzi IP corrispondenti a Azure ad.
- Se vengono aggiunti nuovi indirizzi a Azure AD in futuro, è necessario creare nuove regole NSG.

### <a name="example-nsg-configuration"></a>Esempio di configurazione del gruppo di sicurezza di rete

In questo esempio viene illustrato come configurare le regole NSG per una macchina virtuale da replicare.

- Se si usano regole NSG per controllare la connettività in uscita, usare **Consenti regole in uscita HTTPS** per la porta 443 per tutti gli intervalli di indirizzi IP richiesti.
- Nell'esempio si presuppone che il percorso di origine della macchina virtuale sia **Stati Uniti orientali** e che il percorso di destinazione sia **Stati Uniti centrali**.

#### <a name="nsg-rules---east-us"></a>Regole NSG - Stati Uniti orientali

1. Creare una regola di sicurezza HTTPS in uscita per NSG, come illustrato nello screenshot seguente. Questo esempio usa il **tag del servizio di destinazione**: _storage. eastus_ e gli **intervalli di porte di destinazione**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Screenshot mostra un riquadro Aggiungi regola di sicurezza in uscita per una regola di sicurezza per l'archiviazione punto est U S.":::

1. Creare una regola di sicurezza HTTPS in uscita per NSG, come illustrato nello screenshot seguente. Questo esempio usa il **tag del servizio di destinazione**: _AzureActiveDirectory_ e gli intervalli di **porte di destinazione**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="Screenshot mostra un riquadro Aggiungi regola di sicurezza in uscita per una regola di sicurezza per Azure Active Directory.":::

1. Analogamente alle regole di sicurezza sopra riportate, creare una regola di sicurezza HTTPS in uscita (443) per "EventHub. Centralus" in NSG che corrisponda al percorso di destinazione. In questo modo è possibile accedere al monitoraggio Site Recovery.
1. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureSiteRecovery" in NSG. In questo modo è possibile accedere al servizio Site Recovery in qualsiasi area.

#### <a name="nsg-rules---central-us"></a>Regole NSG - Stati Uniti centrali

Per questo esempio, queste regole NSG sono necessarie in modo che la replica possa essere abilitata dall'area di destinazione all'area di origine dopo il failover:

1. Creare una regola di sicurezza HTTPS in uscita per _storage. centralus_:

   - **Tag del servizio di destinazione**: _storage. centralus_
   - **Intervalli di porte di destinazione**: _443_

1. Creare una regola di sicurezza HTTPS in uscita per _AzureActiveDirectory_.

   - **Tag del servizio di destinazione**: _AzureActiveDirectory_
   - **Intervalli di porte di destinazione**: _443_

1. Analogamente alle regole di sicurezza sopra riportate, creare una regola di sicurezza HTTPS in uscita (443) per "EventHub. Eastus" in NSG che corrisponde alla posizione di origine. In questo modo è possibile accedere al monitoraggio Site Recovery.
1. Creare una regola di sicurezza HTTPS in uscita (443) per "AzureSiteRecovery" in NSG. In questo modo è possibile accedere al servizio Site Recovery in qualsiasi area.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: La configurazione di Site Recovery non è riuscita (151197)

#### <a name="possible-cause"></a>Possibile causa

Non è possibile stabilire una connessione per Azure Site Recovery endpoint di servizio.

#### <a name="resolution"></a>Soluzione

Se si usa un proxy del firewall/regola del gruppo di sicurezza di rete (NSG) di Azure per controllare la connettività di rete in uscita nel computer, è necessario consentire diversi tag di servizio. [Altre informazioni](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: la replica da Azure ad Azure non è riuscita quando il traffico di rete passa attraverso il server proxy locale (151072)

#### <a name="possible-cause"></a>Possibile causa

Le impostazioni proxy personalizzate non sono valide e l'agente del servizio di Azure Site Recovery Mobility non rileva automaticamente le impostazioni proxy da Internet Explorer (IE).

#### <a name="resolution"></a>Soluzione

1. L'agente del servizio Mobility rileva le impostazioni proxy da Internet Explorer in Windows e `/etc/environment` in Linux.
1. Se si preferisce impostare il proxy solo per Azure Site Recovery servizio Mobility, è possibile specificare i dettagli del proxy in _ProxyInfo. conf_ disponibile all'indirizzo:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ deve avere le impostazioni proxy nel formato _ini_ seguente:

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery agente del servizio Mobility supporta solo **proxy non autenticati**.

### <a name="fix-the-problem"></a>Risolvere il problema

Per consentire [gli URL richiesti](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP richiesti](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), attenersi alla procedura descritta nel [documento sulle linee guida](./azure-to-azure-about-networking.md)per la rete.

## <a name="next-steps"></a>Passaggi successivi

[Replica delle macchine virtuali di Azure in un'altra area di Azure](azure-to-azure-how-to-enable-replication.md)
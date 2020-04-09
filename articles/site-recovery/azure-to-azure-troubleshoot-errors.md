---
title: Risolvere i problemi di replica della macchina virtuale di Azure in Azure Site RecoveryTroubleshoot Azure VM replication in Azure Site Recovery
description: Risolvere gli errori durante la replica delle macchine virtuali di Azure per il ripristino di emergenza.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 0882eaa8b54966c7a804cf78a3928771b238e056
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885005"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Risolvere gli errori di replica delle macchine virtuali da Azure ad AzureTroubleshoot Azure-to-Azure VM replication errors

Questo articolo descrive come risolvere gli errori comuni in Azure Site Recovery durante la replica e il ripristino di macchine virtuali di Azure da un'area all'altra. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemi di quota delle risorse di Azure (codice errore 150097)

Assicurarsi che la sottoscrizione sia abilitata per creare macchine virtuali di Azure nell'area di destinazione che si prevede di usare come area di ripristino di emergenza. La sottoscrizione richiede una quota sufficiente per creare macchine virtuali delle dimensioni necessarie. Per impostazione predefinita, Site Recovery sceglie una dimensione della macchina virtuale di destinazione uguale alla dimensione della macchina virtuale di origine. Se la dimensione corrispondente non è disponibile, Site Recovery sceglie automaticamente la dimensione disponibile più vicina.

Se non sono presenti dimensioni che supportano la configurazione della macchina virtuale di origine, viene visualizzato il messaggio seguente:If there's no size that supports the source VM configuration, the following message is displayed:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Possibili cause

- L'ID sottoscrizione non è abilitato per creare macchine virtuali nel percorso dell'area di destinazione.
- L'ID sottoscrizione non è abilitato o non dispone di una quota sufficiente per creare dimensioni di macchina virtuale specifiche nel percorso dell'area di destinazione.
- Non viene trovata alcuna dimensione di macchina virtuale di destinazione adatta corrispondente al numero di schede di interfaccia di rete (NIC) della macchina virtuale di origine (2) per l'ID sottoscrizione nel percorso dell'area di destinazione.

### <a name="fix-the-problem"></a>Risolvere il problema

Contattare il supporto per la fatturazione di [Azure](/azure/azure-portal/supportability/resource-manager-core-quotas-request) per consentire alla sottoscrizione di creare macchine virtuali delle dimensioni richieste nel percorso di destinazione. Quindi, ritentare l'operazione non riuscita.

Se il percorso di destinazione ha un vincolo di capacità, disabilitare la replica in tale posizione. Abilitare quindi la replica in un percorso diverso in cui la sottoscrizione dispone di una quota sufficiente per creare macchine virtuali delle dimensioni necessarie.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificati radice trusted (codice errore 151066)

Se non tutti i certificati radice attendibili più recenti sono presenti nella macchina virtuale, il processo per abilitare la replica per Site Recovery potrebbe non riuscire. L'autenticazione e l'autorizzazione delle chiamate al servizio Site Recovery dalla macchina virtuale hanno esito negativo senza questi certificati.

Se il processo di abilitazione della replica ha esito negativo, viene visualizzato il seguente messaggio:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Causa possibile

I certificati radice attendibili necessari per l'autorizzazione e l'autenticazione non sono presenti nella macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

#### <a name="windows"></a>Windows

Per una macchina virtuale che esegue il sistema operativo Windows, installare gli aggiornamenti di Windows più recenti in modo che tutti i certificati radice attendibili siano presenti nella macchina virtuale. Seguire il tipico processo di gestione degli aggiornamenti di Windows o degli aggiornamenti dei certificati nell'organizzazione per ottenere i certificati radice più recenti e l'elenco di revoche di certificati aggiornato nelle macchine virtuali.

- Se si lavora in un ambiente non connesso, seguire il processo di aggiornamento di Windows standard dell'organizzazione per ottenere i certificati.
- Se i certificati richiesti non sono presenti nella VM, le chiamate al servizio Site Recovery non riescono per motivi di sicurezza.

Per verificare che il problema `login.microsoftonline.com` sia stato risolto, passare a da un browser nella macchina virtuale.

Per ulteriori informazioni, vedere [Configurare radici attendibili e certificati non consentiti](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Seguire le indicazioni fornite dal distributore della versione del sistema operativo Linux per ottenere i certificati radice attendibili più recenti e l'elenco di revoche di certificati più recente nella macchina virtuale.

Poiché SUSE Linux utilizza collegamenti simbolici, o symlinks, per gestire un elenco di certificati, attenersi alla seguente procedura:

1. Accedere come utente **root.** Il simbolo`#`hash ( ) è il prompt dei comandi predefinito.

1. Per modificare la directory, eseguire questo comando:

   `cd /etc/ssl/certs`

1. Verificare se il certificato della CA radice Symantec è presente:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Se il certificato della CA principale Symantec non viene trovato, eseguire il comando seguente per scaricare il file. Verificare la presenza di eventuali errori e seguire le azioni consigliate per gli errori di rete.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Verificare se il certificato della CA radice di Baltimora è presente:

   `ls Baltimore_CyberTrust_Root.pem`

   - Se il certificato della CA radice di Baltimora non viene trovato, eseguire questo comando per scaricare il certificato:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Verificare se il certificato DigiCert_Global_Root_CA è presente:

   `ls DigiCert_Global_Root_CA.pem`

    - Se il DigiCert_Global_Root_CA non viene trovato, eseguire i comandi seguenti per scaricare il certificato:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Per aggiornare gli hash del soggetto del certificato per i certificati appena scaricati, eseguire lo script rehash:

   `c_rehash`

1. Per verificare se gli oggetti del soggetto come collegamenti simbolici sono stati creati per i certificati, eseguire questi comandi:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Creare una copia del file _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ con nome file _b204d74a.0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Creare una copia del file _Baltimore_CyberTrust_Root.pem_ con nome file _653b494a.0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Creare una copia del file _DigiCert_Global_Root_CA.pem_ con nome file _3513523f.0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Verificare che i file siano presenti:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connettività in uscita per gli intervalli IP o gli URL di Site Recovery (codice errore 151037 o 151072)

Affinché la replica di Site Recovery funzioni, è necessaria la connettività in uscita a URL specifici dalla macchina virtuale. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi. Anche se continuiamo a supportare l'accesso in uscita tramite URL, l'utilizzo di un elenco Consenti di intervalli IP non è più supportato.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problema 1: Non è stato possibile registrare la macchina virtuale di Azure con Site Recovery (151195)

#### <a name="possible-causes"></a>Possibili cause

- Impossibile stabilire una connessione agli endpoint di Site Recovery a causa di un errore di risoluzione DNS (Domain Name System).
- Questo problema è più comune durante la riprotezione quando è stato eseguito il failover della macchina virtuale, ma il server DNS non è raggiungibile dall'area di ripristino di emergenza (DR).

#### <a name="fix-the-problem"></a>Risolvere il problema

Se si utilizza DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area di ripristino di emergenza.

Per verificare se la macchina virtuale usa un'impostazione DNS personalizzata:To check if the VM uses a custom DNS setting:

1. Aprire **Macchine virtuali** e selezionare la macchina virtuale.
1. Passare alle **impostazioni** delle macchine virtuali e selezionare **Rete**.
1. In **Rete virtuale/subnet**selezionare il collegamento per aprire la pagina delle risorse della rete virtuale.
1. Passare a **Impostazioni** e selezionare **Server DNS**.

Provare ad accedere al server DNS dalla macchina virtuale. Se il server DNS non è accessibile, renderlo accessibile eseguendo il failover del server DNS o creando la linea di sito tra rete DR e DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error(":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: La configurazione di Site Recovery non è riuscita (151196)

#### <a name="possible-cause"></a>Causa possibile

Impossibile stabilire una connessione agli endpoint IP4 di autenticazione e identità di Office 365.

#### <a name="fix-the-problem"></a>Risolvere il problema

Azure Site Recovery ha richiesto l'accesso agli intervalli IP di Office 365 per l'autenticazione.
Se si usa le regole del gruppo di sicurezza di rete di Azure/proxy del firewall per controllare la connettività di rete in uscita nella macchina virtuale, assicurarsi di usare la regola del gruppo di sicurezza di rete basata su tag del [servizio Azure Active Directory (AAD)](/azure/virtual-network/security-overview#service-tags) per consentire l'accesso ad AAD. Non sono più supportate le regole del gruppo di sicurezza di rete basate su indirizzi IP.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: La configurazione di Site Recovery non è riuscita (151197)

#### <a name="possible-cause"></a>Causa possibile

Non è possibile stabilire una connessione agli endpoint del servizio Azure Site Recovery.A connection can't create to Azure Site Recovery service endpoints.

#### <a name="fix-the-problem"></a>Risolvere il problema

Se si usano le regole del gruppo di sicurezza di rete di Azure/proxy del firewall per controllare la connettività di rete in uscita nella macchina virtuale, assicurarsi di usare i tag del servizio. Non è più supportato l'utilizzo di un elenco Consenti di indirizzi IP tramite gruppi di sicurezza di rete per Azure Site Recovery.We no longer support using an allow list of IP addresses via NSGs for Azure Site Recovery.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problema 4: Replica da Azure ad Azure non riuscita quando il traffico di rete passa attraverso il server proxy locale (151072)Issue 4: Azure to Azure replication failed when the network traffic goes through on-premises proxy server (151072)

#### <a name="possible-cause"></a>Causa possibile

Le impostazioni proxy personalizzate non sono valide e l'agente del servizio Mobility non ha rilevato automaticamente le impostazioni proxy da Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Risolvere il problema

1. L'agente del servizio Mobility rileva le impostazioni `/etc/environment` proxy da Internet Explorer in Windows e in Linux.
1. Se si preferisce impostare il proxy solo per il servizio Mobility, è possibile fornire i dettagli del proxy in _ProxyInfo.conf_ disponibile in:

   - **Linux**:`/usr/local/InMage/config/`
   - **Finestre di windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. Il file _ProxyInfo.conf_ deve avere le impostazioni proxy nel seguente formato _INI._

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> L'agente del servizio Mobility supporta solo **proxy non autenticati.**

### <a name="more-information"></a>Ulteriori informazioni

Per specificare gli [URL necessari](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP necessari,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)seguire le istruzioni in Informazioni sulla rete in Azure per la [replica di Azure.](azure-to-azure-about-networking.md)

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco non trovato nel computer (codice errore 150039)

È necessario inizializzare un nuovo disco collegato alla VM. Se il disco non viene trovato, viene visualizzato il seguente messaggio:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Possibili cause

- Un nuovo disco dati è stato collegato alla macchina virtuale ma non è stato inizializzato.
- Il disco dati all'interno della macchina virtuale non segnala correttamente il valore del numero di unità logica (LUN) a cui il disco è stato collegato alla macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

Assicurarsi che i dischi dati siano inizializzati, quindi ripetere l'operazione.

- **Windows**: [Collegare e inizializzare un nuovo disco](/azure/virtual-machines/windows/attach-managed-disk-portal).
- **Linux**: [Inizializzare un nuovo disco dati in Linux](/azure/virtual-machines/linux/add-disk).

Se il problema persiste, contattare il supporto tecnico.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Uno o più dischi sono disponibili per la protezione (codice di errore 153039)

### <a name="possible-causes"></a>Possibili cause

- Uno o più dischi sono stati aggiunti di recente alla macchina virtuale dopo la protezione.
- Uno o più dischi sono stati inizializzati dopo la protezione della macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

Per rendere nuovamente integro lo stato di replica della macchina virtuale, è possibile scegliere di proteggere i dischi o di ignorare l'avviso.

#### <a name="to-protect-the-disks"></a>Per proteggere i dischi

1. Passare a **Elementi** > replicati_nome_ > VM**Dischi**.
1. Selezionare il disco non protetto e quindi **selezionare Abilita replica:**

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Abilitare la replica nei dischi della macchina virtuale.":::

#### <a name="to-dismiss-the-warning"></a>Per ignorare l'avviso

1. Passare a_Nome macchina virtuale_ **elementi** > replicati .
1. Selezionare l'avviso nella sezione **Panoramica** e quindi scegliere **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Eliminare l'avviso relativo al nuovo disco.":::

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Rimuovere la macchina virtuale dall'insieme di credenziali completata con le informazioni (codice di errore 150225)

Quando Site Recovery protegge la macchina virtuale, crea collegamenti nella macchina virtuale di origine. Quando si rimuove la protezione o si disabilita la replica, Site Recovery rimuove questi collegamenti come parte del processo di pulizia. Se la macchina virtuale dispone di un blocco delle risorse, il processo di pulizia viene completato con le informazioni. Le informazioni indicano che la macchina virtuale è stata rimossa dall'insieme di credenziali di Servizi di ripristino, ma che alcuni dei collegamenti non aggiornati non sono stati puliti nel computer di origine.

È possibile ignorare questo avviso se non si intende più proteggere questa macchina virtuale. Ma se è necessario proteggere questa macchina virtuale in un secondo momento, seguire i passaggi in questa sezione per pulire i collegamenti.

> [!WARNING]
> Se non si fa la pulizia:
>
> - Quando si abilita la replica tramite l'insieme di credenziali di Servizi di ripristino, la macchina virtuale non verrà elencata.
> - Se si tenta di proteggere la macchina virtuale utilizzando**Impostazioni** >  **macchina virtuale** > Ripristino di**emergenza**, l'operazione avrà esito negativo con il messaggio **La replica non può essere abilitata a causa dei collegamenti di risorse non aggiornate esistenti nella macchina virtuale**.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Site Recovery non elimina la macchina virtuale di origine né la modifica in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco dal gruppo di risorse della macchina virtuale o della macchina virtuale. Ad esempio, nell'immagine seguente, il blocco `MoveDemo` delle risorse nella macchina virtuale denominata deve essere eliminato:For example, in the following image, the resource lock on the VM named must be deleted:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Rimuovere il blocco dalla macchina virtuale.":::

1. Scaricare lo script per rimuovere una configurazione di [Site Recovery non aggiornata.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Eseguire lo script _Cleanup-stale-asr-config-Azure-VM.ps1_. Specificare **l'ID sottoscrizione**, il gruppo di risorse **VM**e il nome della **macchina virtuale** come parametri.
1. Se vengono richieste le credenziali di Azure, specificarle. Verificare quindi che lo script venga eseguito senza errori.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Impossibile abilitare la replica a causa di collegamenti di risorse non aggiornati nella macchina virtuale (codice di errore 150226)Replication can't be enabled because of stale resource links on the VM (error code 150226)

### <a name="possible-causes"></a>Possibili cause

La macchina virtuale ha una configurazione non aggiornata rispetto alla protezione di Site Recovery precedente.

Una configurazione non aggiornata può verificarsi in una macchina virtuale di Azure se è stata abilitata la replica per la macchina virtuale di Azure tramite Site Recovery e quindi:A stale configuration can occur on an Azure VM if you enabled replication for the Azure VM by using Site Recovery, and then:

- È stata disabilitata la replica, ma la macchina virtuale di origine disponeva di un blocco delle risorse.
- L'insieme di credenziali di Site Recovery è stato eliminato senza disabilitare in modo esplicito la replica nella macchina virtuale.
- È stato eliminato il gruppo di risorse contenente l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Site Recovery non elimina la macchina virtuale di origine né la modifica in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco dal gruppo di risorse della macchina virtuale o della macchina virtuale. Ad esempio, nell'immagine seguente, il blocco `MoveDemo` delle risorse nella macchina virtuale denominata deve essere eliminato:For example, in the following image, the resource lock on the VM named must be deleted:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Rimuovere il blocco dalla macchina virtuale.":::

1. Scaricare lo script per rimuovere una configurazione di [Site Recovery non aggiornata.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Eseguire lo script _Cleanup-stale-asr-config-Azure-VM.ps1_. Specificare **l'ID sottoscrizione**, il gruppo di risorse **VM**e il nome della **macchina virtuale** come parametri.
1. Se vengono richieste le credenziali di Azure, specificarle. Verificare quindi che lo script venga eseguito senza errori.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Impossibile visualizzare la macchina virtuale di Azure o il gruppo di risorse per la selezione nel processo di abilitazione della replica

### <a name="issue-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Problema 1: il gruppo di risorse e la macchina virtuale di origine si trovano in posizioni diverseIssue 1: The resource group and source virtual machine are in different locations

Site Recovery richiede attualmente che il gruppo di risorse dell'area di origine e le macchine virtuali si trovano nella stessa posizione. In caso contrario, non sarà possibile trovare la macchina virtuale o il gruppo di risorse quando si tenta di applicare la protezione.

Come soluzione alternativa, è possibile abilitare la replica dalla macchina virtuale anziché dall'insieme di credenziali dei servizi di ripristino. Passare a**Proprietà** >  **macchina virtuale** > di origine**Ripristino di emergenza** e abilitare la replica.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problema 2: Il gruppo di risorse non fa parte della sottoscrizione selezionataIssue 2: The resource group isn't part of the selected subscription

Potrebbe non essere possibile trovare il gruppo di risorse al momento della protezione se il gruppo di risorse non fa parte della sottoscrizione selezionata. Assicurarsi che il gruppo di risorse appartenga alla sottoscrizione in uso.

### <a name="issue-3-stale-configuration"></a>Problema 3: configurazione non aggiornataIssue 3: Stale configuration

È possibile che la macchina virtuale che si desidera abilitare per la replica non venga visualizzata se nella macchina virtuale di Azure è presente una configurazione di Site Recovery non aggiornata. Questa condizione può verificarsi se è stata abilitata la replica per la macchina virtuale di Azure tramite Site Recovery e quindi:This condition could occur if you enabled replication for the Azure VM by using Site Recovery, and then:

- L'insieme di credenziali di Site Recovery è stato eliminato senza disabilitare in modo esplicito la replica nella macchina virtuale.
- È stato eliminato il gruppo di risorse contenente l'insieme di credenziali di Site Recovery senza disabilitare in modo esplicito la replica nella macchina virtuale.
- È stata disabilitata la replica, ma la macchina virtuale di origine disponeva di un blocco delle risorse.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Assicurarsi di `AzureRM.Resources` aggiornare il modulo prima di utilizzare lo script indicato in questa sezione. Site Recovery non elimina la macchina virtuale di origine né la modifica in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco, se presente, dal gruppo di risorse della macchina virtuale o della macchina virtuale. Ad esempio, nell'immagine seguente, il blocco `MoveDemo` delle risorse nella macchina virtuale denominata deve essere eliminato:For example, in the following image, the resource lock on the VM named must be deleted:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Rimuovere il blocco dalla macchina virtuale.":::

1. Scaricare lo script per rimuovere una configurazione di [Site Recovery non aggiornata.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Eseguire lo script _Cleanup-stale-asr-config-Azure-VM.ps1_. Specificare **l'ID sottoscrizione**, il gruppo di risorse **VM**e il nome della **macchina virtuale** come parametri.
1. Se vengono richieste le credenziali di Azure, specificarle. Verificare quindi che lo script venga eseguito senza errori.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Impossibile selezionare una macchina virtuale per la protezione

### <a name="possible-cause"></a>Causa possibile

La macchina virtuale ha un'estensione installata in uno stato di errore o non risponde

### <a name="fix-the-problem"></a>Risolvere il problema

Passare a**Estensioni** **impostazioni** >  **macchine virtuali** > e verificare la presenza di eventuali estensioni in uno stato di errore. Disinstallare l'estensione non riuscita e quindi riprovare a proteggere la macchina virtuale.

## <a name="the-vms-provisioning-state-isnt-valid-error-code-150019"></a>Lo stato di provisioning della macchina virtuale non è valido (codice di errore 150019)The VM's provisioning state isn't valid (error code 150019)

Per abilitare la replica nella macchina virtuale, lo stato del provisioning deve essere **Succeeded**. Seguire questi passaggi per controllare lo stato del provisioning:Follow these steps to check the provisioning state:

1. Nel portale di Azure selezionare **Esplora risorse** da Tutti **i servizi**.
1. Espandere l'elenco **Sottoscrizioni** e selezionare la sottoscrizione.
1. Espandere l'elenco **Gruppi di risorse** e selezionare il gruppo di risorse della VM.
1. Espandere l'elenco **Risorse** e selezionare la macchina virtuale.
1. Controllare il campo **provisioningState** nella vista istanza sul lato destro.

### <a name="fix-the-problem"></a>Risolvere il problema

- Se **provisioningState** è **Failed**, contattare il supporto tecnico con i dettagli per la risoluzione dei problemi.
- Se **provisioningState** è **Updating**, potrebbe essere distribuita un'altra estensione. Verificare se sono presenti operazioni in corso nella macchina virtuale, attendere il completamento e quindi ritentare il processo di Site Recovery non riuscito per abilitare la replica.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Impossibile selezionare la macchina virtuale di destinazione (la scheda selezione rete non è disponibile)Unable to select target VM (network selection tab is unavailable)

### <a name="issue-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problema 1: la macchina virtuale è collegata a una rete già mappata a una rete di destinazioneIssue 1: Your VM is attached to a network that's already mapped to a target network

Se la macchina virtuale di origine fa parte di una rete virtuale e un'altra macchina virtuale della stessa rete virtuale è già mappata con una rete nel gruppo di risorse di destinazione, la casella di riepilogo a discesa di selezione della rete non è disponibile (visualizzata in grigio) per impostazione predefinita.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Elenco di selezione della rete non disponibile.":::

### <a name="issue-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Problema 2: In precedenza è stata protetta la macchina virtuale utilizzando Site Recovery e quindi è stata disabilitata la replicaIssue 2: You previously protected the VM by using Site Recovery, and then you disabled the replication

La disabilitazione della replica di una macchina virtuale non comporta l'eliminazione del mapping di rete. Il mapping deve essere eliminato dall'insieme di credenziali di Servizi di ripristino in cui la macchina virtuale è protetta. Passare **all'insieme** > di credenziali dei servizi di ripristino**Mapping di rete****dell'infrastruttura** > di Site Recovery .

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Eliminare il mapping di rete.":::

La rete di destinazione configurata durante l'installazione del ripristino di emergenza può essere modificata dopo l'installazione iniziale e dopo la protezione della macchina virtuale:The target network that was configured during the disaster recovery setup can be changed after the initial setup, and after the VM is protected:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Modificare il mapping di rete.":::

La modifica del mapping di rete influisce su tutte le macchine virtuali protette che utilizzano lo stesso mapping di rete.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Errore del servizio Copia Shadow del volume o COM (codice di errore 151025)

Quando si verifica questo errore, viene visualizzato il seguente messaggio:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Possibili cause

- Il servizio dell'applicazione di sistema COM è disabilitato.
- Il servizio Copia Shadow del volume è disabilitato.

### <a name="fix-the-problem"></a>Risolvere il problema

Impostare l'applicazione di sistema e il servizio Copia Shadow del volume su modalità di avvio automatico o manuale.

1. Aprire la console Servizi in Windows.
1. Assicurarsi che l'applicazione di sistema COM e il servizio di copia Shadow del volume non siano impostati su **Disabilitato** come **tipo di avvio**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Controllare il tipo di avvio di COM più applicazione di sistema e il servizio Copia Shadow del volume.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Dimensioni del disco gestito non supportate (codice di errore 150172)

Quando si verifica questo errore, viene visualizzato il seguente messaggio:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Causa possibile

Il disco è inferiore alla dimensione supportata di 1024 MB.

### <a name="fix-the-problem"></a>Risolvere il problema

Assicurarsi che le dimensioni del disco siano all'interno dell'intervallo di dimensioni supportate, quindi ripetere l'operazione.

## <a name="protection-wasnt-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a>La protezione non è stata abilitata perché la configurazione di GRUB include il nome del dispositivo anziché l'UUID (codice di errore 151126)

### <a name="possible-causes"></a>Possibili cause

I file di configurazione di Linux Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_o _/etc/default/grub_) `root` possono `resume` specificare i nomi effettivi dei dispositivi anziché i valori UUID (Universally Unique Identifier) per i parametri e . Site Recovery richiede UUID perché i nomi dei dispositivi possono cambiare. Al riavvio, una macchina virtuale potrebbe non venire con lo stesso nome in caso di failover, causando problemi.

Gli esempi seguenti sono costituiti da righe da file GRUB in cui vengono visualizzati i nomi dei dispositivi anziché gli UUID richiesti:

- File _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- File: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Risolvere il problema

Sostituire il nome di ogni dispositivo con l'UUID corrispondente:

1. Trovare l'UUID del dispositivo eseguendo `blkid <device name>`il comando . Ad esempio:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Sostituire il nome del dispositivo con il `root=UUID=<UUID>` `resume=UUID=<UUID>`relativo UUID nei formati e . Ad esempio, dopo la sostituzione, la riga da _/boot/grub/menu.lst_ sarà simile alla riga seguente:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Riprovare la protezione.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Abilita protezione non riuscita perché il dispositivo indicato nella configurazione GRUB non esiste (codice di errore 151124)

### <a name="possible-cause"></a>Causa possibile

I file di configurazione di GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub/grub.cfg_, _/boot/grub2/grub.cfg_o _/etc/default/grub_) potrebbero contenere i parametri `rd.lvm.lv` o `rd_LVM_LV`. Questi parametri identificano i dispositivi LVM (Logical Volume Manager) che devono essere individuati in fase di avvio. Se questi dispositivi LVM non esistono, il sistema protetto stesso non si avvierà e sarà bloccato nel processo di avvio. Lo stesso problema si verifica anche con la macchina virtuale di failover. Di seguito sono riportati alcuni esempi:

- File: _/boot/grub2/grub.cfg_ su RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- File: _/etc/default/grub_ su RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- File: _/boot/grub/menu.lst_ in RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

In ogni esempio, GRUB deve rilevare due `root` `swap` dispositivi LVM `rootvg`con i nomi e dal gruppo di volumi .

### <a name="fix-the-problem"></a>Risolvere il problema

Se il dispositivo LVM non esiste, crearlo o rimuovere i parametri corrispondenti dai file di configurazione GRUB. Quindi, riprovare ad abilitare la protezione.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Un aggiornamento del servizio per dispositivi mobili di Site Recovery completato con avvisi (codice di errore 151083)

Il servizio Per dispositivi mobili di Site Recovery dispone di molti componenti, uno dei quali è denominato driver di filtro. Il driver del filtro viene caricato nella memoria di sistema solo durante il riavvio del sistema. Ogni volta che un aggiornamento del servizio Mobility include modifiche del driver di filtro, il computer viene aggiornato, ma viene comunque visualizzato un avviso che indica che alcune correzioni richiedono il riavvio. L'avviso viene visualizzato perché le correzioni del driver di filtro possono avere effetto solo quando viene caricato il nuovo driver di filtro, operazione che si verifica solo durante un riavvio.

> [!NOTE]
> Questo è solo un avviso. La replica esistente continua a funzionare anche dopo l'aggiornamento del nuovo agente. È possibile scegliere di riavviare ogni volta che si desidera i vantaggi del nuovo driver di filtro, ma il driver di filtro precedente continua a funzionare se non si riavvia.
>
> Oltre al driver di filtro, i vantaggi di eventuali altri miglioramenti e correzioni nell'aggiornamento del servizio Mobility hanno effetto senza richiedere il riavvio.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Impossibile abilitare la protezione perché il disco gestito della replica esiste già, senza tag previsti, nel gruppo di risorse di destinazione (codice di errore 150161)

### <a name="possible-cause"></a>Causa possibile

Questo problema può verificarsi se la macchina virtuale è stata precedentemente protetta e quando la replica è stata disabilitata, il disco di replica non è stato rimosso.

### <a name="fix-the-problem"></a>Risolvere il problema

Eliminare il disco di replica identificato nel messaggio di errore e ritentare il processo di protezione non riuscito.

## <a name="next-steps"></a>Passaggi successivi

[Replicare le macchine virtuali di Azure in un'altra area di AzureReplicate Azure VMs to another Azure region](azure-to-azure-how-to-enable-replication.md)

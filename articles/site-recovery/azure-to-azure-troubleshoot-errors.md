---
title: Risolvere i problemi di replica delle macchine virtuali di Azure in Azure Site Recovery
description: Risolvere gli errori durante la replica di macchine virtuali di Azure per il ripristino di emergenza.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: b71c6b834a6217007134b3be961a0ffa103e2706
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368044"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Correggere gli errori di replica delle macchine virtuali da Azure ad Azure

Questo articolo descrive come risolvere gli errori comuni in Azure Site Recovery durante la replica e il ripristino di macchine virtuali (VM) di Azure da un'area a un'altra. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemi di quota delle risorse di Azure (codice errore 150097)

Assicurarsi che la sottoscrizione sia abilitata per creare macchine virtuali di Azure nell'area di destinazione che si prevede di usare come area di ripristino di emergenza. La sottoscrizione richiede una quota sufficiente per creare macchine virtuali con le dimensioni necessarie. Per impostazione predefinita, Site Recovery sceglie le dimensioni della macchina virtuale di destinazione uguali a quelle della VM di origine. Se la dimensione corrispondente non è disponibile, Site Recovery sceglie automaticamente le dimensioni più vicine disponibili.

Se non sono presenti dimensioni che supportano la configurazione della VM di origine, viene visualizzato il messaggio seguente:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Possibili cause

- L'ID sottoscrizione non è abilitato per la creazione di macchine virtuali nel percorso dell'area di destinazione.
- L'ID sottoscrizione non è abilitato o non dispone di una quota sufficiente per creare dimensioni specifiche per le macchine virtuali nel percorso dell'area di destinazione.
- Non sono state trovate dimensioni appropriate per la macchina virtuale di destinazione corrispondenti al numero di schede di interfaccia di rete (NIC) della VM di origine (2), per l'ID sottoscrizione nel percorso dell'area di destinazione.

### <a name="fix-the-problem"></a>Risolvere il problema

Contattare il supporto per la [fatturazione di Azure](../azure-portal/supportability/resource-manager-core-quotas-request.md) per abilitare la sottoscrizione per la creazione di macchine virtuali con le dimensioni richieste nel percorso di destinazione. Quindi, ripetere l'operazione non riuscita.

Se il percorso di destinazione ha un vincolo di capacità, disabilitare la replica in tale percorso. Quindi, abilitare la replica in un percorso diverso in cui la sottoscrizione ha una quota sufficiente per creare VM con le dimensioni necessarie.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificati radice trusted (codice errore 151066)

Se nella macchina virtuale non sono presenti tutti i certificati radice trusted più recenti, il processo di abilitazione della replica per Site Recovery potrebbe non riuscire. L'autenticazione e l'autorizzazione delle chiamate al servizio Site Recovery dalla macchina virtuale hanno esito negativo senza questi certificati.

Se il processo di abilitazione della replica non riesce, viene visualizzato il messaggio seguente:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Possibile causa

I certificati radice attendibili richiesti per l'autorizzazione e l'autenticazione non sono presenti nella macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

#### <a name="windows"></a>Windows

Per una macchina virtuale che esegue il sistema operativo Windows, installare gli ultimi aggiornamenti di Windows in modo che tutti i certificati radice trusted siano presenti nella macchina virtuale. Per ottenere i certificati radice più recenti e l'elenco di revoche di certificati aggiornato nelle VM, seguire il processo di gestione degli aggiornamenti dei certificati o di gestione di Windows.

- Se si lavora in un ambiente non connesso, seguire il processo di aggiornamento di Windows standard dell'organizzazione per ottenere i certificati.
- Se i certificati richiesti non sono presenti nella VM, le chiamate al servizio Site Recovery non riescono per motivi di sicurezza.

Per verificare che il problema sia stato risolto, passare a `login.microsoftonline.com` da un browser nella macchina virtuale.

Per altre informazioni, vedere [configurare le radici attendibili e i certificati non consentiti](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Seguire le indicazioni fornite dal distributore della versione del sistema operativo Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati più recente nella macchina virtuale.

Poiché SUSE Linux usa collegamenti simbolici, o collegamenti simbolici, per gestire un elenco di certificati, seguire questa procedura:

1. Accedere come utente **root** . Il simbolo hash ( `#` ) è il prompt dei comandi predefinito.

1. Per modificare la directory, eseguire questo comando:

   `cd /etc/ssl/certs`

1. Controllare se è presente il certificato CA radice Symantec:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Se il certificato CA radice Symantec non viene trovato, eseguire il comando seguente per scaricare il file. Verificare la presenza di eventuali errori e seguire le azioni consigliate per gli errori di rete.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Controllare se è presente il certificato CA radice Baltimore:

   `ls Baltimore_CyberTrust_Root.pem`

   - Se il certificato CA radice Baltimore non viene trovato, eseguire questo comando per scaricare il certificato:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Controllare se è presente il certificato DigiCert_Global_Root_CA:

   `ls DigiCert_Global_Root_CA.pem`

    - Se il DigiCert_Global_Root_CA non viene trovato, eseguire i comandi seguenti per scaricare il certificato:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Per aggiornare gli hash dell'oggetto del certificato per i certificati appena scaricati, eseguire lo script di rihash:

   `c_rehash`

1. Per verificare se sono stati creati gli hash dell'oggetto come collegamenti simbolici per i certificati, eseguire questi comandi:

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

1. Creare una copia del file _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem_ con il nome file _b204d74a. 0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Creare una copia del file _Baltimore_CyberTrust_Root. pem_ con il nome file _653b494a. 0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Creare una copia del file _DigiCert_Global_Root_CA. pem_ con il nome file _3513523f. 0_:

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

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>URL in uscita o intervalli IP (codice errore 151037 o 151072)

Per il funzionamento della replica Site Recovery, dalla VM è richiesta la connettività in uscita agli URL specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi. Sebbene continuiamo a supportare l'accesso in uscita tramite URL, l'uso di un elenco di indirizzi IP consentiti non è più supportato.

#### <a name="possible-causes"></a>Possibili cause

- Non è possibile stabilire una connessione per Site Recovery endpoint a causa di un errore di risoluzione Domain Name System (DNS).
- Questo problema è più comune durante la riprotezione quando è stato eseguito il failover della macchina virtuale, ma il server DNS non è raggiungibile dall'area di ripristino di emergenza.

#### <a name="fix-the-problem"></a>Risolvere il problema

Se si usa il DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area di ripristino di emergenza.

Per verificare se la macchina virtuale usa un'impostazione DNS personalizzata:

1. Aprire **macchine virtuali** e selezionare la macchina virtuale.
1. Passare alle **Impostazioni** della macchina virtuale e selezionare **rete**.
1. In **rete virtuale/subnet**selezionare il collegamento per aprire la pagina delle risorse della rete virtuale.
1. Passare a **Impostazioni** e selezionare **server DNS**.

Provare ad accedere al server DNS dalla macchina virtuale. Se il server DNS non è accessibile, renderlo accessibile eseguendo il failover del server DNS o creando la riga di sito tra la rete di ripristino di emergenza e il DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com: errore.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problema 2: La configurazione di Site Recovery non è riuscita (151196)

#### <a name="possible-cause"></a>Possibile causa

Non è possibile stabilire una connessione per Microsoft 365 gli endpoint IP4 di autenticazione e identità.

#### <a name="fix-the-problem"></a>Risolvere il problema

Azure Site Recovery l'accesso necessario agli intervalli IP Microsoft 365 per l'autenticazione.
Se si usano regole del gruppo di sicurezza di rete di Azure (NSG)/proxy firewall per controllare la connettività di rete in uscita nella macchina virtuale, assicurarsi di usare la regola NSG basata su [tag di servizio Azure Active Directory (AAD)](../virtual-network/network-security-groups-overview.md#service-tags) per consentire l'accesso ad AAD. Le regole NSG basate sull'indirizzo IP non sono più supportate.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problema 3: La configurazione di Site Recovery non è riuscita (151197)

#### <a name="possible-cause"></a>Possibile causa

Non è possibile stabilire una connessione per Azure Site Recovery endpoint di servizio.

#### <a name="fix-the-problem"></a>Risolvere il problema

Se si usano regole del gruppo di sicurezza di rete di Azure (NSG)/proxy firewall per controllare la connettività di rete in uscita nella macchina virtuale, assicurarsi di usare i tag di servizio. Non è più supportato l'uso di un elenco di indirizzi IP consentiti tramite gruppi per Azure Site Recovery.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Problema 4: la replica non riesce quando il traffico di rete usa il server proxy locale (151072)

#### <a name="possible-cause"></a>Possibile causa

Le impostazioni proxy personalizzate non sono valide e l'agente del servizio Mobility non rileva automaticamente le impostazioni proxy da Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Risolvere il problema

1. L'agente del servizio Mobility rileva le impostazioni proxy da Internet Explorer in Windows e `/etc/environment` in Linux.
1. Se si preferisce impostare il proxy solo per il servizio Mobility, è possibile specificare i dettagli del proxy in _ProxyInfo. conf_ disponibile all'indirizzo:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ deve avere le impostazioni proxy nel formato _ini_ seguente.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> L'agente del servizio Mobility supporta solo **proxy non autenticati**.

### <a name="more-information"></a>Ulteriori informazioni

Per specificare gli [URL richiesti](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP richiesti](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), seguire le istruzioni riportate in [informazioni sulla rete in Azure per la replica di Azure](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Il disco non è stato trovato nella macchina virtuale (codice errore 150039)

È necessario inizializzare un nuovo disco collegato alla VM. Se il disco non viene trovato, viene visualizzato il messaggio seguente:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Possibili cause

- Un nuovo disco dati è stato collegato alla macchina virtuale ma non è stato inizializzato.
- Il disco dati all'interno della macchina virtuale non segnala correttamente il valore del numero di unità logica (LUN) a cui il disco è stato collegato alla macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

Verificare che i dischi dati siano inizializzati, quindi ripetere l'operazione.

- **Windows**: [aggiungere e inizializzare un nuovo disco](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [inizializzare un nuovo disco dati in Linux](../virtual-machines/linux/add-disk.md).

Se il problema persiste, contattare il supporto tecnico.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Più dischi disponibili per la protezione (codice errore 153039)

### <a name="possible-causes"></a>Possibili cause

- Uno o più dischi sono stati aggiunti di recente alla macchina virtuale dopo la protezione.
- Uno o più dischi sono stati inizializzati dopo la protezione della macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

Per rendere integro lo stato della replica della macchina virtuale, è possibile scegliere di proteggere i dischi o di ignorare l'avviso.

#### <a name="to-protect-the-disks"></a>Per proteggere i dischi

1. Passare a **elementi replicati**  >  _dischi nome VM_  >  **Disks**.
1. Selezionare il disco non protetto e quindi selezionare **Abilita replica**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="com: errore.":::

#### <a name="to-dismiss-the-warning"></a>Per ignorare l'avviso

1. Passare a **elementi replicati**  >  _nome VM_.
1. Selezionare l'avviso nella sezione **Panoramica** e quindi fare clic su **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="com: errore.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>Macchina virtuale rimossa dall'insieme di credenziali completata con informazioni (codice errore 150225)

Quando Site Recovery protegge la macchina virtuale, crea collegamenti nella macchina virtuale di origine. Quando si rimuove la protezione o si disabilita la replica, Site Recovery rimuove questi collegamenti come parte del processo di pulizia. Se la macchina virtuale ha un blocco di risorsa, il processo di pulizia viene completato con le informazioni. Le informazioni indicano che la macchina virtuale è stata rimossa dall'insieme di credenziali dei servizi di ripristino, ma non è stato possibile pulire alcuni dei collegamenti non aggiornati nella macchina di origine.

È possibile ignorare questo avviso se non si intende mai proteggere di nuovo la macchina virtuale. Tuttavia, se è necessario proteggere la macchina virtuale in un secondo momento, attenersi alla procedura descritta in questa sezione per pulire i collegamenti.

> [!WARNING]
> Se non si esegue la pulizia:
>
> - Quando si Abilita la replica per mezzo dell'insieme di credenziali di servizi di ripristino, la macchina virtuale non viene elencata.
> - Se si prova a proteggere la macchina **virtuale**usando il  >  ripristino di emergenza**delle impostazioni**della macchina virtuale  >  **Disaster Recovery**, l'operazione avrà esito negativo e **non sarà possibile abilitare la replica dei messaggi a causa dei collegamenti alle risorse obsoleti esistenti nella macchina virtuale**.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Site Recovery non elimina la macchina virtuale di origine o la influisce in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco dalla macchina virtuale o dal gruppo di risorse VM. Nell'immagine seguente, ad esempio, è necessario eliminare il blocco di risorsa nella macchina virtuale denominata `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="com: errore.":::

1. Scaricare lo script per [rimuovere una configurazione di Site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Eseguire lo script _Cleanup-stale-asr-config-Azure-VM.ps1_. Specificare l' **ID sottoscrizione**, il **gruppo di risorse VM**e il **nome della macchina virtuale** come parametri.
1. Se vengono richieste le credenziali di Azure, fornire le credenziali. Quindi verificare che lo script venga eseguito senza errori.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>La replica non è stata abilitata nella macchina virtuale con risorse non aggiornate (codice errore 150226)

### <a name="possible-causes"></a>Possibili cause

La configurazione della macchina virtuale non è aggiornata rispetto alla protezione Site Recovery precedente.

Una configurazione non aggiornata può verificarsi in una macchina virtuale di Azure se è stata abilitata la replica per la macchina virtuale di Azure usando Site Recovery, quindi:

- La replica è stata disabilitata, ma la macchina virtuale di origine ha un blocco di risorsa.
- L'insieme di credenziali di Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.
- Il gruppo di risorse che contiene l'insieme di credenziali Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Site Recovery non elimina la macchina virtuale di origine o la influisce in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco dalla macchina virtuale o dal gruppo di risorse VM. Nell'immagine seguente, ad esempio, è necessario eliminare il blocco di risorsa nella macchina virtuale denominata `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="com: errore.":::

1. Scaricare lo script per [rimuovere una configurazione di Site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Eseguire lo script _Cleanup-stale-asr-config-Azure-VM.ps1_. Specificare l' **ID sottoscrizione**, il **gruppo di risorse VM**e il **nome della macchina virtuale** come parametri.
1. Se vengono richieste le credenziali di Azure, fornire le credenziali. Quindi verificare che lo script venga eseguito senza errori.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Non è possibile selezionare una macchina virtuale o un gruppo di risorse nel processo Abilita replica

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Problema 1: il gruppo di risorse e la macchina virtuale di origine si trovano in posizioni diverse

Site Recovery attualmente richiede che il gruppo di risorse dell'area di origine e le macchine virtuali si trovino nella stessa posizione. In caso affermativo, non sarà possibile trovare la macchina virtuale o il gruppo di risorse quando si tenta di applicare la protezione.

Come soluzione alternativa, è possibile abilitare la replica dalla macchina virtuale anziché dall'insieme di credenziali di servizi di ripristino. Passare a **origine VM**  >  **proprietà**  >  **ripristino di emergenza** e abilitare la replica.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problema 2: il gruppo di risorse non fa parte della sottoscrizione selezionata

Potrebbe non essere possibile trovare il gruppo di risorse al momento della protezione se il gruppo di risorse non appartiene alla sottoscrizione selezionata. Verificare che il gruppo di risorse appartenga alla sottoscrizione che si sta usando.

### <a name="issue-3-stale-configuration"></a>Problema 3: configurazione non aggiornata

È possibile che la macchina virtuale che si vuole abilitare per la replica non sia visualizzata se nella macchina virtuale di Azure è presente una configurazione Site Recovery non aggiornata. Questa condizione può verificarsi se è stata abilitata la replica per la macchina virtuale di Azure usando Site Recovery e quindi:

- L'insieme di credenziali di Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.
- Il gruppo di risorse che contiene l'insieme di credenziali Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.
- La replica è stata disabilitata, ma la macchina virtuale di origine ha un blocco di risorsa.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Assicurarsi di aggiornare il `AzureRM.Resources` modulo prima di usare lo script indicato in questa sezione. Site Recovery non elimina la macchina virtuale di origine o la influisce in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco, se presente, dal gruppo di risorse VM o VM. Nell'immagine seguente, ad esempio, è necessario eliminare il blocco di risorsa nella macchina virtuale denominata `MoveDemo` :

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="com: errore.":::

1. Scaricare lo script per [rimuovere una configurazione di Site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Eseguire lo script _Cleanup-stale-asr-config-Azure-VM.ps1_. Specificare l' **ID sottoscrizione**, il **gruppo di risorse VM**e il **nome della macchina virtuale** come parametri.
1. Se vengono richieste le credenziali di Azure, fornire le credenziali. Quindi verificare che lo script venga eseguito senza errori.

## <a name="unable-to-select-a-vm-for-protection"></a>Non è possibile selezionare una macchina virtuale per la protezione

### <a name="possible-cause"></a>Possibile causa

L'estensione della macchina virtuale è installata in uno stato non riuscito o non risponde

### <a name="fix-the-problem"></a>Risolvere il problema

Passare a **macchine virtuali**  >  **estensioni delle impostazioni**  >  **Extensions** e verificare la presenza di eventuali estensioni in stato di errore. Disinstallare le estensioni non riuscite, quindi riprovare a proteggere la macchina virtuale.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>Lo stato di provisioning della macchina virtuale non è valido (codice errore 150019)

Per abilitare la replica nella macchina virtuale, lo stato di provisioning deve essere **succeeded**. Per verificare lo stato del provisioning, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare l' **Esplora inventario risorse** da tutti i **Servizi**.
1. Espandere l'elenco **Sottoscrizioni** e selezionare la sottoscrizione.
1. Espandere l'elenco **Gruppi di risorse** e selezionare il gruppo di risorse della VM.
1. Espandere l'elenco **delle risorse** e selezionare la macchina virtuale.
1. Controllare il campo **provisioningState** nella visualizzazione dell'istanza sul lato destro.

### <a name="fix-the-problem"></a>Risolvere il problema

- Se il **ProvisioningState** **non è riuscito**, contattare il supporto tecnico con i dettagli per la risoluzione dei problemi.
- Se il **provisioningState** è in fase di **aggiornamento**, è possibile che venga distribuita un'altra estensione. Controllare se sono presenti operazioni in corso nella macchina virtuale, attenderne il completamento, quindi ripetere il processo di Site Recovery non riuscito per abilitare la replica.

## <a name="unable-to-select-target-vm"></a>Non è possibile selezionare la macchina virtuale di destinazione

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problema 1: la macchina virtuale è collegata a una rete di cui è già stato eseguito il mapping a una rete di destinazione

Durante la configurazione del ripristino di emergenza, se la VM di origine fa parte di una rete virtuale e un'altra VM della stessa rete virtuale è già mappata a una rete nel gruppo di risorse di destinazione, per impostazione predefinita la casella di riepilogo a discesa Selezione rete non è disponibile (visualizzata in grigio).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="com: errore.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Problema 2: la macchina virtuale è stata precedentemente protetta e quindi la replica è stata disabilitata

La disabilitazione della replica di una macchina virtuale non comporta l'eliminazione del mapping di rete. Il mapping deve essere eliminato dall'insieme di credenziali dei servizi di ripristino in cui la macchina virtuale è stata protetta. Selezionare l'insieme di credenziali di **servizi di ripristino** e passare a **Gestisci**  >  **Site Recovery infrastruttura**  >  **per macchine virtuali di Azure**  >  **mapping di rete**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="com: errore.":::

La rete di destinazione configurata durante l'installazione del ripristino di emergenza può essere modificata dopo la configurazione iniziale e dopo la protezione della macchina virtuale. Per **modificare il mapping di rete** , selezionare il nome di rete:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="com: errore.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ o VSS (codice errore 151025)

Quando si verifica l'errore COM+ o Servizio Copia Shadow del volume (VSS), viene visualizzato il messaggio seguente:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Possibili cause

- Il servizio applicazione di sistema COM+ è disabilitato.
- Il Servizio Copia Shadow del volume è disabilitato.

### <a name="fix-the-problem"></a>Risolvere il problema

Impostare l'applicazione di sistema COM+ e Servizio Copia Shadow del volume sulla modalità di avvio automatica o manuale.

1. Aprire la console dei servizi in Windows.
1. Verificare che l'applicazione di sistema COM+ e la Servizio Copia Shadow del volume non siano impostate su **disabled** come **tipo di avvio**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="com: errore.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Dimensioni del disco gestito non supportate (codice errore 150172)

Quando si verifica questo errore, viene visualizzato il messaggio seguente:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Possibile causa

Il disco è inferiore alle dimensioni supportate di 1024 MB.

### <a name="fix-the-problem"></a>Risolvere il problema

Verificare che le dimensioni del disco siano comprese nell'intervallo di dimensioni supportato, quindi ripetere l'operazione.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Protezione non abilitata quando GRUB usa il nome del dispositivo (codice errore 151126)

### <a name="possible-causes"></a>Possibili cause

I file di configurazione di Linux Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/GRUB2/grub.cfg_o _/etc/default/grub_) potrebbero specificare i nomi di dispositivo effettivi anziché i valori dell'identificatore univoco universale (UUID) per i `root` `resume` parametri e. Site Recovery richiede UUID perché i nomi dei dispositivi possono cambiare. Al riavvio, una macchina virtuale potrebbe non avere lo stesso nome in caso di failover, causando problemi.

Gli esempi seguenti sono righe da file GRUB in cui vengono visualizzati i nomi dei dispositivi invece degli UUID necessari:

- _/Boot/GRUB2/grub.cfg_file:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- File: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Risolvere il problema

Sostituire ogni nome di dispositivo con l'UUID corrispondente:

1. Trovare l'UUID del dispositivo eseguendo il comando `blkid <device name>` . Esempio:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Sostituire il nome del dispositivo con il relativo UUID, nei formati `root=UUID=<UUID>` e `resume=UUID=<UUID>` . Dopo la sostituzione, ad esempio, la riga da _/boot/grub/menu.lst_ sarà simile alla riga seguente:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Ripetere la protezione.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>La protezione non è riuscita perché il dispositivo GRUB non esiste (codice errore 151124)

### <a name="possible-cause"></a>Possibile causa

I file di configurazione di GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/GRUB2/grub.cfg_o _/etc/default/grub_) potrebbero contenere i parametri `rd.lvm.lv` o `rd_LVM_LV` . Questi parametri identificano i dispositivi LVM (Logical Volume Manager) che devono essere individuati in fase di avvio. Se questi dispositivi LVM non esistono, il sistema protetto non viene avviato e si blocca nel processo di avvio. Lo stesso problema verrà visualizzato anche con la macchina virtuale di failover. Di seguito sono riportati alcuni esempi:

- File: _/boot/GRUB2/grub.cfg_ in RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- File: _/etc/default/grub_ in RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- File: _/boot/grub/menu.lst_ in rhel6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

In ogni esempio, GRUB deve rilevare due dispositivi LVM con i nomi `root` e `swap` dal gruppo di volumi `rootvg` .

### <a name="fix-the-problem"></a>Risolvere il problema

Se il dispositivo LVM non esiste, crearlo o rimuovere i parametri corrispondenti dai file di configurazione di GRUB. Quindi, riprovare ad abilitare la protezione.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Aggiornamento del servizio Mobility completato con avvisi (codice errore 151083)

Il servizio Mobility Site Recovery dispone di molti componenti, uno dei quali è denominato driver del filtro. Il driver del filtro viene caricato nella memoria di sistema solo durante il riavvio del sistema. Ogni volta che un aggiornamento del servizio Mobility include modifiche al driver del filtro, il computer viene aggiornato, ma viene comunque visualizzato un avviso che richiede il riavvio di alcune correzioni. L'avviso viene visualizzato perché le correzioni del driver del filtro possono essere applicate solo quando viene caricato il nuovo driver del filtro, che si verifica solo durante un riavvio.

> [!NOTE]
> Questo è solo un avviso. La replica esistente continuerà a funzionare anche dopo il nuovo aggiornamento dell'agente. È possibile scegliere di riavviare ogni volta che si desiderano i vantaggi del nuovo driver di filtro, ma il driver del filtro precedente continuerà a funzionare se non si riavvia.
>
> Oltre al driver del filtro, i vantaggi di qualsiasi altro miglioramento e correzione nell'aggiornamento del servizio Mobility diventano effettivi senza richiedere un riavvio.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Protezione non abilitata se esiste un disco gestito di replica

Questo errore si verifica quando il disco gestito di replica esiste già, senza i tag previsti, nel gruppo di risorse di destinazione.

### <a name="possible-cause"></a>Possibile causa

Questo problema può verificarsi se la macchina virtuale è stata precedentemente protetta e quando la replica è stata disabilitata, il disco di replica non è stato rimosso.

### <a name="fix-the-problem"></a>Risolvere il problema

Eliminare il disco di replica identificato nel messaggio di errore e ripetere il processo di protezione non riuscita.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>L'abilitazione della protezione non è riuscita perché il programma di installazione non è in grado di trovare il disco radice (codice errore 151137)

Questo errore si verifica per i computer Linux in cui il disco del sistema operativo è crittografato usando crittografia dischi di Azure (ADE). Si tratta di un problema valido solo nella versione Agent 9,35.

### <a name="possible-causes"></a>Possibili cause

Il programma di installazione non è in grado di trovare il disco radice che ospita il file system radice.

### <a name="fix-the-problem"></a>Risolvere il problema

Per risolvere il problema, attenersi alla procedura seguente:

1. Trovare i bit dell'agente nella directory _/var/lib/waagent_ nei computer RHEL e CentOS usando il comando seguente: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Output previsto:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Creare una nuova directory e modificare la directory in questa nuova directory.
3. Estrarre il file dell'agente trovato nel primo passaggio, usando il comando seguente:

    `tar -xf <Tar Ball File>`

4. Aprire il file _prereq_check_installer.js_ ed eliminare le righe seguenti. Salvare il file.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Richiamare il programma di installazione usando il comando: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Se il programma di installazione ha esito positivo, ripetere il processo di abilitazione della replica.

## <a name="next-steps"></a>Passaggi successivi

[Replica delle macchine virtuali di Azure in un'altra area di Azure](azure-to-azure-how-to-enable-replication.md)
---
title: Azure Site Recovery risoluzione dei problemi per gli errori di replica da Azure ad Azure | Microsoft Docs
description: Risolvere gli errori durante la replica di macchine virtuali di Azure per il ripristino di emergenza.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: baf7a21d04e8f9bcf86c67abde302a558dfba01c
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910392"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Risolvere gli errori di replica delle macchine virtuali da Azure ad Azure

Questo articolo descrive come risolvere gli errori comuni in Azure Site Recovery durante la replica e il ripristino di macchine virtuali (VM) di Azure da un'area a un'altra. Per altre informazioni sulle configurazioni supportate, vedere la [matrice di supporto per la replica delle VM di Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemi di quota delle risorse di Azure (codice errore 150097)

Assicurarsi che la sottoscrizione sia abilitata per creare macchine virtuali di Azure nell'area di destinazione che si prevede di usare come area di ripristino di emergenza. Assicurarsi inoltre che la sottoscrizione disponga di una quota sufficiente per creare macchine virtuali con le dimensioni necessarie. Per impostazione predefinita, Site Recovery sceglie le dimensioni della macchina virtuale di destinazione uguali a quelle della VM di origine. Se la dimensione corrispondente non è disponibile, Site Recovery sceglie automaticamente le dimensioni più vicine disponibili.

Se non sono presenti dimensioni che supportano la configurazione della VM di origine, viene visualizzato il messaggio seguente:

> "Non è stato possibile abilitare la replica per la macchina virtuale *VmName*".

### <a name="possible-causes"></a>Possibili cause

- L'ID sottoscrizione non è abilitato per la creazione di macchine virtuali nel percorso dell'area di destinazione.
- L'ID sottoscrizione non è abilitato o non dispone di una quota sufficiente per creare dimensioni specifiche per le macchine virtuali nel percorso dell'area di destinazione.
- Non sono state trovate dimensioni appropriate per la macchina virtuale di destinazione corrispondenti al numero di schede di interfaccia di rete (NIC) della VM di origine (2), per l'ID sottoscrizione nel percorso dell'area di destinazione.

### <a name="fix-the-problem"></a>Risolvere il problema

Contattare il supporto per la [fatturazione di Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) per abilitare la sottoscrizione per la creazione di macchine virtuali con le dimensioni richieste nel percorso di destinazione. Quindi, ripetere l'operazione non riuscita.

Se il percorso di destinazione ha un vincolo di capacità, disabilitare la replica. Quindi, abilitare la replica in un percorso diverso in cui la sottoscrizione ha una quota sufficiente per creare VM con le dimensioni necessarie.

## <a name="trusted-root-certificates-error-code-151066"></a>Certificati radice attendibili (codice errore 151066)

Se nella macchina virtuale non sono presenti tutti i certificati radice trusted più recenti, il processo di abilitazione della replica Site Recovery potrebbe non riuscire. L'autenticazione e l'autorizzazione delle chiamate al servizio Site Recovery dalla macchina virtuale hanno esito negativo senza questi certificati. 

Se il processo di abilitazione della replica non riesce, viene visualizzato il messaggio seguente:

> "Site Recovery configurazione non riuscita."

### <a name="possible-cause"></a>Causa possibile

I certificati radice attendibili richiesti per l'autorizzazione e l'autenticazione non sono presenti nella macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

#### <a name="windows"></a>Windows

Per una macchina virtuale che esegue il sistema operativo Windows, installare gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. Per ottenere i certificati radice più recenti e l'elenco di revoche di certificati aggiornato nelle VM, seguire il processo tipico di gestione degli aggiornamenti di Windows o di gestione dei certificati nell'organizzazione.

Se si lavora in un ambiente non connesso, seguire il processo di aggiornamento di Windows standard dell'organizzazione per ottenere i certificati. Se i certificati richiesti non sono presenti nella VM, le chiamate al servizio Site Recovery non riescono per motivi di sicurezza.

Per verificare che il problema sia stato risolto, passare a login.microsoftonline.com da un browser nella VM.

Per altre informazioni, vedere [configurare le radici attendibili e i certificati non consentiti](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Seguire le indicazioni fornite dal distributore della versione del sistema operativo Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati più recente nella macchina virtuale.

Poiché SuSE Linux utilizza collegamenti simbolici ( *o collegamenti simbolici*) per gestire un elenco di certificati, attenersi alla seguente procedura:

1. Accedere come utente ROOT.

1. Eseguire questo comando per modificare la directory:

    **n. CD/etc/ssl/certs**

1. Controllare se è presente il certificato CA radice Symantec:

    **# LS VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Se il certificato CA radice Symantec non viene trovato, eseguire il comando seguente per scaricare il file. Verificare la presenza di eventuali errori e seguire le azioni consigliate per gli errori di rete.

    **# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Controllare se è presente il certificato CA radice Baltimore:

    **# LS Baltimore_CyberTrust_Root. pem**

1. Se il certificato CA radice Baltimore non viene trovato, eseguire questo comando per scaricare il certificato:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root. pem**

1. Controllare se è presente il certificato DigiCert_Global_Root_CA:

    **# LS DigiCert_Global_Root_CA. pem**

1. Se DigiCert_Global_Root_CA non viene trovato, eseguire i comandi seguenti per scaricare il certificato:

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# OpenSSL X509-in DigiCertGlobalRootCA. CRT-informare der-OutForm pem-out DigiCert_Global_Root_CA. pem**

1. Eseguire lo script rehash per aggiornare gli hash dell'oggetto del certificato per i certificati appena scaricati:

    **# c_rehash**

1. Eseguire questi comandi per verificare se sono stati creati gli hash dell'oggetto come collegamenti simbolici per i certificati:

    - Comando:

        **# ls-l | grep Baltimore**

    - Output:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Comando:

        **# ls-l | VeriSign_Class_3_Public_Primary_Certification_Authority_G5 grep**

    - Output:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Comando:

        **# ls-l | DigiCert_Global_Root grep**

    - Output:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Creare una copia del file VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem con il nome file b204d74a. 0:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem b204d74a. 0**

1. Creare una copia del file Baltimore_CyberTrust_Root. pem con il nome file 653b494a. 0:

    **# CP Baltimore_CyberTrust_Root. pem 653b494a. 0**

1. Creare una copia del file DigiCert_Global_Root_CA. pem con il nome file 3513523f. 0:

    **# CP DigiCert_Global_Root_CA. pem 3513523f. 0**

1. Controllare se i file sono presenti:

    - Comando:

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Output

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Connettività in uscita per gli intervalli IP o gli URL di Site Recovery (codice errore 151037 o 151072)

Per il funzionamento della replica Site Recovery, è richiesta la connettività in uscita dalla macchina virtuale a URL o intervalli IP specifici. Se la macchina virtuale è protetta da un firewall o usa regole di gruppi di sicurezza di rete (NGS) per controllare la connettività in uscita, potrebbe verificarsi uno di questi problemi.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problema 1: Non è stato possibile registrare la macchina virtuale di Azure con Site Recovery (codice errore 151195)

#### <a name="possible-cause"></a>Causa possibile 

Non è possibile stabilire la connessione agli endpoint Site Recovery a causa di un errore di risoluzione DNS.

Questo problema si verifica con maggiore frequenza durante la riprotezione, quando è stato eseguito il failover della macchina virtuale ma il server DNS non è raggiungibile dall'area di ripristino di emergenza.

#### <a name="fix-the-problem"></a>Risolvere il problema

Se si usa un DNS personalizzato, assicurarsi che il server DNS sia accessibile dall'area di ripristino di emergenza. Per sapere se si dispone di un DNS personalizzato, nella macchina virtuale passare a**server DNS**di *rete* > di ripristino di emergenza.

![Elenco di server DNS personalizzati](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

Provare ad accedere al server DNS dalla macchina virtuale. Se il server non è accessibile, renderlo accessibile eseguendo il failover del server DNS o creando la riga di sito tra la rete di ripristino di emergenza e il DNS.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>Problema 2: Configurazione Site Recovery non riuscita (codice errore 151196)

#### <a name="possible-cause"></a>Causa possibile

Non è possibile stabilire la connessione agli endpoint di autenticazione di Office 365 e di identità IP4.

#### <a name="fix-the-problem"></a>Risolvere il problema

Site Recovery richiede l'accesso agli intervalli IP di Office 365 per l'autenticazione.
Se si usano regole di Azure NSG o proxy firewall per controllare la connettività di rete in uscita nella macchina virtuale, assicurarsi di consentire la comunicazione con gli intervalli IP di Office 365. Creare una regola NSG basata su un [tag del servizio Azure Active Directory (Azure ad)](../virtual-network/security-overview.md#service-tags), consentendo l'accesso a tutti gli indirizzi IP corrispondenti a Azure ad. Se vengono aggiunti nuovi indirizzi a Azure AD in futuro, è necessario creare nuove regole NSG.

> [!NOTE]
> Se le macchine virtuali si trovano dietro un servizio di bilanciamento del carico interno *standard* , per impostazione predefinita il servizio di bilanciamento del carico non ha accesso agli intervalli IP di Office 365, ovvero login.microsoftonline.com. Modificare il tipo di servizio di bilanciamento del carico interno in *base* o creare l'accesso in uscita, come descritto nell'articolo [configurare le regole di bilanciamento del carico e in uscita](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>Problema 3: Configurazione Site Recovery non riuscita (codice errore 151197)

#### <a name="possible-cause"></a>Causa possibile

Non è possibile stabilire la connessione per Site Recovery endpoint di servizio.

#### <a name="fix-the-problem"></a>Risolvere il problema

Site Recovery richiede l'accesso a [Site Recovery intervalli IP](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges), a seconda dell'area. Assicurarsi che gli intervalli di indirizzi IP richiesti siano accessibili dalla macchina virtuale.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>Problema 4: La replica da Azure ad Azure non è riuscita quando il traffico di rete passa attraverso un server proxy locale (codice errore 151072)

#### <a name="possible-cause"></a>Causa possibile

Le impostazioni proxy personalizzate non sono valide e il Site Recovery agente del servizio Mobility non ha rilevato automaticamente le impostazioni proxy da Internet Explorer.

#### <a name="fix-the-problem"></a>Risolvere il problema

L'agente del servizio Mobility rileva le impostazioni proxy da Internet Explorer in Windows e da/etc/environment in Linux.

Se si preferisce impostare il proxy solo per il servizio Mobility, è possibile specificare i dettagli del proxy nel file ProxyInfo. conf nei percorsi seguenti:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft Azure Site Recovery\Config

In ProxyInfo. conf specificare le impostazioni proxy nel formato di file di inizializzazione seguente:

> [*proxy*]

> Indirizzo = *http://1.2.3.4*

> Porta =*567*


> [!NOTE]
> L'agente del servizio Mobility Site Recovery supporta solo *proxy non autenticati*.

### <a name="more-information"></a>Altre informazioni

Per specificare gli [URL richiesti](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) o gli [intervalli IP richiesti](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), seguire le istruzioni riportate in [informazioni sulla rete in Azure per la replica di Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Disco non trovato nel computer (codice errore 150039)

È necessario inizializzare un nuovo disco collegato alla VM. Se il disco non viene trovato, viene visualizzato il messaggio seguente:

> "Azure Data disk *DiskName* *DiskURI* con numero di unità logica *lun* *LUNValue* non è stato mappato a un disco corrispondente segnalato dall'interno della macchina virtuale con lo stesso valore lun.

### <a name="possible-causes"></a>Possibili cause

- Un nuovo disco dati è stato collegato alla macchina virtuale ma non è stato inizializzato.
- Il disco dati all'interno della macchina virtuale non riporta correttamente il valore del numero di unità logica (LUN) a cui il disco è stato collegato alla macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

Verificare che i dischi dati siano inizializzati, quindi ripetere l'operazione.

- **Windows**: [Connettere e inizializzare un nuovo disco](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Inizializzare un nuovo disco dati in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Se il problema persiste, contattare il supporto tecnico.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Uno o più dischi sono disponibili per la protezione (codice errore 153039)

### <a name="possible-causes"></a>Possibili cause

- Uno o più dischi sono stati aggiunti di recente alla macchina virtuale dopo la protezione.
- Uno o più dischi sono stati inizializzati dopo la protezione della macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

Per rendere integro lo stato della replica della macchina virtuale, è possibile scegliere di proteggere i dischi o di ignorare l'avviso.

#### <a name="to-protect-the-disks"></a>Per proteggere i dischi

1. Passare a **elementi replicati** > *dischi nome* > **VM**.
1. Selezionare il disco non protetto e quindi selezionare **Abilita replica**:

    ![Abilitare la replica nei dischi delle macchine virtuali](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Per ignorare l'avviso

1. Passare a **elementi replicati** > *nome VM*.
1. Selezionare l'avviso nella sezione **Panoramica** e quindi fare clic su **OK**.

    ![Ignora nuovo-avviso del disco](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Rimuovere la macchina virtuale dall'insieme di credenziali completata con le informazioni (codice errore 150225)

Quando protegge la macchina virtuale, Site Recovery crea alcuni collegamenti nella macchina virtuale di origine. Quando si rimuove la protezione o si disabilita la replica, Site Recovery rimuove questi collegamenti come parte del processo di pulizia. Se la macchina virtuale ha un blocco di risorsa, il processo di pulizia viene completato con le informazioni. Le informazioni indicano che la macchina virtuale è stata rimossa dall'insieme di credenziali dei servizi di ripristino, ma non è stato possibile pulire alcuni dei collegamenti non aggiornati nella macchina di origine.

È possibile ignorare questo avviso se non si intende mai proteggere di nuovo la macchina virtuale. Tuttavia, se è necessario proteggere la macchina virtuale in un secondo momento, attenersi alla procedura descritta in "correggere il problema" per pulire i collegamenti.

> [!WARNING]
> Se non si esegue la pulizia:
>
> - Quando si Abilita la replica per mezzo dell'insieme di credenziali di servizi di ripristino, la macchina virtuale non viene elencata.
> - Se si prova a proteggere la macchina virtuale usando il**ripristino di emergenza** **delle impostazioni** > della **macchina** > virtuale, l'operazione avrà esito negativo con il messaggio "Impossibile abilitare la replica a causa dei collegamenti alle risorse obsoleti esistenti in la macchina virtuale ".

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Site Recovery non elimina la macchina virtuale di origine o la influisce in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco dalla macchina virtuale o dal gruppo di risorse VM. Nell'immagine seguente, ad esempio, è necessario eliminare il blocco di risorsa nella macchina virtuale denominata "MoveDemo":

    ![Rimuovi blocco dalla macchina virtuale](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Scaricare lo script per [rimuovere una configurazione di Site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Eseguire lo script, denominato Cleanup-stale-asr-config-Azure-VM. ps1. Specificare l'ID sottoscrizione, il gruppo di risorse VM e il nome della macchina virtuale come parametri.
1. Se vengono richieste le credenziali di Azure, fornire le credenziali. Quindi verificare che lo script venga eseguito senza errori.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Non è possibile abilitare la replica a causa di collegamenti a risorse non aggiornati nella VM (codice errore 150226)

### <a name="possible-cause"></a>Causa possibile

La configurazione della macchina virtuale non è aggiornata rispetto alla protezione Site Recovery precedente.

Una configurazione non aggiornata può verificarsi in una macchina virtuale di Azure se è stata abilitata la replica per la macchina virtuale di Azure usando Site Recovery, quindi:

- La replica è stata disabilitata, ma la macchina virtuale di origine ha un blocco di risorsa.
- L'insieme di credenziali di Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.
- Il gruppo di risorse che contiene l'insieme di credenziali Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Site Recovery non elimina la macchina virtuale di origine o la influisce in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco dalla macchina virtuale o dal gruppo di risorse VM. Nell'immagine seguente, ad esempio, è necessario eliminare il blocco di risorsa nella macchina virtuale denominata "MoveDemo":

    ![Rimuovi blocco dalla macchina virtuale](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Scaricare lo script per [rimuovere una configurazione di Site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Eseguire lo script, denominato Cleanup-stale-asr-config-Azure-VM. ps1. Specificare l'ID sottoscrizione, il gruppo di risorse VM e il nome della macchina virtuale come parametri.
1. Se vengono richieste le credenziali di Azure, fornire le credenziali. Quindi verificare che lo script venga eseguito senza errori.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Non è possibile visualizzare la macchina virtuale o il gruppo di risorse di Azure per la selezione nel processo di abilitazione della replica

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Causa 1: Il gruppo di risorse e la macchina virtuale di origine si trovano in posizioni diverse

Site Recovery attualmente richiede che il gruppo di risorse dell'area di origine e le macchine virtuali si trovino nella stessa posizione. In caso contrario, non sarà possibile trovare la macchina virtuale o il gruppo di risorse quando si tenta di applicare la protezione.

Come soluzione alternativa, è possibile abilitare la replica dalla macchina virtuale anziché dall'insieme di credenziali di servizi di ripristino. Passare a **origine VM** > **Proprietà** > **ripristino di emergenza** e abilitare la replica.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Causa 2: Il gruppo di risorse non fa parte della sottoscrizione selezionata

Potrebbe non essere possibile trovare il gruppo di risorse al momento della protezione se il gruppo di risorse non fa parte della sottoscrizione selezionata. Verificare che il gruppo di risorse appartenga alla sottoscrizione che si sta usando.

### <a name="cause-3-stale-configuration"></a>Causa 3: Configurazione non aggiornata

È possibile che la macchina virtuale che si vuole abilitare per la replica non sia visualizzata se nella macchina virtuale di Azure è stata lasciata una configurazione Site Recovery non aggiornata. Questa condizione può verificarsi se è stata abilitata la replica per la macchina virtuale di Azure usando Site Recovery e quindi:

- L'insieme di credenziali di Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.
- Il gruppo di risorse che contiene l'insieme di credenziali Site Recovery è stato eliminato senza disabilitare esplicitamente la replica nella macchina virtuale.
- La replica è stata disabilitata, ma la macchina virtuale di origine ha un blocco di risorsa.

### <a name="fix-the-problem"></a>Risolvere il problema

> [!NOTE]
> Assicurarsi di aggiornare il modulo "AzureRM. resources" prima di usare lo script indicato in questa sezione.  Site Recovery non elimina la macchina virtuale di origine o la influisce in alcun modo durante l'esecuzione di questi passaggi.

1. Rimuovere il blocco, se presente, dal gruppo di risorse VM o VM. Nell'immagine seguente, ad esempio, è necessario eliminare il blocco di risorsa nella macchina virtuale denominata "MoveDemo":

    ![Rimuovi blocco dalla macchina virtuale](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Scaricare lo script per [rimuovere una configurazione di Site Recovery obsoleta](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Eseguire lo script, denominato Cleanup-stale-asr-config-Azure-VM. ps1. Specificare l'ID sottoscrizione, il gruppo di risorse VM e il nome della macchina virtuale come parametri.
1. Se vengono richieste le credenziali di Azure, fornire le credenziali. Quindi verificare che lo script venga eseguito senza errori.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Non è possibile selezionare una macchina virtuale per la protezione

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Causa 1: L'estensione della macchina virtuale è installata in uno stato non riuscito o non risponde

Passare a **macchine** > virtuali**estensioni** **delle impostazioni** > e verificare la presenza di eventuali estensioni in stato di errore. Disinstallare le estensioni non riuscite, quindi riprovare a proteggere la macchina virtuale.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Causa 2: Lo stato di provisioning della macchina virtuale non è valido

Vedere la procedura di risoluzione dei problemi nello [stato di provisioning della macchina virtuale non è valida](#the-vms-provisioning-state-is-not-valid-error-code-150019), più avanti in questo articolo.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>Lo stato di provisioning della macchina virtuale non è valido (codice errore 150019)

Per abilitare la replica nella macchina virtuale, lo stato di provisioning deve essere **succeeded**. Per verificare lo stato del provisioning, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare l' **Esplora inventario risorse** da tutti i **Servizi**.
1. Espandere l'elenco **Sottoscrizioni** e selezionare la sottoscrizione.
1. Espandere l'elenco **Gruppi di risorse** e selezionare il gruppo di risorse della VM.
1. Espandere l'elenco **delle risorse** e selezionare la macchina virtuale.
1. Controllare il campo **provisioningState** nella visualizzazione istanza sul lato destro.

### <a name="fix-the-problem"></a>Risolvere il problema

- Se **provisioningState** è **Non riuscito**, contattare il supporto tecnico specificando i dettagli necessari per risolvere il problema.
- Se **provisioningState** è in corso di **aggiornamento**, è possibile che venga distribuita un'altra estensione. Controllare se sono presenti operazioni in corso nella macchina virtuale, attenderne il completamento, quindi riprovare il processo di "Abilitazione della replica" non riuscito Site Recovery.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Non è possibile selezionare la macchina virtuale di destinazione (la scheda Selezione rete non è disponibile)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Causa 1: La VM è collegata a una rete di cui è già stato eseguito il mapping a una rete di destinazione

Se la VM di origine fa parte di una rete virtuale e un'altra VM della stessa rete virtuale è già mappata a una rete nel gruppo di risorse di destinazione, la casella di riepilogo a discesa Selezione rete non è disponibile (visualizzata in grigio) per impostazione predefinita.

![Elenco di selezione di rete non disponibile](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Causa 2: La macchina virtuale è stata precedentemente protetta usando Site Recovery e quindi è stata disabilitata la replica

La disabilitazione della replica di una macchina virtuale non comporta l'eliminazione del mapping di rete. Il mapping deve essere eliminato dall'insieme di credenziali dei servizi di ripristino in cui la macchina virtuale è stata protetta. Passare a insieme di credenziali di *Servizi* > di ripristino Site Recovery**mapping di rete**dell'**infrastruttura** > .

![Eliminare il mapping di rete](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

La rete di destinazione configurata durante l'installazione del ripristino di emergenza può essere modificata dopo la configurazione iniziale, dopo la protezione della macchina virtuale:

![Modificare il mapping di rete](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Si noti che la modifica del mapping di rete interessa tutte le macchine virtuali protette che usano lo stesso mapping di rete.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ o errore del servizio Copia Shadow del volume (codice errore 151025)

Quando si verifica questo errore, viene visualizzato il messaggio seguente:

> "Impossibile installare l'estensione Site Recovery"

### <a name="possible-causes"></a>Possibili cause

- Il servizio applicazione di sistema COM+ è disabilitato.
- Il servizio Copia Shadow del volume è disabilitato.

### <a name="fix-the-problem"></a>Risolvere il problema

Impostare l'applicazione di sistema COM+ e i servizi copia shadow del volume sulla modalità di avvio automatica o manuale.

1. Aprire la console dei servizi in Windows.
1. Verificare che l'applicazione di sistema COM+ e i servizi copia shadow del volume non siano impostati su **disabilitato** come **tipo di avvio**.

    ![Controllare il tipo di avvio dell'applicazione di sistema COM+ e i servizi copia shadow del volume](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Dimensioni del disco gestito non supportate (codice errore 150172)

Quando si verifica questo errore, viene visualizzato il messaggio seguente:

> "Non è stato possibile abilitare la protezione per la macchina virtuale perché contiene *DiskName* con dimensioni *fissoDimensione*) * minore della dimensione minima supportata 1024 MB".

### <a name="possible-cause"></a>Causa possibile

Il disco è inferiore alle dimensioni supportate di 1024 MB.

### <a name="fix-the-problem"></a>Risolvere il problema

Verificare che le dimensioni del disco siano comprese nell'intervallo di dimensioni supportato, quindi ripetere l'operazione.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>La protezione non è stata abilitata perché la configurazione di GRUB include il nome del dispositivo anziché l'UUID (codice errore 151126)

### <a name="possible-cause"></a>Causa possibile

I file di configurazione GRUB di Linux (/boot/grub/menu.lst ",/boot/grub/grub.cfg,/boot/GRUB2/grub.cfg o/etc/default/grub) potrebbero specificare i nomi di dispositivo effettivi anziché i valori UUID per i parametri *root* e *Resume* . Site Recovery richiede UUID perché i nomi dei dispositivi possono cambiare. Al riavvio, una macchina virtuale potrebbe non avere lo stesso nome in caso di failover, causando problemi.

Gli esempi seguenti sono le righe dei file di GRUB in cui vengono visualizzati i nomi dei dispositivi (visualizzati in grassetto) invece degli UUID necessari:

- /Boot/GRUB2/grub.cfg file

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- File:/boot/grub/menu.lst

  > kernel/boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **resume =/dev/sda1** splash = silent crashkernel = 256M-: 128M showopts VGA = 0x314


### <a name="fix-the-problem"></a>Risolvere il problema

Sostituire ogni nome di dispositivo con l'UUID corrispondente:

1. Trovare l'UUID del dispositivo eseguendo il comando **blkid** ***nome dispositivo***. Ad esempio:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Sostituire il nome del dispositivo con il relativo UUID, in formats **root = UUID**==*UUID* e **Resume =** UUID*UUID*. Dopo la sostituzione, ad esempio, la riga da/boot/grub/menu.lst (illustrata in precedenza) avrà un aspetto simile al seguente:

    > kernel/boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433B-431B-9ca1-4dd2f6f74f6b** splash = silent crashkernel = 256M-: 128M showopts VGA = 0x314

1. Ripetere la protezione.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>L'abilitazione della protezione non è riuscita perché il dispositivo indicato nella configurazione di GRUB non esiste (codice errore 151124)

### <a name="possible-cause"></a>Causa possibile

I file di configurazione di GRUB (/boot/grub/menu.lst,/boot/grub/grub.cfg,/boot/GRUB2/grub.cfg o/etc/default/grub) potrebbero contenere i parametri *Rd.LVM.lv* o *rd_LVM_LV*. Questi parametri identificano i dispositivi LVM (Logical Volume Manager) che devono essere individuati in fase di avvio. Se questi dispositivi LVM non esistono, il sistema protetto non viene avviato e si blocca nel processo di avvio. Lo stesso problema verrà visualizzato anche con la macchina virtuale di failover. Di seguito sono riportati alcuni esempi:

- File:/boot/GRUB2/grub.cfg in RHEL7:

    > Linux16/vmlinuz-3.10.0-957.EL7.x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet LANG = en_US. UTF-8

- File:/etc/default/grub in RHEL7:

    > GRUB_CMDLINE_LINUX = "crashkernel = auto **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet"

- File:/boot/grub/menu.lst in RHEL6:

    > kernel/vmlinuz-2.6.32-754.el6.x86_64 ro root = UUID = 36dd8b45-E90D-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = PC portatable = US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

In ogni esempio, la parte in grassetto mostra che GRUB deve rilevare due dispositivi LVM con i nomi "root" e "swap" dal gruppo di volumi "rootvg".

### <a name="fix-the-problem"></a>Risolvere il problema

Se il dispositivo LVM non esiste, crearlo o rimuovere i parametri corrispondenti dai file di configurazione di GRUB. Quindi, riprovare ad abilitare la protezione.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>Aggiornamento di un servizio di Site Recovery Mobility completato con avvisi (codice errore 151083)

Il servizio Mobility Site Recovery dispone di molti componenti, uno dei quali è denominato driver del filtro. Il driver del filtro viene caricato nella memoria di sistema solo durante il riavvio del sistema. Ogni volta che un aggiornamento del servizio Mobility include modifiche al driver del filtro, il computer viene aggiornato, ma viene comunque visualizzato un avviso che richiede il riavvio di alcune correzioni. L'avviso viene visualizzato perché le correzioni del driver del filtro possono essere applicate solo quando viene caricato il nuovo driver del filtro, che si verifica solo durante un riavvio.

> [!NOTE]
> Questo è solo un avviso. La replica esistente continuerà a funzionare anche dopo il nuovo aggiornamento dell'agente. È possibile scegliere di riavviare ogni volta che si desiderano i vantaggi del nuovo driver di filtro, ma il driver del filtro precedente continuerà a funzionare se non si riavvia.
>
> Oltre al driver del filtro, i vantaggi di qualsiasi altro miglioramento e correzione nell'aggiornamento del servizio Mobility diventano effettivi senza richiedere un riavvio.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Non è stato possibile abilitare la protezione perché il disco gestito di replica esiste già, senza i tag previsti, nel gruppo di risorse di destinazione (codice errore 150161)

### <a name="possible-cause"></a>Causa possibile

Questo problema può verificarsi se la macchina virtuale è stata precedentemente protetta e quando la replica è stata disabilitata, il disco di replica non è stato pulito.

### <a name="fix-the-problem"></a>Risolvere il problema

Eliminare il disco di replica identificato nel messaggio di errore e ripetere il processo di protezione non riuscita.

## <a name="next-steps"></a>Passaggi successivi

[Replicare le macchine virtuali di Azure](site-recovery-replicate-azure-to-azure.md)

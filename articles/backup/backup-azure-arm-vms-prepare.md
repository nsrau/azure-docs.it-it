---
title: 'Backup di Azure: preparare il backup di macchine virtuali'
description: Assicurarsi che l'ambiente sia pronto per il backup di macchine virtuali in Azure.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: backup; eseguire il backup;
ms.service: backup
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: raynew
ms.openlocfilehash: 1092f5e21eab1e037c360408f17548b544a9e922
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422797"
---
# <a name="prepare-to-back-up-azure-vms"></a>Preparare il backup delle macchine virtuali di Azure

Questo articolo illustra i passaggi per preparare l'ambiente per il backup di una macchina virtuale (VM) distribuita con Azure Resource Manager. I passaggi descritti nelle procedure usano il portale di Azure. Quando si esegue il backup di una macchina virtuale, i dati di backup o i punti di ripristino vengono archiviati in un insieme di credenziali di backup di Servizi di ripristino.



Prima di proteggere una macchina virtuale distribuita con Resource Manager o eseguirne il backup, assicurarsi di rispettare questi prerequisiti:

* Creare o identificare un insieme di credenziali di servizi di ripristino *nella stessa area della macchina virtuale*.
* Selezionare uno scenario, definire i criteri di backup e specificare gli elementi da proteggere.
* Verificare l'installazione di un agente di macchine virtuali (estensione) nella macchina virtuale.
* Controllare la connettività di rete.
* Per le VM Linux, se si vuole personalizzare l'ambiente di backup per backup coerenti con l'applicazione, seguire i [passaggi per configurare gli script pre-snapshot e post-snapshot](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Se nell'ambiente esistono già queste condizioni, passare all'articolo su come [eseguire il backup delle VM](backup-azure-arm-vms.md). Se è necessario configurare o verificare uno qualsiasi di questi prerequisiti, questo articolo illustra i passaggi necessari.

## <a name="supported-operating-systems-for-backup"></a>Sistemi operativi supportati per il backup

 * **Linux**: Backup di Azure supporta [un elenco di distribuzioni approvate da Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), ad eccezione di CoreOS Linux e il sistema operativo a 32 bit. Per l'elenco dei sistemi operativi Linux che supportano il ripristino dei file, vedere [Recupero file da backup della macchina virtuale](backup-azure-restore-files-from-vm.md#for-linux-os).

    > [!NOTE]
    > È possibile usare altre distribuzioni Bring Your Own Linux, a condizione che l'agente di macchine virtuali sia disponibile nella VM e che sia presente il supporto per Python. Tuttavia, queste distribuzioni non sono supportate.
    >
 * **Windows Server**, **Windows client**: le versioni precedenti a Windows Server 2008 R2 o Windows 7 non sono supportate.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitazioni durante il backup e il ripristino di una VM
Prima di preparare l'ambiente, assicurarsi di conoscere queste limitazioni:

* Il backup di macchine virtuali con più di 16 dischi dati non è supportato.
* Il backup di VM Linux crittografate con la crittografia Linux Unified Key Setup (LUKS) non è supportato.
* Non è consigliabile eseguire il backup di VM contenenti la configurazione di volumi condivisi del cluster o file server di scalabilità orizzontale, in caso contrario, potranno verificarsi errori dei writer CSV per cui durante un'attività di snapshot è necessario il coinvolgimento di tutte le VM incluse nella configurazione cluster. Backup di Azure non supporta la coerenza tra più macchine virtuali.
* I dati di backup non includono le unità di rete montate che sono collegate a una VM.
* L'opzione **Sostituisci esistente**  in **Configurazione di ripristino** consente di sostituire i dischi esistenti nella macchina virtuale corrente con il punto di ripristino selezionato. Questa operazione può essere eseguita solo se la macchina virtuale corrente esiste. 
* L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
* Durante la configurazione del backup, verificare che le impostazioni di **Firewall e reti virtuali** per l'account di archiviazione consentano l'accesso da Tutte le reti.
* Per le reti selezionate, dopo la configurazione del firewall e delle impostazioni di rete virtuale per l'account di archiviazione, selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** come un'eccezione per abilitare il servizio Backup di Azure per accedere all'account di archiviazione di rete con restrizioni. Il ripristino a livello di elemento non è supportato per gli account di archiviazione di rete con restrizioni.
* È possibile eseguire il backup di macchine virtuali in tutte le aree pubbliche di Azure. Vedere l'[elenco di controllo](https://azure.microsoft.com/regions/#services) delle aree supportate. Se l'area desiderata non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
* Il ripristino di un controller di dominio di VM che fa parte di una configurazione con controller di dominio è supportato solo tramite PowerShell. Per altre informazioni, vedere la sezione relativa al [ripristino di un controller di dominio con più controller di dominio](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* La creazione di snapshot su dischi abilitati per l'acceleratore di scrittura non è supportata. Questa restrizione impedisce al servizio Backup di Azure di creare uno snapshot coerente con l'applicazione di tutti i dischi della macchina virtuale.
* Backup di Azure non supporta l'adattamento automatico dell'orologio per l'ora legale per il backup delle macchine virtuali di Azure. Se necessario, modificare i criteri per tenere conto dell'ora legale.
* Il ripristino delle macchine virtuali che presentano le seguenti configurazioni di rete speciali è supportato solo tramite PowerShell. Le VM create tramite il flusso di lavoro di ripristino nell'interfaccia utente non avranno queste configurazioni di rete al termine dell'operazione di ripristino. Per altre informazioni, vedere [Ripristino delle macchine virtuali con configurazioni di rete speciali](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Macchine virtuali con configurazione del servizio di bilanciamento del carico (interno ed esterno)
  * Macchine virtuali con più indirizzi IP riservati
  * Macchine virtuali con più schede di rete

  > [!NOTE]
  > Backup di Azure supporta i [dischi gestiti SDD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/), un nuovo tipo di archiviazione durevole per macchine virtuali di Microsoft Azure. Il supporto è garantito per i dischi gestiti nella [versione 2 dello stack di backup di macchine virtuali di Azure](backup-upgrade-to-vm-backup-stack-v2.md).

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Creare un insieme di credenziali dei servizi di ripristino per una VM
Un insieme di credenziali dei servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Nel menu **Hub** selezionare **Esplora** e quindi digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco delle risorse viene filtrato in base all'input. Selezionare **Insiemi di credenziali dei servizi di ripristino**.

    ![Digitazione nella casella e selezione di "Insiemi di credenziali dei servizi di ripristino" nei risultati](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
1. Nel menu di **Insiemi di credenziali dei servizi di ripristino** selezionare **Aggiungi**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Verrà visualizzato il riquadro **Insiemi di credenziali dei servizi di ripristino**, in cui viene richiesto di specificare le informazioni relative a **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Riquadro "Insiemi di credenziali dei servizi di ripristino"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
1. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome contenente da 2 a 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
1. Selezionare **Sottoscrizione** per visualizzare l'elenco delle sottoscrizioni disponibili. Se non si è certi della sottoscrizione da usare, usare quella predefinita (o suggerita). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
1. Selezionare **Gruppo di risorse** per visualizzare l'elenco dei gruppi di risorse disponibili oppure **Nuovo** per crearne uno nuovo. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
1. Selezionare **Località** per selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali *deve* trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere.

   > [!IMPORTANT]
   > Se non si è certi della località in cui si trova la VM, chiudere la finestra di dialogo per la creazione dell'insieme di credenziali e passare all'elenco delle macchine virtuali nel portale. Se si hanno macchine virtuali in più aree, è necessario creare un insieme di credenziali dei servizi di ripristino in ogni area. Creare l'insieme di credenziali nella prima località prima di passare a quella successiva. Non è necessario specificare account di archiviazione per archiviare i dati di backup. Questo aspetto viene gestito automaticamente dall'insieme di credenziali dei servizi di ripristino e dal servizio Backup di Azure.
   >
   >

1. Selezionare **Create**. La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. Al termine della creazione, l'insieme di credenziali verrà visualizzato nell'elenco degli insiemi di credenziali dei servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Ora che l'insieme di credenziali è stato creato, è possibile apprendere come impostare la replica di archiviazione.

## <a name="set-storage-replication"></a>Impostare la replica di archiviazione
L'opzione di replica di archiviazione consente di scegliere tra archiviazione con ridondanza geografica e archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si tratta del backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale.

Per modificare le impostazioni di replica di archiviazione:

1. Nel riquadro **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali.
    Quando si seleziona l'insieme di credenziali, vengono visualizzati il riquadro **Impostazioni**, con il nome dell'insieme di credenziali nella parte superiore, e il riquadro dei dettagli dell'insieme di credenziali.

   ![Scegliere l'insieme di credenziali dall'elenco degli insiemi di credenziali di backup](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

1. Nel riquadro **Impostazioni** usare il dispositivo di scorrimento verticale per passare alla sezione **Gestisci** e selezionare **Infrastruttura di backup**. Nella sezione **Generale** selezionare **Configurazione di backup**. Nel riquadro **Configurazione di backup** scegliere l'opzione di replica di archiviazione per l'insieme di credenziali. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica.

   ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Se si usa Azure come endpoint di archiviazione di backup primario, continuare a usare l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint di archiviazione di backup non primario, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione, vedere la panoramica [Replica di Archiviazione di Azure](../storage/common/storage-redundancy.md).

1. Se è stato modificato il tipo di replica di archiviazione, selezionare **Salva**.

Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare la VM all'insieme di credenziali. Per iniziare l'associazione, è necessario trovare e registrare le macchine virtuali di Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selezionare un obiettivo di backup, impostare i criteri e definire gli elementi da proteggere
Prima di registrare una macchina virtuale in un insieme di credenziali di Servizi di ripristino, eseguire il processo di individuazione per identificare tutte le nuove macchine virtuali aggiunte alla sottoscrizione. Il processo di individuazione esegue una query su Azure per ottenere l'elenco delle macchine virtuali disponibili nella sottoscrizione. Se vengono trovate nuove macchine virtuali, il portale visualizza il nome del servizio cloud e l'area geografica associata. Lo *scenario* nel portale di Azure fa riferimento a ciò che si inserisce nell'insieme di credenziali di Servizi di ripristino. I *criteri* determinano la pianificazione relativa a quando e con quale frequenza verranno acquisiti i punti di ripristino. I criteri includono anche il periodo di mantenimento dati per i punti di ripristino.

1. Se un insieme di credenziali dei servizi di ripristino è già aperto, procedere al passaggio 2. Se non è già aperto un insieme di credenziali dei servizi di ripristino, aprire il [portale di Azure](https://portal.azure.com/). Nel menu **Hub** selezionare **Altri servizi**.

   a. Nell'elenco di risorse digitare **servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

      ![Digitazione nella casella e selezione di "Insiemi di credenziali dei servizi di ripristino" nei risultati](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Se nella sottoscrizione non sono presenti insiemi di credenziali, l'elenco è vuoto.

      ![Visualizzazione dell'elenco degli insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

      Verranno visualizzati il riquadro **Impostazioni** e il dashboard dell'insieme di credenziali scelto.

      ![Riquadro Impostazioni e dashboard dell'insieme di credenziali](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
1. Nel menu del dashboard dell'insieme di credenziali selezionare **Backup**.

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Verranno visualizzati i riquadri **Backup** e **Obiettivo del backup**.

1. Nel riquadro **Obiettivo del backup** impostare **Posizione di esecuzione del carico di lavoro** su **Azure** ed **Elementi di cui eseguire il backup** su **Macchina virtuale**. Selezionare **OK**.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Questo passaggio registra l'estensione della VM nell'insieme di credenziali. Il riquadro **Obiettivo del backup** verrà chiuso e verrà visualizzato il riquadro **Criteri di backup**.

   ![Riquadri Backup e Criteri di backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
1. Nel riquadro **Criteri di backup** selezionare i criteri di backup da applicare all'insieme di credenziali.

   ![Selezionare il criterio di backup](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   I dettagli dei criteri predefiniti vengono elencati nel menu a discesa. Per creare un nuovo criterio, selezionare **Crea nuovo** dal menu a discesa. Per istruzioni sulla definizione di un criterio di backup, vedere [Definizione di un criterio di backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Selezionare **OK** per associare i criteri di backup all'insieme di credenziali.

   Il riquadro **Criteri di backup** verrà chiuso e verrà visualizzato il riquadro **Seleziona macchine virtuali**.
1. Nel riquadro **Seleziona macchine virtuali** scegliere le macchine virtuali da associare ai criteri specificati e selezionare **OK**.

   ![Riquadro "Seleziona macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Viene convalidata la macchina virtuale selezionata. Se le macchine virtuali previste non sono visibili, controllare che si trovino nella stessa area di Azure dell'insieme di credenziali di Servizi di ripristino. Se non vengono ancora visualizzate, verificare che non siano già protette con un altro insieme di credenziali. L'area in cui si trova l'insieme di credenziali di Servizi di ripristino viene visualizzata nel dashboard dell'insieme di credenziali.

1. Dopo aver definito tutte le impostazioni per l'insieme di credenziali, nel riquadro **Backup** selezionare **Abilita backup**. Questo passaggio distribuisce i criteri nell'insieme di credenziali e nelle VM, ma non crea il punto di ripristino iniziale per la macchina virtuale.

   ![Pulsante Abilita backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo che il backup è stato abilitato, i criteri di backup verranno eseguiti come pianificato. Se si vuole generare un processo di backup su richiesta per eseguire subito il backup delle macchine virtuali, vedere [Attivazione del processo di backup](./backup-azure-vms-first-look-arm.md#initial-backup).

In caso di problemi con la registrazione della VM, vedere le informazioni seguenti relative all'installazione dell'agente di macchine virtuali e alla connettività di rete. Probabilmente le informazioni seguenti non sono necessarie se si stanno proteggendo macchine virtuali create in Azure. Se è stata eseguita la migrazione delle macchine virtuali ad Azure, tuttavia, assicurarsi che l'agente di macchine virtuali sia installato correttamente e che la VM possa comunicare con la rete virtuale.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installare l'agente di macchine virtuali nella VM
Per il funzionamento dell'estensione di backup, è necessario che nella VM di Azure sia installato l'[agente di macchine virtuali](../virtual-machines/extensions/agent-windows.md) di Azure. Se la VM è stata creata da Azure Marketplace, l'agente di macchine virtuali è già presente.

Le informazioni seguenti riguardano le situazioni in cui *non* si usa una VM creata da Azure Marketplace, **ad esempio perché è stata eseguita la migrazione di una macchina virtuale da un centro dati locale. In tal caso, per proteggere la macchina virtuale è necessario installare l'agente di macchine virtuali.**

**Nota**: dopo aver installato l'agente di macchine virtuali, è anche necessario usare Azure PowerShell per aggiornare la proprietà ProvisionGuestAgent perché Azure sappia in quale macchina virtuale è installato l'agente.

In caso di problemi con il backup della VM di Azure, usare la tabella seguente per verificare che l'agente di macchine virtuali di Azure sia installato correttamente nella VM. La tabella contiene informazioni aggiuntive sull'agente di macchine virtuali per VM Windows e Linux.

| **operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installazione dell'agente di macchine virtuali |Scaricare e installare il file [MSI per l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari privilegi di amministratore. |<li> Installare l'[agente Linux](../virtual-machines/extensions/agent-linux.md) più recente. Per completare l'installazione sono necessari privilegi di amministratore. È consigliabile installare l'agente dal repository di distribuzione. **Non è consigliabile** installare l'agente di macchine virtuali Linux direttamente da GitHub.  |
| Aggiornamento dell'agente di VM |L'aggiornamento dell'agente di VM è semplice quanto la reinstallazione dei [file binari dell'agente di VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |Seguire le istruzioni sull'[aggiornamento dell'agente di macchine virtuali Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È consigliabile aggiornare l'agente dal repository di distribuzione. **Non è consigliabile** aggiornare l'agente di macchine virtuali Linux direttamente da GitHub.<br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente di VM. |
| Convalida dell'installazione dell'agente di macchine virtuali |<li>Passare alla cartella *C:\WindowsAzure\Packages* nella VM di Azure, <li>che dovrebbe includere il file WaAppAgent.exe.<li> Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo Versione prodotto deve essere 2.6.1198.718 o superiore. |N/D |

### <a name="backup-extension"></a>Estensione di backup
Al termine dell'installazione dell'agente di macchine virtuali nella VM, il servizio Backup di Azure installa l'estensione di backup nell'agente. Il servizio Backup applica automaticamente aggiornamenti e patch all'estensione di backup.

Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno. Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup continua tuttavia a eseguire il backup della VM anche se questa è disattivata e non è stato possibile installare l'estensione. Questa situazione è detta *VM offline*. In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*.

## <a name="establish-network-connectivity"></a>Stabilire la connettività di rete
Per gestire gli snapshot di VM, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. Senza la connettività Internet appropriata, si verifica il timeout delle richieste HTTP della macchina virtuale e l'operazione di backup non riesce. Se la distribuzione ha restrizioni di accesso, ad esempio tramite un gruppo di sicurezza di rete (NSG), scegliere una delle opzioni seguenti per specificare un percorso chiaro per il traffico di backup:

* [Aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti](https://www.microsoft.com/download/details.aspx?id=41653)
* Distribuire un server proxy HTTP per eseguire il routing del traffico

Nella scelta dell'opzione da usare, sono necessari compromessi tra gestibilità, controllo granulare e costo.

| Opzione | Vantaggi | Svantaggi: |
| --- | --- | --- |
| Aggiungere gli intervalli IP all'elenco elementi consentiti  |Senza costi aggiuntivi.<br><br>Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet **Set-AzureNetworkSecurityRule**. |Complessità di gestione a causa della variazione nel tempo degli intervalli IP interessati.<br><br>Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di archiviazione. |
| Usare un proxy HTTP |Possibilità di controllo granulare nel proxy sugli URL di archiviazione.<br><br>Singolo punto di accesso Internet alle VM.<br><br>Non è soggetto alle modifiche degli indirizzi IP di Azure. |Costi aggiuntivi per l'esecuzione di una VM con il software proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Microsoft Azure Datacenter IP Ranges 
Per aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti, vedere il [sito Web Azure](https://www.microsoft.com/download/details.aspx?id=41653) per informazioni dettagliate sugli intervalli IP e istruzioni.

È possibile consentire le connessioni alle risorse di archiviazione dell'area specifica usando [tag di servizio](../virtual-network/security-overview.md#service-tags). Verificare che la regola che consente l'accesso all'account di archiviazione abbia priorità più alta rispetto alla regola che blocca l'accesso a Internet.

![NSG con i tag di archiviazione per un'area](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Il video seguente illustra la procedura dettagliata per configurare i tag di servizio:

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!NOTE]
> Per un elenco di tag e aree di servizio per l'archiviazione, vedere [Tag di servizio per l'archiviazione](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Usare un proxy HTTP per i backup delle VM
Quando si esegue il backup di una macchina virtuale, l'estensione di backup nella VM invia i comandi di gestione degli snapshot ad Archiviazione di Azure usando un'API HTTPS. Indirizzare il traffico dell'estensione di backup attraverso il proxy HTTP perché è l'unico componente configurato per l'accesso alla rete Internet pubblica.

> [!NOTE]
> Non viene consigliato uno specifico software proxy da usare. Assicurarsi di scegliere un proxy compatibile con la procedura di configurazione descritta di seguito.
>
>

L'immagine di esempio seguente mostra i tre passaggi di configurazione necessari per usare un proxy HTTP:

* La VM dell'app indirizza tutto il traffico HTTP diretto alla rete Internet pubblica attraverso la VM del proxy.
* La VM del proxy consente il traffico in ingresso proveniente dalle VM nella rete virtuale.
* Il gruppo di sicurezza di rete denominato NSG-lockdown ha bisogno di una regola di sicurezza che consenta il traffico Internet in uscita dalla VM del proxy.

Per usare un proxy HTTP per la comunicazione con la rete Internet pubblica, completare i passaggi seguenti.

> [!NOTE]
> In questi passaggi vengono usati nomi e valori specifici per l'esempio. Quando si aggiungono o si incollano i dettagli nel proprio codice, usare i nomi e i valori appropriati alla distribuzione specifica.

#### <a name="step-1-configure-outgoing-network-connections"></a>Passaggio 1: Configurare le connessioni di rete in uscita
###### <a name="for-windows-machines"></a>Per macchine Windows
Questa procedura imposta la configurazione del server proxy per l'account di sistema locale.

1. Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
1. Aprire Internet Explorer eseguendo questo comando da un prompt con privilegi elevati:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

1. In Internet Explorer passare a **Strumenti** > **Opzioni Internet** > **Connessioni** > **Impostazioni LAN**.
1. Verificare le impostazioni del proxy per l'account di sistema. Impostare l'indirizzo IP e la porta del proxy.
1. Chiudere Internet Explorer.

Lo script seguente imposta una configurazione proxy a livello di computer e la usa per tutto il traffico HTTP o HTTPS in uscita. Se è stato configurato un server proxy in un account utente corrente e non in un account di sistema locale, usare questo script per applicare le impostazioni a SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Se nel log del server proxy viene visualizzato il messaggio "(407) Necessaria autenticazione proxy", verificare che l'autenticazione sia configurata correttamente.
>
>

###### <a name="for-linux-machines"></a>Per macchine Linux
Aggiungere la riga seguente al file ```/etc/environment``` :

```
http_proxy=http://<proxy IP>:<proxy port>
```

Aggiungere le righe seguenti al file ```/etc/waagent.conf``` :

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Passaggio 2: Consentire le connessioni in ingresso nel server proxy
1. Aprire Windows Firewall nel server proxy. Il modo più semplice per accedere al firewall consiste nel cercare **Windows Firewall con sicurezza avanzata**.
1. Nella finestra di dialogo **Windows Firewall con sicurezza avanzata** fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e scegliere **Nuova regola**.
1. Nella pagina **Tipo di regola** della Creazione guidata nuova regola connessioni in entrata selezionare **Personalizzata** e quindi **Avanti**.
1. Nella pagina **Programma** selezionare **Tutti i programmi** e quindi **Avanti**.
1. Nella pagina **Protocollo e porte** immettere le informazioni seguenti e selezionare **Avanti**:
   * Per **Tipo di protocollo** selezionare **TCP**.
   * Per **Porta locale** selezionare **Porte specifiche**. Nella casella seguente specificare il numero della porta del proxy che è stata configurata.
   * Per **Porta remota** selezionare **Tutte le porte**.

Per il resto della procedura guidata accettare le impostazioni predefinite fino alla fine. Assegnare quindi un nome alla regola.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Passaggio 3 Aggiungere una regola di eccezione al gruppo di sicurezza di rete
Il comando aggiunge un'eccezione per il gruppo di sicurezza di rete (NSG). Questa eccezione consente il traffico TCP da qualsiasi porta su 10.0.0.5 a qualsiasi indirizzo Internet sulla porta 80 (HTTP) o 443 (HTTPS). Se è necessario usare una porta specifica sulla rete Internet pubblica, assicurarsi di aggiungere tale porta a ```-DestinationPortRange```.

In un prompt dei comandi di Azure PowerShell digitare il comando seguente:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, [inviare commenti e suggerimenti](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato preparato l'ambiente per il backup della VM, il passaggio logico successivo consiste nel creare un backup. L'articolo sulla pianificazione offre informazioni più dettagliate sul backup delle VM.

* [Eseguire il backup di macchine virtuali](backup-azure-arm-vms.md)
* [Pianificare l'infrastruttura di backup delle VM](backup-azure-vms-introduction.md)
* [Gestire backup di macchine virtuali](backup-azure-manage-vms.md)

---
title: 'Backup di Azure: preparare il backup di macchine virtuali | Microsoft Docs'
description: Assicurarsi che l'ambiente sia pronto per il backup di macchine virtuali in Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: backup; eseguire il backup;
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/21/2017
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 568509eba47facfc5966d06dff5a1b32dce1008f
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Preparare l’ambiente per il backup di macchine virtuali distribuite con Resource Manager

Questo articolo illustra i passaggi per preparare l'ambiente per il backup di una macchina virtuale (VM) distribuita con Azure Resource Manager. I passaggi descritti nelle procedure usano il portale di Azure.  

Per proteggere le VM, il servizio Backup di Azure offre due tipi di insiemi di credenziali: insiemi di credenziali di backup e insiemi di credenziali dei servizi di ripristino. Un insieme di credenziali di backup protegge le VM distribuite tramite il modello di distribuzione classica. Un insieme di credenziali dei servizi di ripristino protegge le *VM distribuite sia con il modello di distribuzione classica che con Resource Manager*. Se si vuole proteggere una VM distribuita con Resource Manager, è necessario usare un insieme di credenziali dei servizi di ripristino.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

Prima di proteggere una macchina virtuale distribuita con Resource Manager o eseguirne il backup, assicurarsi di rispettare questi prerequisiti:

* Creare un insieme di credenziali dei servizi di ripristino o identificarne uno esistente *nella stessa località della VM*.
* Selezionare uno scenario, definire i criteri di backup e specificare gli elementi da proteggere.
* Verificare l'installazione di un agente di macchine virtuali nella VM.
* Controllare la connettività di rete.
* Per le VM Linux, se si vuole personalizzare l'ambiente di backup per backup coerenti con l'applicazione, seguire i [passaggi per configurare gli script pre-snapshot e post-snapshot](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Se nell'ambiente esistono già queste condizioni, passare all'articolo su come [eseguire il backup delle VM](backup-azure-arm-vms.md). Se è necessario configurare o verificare uno qualsiasi di questi prerequisiti, questo articolo illustra i passaggi necessari.

## <a name="supported-operating-systems-for-backup"></a>Sistemi operativi supportati per il backup
 * **Linux**: Backup di Azure supporta [un elenco di distribuzioni approvate da Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), ad eccezione di CoreOS Linux. 
 
    > [!NOTE] 
    > È possibile usare altre distribuzioni Bring Your Own Linux, a condizione che l'agente di macchine virtuali sia disponibile nella VM e che sia presente il supporto per Python. Tali distribuzioni non sono tuttavia approvate da Microsoft per il backup.
 * **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitazioni durante il backup e il ripristino di una VM
Prima di preparare l'ambiente, assicurarsi di conoscere queste limitazioni:

* Il backup di macchine virtuali con più di 16 dischi dati non è supportato.
* Il backup di macchine virtuali con dischi dati di dimensioni superiori a 1023 GB non è supportato.

  > [!NOTE]
  > È disponibile un'anteprima privata per il supporto di backup per macchine virtuali con dischi di dimensioni superiori a 1 TB. Per informazioni dettagliate, vedere l'[anteprima privata per il supporto di backup di VM con dischi di grandi dimensioni](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a).
  >

* Il backup di macchine virtuali con un indirizzo IP riservato e nessun endpoint definito non è supportato.
* Il backup di VM crittografate soltanto tramite una chiave di crittografia BitLocker (BEK) non è supportato. Il backup di VM Linux crittografate con la crittografia Linux Unified Key Setup (LUKS) non è supportato.
* Non è consigliabile eseguire il backup di VM contenenti la configurazione di volumi condivisi del cluster o file server di scalabilità orizzontale, per cui durante un'attività di snapshot è necessario il coinvolgimento di tutte le VM incluse nella configurazione cluster. Backup di Azure non supporta la coerenza tra più macchine virtuali. 
* I dati di backup non includono le unità di rete montate che sono collegate a una VM.
* La sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. Se si tenta di ripristinare una macchina virtuale che esiste, l'operazione di ripristino non viene eseguita.
* L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
* Il backup e il ripristino di macchine virtuali tramite dischi non gestiti negli account di archiviazione con regole di rete applicate non sono attualmente supportati. Durante la configurazione del backup, verificare che le impostazioni di "Firewall e reti virtuali" per l'account di archiviazione consentano l'accesso da "Tutte le reti".
* È possibile eseguire il backup di macchine virtuali in tutte le aree pubbliche di Azure. Vedere l'[elenco di controllo](https://azure.microsoft.com/regions/#services) delle aree supportate. Se l'area desiderata non è attualmente supportata, tale area non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
* Il ripristino di un controller di dominio di VM che fa parte di una configurazione con controller di dominio è supportato solo tramite PowerShell. Per altre informazioni, vedere la sezione relativa al [ripristino di un controller di dominio con più controller di dominio](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Il ripristino delle macchine virtuali che presentano le seguenti configurazioni di rete speciali è supportato solo tramite PowerShell. Le VM create tramite il flusso di lavoro di ripristino nell'interfaccia utente non avranno queste configurazioni di rete al termine dell'operazione di ripristino. Per altre informazioni, vedere [Ripristino delle macchine virtuali con configurazioni di rete speciali](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Macchine virtuali con configurazione del servizio di bilanciamento del carico (interno ed esterno)
  * Macchine virtuali con più indirizzi IP riservati
  * Macchine virtuali con più schede di rete

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Creare un insieme di credenziali dei servizi di ripristino per una VM
Un insieme di credenziali dei servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu **Hub** selezionare **Esplora** e quindi digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco delle risorse viene filtrato in base all'input. Selezionare **Insiemi di credenziali dei servizi di ripristino**.

    ![Digitazione nella casella e selezione di "Insiemi di credenziali dei servizi di ripristino" nei risultati](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
3. Nel menu di **Insiemi di credenziali dei servizi di ripristino** selezionare **Aggiungi**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Verrà visualizzato il riquadro **Insiemi di credenziali dei servizi di ripristino**, in cui viene richiesto di specificare le informazioni relative a **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Riquadro "Insiemi di credenziali dei servizi di ripristino"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome contenente da 2 a 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
5. Selezionare **Sottoscrizione** per visualizzare l'elenco delle sottoscrizioni disponibili. Se non si è certi della sottoscrizione da usare, usare quella predefinita (o suggerita). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure.
6. Selezionare **Gruppo di risorse** per visualizzare l'elenco dei gruppi di risorse disponibili oppure **Nuovo** per crearne uno nuovo. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Selezionare **Località** per selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali *deve* trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere.

   > [!IMPORTANT]
   > Se non si è certi della località in cui si trova la VM, chiudere la finestra di dialogo per la creazione dell'insieme di credenziali e passare all'elenco delle macchine virtuali nel portale. Se si hanno macchine virtuali in più aree, è necessario creare un insieme di credenziali dei servizi di ripristino in ogni area. Creare l'insieme di credenziali nella prima località prima di passare a quella successiva. Non è necessario specificare account di archiviazione per archiviare i dati di backup. Questo aspetto viene gestito automaticamente dall'insieme di credenziali dei servizi di ripristino e dal servizio Backup di Azure.
   >
   >

8. Selezionare **Create**. La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. Al termine della creazione, l'insieme di credenziali verrà visualizzato nell'elenco degli insiemi di credenziali dei servizi di ripristino. Se non viene visualizzato, selezionare **Aggiorna**.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Ora che l'insieme di credenziali è stato creato, è possibile apprendere come impostare la replica di archiviazione.

## <a name="set-storage-replication"></a>Impostare la replica di archiviazione
L'opzione di replica di archiviazione consente di scegliere tra archiviazione con ridondanza geografica e archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si tratta del backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale.

Per modificare le impostazioni di replica di archiviazione:

1. Nel riquadro **Insiemi di credenziali dei servizi di ripristino** selezionare l'insieme di credenziali.
    Quando si seleziona l'insieme di credenziali, vengono visualizzati il riquadro **Impostazioni**, con il nome dell'insieme di credenziali nella parte superiore, e il riquadro dei dettagli dell'insieme di credenziali.

   ![Scegliere l'insieme di credenziali dall'elenco degli insiemi di credenziali di backup](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Nel riquadro **Impostazioni** usare il dispositivo di scorrimento verticale per passare alla sezione **Gestisci** e selezionare **Infrastruttura di backup**. Nella sezione **Generale** selezionare **Configurazione di backup**. Nel riquadro **Configurazione di backup** scegliere l'opzione di replica di archiviazione per l'insieme di credenziali. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica.

   ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Se si usa Azure come endpoint di archiviazione di backup primario, continuare a usare l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint di archiviazione di backup non primario, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione, vedere la panoramica [Replica di Archiviazione di Azure](../storage/common/storage-redundancy.md).

3. Se è stato modificato il tipo di replica di archiviazione, selezionare **Salva**.
    
Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, è possibile associare la VM all'insieme di credenziali. Per iniziare l'associazione, è necessario trovare e registrare le macchine virtuali di Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selezionare un obiettivo di backup, impostare i criteri e definire gli elementi da proteggere
Prima di registrare una VM in un insieme di credenziali, eseguire il processo di individuazione per assicurarsi che vengano identificate tutte le nuove macchine virtuali aggiunte alla sottoscrizione. Il processo esegue una query su Azure per ottenere l'elenco delle macchine virtuali disponibili nella sottoscrizione, insieme a informazioni come il nome del servizio cloud e l'area. 

Lo *scenario* nel portale di Azure fa riferimento a ciò che si inserirà nell'insieme di credenziali dei servizi di ripristino. I *criteri* determinano la pianificazione relativa a quando e con quale frequenza verranno acquisiti i punti di ripristino. I criteri includono anche il periodo di mantenimento dati per i punti di ripristino.

1. Se un insieme di credenziali dei servizi di ripristino è già aperto, procedere al passaggio 2. Se non è già aperto un insieme di credenziali dei servizi di ripristino, aprire il [portale di Azure](https://portal.azure.com/). Nel menu **Hub** selezionare **Altri servizi**.

   a. Nell'elenco di risorse digitare **servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

      ![Digitazione nella casella e selezione di "Insiemi di credenziali dei servizi di ripristino" nei risultati](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Se nella sottoscrizione non sono presenti insiemi di credenziali, l'elenco è vuoto.

      ![Visualizzazione dell'elenco degli insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

      Verranno visualizzati il riquadro **Impostazioni** e il dashboard dell'insieme di credenziali scelto.

      ![Riquadro Impostazioni e dashboard dell'insieme di credenziali](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. Nel menu del dashboard dell'insieme di credenziali selezionare **Backup**.

   ![Pulsante Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Verranno visualizzati i riquadri **Backup** e **Obiettivo del backup**.

3. Nel riquadro **Obiettivo del backup** impostare **Posizione di esecuzione del carico di lavoro** su **Azure** ed **Elementi di cui eseguire il backup** su **Macchina virtuale**. Selezionare **OK**.

   ![Riquadri Backup e Obiettivo del backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Questo passaggio registra l'estensione della VM nell'insieme di credenziali. Il riquadro **Obiettivo del backup** verrà chiuso e verrà visualizzato il riquadro **Criteri di backup**.

   ![Riquadri Backup e Criteri di backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. Nel riquadro **Criteri di backup** selezionare i criteri di backup da applicare all'insieme di credenziali.

   ![Selezionare il criterio di backup](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   I dettagli dei criteri predefiniti vengono elencati nel menu a discesa. Per creare un nuovo criterio, selezionare **Crea nuovo** dal menu a discesa. Per istruzioni sulla definizione di un criterio di backup, vedere [Definizione di un criterio di backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Selezionare **OK** per associare i criteri di backup all'insieme di credenziali.

   Il riquadro **Criteri di backup** verrà chiuso e verrà visualizzato il riquadro **Seleziona macchine virtuali**.
5. Nel riquadro **Seleziona macchine virtuali** scegliere le macchine virtuali da associare ai criteri specificati e selezionare **OK**.

   ![Riquadro "Seleziona macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Viene convalidata la macchina virtuale selezionata. Se non vengono visualizzate le macchine virtuali previste, controllare che si trovino nella stessa località di Azure dell'insieme di credenziali dei servizi di ripristino e che non siano già protette in un altro insieme di credenziali. La località dell'insieme di credenziali dei servizi di ripristino viene visualizzata nel dashboard dell'insieme di credenziali.

6. Dopo aver definito tutte le impostazioni per l'insieme di credenziali, nel riquadro **Backup** selezionare **Abilita backup**. Questo passaggio distribuisce i criteri nell'insieme di credenziali e nelle VM, ma non crea il punto di ripristino iniziale per la macchina virtuale.

   ![Pulsante Abilita backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo che il backup è stato abilitato, i criteri di backup verranno eseguiti come pianificato. Se si vuole generare un processo di backup su richiesta per eseguire subito il backup delle macchine virtuali, vedere [Attivazione del processo di backup](./backup-azure-arm-vms.md#triggering-the-backup-job).

In caso di problemi con la registrazione della VM, vedere le informazioni seguenti relative all'installazione dell'agente di macchine virtuali e alla connettività di rete. Probabilmente le informazioni seguenti non sono necessarie se si stanno proteggendo macchine virtuali create in Azure. Se è stata eseguita la migrazione delle macchine virtuali ad Azure, tuttavia, assicurarsi che l'agente di macchine virtuali sia installato correttamente e che la VM possa comunicare con la rete virtuale.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installare l'agente di macchine virtuali nella VM
Per il funzionamento dell'estensione di backup, è necessario che nella VM di Azure sia installato l'[agente di macchine virtuali](../virtual-machines/windows/agent-user-guide.md) di Azure. Se la VM è stata creata da Azure Marketplace, l'agente di macchine virtuali è già presente. 

Le informazioni seguenti riguardano le situazioni in cui *non* si usa una VM creata da Azure Marketplace, ad esempio perché è stata eseguita la migrazione di una VM da un data center locale. In tal caso, per proteggere la VM è necessario installare l'agente di macchine virtuali.

In caso di problemi con il backup della VM di Azure, usare la tabella seguente per verificare che l'agente di macchine virtuali di Azure sia installato correttamente nella VM. La tabella contiene informazioni aggiuntive sull'agente di macchine virtuali per VM Windows e Linux.

| **Operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installare l'agente di macchine virtuali |Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione è necessario disporre dei privilegi di amministratore. |Installare l'[agente Linux](../virtual-machines/linux/agent-user-guide.md) più recente. Per completare l'installazione è necessario disporre dei privilegi di amministratore. È consigliabile installare l'agente dal repository di distribuzione. *Non è consigliabile* installare l'agente di macchine virtuali Linux direttamente da GitHub.  |
| Aggiornare l'agente di macchine virtuali |L'aggiornamento dell'agente VM è semplice quanto la reinstallazione dei [file binari dell'agente VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È consigliabile aggiornare l'agente dal repository di distribuzione. *Non è consigliabile* aggiornare l'agente di macchine virtuali Linux direttamente da GitHub.<br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |
| Convalidare l'installazione dell'agente di macchine virtuali |1. Passare alla cartella C:\WindowsAzure\Packages nella macchina virtuale di Azure. <br><br>2. Trovare il file WaAppAgent.exe. <br><br>3. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo **Versione prodotto** dovrebbe contenere 2.6.1198.718 o un numero più elevato. |N/D |

### <a name="backup-extension"></a>Estensione di backup
Al termine dell'installazione dell'agente di macchine virtuali nella VM, il servizio Backup di Azure installa l'estensione di backup nell'agente. Il servizio Backup applica automaticamente aggiornamenti e patch all'estensione di backup.

Il servizio Backup installa l'estensione di backup indipendentemente dal fatto che la VM sia in esecuzione o meno. Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup continua tuttavia a eseguire il backup della VM anche se questa è disattivata e non è stato possibile installare l'estensione. Questa situazione è detta *VM offline*. In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*.

## <a name="establish-network-connectivity"></a>Stabilire la connettività di rete
Per gestire gli snapshot di VM, l'estensione di backup richiede la connettività agli indirizzi IP pubblici di Azure. Senza la connettività Internet appropriata, si verifica il timeout delle richieste HTTP della macchina virtuale e l'operazione di backup non riesce. Se la distribuzione ha restrizioni di accesso, ad esempio tramite un gruppo di sicurezza di rete (NSG), scegliere una delle opzioni seguenti per specificare un percorso chiaro per il traffico di backup:

* [Aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
* Distribuire un server proxy HTTP per eseguire il routing del traffico

Nella scelta dell'opzione da usare, sono necessari compromessi tra gestibilità, controllo granulare e costo.

| Opzione | Vantaggi | Svantaggi: |
| --- | --- | --- |
| Aggiungere gli intervalli IP all'elenco elementi consentiti  |Senza costi aggiuntivi.<br><br>Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet **Set-AzureNetworkSecurityRule**. |Complessità di gestione a causa della variazione nel tempo degli intervalli IP interessati.<br><br>Possibilità di accesso a tutto l'ambiente Azure, non solo al servizio di archiviazione. |
| Usare un proxy HTTP |Possibilità di controllo granulare nel proxy sugli URL di archiviazione.<br><br>Singolo punto di accesso Internet alle VM.<br><br>Non è soggetto alle modifiche degli indirizzi IP di Azure. |Costi aggiuntivi per l'esecuzione di una VM con il software proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Microsoft Azure Datacenter IP Ranges 
Per aggiungere gli intervalli IP dei data center di Azure all'elenco elementi consentiti, vedere il [sito Web Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) per informazioni dettagliate sugli intervalli IP e istruzioni.

È possibile consentire le connessioni alle risorse di archiviazione dell'area specifica usando [tag di servizio](../virtual-network/security-overview.md#service-tags). Verificare che la regola che consente l'accesso all'account di archiviazione abbia priorità più alta rispetto alla regola che blocca l'accesso a Internet. 

![NSG con i tag di archiviazione per un'area](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> I tag di servizio delle risorse di archiviazione sono disponibili in anteprima solo in aree specifiche. Per un elenco delle aree, vedere i [tag di servizio per Archiviazione](../virtual-network/security-overview.md#service-tags).

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
2. Aprire Internet Explorer eseguendo questo comando da un prompt con privilegi elevati:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. In Internet Explorer passare a **Strumenti** > **Opzioni Internet** > **Connessioni** > **Impostazioni LAN**.
4. Verificare le impostazioni del proxy per l'account di sistema. Impostare l'indirizzo IP e la porta del proxy.
5. Chiudere Internet Explorer.

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
2. Nella finestra di dialogo **Windows Firewall con sicurezza avanzata** fare clic con il pulsante destro del mouse su **Regole connessioni in entrata** e scegliere **Nuova regola**.
3. Nella pagina **Tipo di regola** della Creazione guidata nuova regola connessioni in entrata selezionare **Personalizzata** e quindi **Avanti**.
4. Nella pagina **Programma** selezionare **Tutti i programmi** e quindi **Avanti**.
5. Nella pagina **Protocollo e porte** immettere le informazioni seguenti e selezionare **Avanti**:
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
In caso di domande o se si vuole che venga inclusa una funzionalità, [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato preparato l'ambiente per il backup della VM, il passaggio logico successivo consiste nel creare un backup. L'articolo sulla pianificazione offre informazioni più dettagliate sul backup delle VM.

* [Eseguire il backup di macchine virtuali](backup-azure-arm-vms.md)
* [Pianificare l'infrastruttura di backup delle VM](backup-azure-vms-introduction.md)
* [Gestire backup di macchine virtuali](backup-azure-manage-vms.md)

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
ms.date: 9/3/2017
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 3c2ea9e5872454b0bac67c39362a1f94b6fa47b8
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Preparare l’ambiente per il backup di macchine virtuali distribuite con Resource Manager

Questo articolo fornisce i passaggi per preparare l'ambiente per eseguire il backup di una macchina virtuale distribuita di gestione risorse di Azure (VM). I passaggi descritti nelle procedure usano il portale di Azure.  

Il servizio di Backup di Azure è disponibili due tipi di insiemi di credenziali per la protezione delle macchine virtuali: insiemi di credenziali di servizi di ripristino e backup. Un insieme di credenziali di backup consente di proteggere le macchine virtuali distribuite tramite il modello di distribuzione classica. Un insieme di credenziali di servizi di ripristino consente di proteggere *macchine virtuali sia distribuito classica e distribuite di gestione risorse*. Se si desidera proteggere una macchina virtuale distribuita di gestione risorse, è necessario utilizzare un insieme di credenziali di servizi di ripristino.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

Prima di poter proteggere o eseguire il backup di una macchina virtuale distribuita di gestione risorse, verificare che questi prerequisiti esistono:

* Creare un insieme di credenziali di servizi di ripristino (o identificare un insieme di credenziali di servizi di ripristino esistenti) *nella stessa posizione di una macchina virtuale*.
* Selezionare uno scenario, definire i criteri di backup e specificare gli elementi da proteggere.
* Verifica dell'installazione di un agente di macchine Virtuali nella macchina virtuale.
* Verificare la connettività di rete.
* Per le macchine virtuali Linux, se si desidera personalizzare l'ambiente di backup per i backup coerenti con l'applicazione, seguire la [passaggi per configurare gli script di pre-snapshot e post-snapshot](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Se queste condizioni esistono già nell'ambiente in uso, procedere con il [eseguire il backup delle macchine virtuali](backup-azure-arm-vms.md) articolo. Se si desidera impostare o verificare uno di questi prerequisiti, in questo articolo illustra i passaggi.

## <a name="supported-operating-systems-for-backup"></a>Sistemi operativi supportati per il backup
 * **Linux**: Backup di Azure supporta [un elenco delle distribuzioni di Azure Approva](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), ad eccezione di CoreOS Linux. 
 
    > [!NOTE] 
    > Altri bring-your-proprietari-distribuzioni di Linux potrebbero funzionare, purché l'agente VM è disponibile nella macchina virtuale e il supporto per Python esiste. È tuttavia verificato tali distribuzioni per il backup.
 * **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitazioni durante il backup e il ripristino di una VM
Prima di preparare l'ambiente, assicurarsi di comprendere le seguenti limitazioni:

* Il backup di macchine virtuali con più di 16 dischi dati non è supportato.
* Backup di macchine virtuali con dati maggiore di 1023 GB le dimensioni dei dischi non è supportato.

  > [!NOTE]
  > È disponibile un'anteprima privata per il supporto di backup per le macchine virtuali con i dischi di 1 TB (o versione successiva) non gestiti. Per informazioni dettagliate, vedere [anteprima privata per il supporto di backup di grandi dimensioni disco VM](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a).
  >

* Il backup di macchine virtuali con un indirizzo IP riservato e nessun endpoint definito non è supportato.
* Backup delle macchine virtuali crittografate tramite solo una chiave di crittografia BitLocker (BEK) non è supportato. Backup delle macchine virtuali Linux crittografate tramite crittografia Linux unificata chiave il programma di installazione (LUKS) non è supportato.
* Si sconsiglia di backup delle macchine virtuali che contengono la configurazione di volumi condivisi Cluster (CSV) o File Server di scalabilità orizzontale. Tali sviluppatori richiedono che include tutte le macchine virtuali incluse nella configurazione del cluster durante un'operazione di snapshot. Backup di Azure non supporta la coerenza tra più macchine virtuali. 
* Dati di backup non includono le unità di rete montato associate a una macchina virtuale.
* La sostituzione di una macchina virtuale esistente durante il ripristino non è supportata. Se si tenta di ripristinare una macchina virtuale che esiste, l'operazione di ripristino non viene eseguita.
* L'operazione di backup e ripristino tra aree geografiche diverse non è supportata.
* Backup e ripristino di archiviazione di macchine virtuali che non è supportata al momento. Backup di macchine virtuali non è supportato se è stata abilitata l'archiviazione su funzionalità di rete virtuale che consente agli account di archiviazione di accedervi solo da alcuni/subnet reti virtuali e/o gli indirizzi IP.
* È possibile eseguire il backup di macchine virtuali in tutte le aree pubbliche di Azure. (Vedere il [checklist](https://azure.microsoft.com/regions/#services) delle aree geografiche supportate.) Se l'area che si sta cercando è attualmente supportata, non verrà visualizzata nell'elenco a discesa durante la creazione dell'insieme di credenziali.
* Il ripristino di un controller di dominio di VM che fa parte di una configurazione con controller di dominio è supportato solo tramite PowerShell. Per ulteriori informazioni, vedere [ripristino di un controller di dominio con più controller di dominio](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Il ripristino delle macchine virtuali che presentano le seguenti configurazioni di rete speciali è supportato solo tramite PowerShell. Macchine virtuali create tramite il flusso di lavoro di ripristino nell'interfaccia utente non avrà queste configurazioni di rete dopo l'operazione di ripristino è stata completata. Per altre informazioni, vedere [Ripristino delle macchine virtuali con configurazioni di rete speciali](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Macchine virtuali con configurazione del servizio di bilanciamento del carico (interno ed esterno)
  * Macchine virtuali con più indirizzi IP riservati
  * Macchine virtuali con più schede di rete

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Creare un insieme di credenziali di servizi di ripristino per una macchina virtuale
Un insieme di credenziali di servizi di ripristino è un'entità a cui sono archiviati i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali di servizi di ripristino contiene anche i criteri di backup associati a macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel **Hub** dal menu **Sfoglia**, quindi digitare **servizi di ripristino**. Si inizia a digitare, l'input dell'utente filtra l'elenco di risorse. Selezionare **insiemi di credenziali di servizi di ripristino**.

    ![Nell'apposita casella e selezionando "Insiemi di credenziali di servizi di ripristino" nei risultati](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
3. Nel **insiemi di credenziali di servizi di ripristino** dal menu **Aggiungi**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Il **insiemi di credenziali di servizi di ripristino** viene visualizzato il riquadro. Viene richiesto di fornire informazioni per **nome**, **sottoscrizione**, **gruppo di risorse**, e **percorso**.

    ![Riquadro "Archivi di servizi di ripristino"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome contenente caratteri da 2 a 50. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.
5. Selezionare **sottoscrizione** per visualizzare l'elenco delle sottoscrizioni disponibile. Se non si è certi la sottoscrizione da usare, usare il valore predefinito (o suggerito) sottoscrizione. Sono disponibili solo se di scelte più il lavoro o scuola account è associato a più sottoscrizioni di Azure.
6. Selezionare **gruppo di risorse** per visualizzare l'elenco di gruppi di risorse disponibile oppure selezionare **New** per creare un nuovo gruppo di risorse. Per informazioni complete su gruppi di risorse, vedere [Panoramica di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md).
7. Selezionare **percorso** per selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali *deve* trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere.

   > [!IMPORTANT]
   > Se si conosce la posizione in cui esiste la macchina virtuale, chiudere la finestra di dialogo di creazione dell'insieme di credenziali e passare all'elenco di macchine virtuali nel portale. Se si dispongono di macchine virtuali in più aree, è necessario creare un insieme di credenziali di servizi di ripristino in ogni area. Creare l'insieme di credenziali nella prima località prima di passare a quella successiva. Non è necessario specificare gli account di archiviazione per archiviare i dati di backup. L'insieme di credenziali di servizi di ripristino e il servizio di Backup di Azure gestisce che automaticamente.
   >
   >

8. Selezionare **Create**. La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. Dopo aver creato l'insieme di credenziali, viene visualizzato nell'elenco degli insiemi di credenziali di servizi di ripristino. Se non viene visualizzato l'insieme di credenziali, selezionare **aggiornamento**.

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Ora che l'insieme di credenziali è stato creato, è possibile apprendere come impostare la replica di archiviazione.

## <a name="set-storage-replication"></a>Imposta replica di archiviazione
L'opzione di replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si tratta del backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale.

Per modificare le impostazioni di replica di archiviazione:

1. Nel **insiemi di credenziali di servizi di ripristino** riquadro, selezionare l'insieme di credenziali.
    Quando si seleziona l'insieme di credenziali, il **impostazioni** riquadro (che ha il nome dell'insieme di credenziali nella parte superiore) e aprire il riquadro dei dettagli insieme di credenziali.

   ![Scegliere l'insieme di credenziali dall'elenco di insiemi di credenziali di backup](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Nel **impostazioni** riquadro, utilizzare il dispositivo di scorrimento verticale per scorrere verso il basso il **Gestisci** sezione e selezionare **infrastruttura Backup**. Nel **generale** selezionare **la configurazione del Backup**. Nel **la configurazione del Backup** riquadro, scegliere l'opzione di replica di archiviazione per l'insieme di credenziali. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica.

   ![Elenco degli insiemi di credenziali per il backup](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Se si usa Azure come un endpoint di archiviazione di backup primario, continuare a usare l'archiviazione con ridondanza geografica. Se si usa Azure come un endpoint di archiviazione di backup non primario, scegliere l'archiviazione con ridondanza locale. Altre informazioni sulle opzioni di archiviazione nel [Cenni preliminari sulla replica di archiviazione di Azure](../storage/common/storage-redundancy.md).

3. Se si modifica il tipo di replica di archiviazione, selezionare **salvare**.
    
Dopo aver scelto l'opzione di archiviazione per l'insieme di credenziali, si è pronti per associare la macchina virtuale con l'insieme di credenziali. Per iniziare l'associazione, è necessario trovare e registrare le macchine virtuali di Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selezionare un obiettivo di backup, impostare criteri e definire gli elementi da proteggere
Prima di registrare una macchina virtuale con un insieme di credenziali, eseguire il processo di individuazione per assicurarsi che vengono identificate le nuove macchine virtuali che sono stati aggiunti alla sottoscrizione. L'elaborazione delle query Azure per un elenco di macchine virtuali nella sottoscrizione, insieme a informazioni quali il nome del servizio cloud e l'area. 

Nel portale di Azure, *scenario* fa riferimento a ciò che viene inserito nell'insieme di credenziali di servizi di ripristino. *Criteri* è la pianificazione per la frequenza e quando vengono eseguiti i punti di ripristino. I criteri includono anche il periodo di mantenimento dati per i punti di ripristino.

1. Se un insieme di credenziali dei servizi di ripristino è già aperto, procedere al passaggio 2. Se non è un insieme di credenziali di servizi di ripristino aperto, aprire il [portale di Azure](https://portal.azure.com/). Nel **Hub** dal menu **più servizi**.

   a. Nell'elenco di risorse digitare **servizi di ripristino**. Si inizia a digitare, l'elenco viene filtrato l'input dell'utente. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

      ![Nell'apposita casella e selezionando "Insiemi di credenziali di servizi di ripristino" nei risultati](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino. Se sono non presenti alcun gli insiemi di credenziali nella sottoscrizione, questo elenco è vuoto.

      ![Visualizzazione dell'elenco degli insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

      Il **impostazioni** riquadro e il dashboard dell'insieme di credenziali per la prescelta archivio aperto.

      ![Dashboard delle impostazioni di riquadro e l'insieme di credenziali](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. Nel menu del dashboard dell'insieme di credenziali, selezionare **Backup**.

   ![Pulsante di backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Il **Backup** e **Backup obiettivo** aperti i riquadri.

3. Nel **obiettivo Backup** riquadro, impostare **in cui viene eseguito il carico di lavoro?** a **Azure** e **cosa si desidera eseguire il backup?** a  **Macchina virtuale**. Selezionare **OK**.

   ![Riquadri di backup e l'obiettivo di Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Questo passaggio registra l'estensione della macchina virtuale con l'insieme di credenziali. Il **Backup obiettivo** riquadro viene chiuso e **criteri di Backup** viene visualizzato il riquadro.

   !["Backup" e "Criteri di Backup" riquadri](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. Nel **criteri di Backup** riquadro, selezionare il criterio di backup che si desidera applicare l'insieme di credenziali.

   ![Selezionare il criterio di backup](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   I dettagli dei criteri predefiniti vengono elencati nel menu a discesa. Per creare un nuovo criterio, selezionare **Crea nuovo** dal menu a discesa. Per istruzioni sulla definizione di un criterio di backup, vedere [Definizione di un criterio di backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Selezionare **OK** per associare il criterio di backup nell'insieme di credenziali.

   Il **criteri di Backup** riquadro viene chiuso e **selezionare le macchine virtuali** viene visualizzato il riquadro.
5. Nel **selezionare le macchine virtuali** riquadro, scegliere le macchine virtuali per associare i criteri specificati e selezionare **OK**.

   ![Riquadro "Selezionare le macchine virtuali"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Viene convalidata la macchina virtuale selezionata. Se le macchine virtuali che si prevedono di vedere non viene visualizzata, verificare che esistano nello stesso percorso di Azure dell'insieme di credenziali di servizi di ripristino e non sono già protetti in un altro insieme di credenziali. Il dashboard dell'insieme di credenziali Mostra la posizione dell'insieme di credenziali di servizi di ripristino.

6. Ora che sono state definite tutte le impostazioni per l'insieme di credenziali nel **Backup** riquadro, selezionare **Abilita backup**. Questo passaggio consente di distribuire i criteri per l'insieme di credenziali e le macchine virtuali. Questo passaggio non crea il punto di ripristino iniziale per la macchina virtuale.

   ![Pulsante "Abilita backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo avere abilitato correttamente il backup, i criteri di backup verranno eseguito su pianificazione. Se si desidera generare un processo di backup su richiesta per eseguire il backup delle macchine virtuali a questo punto, vedere [attivare il processo di backup](./backup-azure-arm-vms.md#triggering-the-backup-job).

Nel caso di problemi durante la registrazione della macchina virtuale, vedere le seguenti informazioni sull'installazione dell'agente VM e sulla connettività di rete. Probabilmente le informazioni seguenti non sono necessarie se si stanno proteggendo macchine virtuali create in Azure. Ma se è eseguita la migrazione di macchine virtuali in Azure, assicurarsi che è installato correttamente l'agente VM e che la macchina virtuale possa comunicare con la rete virtuale.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installare l'agente VM nella macchina virtuale
Per l'estensione di Backup siano funzionanti, Azure [agente VM](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux) deve essere installato nella macchina virtuale di Azure. Se la macchina virtuale è stata creata da Azure Marketplace, l'agente VM è già presente nella macchina virtuale. 

Sono disponibili le informazioni seguenti per le situazioni in cui ti trovi *non* utilizza una macchina virtuale creata da Azure Marketplace. Ad esempio, la migrazione a una macchina virtuale da un data center locale. In tal caso, l'agente VM deve essere installato per proteggere la macchina virtuale.

Se si verificano problemi di backup della macchina virtuale di Azure, usare la tabella seguente per verificare che l'agente VM di Azure sia installato correttamente nella macchina virtuale. La tabella fornisce informazioni aggiuntive sull'agente VM per Windows e le macchine virtuali Linux.

| **Operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installare l'agente VM |Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione è necessario disporre dei privilegi di amministratore. |Installare l'[agente Linux](../virtual-machines/linux/agent-user-guide.md) più recente. Per completare l'installazione è necessario disporre dei privilegi di amministratore. È consigliabile installare l'agente dal repository di distribuzione. *Non è consigliabile* installare l'agente di macchine virtuali Linux direttamente da GitHub.  |
| Aggiornare l'agente VM |L'aggiornamento dell'agente VM è semplice quanto la reinstallazione dei [file binari dell'agente VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |Seguire le istruzioni per l'[aggiornamento dell'agente di macchine virtuali Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si consiglia di aggiornare l'agente dal proprio archivio di distribuzione. Abbiamo *non è consigliabile* ad aggiornare l'agente VM Linux direttamente da GitHub.<br><br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |
| Convalidare l'installazione dell'agente VM |1. Passare alla cartella C:\WindowsAzure\Packages nella macchina virtuale di Azure. <br><br>2. Trovare il file WaAppAgent.exe. <br><br>3. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo **Versione prodotto** dovrebbe contenere 2.6.1198.718 o un numero più elevato. |N/D |

### <a name="backup-extension"></a>Estensione di backup
Dopo aver installato l'agente VM nella macchina virtuale, il servizio di Backup di Azure installa l'estensione di backup per l'agente VM. Il servizio di Backup facilmente aggiornamenti e patch l'estensione del backup.

Il servizio di Backup è installata l'estensione di backup della macchina virtuale è in esecuzione o meno. Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione. Tuttavia, il servizio di Backup continua a eseguire il backup della macchina virtuale, anche se è stato disattivato e non è stato installato l'estensione. Questo è noto come *VM non in linea*. In questo caso, il punto di ripristino sarà *coerente con l'arresto anomalo*.

## <a name="establish-network-connectivity"></a>Stabilire la connettività di rete
Per gestire gli snapshot di macchina virtuale, l'estensione backup necessita della connettività per gli indirizzi IP pubblici Azure. Senza la connettività internet destra, timeout di richieste HTTP della macchina virtuale e l'operazione di backup non riesce. Se la distribuzione dispone di restrizioni di accesso, tramite un rete gruppo di sicurezza (), ad esempio, scegliere una di queste opzioni per fornire un percorso crittografato per il traffico di backup:

* [Gli intervalli di IP di Data Center di Azure whitelist](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Distribuire un server proxy HTTP per eseguire il routing del traffico

Per decidere quale opzione utilizzare, i compromessi sono compresi tra gestibilità, un controllo granulare e costi.

| Opzione | Vantaggi | Svantaggi: |
| --- | --- | --- |
| Aggiungere gli intervalli IP all'elenco elementi consentiti  |Senza costi aggiuntivi.<br><br>Per aprire l'accesso in un gruppo di sicurezza di rete, usare il cmdlet **Set-AzureNetworkSecurityRule**. |Complesso da gestire come agli intervalli IP cambiano nel tempo.<br><br>Fornisce l'accesso all'insieme di Azure e non solo l'archiviazione. |
| Utilizzare un proxy HTTP |URL di un controllo granulare nel proxy lo spazio di archiviazione è consentita.<br><br>Singolo punto di connessione Internet per le macchine virtuali.<br><br>Non è soggetto alle modifiche degli indirizzi IP di Azure. |Costi aggiuntivi per l'esecuzione di una VM con il software proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Microsoft Azure Datacenter IP Ranges 
All'elenco elementi consentiti gli intervalli di IP di Data Center di Azure, vedere il [sito Web di Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) per informazioni dettagliate sulle istruzioni e gli intervalli IP.

È possibile consentire le connessioni di archiviazione dell'area specifica utilizzando [matricola](../virtual-network/security-overview.md#service-tags). Assicurarsi che la regola che consente l'accesso all'account di archiviazione ha una priorità superiore rispetto alla regola che blocca l'accesso a internet. 

![NSG con i tag di archiviazione per un'area](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> I tag di archiviazione sono disponibili in anteprima solo in aree specifiche. Per un elenco delle aree, vedere [tag del servizio per l'archiviazione](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Usare un proxy HTTP per i backup VM
Quando esegue il backup di una macchina virtuale, l'estensione del backup della macchina virtuale invia i comandi di gestione di snapshot in archiviazione di Azure utilizzando un'API di HTTPS. Indirizzare il traffico di estensione del backup tramite il proxy HTTP, perché è l'unico componente configurato per l'accesso alla rete internet pubblica.

> [!NOTE]
> Non è consigliabile software proxy specifico da utilizzare. Assicurarsi di selezionare un proxy che è compatibile con i passaggi di configurazione che seguono.
>
>

L'immagine di esempio seguente mostra i passaggi di tre configurazione necessario utilizzare un proxy HTTP:

* Le route VM app tutto il traffico HTTP associato per la rete internet pubblica attraverso il proxy di macchina virtuale.
* Il proxy VM consente il traffico in ingresso da macchine virtuali nella rete virtuale.
* Il gruppo di sicurezza di rete denominato Scoperto lockdown necessita di una regola di sicurezza che consenta il traffico internet in uscita dal proxy di macchina virtuale.

Per utilizzare un proxy HTTP per comunicare con la rete internet pubblica, completare i passaggi seguenti.

> [!NOTE]
> Questi passaggi è possibile utilizzare nomi specifici e i valori per questo esempio. Quando si sta immettendo (o incollare) i dettagli nel codice, utilizzare i nomi e valori per la distribuzione.

#### <a name="step-1-configure-outgoing-network-connections"></a>Passaggio 1: Configurare le connessioni di rete in uscita
###### <a name="for-windows-machines"></a>Per macchine Windows
Questa procedura imposta la configurazione del server proxy per l'account sistema locale.

1. Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Aprire Internet Explorer eseguendo il comando seguente da un prompt dei comandi con privilegi elevati:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. In Internet Explorer, passare a **strumenti** > **Opzioni Internet** > **connessioni** > **impostazioni LAN**.
4. Verificare le impostazioni proxy per l'account di sistema. Impostare il proxy IP e porta.
5. Chiudere Internet Explorer.

Lo script seguente consente di impostare una configurazione del proxy a livello di computer e utilizzarla per tutto il traffico HTTP o HTTPS in uscita. Se è stato configurato un server proxy in un account utente corrente (non un account di sistema locale), è possibile utilizzare questo script per applicarli ai SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Se si osservano "(407) richiesta autenticazione Proxy" nel log del server proxy, verificare che l'autenticazione è configurato correttamente.
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

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Passaggio 2: Consenti connessioni in ingresso nel server proxy
1. Aprire Windows Firewall nel server proxy. Il modo più semplice per accedere il firewall è per la ricerca **Windows Firewall con sicurezza avanzata**.
2. Nel **Windows Firewall con sicurezza avanzata** la finestra di dialogo, fare doppio clic su **regole connessioni in entrata** e selezionare **nuova regola**.
3. Nelle connessioni in entrata Creazione guidata nuova regola, nel **tipo di regola** pagina, selezionare il **personalizzato** opzione e selezionare **Avanti**.
4. Nel **programma** selezionare **tutti i programmi** e selezionare **Avanti**.
5. Nel **protocollo e porte** pagina, immettere le informazioni seguenti e selezionare **Avanti**:
   * Per **tipo di protocollo**selezionare **TCP**.
   * Per **porta locale**selezionare **porte specifiche**. Nella casella in basso, specificare il numero della porta proxy che è stato configurato.
   * Per **porta remota**selezionare **tutte le porte**.

Per il resto della procedura guidata, accettare le impostazioni predefinite fino alla fine. Assegnare quindi questa regola un nome. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Passaggio 3: Aggiungere una regola di eccezione per il gruppo
Il comando aggiunge un'eccezione per il gruppo di sicurezza di rete (NSG). Questa eccezione consente il traffico TCP da qualsiasi porta 10.0.0.5 a qualsiasi indirizzo internet sulla porta 80 (HTTP) o 443 (HTTPS). Se è necessaria una porta specifica sulla rete internet pubblica, assicurarsi di aggiungere tale porta per ```-DestinationPortRange```.

In un prompt dei comandi di Azure PowerShell digitare il comando seguente:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Domande?
In caso di domande o se è presente una qualsiasi funzionalità che si desidera vedere incluso, [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato preparato l'ambiente per il backup della VM, il passaggio logico successivo consiste nel creare un backup. L'articolo sulla pianificazione offre informazioni più dettagliate sul backup delle VM.

* [Eseguire il backup di macchine virtuali](backup-azure-arm-vms.md)
* [Pianificare l'infrastruttura di backup delle VM](backup-azure-vms-introduction.md)
* [Gestire backup di macchine virtuali](backup-azure-manage-vms.md)

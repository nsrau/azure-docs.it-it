<properties
	pageTitle="Risoluzione dei problemi del backup di una macchina virtuale di Azure | Microsoft Azure"
	description="Risolvere i problemi relativi al backup e al ripristino delle macchine virtuali di Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="trinadhk;aashishr;jimpark"/>


# Risolvere i problemi relativi al backup delle macchine virtuali di Azure
È possibile risolvere gli errori rilevati durante l'uso di Backup di Azure con le informazioni elencate nella tabella seguente.

## Individuazione

| Operazione di backup | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Individuazione | Non è stato possibile individuare nuovi elementi. Si è verificato un errore interno del backup di Microsoft Azure. Attendere alcuni minuti e ripetere l'operazione. | Ripetere il processo di individuazione dopo 15 minuti.
| Individuazione | Non è stato possibile individuare nuovi elementi. Un'altra operazione di individuazione è già in corso. Attendere fino al completamento dell'operazione di individuazione corrente. | Nessuno |

## Registra
| Operazione di backup | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Registra | Il numero di dischi dati collegati alla macchina virtuale ha superato il limite supportato. Scollegare alcuni dischi dati in questa macchina virtuale e ripetere l'operazione. Il backup di Azure supporta un massimo di 16 dischi dati collegati a una macchina virtuale di Azure per il backup | Nessuno |
| Registra | Microsoft Azure Backup ha rilevato un errore interno. Attendere qualche minuto prima di ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. | È possibile che questo errore sia provocato da una delle configurazioni non supportate seguenti: <ul><li>Archiviazione con ridondanza locale Premium </ul> |
| Registra | Registrazione non riuscita con timeout dell'operazione dell'agente di installazione | Controllare se la versione del sistema operativo della macchina virtuale è supportata. |
| Registra | L'esecuzione del comando non è riuscita. In questo elemento è già in corso un'altra operazione. Attendere il completamento dell'operazione precedente. | Nessuno |
| Registra | Le macchine virtuali con dischi rigidi virtuali archiviati nel servizio di archiviazione Premium non sono supportate per il backup | Nessuno |
| Registra | L'agente di macchine virtuali non è presente nella macchina virtuale. Installare i prerequisiti necessari, l'agente della macchina virtuale e ripetere l'operazione. | [Altre informazioni](#vm-agent) sull'installazione dell'agente di VM e su come convalidare l'installazione dell'agente di VM. |

## Backup

| Operazione di backup | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Backup | Si è verificato il timeout della copia dei dischi rigidi virtuali dall'insieme di credenziali per il backup. Attendere qualche minuto prima di ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. | Questo errore si verifica quando la quantità di dati da copiare è eccessiva. Controllare se sono disponibili meno di 16 dischi dati. |
| Backup | Non è stato possibile comunicare con l'agente di macchine virtuali per ottenere lo stato dello snapshot. Timeout della sottoattività della macchina virtuale snapshot. Per risolvere il problema, vedere la Guida alla risoluzione dei problemi. | Questo errore viene generato se si verifica un problema con l'agente di VM o se l'accesso di rete all'infrastruttura di Azure è bloccato in qualche modo. <ul><li>Altre informazioni sul [debug dei problemi dell'agente di macchine virtuali](#vm-agent) <li>Altre informazioni sul [debug dei problemi di rete](#networking) </ul><br>Se l'agente di macchine virtuali non causa problemi, riavviare la macchina virtuale. Talvolta, uno stato della macchina virtuale non corretto genera problemi che vengono corretti riavviando la macchina virtuale. |
| Backup | Il backup non è riuscito e si è verificato un errore interno. Attendere qualche minuto prima di ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. | Questo errore si può verificare per due motivi: <ol><li> La quantità di dati da copiare è eccessiva. <li>La VM originale è stata eliminata e non è quindi possibile eseguire il backup. Per mantenere i dati di backup per una VM eliminata ma evitare gli errori di backup, annullare la protezione della VM e scegliere l'opzione relativa alla conservazione dei dati. La pianificazione del backup verrà interrotta e non verranno più visualizzati i messaggi di errore ricorrenti. |
| Backup | Non è stato possibile installare l'estensione Servizi di ripristino di Azure nell'elemento selezionato. L'agente di VM è un prerequisito dell'estensione Servizi di ripristino di Azure. Installare l'agente di VM di Azure e riavviare l'operazione di registrazione. | <ol> <li>Controllare se l'agente di VM è stato installato correttamente. <li>Assicurarsi che il flag sul file di configurazione della VM sia impostato correttamente.</ol> [Altre informazioni](#validating-vm-agent-installation) sull'installazione dell'agente di VM e su come convalidare l'installazione dell'agente di VM. |
| Backup | L'esecuzione del comando non è riuscita. In questo elemento è attualmente in corso un'altra operazione. Attendere il completamento dell'operazione precedente, quindi riprovare. | È in esecuzione un processo di backup o ripristino esistente per la VM e non è possibile avviare un nuovo processo mentre è in esecuzione il processo esistente. |
| Backup | L'installazione dell'estensione non è riuscita con un errore che indica che il servizio COM+ non può comunicare con Microsoft Distributed Transaction Coordinator | Questo errore in genere indica che il servizio COM+ non è in esecuzione. Per informazioni su come correggere l'errore, contattare il supporto tecnico Microsoft. |
| Backup | L'operazione di snapshot non è riuscita con un errore dell'operazione del Servizio Copia Shadow del volume che indica che l'unità è bloccata da Crittografia unità BitLocker. Sbloccare l'unità dal Pannello di controllo. | Disattivare BitLocker per tutte le unità della macchina virtuale e verificare se il problema relativo al Servizio Copia Shadow del volume è stato risolto |
| Backup | Le macchine virtuali con dischi rigidi virtuali archiviati nel servizio di archiviazione Premium non sono supportate per il backup | Nessuno |
| Backup | La macchina virtuale di Azure non è stata trovata. | Questo errore si verifica quando viene rilevata la macchina virtuale primaria ma i criteri di backup continuano a cercare una macchina virtuale per il backup. Per correggere l'errore: <ol><li>Ricreare la macchina virtuale con lo stesso nome e lo stesso nome del gruppo di risorse [nome del servizio cloud] <br>(OPPURE) <li> Disabilitare la protezione per questa macchina virtuale in modo che non vengano creati i processi di backup </ol> |
| Backup | L'agente di macchine virtuali non è presente nella macchina virtuale. Installare i prerequisiti necessari, l'agente della macchina virtuale e ripetere l'operazione. | [Altre informazioni](#vm-agent) sull'installazione dell'agente di VM e su come convalidare l'installazione dell'agente di VM. |

## Processi
| Operazione | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Annulla processo | L'annullamento non è supportato per questo tipo di processo. Attendere il completamento del processo. | Nessuno |
| Annulla processo | Il processo non si trova in uno stato annullabile. Attendere il completamento del processo. <br>OPPURE<br> Il processo selezionato non si trova in uno stato annullabile. Attendere il completamento del processo.| È probabile che il processo sia quasi completato. Attendere il completamento del processo. |
| Annulla processo | Non è possibile annullare il processo perché non è in corso. L'annullamento è supportato solo per i processi in corso. Tentare l'annullamento solo per un processo in corso. | Questo errore è causato da uno stato temporaneo. Attendere un minuto e ripetere l'operazione di annullamento. |
| Annulla processo | Non è stato possibile annullare il processo. Attendere il completamento del processo. | Nessuno |


## Ripristino
| Operazione | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Ripristino | Ripristino non riuscito con errore interno del cloud | <ol><li>Il servizio cloud in cui si sta tentando di eseguire il ripristino è configurato con le impostazioni DNS. Verificare <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se è presente un indirizzo configurato, le impostazioni DNS sono configurate.<br> <li>Il servizio cloud che si sta tentando di ripristinare è configurato con ReservedIP e le macchine virtuali che si trovano nel servizio cloud sono state arrestate.<br>È possibile controllare che un servizio cloud abbia un IP riservato tramite i seguenti cmdlet di powershell:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Si sta tentando di ripristinare una macchina virtuale con le seguenti configurazioni di rete speciali nello stesso servizio cloud. <br>- Le macchine virtuali nella configurazione del servizio di bilanciamento del carico (ADN interno ed esterno)<br>- Macchine virtuali con più indirizzi IP riservati<br>- Macchine virtuali con più schede di rete<br>Si prega di selezionare un nuovo servizio cloud nell'interfaccia utente o di fare riferimento a [ripristinare le considerazioni](backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) per macchine virtuali con configurazioni di rete speciali</ol> |
| Ripristino | Il nome DNS selezionato è già in uso. Specificare un nome DNS diverso e riprovare. | Il nome DNS fa riferimento al nome del servizio cloud, che in genere termina con .cloudapp.net. Questo nome deve essere univoco. Se si verifica questo errore, è necessario scegliere un altro nome di macchina virtuale durante il ripristino. <br><br> Si noti che questo errore viene visualizzato solo dagli utenti del portale di Azure. L'operazione di ripristino tramite PowerShell riuscirà perché ripristina solo i dischi e non crea la macchina virtuale. L'errore viene restituito quando la macchina virtuale viene creata in modo esplicito dall'utente dopo l'operazione di ripristino dei dischi. |
| Ripristino | La configurazione di rete virtuale specificata non è corretta. Specificare un'altra configurazione di rete virtuale e riprovare. | Nessuno |
| Ripristino | Il servizio cloud specificato usa un indirizzo IP riservato che non corrisponde alla configurazione della macchina virtuale in fase di ripristino. Specificare un altro servizio cloud che non usa un indirizzo IP riservato o scegliere un altro punto di ripristino da cui eseguire l'operazione. | Nessuno |
| Ripristino | Il servizio cloud ha raggiunto il limite consentito per il numero di endpoint di input. Ripetere l'operazione specificando un altro servizio cloud o usando un endpoint esistente. | Nessuno |
| Ripristino | L'insieme di credenziali di backup e l'account di archiviazione di destinazione si trovano in due aree diverse. Assicurarsi che l'account di archiviazione specificato nell'operazione di ripristino si trovi nella stessa area di Azure dell'insieme di credenziali di backup. | Nessuno |
| Ripristino | L'account di archiviazione specificato per l'operazione di ripristino non è supportato. Sono supportati solo gli account di archiviazione dei piani Basic/Standard con le impostazioni di replica con ridondanza locale o ridondanza geografica. Selezionare un account di archiviazione supportato | Nessuno |
| Ripristino | Il tipo di account di archiviazione specificato per l'operazione di ripristino non è online. Assicurarsi che l'account di archiviazione specificato nell'operazione di ripristino sia online | Questo problema può essere provocato da un errore temporaneo nel servizio di archiviazione di Azure o da un'interruzione del servizio. Scegliere un altro account di archiviazione. |
| Ripristino | È stata raggiunta la quota del gruppo di risorse. Eliminare alcuni gruppi di risorse dal portale di anteprima o contattare il supporto tecnico di Azure per richiedere un aumento dei limiti. | Nessuno |
| Ripristino | La subnet selezionata non esiste. Selezionare una subnet esistente | Nessuno |


## Criterio
| Operazione | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Crea criteri | Non è stato possibile creare i criteri. Per continuare con la configurazione dei criteri, ridurre le opzioni di conservazione. | Nessuno |


## Agente di macchine virtuali

### Configurazione dell'agente di VM
L'agente di VM è in genere già presente nelle VM create dalla raccolta di Azure. Nelle macchine virtuali di cui viene eseguita la migrazione da data center locali non è installato l'agente di VM. Per queste VM è necessario installare esplicitamente l'agente di VM. Altre informazioni sull'[installazione dell'agente di VM in una VM esistente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Per VM di Windows:

- Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari privilegi di amministratore.
- [Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato.

Per le macchine virtuali Linux:

- Installare l'[agente Linux](https://github.com/Azure/WALinuxAgent) più recente da github.
- [Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato.


### Aggiornamento dell'agente di VM
Per VM di Windows:

- L'aggiornamento dell'agente di VM è semplice quanto la reinstallazione dei [file binari dell'agente di VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). È tuttavia necessario assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente di VM.

Per le macchine virtuali Linux:

- Seguire le istruzioni in [Aggiornamento dell'agente di VM Linux](../virtual-machines-linux-update-agent.md).


### Convalida dell'installazione dell'agente di VM
Come controllare la versione dell'agente di VM nelle macchine virtuali di Windows:

1. Accedere alla macchina virtuale di Azure e passare alla cartella *C:\\WindowsAzure\\Packages*, che dovrebbe includere il file WaAppAgent.exe.
2. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà**, e quindi selezionare la scheda **Dettagli**. Il campo Versione prodotto deve essere 2.6.1198.718 o superiore.

## Rete
Analogamente a tutte le estensioni, per il funzionamento delle estensioni di Backup è necessario l'accesso a Internet pubblico. L'assenza di accesso a Internet pubblico può manifestarsi in diversi modi:

- Possono verificarsi errori di installazione dell'estensione.
- Possono verificarsi errori delle operazioni di backup, ad esempio lo snapshot del disco.
- Possono verificarsi errori di visualizzazione dello stato dell'operazione di backup.

La necessità di risolvere gli indirizzi Internet pubblici è stata illustrata [qui](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Sarà necessario controllare le configurazioni DNS per la rete virtuale e assicurarsi che sia possibile risolvere gli URI di Azure.

Dopo la corretta risoluzione dei nomi, sarà necessario fornire anche l'accesso agli IP di Azure. Per sbloccare l'accesso all'infrastruttura di Azure, eseguire la procedura seguente:

1. Aggiungere all'elenco elementi consentiti gli intervalli IP dei data center di Azure.
    - Ottenere l'elenco di [IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) da aggiungere all'elenco di IP consentiti.
    - Sbloccare gli IP mediante il cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx). Eseguire questo cmdlet all’interno della macchina virtuale di Azure, in una finestra di PowerShell con privilegi elevati, eseguita come amministratore.
    - Aggiungere regole al gruppo di sicurezza di rete (se esistente) per consentire l'accesso agli indirizzi IP.
2. Creare un percorso per il flusso del traffico HTTP
    - Se si dispone di alcune limitazioni di rete (un gruppo di sicurezza di rete, ad esempio) distribuire un server proxy HTTP per indirizzare il traffico. I passaggi per distribuire un server proxy HTTP sono reperibili [qui](backup-azure-vms-prepare.md#2-network-connectivity).
    - Aggiungere regole al gruppo di sicurezza di rete (se esistente) per consentire l'accesso a INTERNET dal proxy HTTP.

<!---HONumber=AcomDC_1217_2015-->
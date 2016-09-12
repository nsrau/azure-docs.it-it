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
	ms.date="08/26/2016"
	ms.author="trinadhk;jimpark;"/>


# Risolvere i problemi relativi al backup delle macchine virtuali di Azure

> [AZURE.SELECTOR]
- [Insieme di credenziali dei servizi di ripristino](backup-azure-vms-troubleshoot.md)
- [Insieme di credenziali per il backup](backup-azure-vms-troubleshoot-classic.md)

È possibile risolvere gli errori rilevati durante l'uso di Backup di Azure con le informazioni elencate nella tabella seguente.

## Backup

| Operazione di backup | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
|Backup|	Impossibile eseguire l'operazione perché la VM non esiste più. Arrestare la protezione della macchina virtuale senza eliminare i dati del backup. Per altre informazioni, vedere la pagina http://go.microsoft.com/fwlink/?LinkId=808124 |Questo errore si verifica quando viene rilevata la macchina virtuale primaria ma i criteri di backup continuano a cercare una macchina virtuale per il backup. Per correggere l'errore: <ol><li> Ricreare la macchina virtuale con lo stesso nome e lo stesso nome del gruppo di risorse [nome del servizio cloud],<br>(OPPURE)</li><li> Arrestare la protezione della macchina virtuale eliminando o senza eliminare i dati del backup. [Altre informazioni](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Backup|Non è stato possibile comunicare con l'agente VM per lo stato dello snapshot. Assicurarsi che la VM abbia accesso a Internet. Aggiornare anche l'agente VM come indicato nella Guida alla risoluzione dei problemi all'indirizzo http://go.microsoft.com/fwlink/?LinkId=800034 |Questo errore viene generato se si verifica un problema con l'agente di VM o se l'accesso di rete all'infrastruttura di Azure è bloccato in qualche modo. Vedere altre informazioni sul debug dei problemi di snapshot della VM.<br> Se l'agente VM non causa alcun problema, riavviare la VM. Talvolta, uno stato della macchina virtuale non corretto genera problemi che vengono corretti riavviando la macchina virtuale.|
|Backup|	Operazione di estensione dei servizi di ripristino non riuscita. Assicurarsi che nella macchina virtuale sia presente l'agente di macchine virtuali più recente e che il servizio agente sia in esecuzione. Ripetere l'operazione di backup e, in caso di esito negativo, contattare il supporto tecnico Microsoft.|	Questo errore viene generato quando l'agente VM non è aggiornato. Per aggiornare l'agente VM, fare riferimento alla sezione "Aggiornamento dell'agente VM" riportata di seguito.|
|Backup |La macchina virtuale non esiste. Verificare che la macchina virtuale sia presente o selezionarne un'altra. | Questo errore si verifica quando viene rilevata la macchina virtuale primaria ma i criteri di backup continuano a cercare una macchina virtuale per il backup. Per correggere l'errore: <ol><li> Ricreare la macchina virtuale con lo stesso nome e lo stesso nome del gruppo di risorse [nome del servizio cloud],<br>(OPPURE)<br></li><li>Arrestare la protezione della macchina virtuale senza eliminare i dati del backup. [Altre informazioni](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Backup |L'esecuzione del comando non è riuscita. In questo elemento è attualmente in corso un'altra operazione. Attendere il completamento dell'operazione precedente, quindi riprovare. |È in esecuzione un processo di backup o ripristino esistente per la VM e non è possibile avviare un nuovo processo mentre è in esecuzione il processo esistente.|
| Backup | Si è verificato il timeout della copia dei dischi rigidi virtuali dall'insieme di credenziali per il backup. Attendere qualche minuto prima di ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. | Questo errore si verifica quando la quantità di dati da copiare è eccessiva. Controllare se sono disponibili meno di 16 dischi dati. |
| Backup | Il backup non è riuscito e si è verificato un errore interno. Attendere qualche minuto prima di ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. | Questo problema può verificarsi per 2 ragioni: <ol><li> C'è un problema temporaneo nell'accesso all'archiviazione della VM. Verificare lo [stato di Azure](https://azure.microsoft.com/status/) per vedere se esiste un problema in corso relativo a calcolo/archiviazione/rete nell'area. Riprovare il backup dopo aver risolto il problema. <li>La VM originale è stata eliminata e non è quindi possibile eseguire il backup. Per mantenere i dati di backup per una VM eliminata, ma evitare gli errori di backup, annullare la protezione della VM e scegliere l'opzione relativa alla conservazione dei dati. La pianificazione del backup verrà interrotta e non verranno più visualizzati i messaggi di errore ricorrenti. |
| Backup | Non è stato possibile installare l'estensione Servizi di ripristino di Azure nell'elemento selezionato. L'agente di VM è un prerequisito dell'estensione Servizi di ripristino di Azure. Installare l'agente di VM di Azure e riavviare l'operazione di registrazione. | <ol> <li>Controllare se l'agente di VM è stato installato correttamente. <li>Assicurarsi che il flag sul file di configurazione della VM sia impostato correttamente.</ol> [Altre informazioni](#validating-vm-agent-installation) sull'installazione dell'agente di VM e su come convalidare l'installazione dell'agente di VM. |
| Backup | L'installazione dell'estensione non è riuscita con un errore che indica che il servizio COM+ non può comunicare con Microsoft Distributed Transaction Coordinator | Questo errore in genere indica che il servizio COM+ non è in esecuzione. Per informazioni su come correggere l'errore, contattare il supporto tecnico Microsoft. |
| Backup | L'operazione di snapshot non è riuscita con un errore dell'operazione del Servizio Copia Shadow del volume che indica che l'unità è bloccata da Crittografia unità BitLocker. Sbloccare l'unità dal Pannello di controllo. | Disattivare BitLocker per tutte le unità della macchina virtuale e verificare se il problema relativo al Servizio Copia Shadow del volume è stato risolto |
| Backup | Le macchine virtuali con dischi rigidi virtuali archiviati nel servizio di archiviazione Premium non sono supportate per il backup | None |
| Backup | La macchina virtuale di Azure non è stata trovata. | Questo errore si verifica quando viene rilevata la macchina virtuale primaria ma i criteri di backup continuano a cercare una macchina virtuale per il backup. Per correggere l'errore: <ol><li>Ricreare la macchina virtuale con lo stesso nome e lo stesso nome del gruppo di risorse [nome del servizio cloud] <br>(OPPURE) <li> Disabilitare la protezione per questa macchina virtuale in modo che non vengano creati i processi di backup </ol> |
| Backup | L'agente di macchine virtuali non è presente nella macchina virtuale. Installare i prerequisiti necessari, l'agente della macchina virtuale e ripetere l'operazione. | [Altre informazioni](#vm-agent) sull'installazione dell'agente di VM e su come convalidare l'installazione dell'agente di VM. |

## Processi

| Operazione | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Annulla processo | L'annullamento non è supportato per questo tipo di processo. Attendere il completamento del processo. | None |
| Annulla processo | Il processo non si trova in uno stato annullabile. Attendere il completamento del processo. <br>OPPURE<br> Il processo selezionato non si trova in uno stato annullabile. Attendere il completamento del processo.| È probabile che il processo sia quasi completato. Attendere il completamento del processo. |
| Annulla processo | Non è possibile annullare il processo perché non è in corso. L'annullamento è supportato solo per i processi in corso. Tentare l'annullamento solo per un processo in corso. | Questo errore è causato da uno stato temporaneo. Attendere un minuto e ripetere l'operazione di annullamento. |
| Annulla processo | Non è stato possibile annullare il processo. Attendere il completamento del processo. | None |


## Ripristino
| Operazione | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Ripristino | Ripristino non riuscito con errore interno del cloud | <ol><li>Il servizio cloud in cui si sta tentando di eseguire il ripristino è configurato con le impostazioni DNS. Verificare <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br>Se è presente un indirizzo configurato, le impostazioni DNS sono configurate.<br> <li>Il servizio cloud che si sta tentando di ripristinare è configurato con ReservedIP e le macchine virtuali che si trovano nel servizio cloud sono state arrestate.<br>È possibile verificare che un servizio cloud abbia un IP riservato tramite i cmdlet di PowerShell seguenti:<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>Si sta tentando di ripristinare una macchina virtuale con le configurazioni di rete speciali seguenti nello stesso servizio cloud. <br>- Macchine virtuali nella configurazione del servizio di bilanciamento del carico (interno ed esterno)<br>- Macchine virtuali con più indirizzi IP riservati<br>- Macchine virtuali con più schede di rete<br>Selezionare un nuovo servizio cloud nell'interfaccia utente o vedere le [considerazioni di ripristino](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations) per macchine virtuali con configurazioni di rete speciali</ol> |
| Ripristino | Il nome DNS selezionato è già in uso. Specificare un nome DNS diverso e riprovare. | Il nome DNS fa riferimento al nome del servizio cloud, che in genere termina con .cloudapp.net. Questo nome deve essere univoco. Se si verifica questo errore, è necessario scegliere un altro nome di macchina virtuale durante il ripristino. <br><br> Questo errore viene visualizzato solo dagli utenti del portale di Azure. L'operazione di ripristino tramite PowerShell riuscirà perché ripristina solo i dischi e non crea la macchina virtuale. L'errore viene restituito quando la macchina virtuale viene creata in modo esplicito dall'utente dopo l'operazione di ripristino dei dischi. |
| Ripristino | La configurazione di rete virtuale specificata non è corretta. Specificare un'altra configurazione di rete virtuale e riprovare. | None |
| Ripristino | Il servizio cloud specificato usa un indirizzo IP riservato che non corrisponde alla configurazione della macchina virtuale in fase di ripristino. Specificare un altro servizio cloud che non usa un indirizzo IP riservato o scegliere un altro punto di ripristino da cui eseguire l'operazione. | None |
| Ripristino | Il servizio cloud ha raggiunto il limite consentito per il numero di endpoint di input. Ripetere l'operazione specificando un altro servizio cloud o usando un endpoint esistente. | None |
| Ripristino | L'insieme di credenziali di backup e l'account di archiviazione di destinazione si trovano in due aree diverse. Assicurarsi che l'account di archiviazione specificato nell'operazione di ripristino si trovi nella stessa area di Azure dell'insieme di credenziali di backup. | None |
| Ripristino | L'account di archiviazione specificato per l'operazione di ripristino non è supportato. Sono supportati solo gli account di archiviazione dei piani Basic/Standard con le impostazioni di replica con ridondanza locale o ridondanza geografica. Selezionare un account di archiviazione supportato | None |
| Ripristino | Il tipo di account di archiviazione specificato per l'operazione di ripristino non è online. Assicurarsi che l'account di archiviazione specificato nell'operazione di ripristino sia online | Questo problema può essere provocato da un errore temporaneo nel servizio di archiviazione di Azure o da un'interruzione del servizio. Scegliere un altro account di archiviazione. |
| Ripristino | È stata raggiunta la quota del gruppo di risorse. Eliminare alcuni gruppi di risorse dal portale di Azure o contattare il supporto tecnico di Azure per richiedere un aumento dei limiti. | None |
| Ripristino | La subnet selezionata non esiste. Selezionare una subnet esistente | None |


## Criterio
| Operazione | Dettagli errore | Soluzione alternativa |
| -------- | -------- | -------|
| Crea criteri | Non è stato possibile creare i criteri. Per continuare con la configurazione dei criteri, ridurre le opzioni di conservazione. | None |


## Agente di macchine virtuali

### Configurazione dell'agente di VM
L'agente di VM è in genere già presente nelle VM create dalla raccolta di Azure. Nelle macchine virtuali di cui viene eseguita la migrazione da data center locali non è installato l'agente di VM. Per queste VM è necessario installare esplicitamente l'agente di VM. Altre informazioni sull'[installazione dell'agente di VM in una VM esistente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Per VM di Windows:

- Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari privilegi di amministratore.
- [Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato.

Per le macchine virtuali Linux:

- Installare l'[agente Linux](https://github.com/Azure/WALinuxAgent) più recente da Github.
- [Aggiornare le proprietà della VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato.


### Aggiornamento dell'agente di VM
Per VM di Windows:

- L'aggiornamento dell'agente di VM è semplice quanto la reinstallazione dei [file binari dell'agente di VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). È tuttavia necessario assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente di VM.

Per le macchine virtuali Linux:

- Seguire le istruzioni in [Come aggiornare l'agente Linux di Azure su una macchina virtuale per la versione più recente da GitHub](../virtual-machines/virtual-machines-linux-update-agent.md).


### Convalida dell'installazione dell'agente di VM
Come controllare la versione dell'agente di VM nelle macchine virtuali di Windows:

1. Accedere alla macchina virtuale di Azure e passare alla cartella *C:\\WindowsAzure\\Packages*, che dovrebbe includere il file WaAppAgent.exe.
2. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo Versione prodotto deve essere 2.6.1198.718 o superiore.

## Risoluzione dei problemi relativi allo snapshot della macchina virtuale
Il backup delle macchine virtuali si basa sull'esecuzione del comando di snapshot sull'archiviazione sottostante. Non disporre dell'accesso all'archiviazione o il ritardo nell'esecuzione dell'attività dello snapshot può causare il fallimento del backup. I seguenti elementi possono causare il fallimento dell'attività di backup.

1. L'accesso alla rete per l'archiviazione è bloccato mediante NSG<br> Vedere altre informazioni su come [abilitare l'accesso alla rete](backup-azure-vms-prepare.md#2-network-connectivity) per l'archiviazione tramite gli elenchi di indirizzi IP consentiti o server proxy.
2.  Le VM con il backup di SQL Server configurato possono causare ritardi nelle attività di snapshot <br> Per impostazione predefinita, il backup delle VM genera un backup VSS completo sulle VM Windows. In VM che eseguono SQL Server e in cui è configurato il backup di SQL Server, potrebbero verificarsi ritardi nell'esecuzione di snapshot. Impostare la seguente chiave del Registro di sistema se si verificano errori di backup a causa di problemi di snapshot.

	```
	[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
	"USEVSSCOPYBACKUP"="TRUE"
	```
3.  Stato della VM segnalato in modo non corretto poiché la VM viene arrestata in RDP. <br> Se la macchina virtuale si è fermata in RDP, verificare nel portale che lo stato della VM venga indicato correttamente. In caso contrario, arrestare la VM nel portale tramite l'opzione ''Shutdown'' nel dashboard della VM.
4.  Se più di quattro VM condividono lo stesso servizio cloud, configurare criteri di backup multipli per organizzare i tempi di backup in modo che non vengano eseguiti più di quattro backup delle VM allo stesso tempo. Provare a distribuire i tempi di avvio del backup ogni ora tra i criteri.
5.  La VM è in esecuzione con un uso elevato di CPU/memoria.<br> Se la macchina virtuale è in esecuzione con un uso elevato della CPU (> 90%) o della memoria, l'attività di snapshot viene messa in coda, ritardata e infine messa in timeout. In tali situazioni, provare a eseguire un backup su richiesta.

<br>

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

>[AZURE.NOTE] DHCP deve essere abilitato nel computer guest per consentire il funzionamento del backup delle VM IaaS. Se è necessario un indirizzo IP privato statico, è necessario configurarlo tramite la piattaforma. L'opzione DHCP all'interno della VM deve essere abilitata. Vedere altre informazioni sull'[impostazione di un indirizzo IP privato interno statico](../virtual-network/virtual-networks-reserved-private-ip.md).

<!---HONumber=AcomDC_0831_2016-->
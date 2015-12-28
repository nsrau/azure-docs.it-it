<properties
	pageTitle="Failover in Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="raynew"/>

# Failover in Site Recovery

Il [servizio Azure Site Recovery](site-recovery-overview.md) fornisce un’efficace soluzione di ripristino di emergenza e continuità aziendale che consente di proteggere le macchine virtuali e i server fisici locali orchestrando e automatizzando la replica e il failover in Azure o in un centro dati locale secondario. In questo articolo vengono fornite informazioni e istruzioni per il ripristino (failover e failback) di macchine virtuali e server fisici protetti con Site Recovery.

In caso di domande dopo la lettura di questo articolo, è possibile pubblicarle nel [forum relativo a Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Panoramica

Dopo aver configurato la protezione per le macchine virtuali e i server fisici, inizia la replica nella posizione secondaria. Una volta completata la replica, è possibile eseguire i failover in base alla necessità. Site Recovery supporta diversi tipi di failover.

**Failover** | **Quando eseguire** | **Dettagli** | **Processo**
---|---|---|---
**Failover di test** | Eseguire per convalidare la strategia di replica o per eseguire un'esercitazione per il ripristino d'emergenza | <p>Senza perdita di dati e tempi di inattività</p><p>Nessun impatto sulla replica</p><p>Nessun impatto sull'ambiente di produzione</p> | <p>Avviare il failover</p><p>Specificare la modalità di connessione delle macchine di test alle reti dopo il failover</p><p>Tenere traccia dell'avanzamento sulla scheda **Processi**. Le macchine di test vengono create e avviate nella posizione secondaria</p><p>Azure: connettersi alla macchina nel portale di Azure</p><p>Sito secondario: accedere alla macchina nello stesso host e cloud</p><p>Completare il test e rimuovere automaticamente le impostazioni del failover di test.</p>
**Failover pianificato** | <p>Eseguire per soddisfare i requisiti di conformità</p><p>Eseguire per la manutenzione pianificata</p><p>Eseguire per effettuare il failover dai dati per mantenere i carichi di lavoro in esecuzione per le interruzioni note, ad esempio un'interruzione dell'alimentazione previsto o bollettini meteo negativi</p> <p>Eseguire per effettuare il failback dopo il failover da primario a secondario | <p>Nessuna perdita di dati</p><p>Tempi di inattività dovuti all’arresto della macchina virtuale nel server primario e all’attivazione nella posizione secondaria.</p><p>Impatto sulle macchine virtuali quando le macchine di destinazione diventano macchine di origine dopo il failover.</p> | <p>Avviare il failover</p><p>Tenere traccia dell’avanzamento nella scheda **Processi**. Le macchine di origine vengono arrestate</p><p>Le macchine di replica vengono avviate nella posizione secondaria</p><p>Azure: connettersi alla macchina di replica nel portale di Azure</p><p>Sito secondario: accedere alla macchine sullo stesso host e nello stesso cloud</p><p>Eseguire il commit del failover</p>
**Failover non pianificato** | <p>Eseguire questo tipo di failover in modo reattivo quando un sito primario diventa inaccessibile a causa di un evento imprevisto, ad esempio un’interruzione dell’alimentazione o un attacco di virus</p><p>È possibile eseguire un failover non pianificato anche se il sito primario non è disponibile.<p> | <p>Perdita di dati dipendente dalle impostazioni di frequenza di replica</p> <p>I dati verranno aggiornati secondo l'ultima sincronizzazione</p> | <p>Avviare il failover</p><p>Tenere traccia dell’avanzamento nella scheda **Processi**. Facoltativamente, provare ad arrestare le macchine virtuali e a sincronizzare gli ultimi dati</p><p>Le macchine di replica vengono avviate nella posizione secondaria</p><p>Azure: connettersi alla macchina di replica nel portale di Azure</p><p>Accedere dal sito secondario alla macchina sullo stesso host e nello stesso cloud</p><p>Eseguire il commit del failover</p>


I tipi di failover supportati dipendono dallo scenario di distribuzione.

**Direzione del failover** | **Failover di test** | **Failover pianificato** | **Failover non pianificato**
---|---|---|---
Da sito VMM primario a sito VMM | Supportato | Supportato | Supportato 
Da sito VMM secondario a sito VMM primario | Supportato | Supportato | Supportato
Da cloud a cloud (singolo server VMM) | Supportato | Supportato | Supportato
Da sito VMM ad Azure | Supportato | Supportato | Supportato 
Da Azure a sito VMM | Non supportato | Supportato | Non supportato 
Da sito Hyper-V ad Azure | Supportato | Supportato | Supportato
Da Azure a sito Hyper-V | Non supportato | Supportato | Non supportato
Da sito VMware ad Azure | Non supportato |In questo scenario viene utilizzata la replica continua per cui non esiste alcuna distinzione tra failover pianificato e non pianificato. Si seleziona **Failover** | ND
Da server fisico ad Azure | Non supportato | In questo scenario viene utilizzata la replica continua per cui non esiste alcuna distinzione tra failover pianificato e non pianificato. Si seleziona **Failover** | ND

## Failover e failback

Si esegue il failover delle macchine virtuali in un sito secondario in locale o in Azure, a seconda della distribuzione. Un computer che esegue il failover in Azure viene creato come macchina virtuale di Azure. È possibile eseguire il failover di una singola macchina virtuale o server fisico o un piano di ripristino. Il piano di ripristino è costituito da uno o più gruppi ordinati che contengono macchine virtuali o server fisici protetti. Vengono utilizzati per orchestrare il failover di più macchine che eseguono il failover in base al gruppo a cui appartengono. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

Dopo aver completato il failover, quando le macchine sono attive e in esecuzione in una posizione secondaria, si noti che:

- Se si è eseguito il failover in Azure, dopo il failover le macchine non sono protette e non eseguono la replica nella posizione primaria o secondaria. In Azure le macchine virtuali vengono archiviate in risorse di archiviazione replicate geograficamente, che forniscono la resilienza, ma non la replica.
- Se è stato eseguito un failover non pianificato in un sito secondario, dopo il failover le macchine della posizione secondaria non sono protette e non eseguono la replica.
- Se è stato eseguito un failover pianificato in un sito secondario, dopo il failover le macchine della posizione secondaria sono protette.
 

### Failback dal sito secondario

Il failback da un sito secondario a un sito primario utilizza lo stesso processo del failover da sito primario a secondario. Dopo aver eseguito il commit e completato il failover dal centro dati primario a quello secondario, è possibile avviare la replica inversa quando il sito primario diventa disponibile. La replica inversa avvia la replica tra il sito secondario e primario sincronizzando i dati differenziali. La replica inversa porta le macchine virtuali in uno stato protetto, ma il centro dati secondario resta la posizione attiva. Per rendere il sito primario la posizione attiva, occorre avviare un failover pianificato da sito secondario a primario, seguito da un'altra replica inversa.

### Failback da Azure

Se è stato eseguito il failover in Azure le macchine virtuali sono protette dalle funzionalità di resilienza di Azure per le macchine virtuali. Per rendere il sito primario originale la posizione attiva, occorre eseguire un failover pianificato. Se il sito originale non è disponibile, è possibile eseguire il failback nella posizione originale o in una posizione alternativa. Per avviare di nuovo la replica dopo il failback nella posizione primaria è possibile avviare la replica inversa.

### Considerazioni sul failover

- **Indirizzo IP dopo il failover**: per impostazione predefinita, una macchina di failover avrà un indirizzo IP diverso dal computer di origine. Se si desidera mantenere lo stesso indirizzo IP, vedere: 
	- **Sito secondario**: se si esegue il failover a un sito secondario e si desidera mantenere un indirizzo IP, [leggere](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) questo articolo. Si noti che, se il provider di servizi Internet lo consente, è possibile mantenere un indirizzo IP pubblico.
	- **Azure**: se si esegue il failover in Azure è possibile specificare l'indirizzo IP che si desidera assegnare nella scheda **Configura** delle proprietà della macchina virtuale. Non è possibile conservare un indirizzo IP pubblico dopo il failover ad Azure. È possibile mantenere gli spazi degli indirizzi RFC 1918 utilizzati come indirizzi interni.
- **Failover parziale**: se si desidera eseguire il failover di parte di un sito anziché di un intero sito, si noti che: 
	- **Sito secondario**: se si esegue il failover di parte di un sito primario a un sito secondario e si desidera riconnettersi al sito primario, utilizzare una connessione VPN da sito a sito per collegare le applicazioni del failover nel sito secondario con i componenti infrastrutturali in esecuzione nel sito primario. In caso di failover di un'intera subnet, è possibile mantenere l'indirizzo IP della macchina virtuale. Se si esegue il failover su una subnet parziale, non è possibile mantenere l'indirizzo IP della macchina virtuale perché le subnet non possono essere suddivise tra siti.
	- **Azure**: se si esegue il failover parziale di un sito in Azure e si desidera riconnettersi al sito primario, è possibile utilizzare una VPN da sito a sito per la connessione di un’applicazione di failover in Azure ai componenti dell'infrastruttura in esecuzione nel sito primario. Si noti che in caso di failover dell'intera subnet, è possibile conservare l'indirizzo IP della macchina virtuale. Se si esegue il failover su una subnet parziale, non è possibile mantenere l'indirizzo IP della macchina virtuale perché le subnet non possono essere suddivise tra siti.
 
- **Lettera di unità**: se si desidera mantenere la lettera di unità nelle macchine virtuali dopo il failover è possibile impostare il criterio SAN per la macchina virtuale su **Attivo**. I dischi delle macchine virtuali passano automaticamente online. [Altre informazioni](https://technet.microsoft.com/library/gg252636.aspx).
- **Instradare le richieste client**: Site Recovery funziona con Gestione traffico di Azure per indirizzare le richieste client all'applicazione dopo il failover.




## Eseguire un failover di test

Quando si esegue un failover di test viene chiesto di selezionare le impostazioni di rete per le macchine di replica di test. Si dispone di una serie di opzioni.

**Opzione di failover di test** | **Descrizione** | **Controllo del failover** | **Dettagli**
---|---|---|---
**Eseguire il failover in Azure, senza rete** | Non selezionare una rete di destinazione di Azure | Il failover controlla che la macchina virtuale venga avviata come previsto in Azure | <p>Tutte le macchine virtuali di test di un piano di ripristino vengono aggiunte in un singolo servizio cloud e possono connettersi tra loro</p><p>Le macchine non sono connesse a una rete di Azure dopo il failover.</p><p>Gli utenti possono connettersi alle macchine di test con un indirizzo IP pubblico</p>
**Eseguire il failover in Azure, con rete** | Selezionare una rete di destinazione di Azure | Il failover controlla che le macchine di test siano connesse alla rete | <p>Creare una rete di Azure isolata dalla rete di produzione di Azure e configurare l'infrastruttura per la macchina virtuale replicata in modo che funzioni come previsto.</p><p>La subnet della macchina virtuale di test è basata sulla subnet in cui la macchina virtuale di failover deve connettersi in caso di failover pianificato o non pianificato.</p>
**Eseguire il failover a un sito VMM secondario, senza rete** | Non selezionare una rete VM | Il failover controlla che le macchine di test vengano create <p>La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.</p></p>| <p>La macchina di failover non sarà connessa ad alcuna rete.</p><p>La macchina può essere connessa a una rete VM dopo che è stata creata</p>
**Eseguire il failover a un sito VMM secondario, con rete** | Selezionare una rete VM esistente | Il failover controlla che le macchine virtuali vengono create | <p>La macchina virtuale di test verrà creata sullo stesso host in cui è presente la macchina virtuale di replica, non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.</p><p>Creare una rete VM isolata dalla rete di produzione</p><p>Se si utilizza una rete basata su VLAN, è consigliabile creare una rete logica separata (non utilizzata nell'ambiente di produzione) in VMM per questo scopo. Questa rete logica viene utilizzata per creare reti VM a scopo di failover di test.</p><p>La rete logica deve essere associata almeno a una delle schede di rete di tutti i server Hyper-V che ospitano macchine virtuali.</p><p>Per le reti logiche VLAN, i siti della rete aggiunti alla rete logica devono essere isolati.</p><p>Se si utilizza una rete logica basata su virtualizzazione rete Windows, Azure Site Recovery crea automaticamente reti VM isolate.</p>
**Eseguire il failover a un sito VMM secondario, creare una rete** | Una rete di test temporanea verrà creata automaticamente in base all'impostazione specificata in **Rete logica** e ai relativi siti di rete | Il failover controlla che le macchine virtuali vengono create | <p>Utilizzare questa opzione se il piano di ripristino utilizza più reti VM. Se si utilizzano reti di virtualizzazione rete Windows, è possibile utilizzare questa opzione per creare automaticamente reti VM con le stesse impostazioni (subnet e pool di indirizzi IP) specificate nella rete della macchina virtuale di replica. Queste reti VM verranno eliminate automaticamente al termine del failover di test.</p><p>La macchina virtuale di test verrà creata nello stesso host in cui si trova la macchina virtuale di replica, non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.</p>

>[AZURE.NOTE]L'indirizzo IP assegnato a una macchina virtuale in un failover di test corrisponde all'indirizzo IP che otterrebbe nel caso di un failover pianificato o non pianificato, a condizione che questo indirizzo IP sia disponibile nella rete di failover di test. Se lo stesso indirizzo IP non è disponibile nella rete di failover di test, la macchina virtuale otterrà un altro indirizzo IP disponibile in tale rete.



### Eseguire un failover di test da locale ad Azure

Questa procedura descrive come eseguire un failover di test per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda **Macchine virtuale**.

1. Selezionare **Piani di ripristino** > *nome\_pianodiripristino*. Fare clic su **Failover** > **Failover di test**.
2. Nella pagina **Conferma failover di test** selezionare il modo in cui le macchine di replica saranno connesse a una rete di Azure dopo il failover.
3. Se si esegue il failover in Azure e la crittografia dei dati è abilitata per il cloud in **Chiave di crittografia**, selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione di Provider. 
4. Tenere traccia dello stato di avanzamento del failover nella scheda **Processi**. Nel portale di Azure dovrebbe anche essere possibile vedere la macchina di replica di test.
5. È possibile accedere alle macchine di replica in Azure dal sito locale avviando una connessione RDP alla macchina virtuale. La porta 3389 dovrà essere aperta nell'endpoint per la macchina virtuale.
5. Quando il processo di failover raggiunge la fase **conclusiva del test**, fare clic su **Completa test** per completare il test.
5. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.
8. Fare clic su **Failover di test completato** per pulire automaticamente l'ambiente di test. Dopo aver completato il failover di test verrà visualizzato lo stato C**ompletato**.

> [AZURE.NOTE]Se un test di failover continua per più di due settimane, verrà completato forzatamente. Eventuali elementi o macchine virtuali create automaticamente durante il failover di test verranno eliminate.
  

### Eseguire un failover di test da un sito primario locale a un sito secondario locale

È necessario effettuare una serie di operazioni per eseguire un failover di test, tra cui la copia del controller di dominio e il posizionamento di server DHCP e DNS di test nell'ambiente di test. È possibile procedere in due modi:

- Se si desidera eseguire un failover di test utilizzando una rete esistente, preparare Active Directory, DHCP e DNS in tale rete.
- Se si desidera eseguire un failover di test utilizzando l'opzione per creare automaticamente reti VM, aggiungere un passaggio manuale prima del gruppo 1 del piano di ripristino che si intende utilizzare per il failover di test e quindi aggiungere le risorse dell'infrastruttura alla rete creata automaticamente prima di eseguire il failover di test.

#### Punti da notare

- In caso di replica a un sito secondario, il tipo di rete utilizzato dalla macchina di replica non deve necessariamente corrispondere al tipo di rete logica utilizzato per il failover di test, ma alcune combinazioni potrebbero non funzionare. Se la replica utilizza DHCP e isolamento basato su VLAN, non è necessario un pool di indirizzi IP statici per la rete VM per la replica. Di conseguenza, l’uso della virtualizzazione di rete Windows per il failover di test non funziona perché non è disponibile alcun pool di indirizzi. Il failover di test, inoltre, non funzionerà se la rete di replica è senza isolamento e la rete di test è basata sulla virtualizzazione rete Windows. Questo accade perché la rete senza isolamento non dispone delle subnet necessarie per creare una rete di virtualizzazione rete Windows.
- Il modo in cui le macchine virtuali di replica sono connesse alle reti VM mappate dopo il failover dipende dalla configurazione della rete VM nella console VMM:
	- **Rete VM configurata senza isolamento o isolamento VLAN**: se DHCP è definito per la rete VM, la macchina virtuale di replica verrà connessa all'ID VLAN utilizzando le impostazioni specificate per il sito di rete nella rete logica associata. La macchina virtuale riceverà il relativo indirizzo IP dal server DHCP disponibile. Non è necessario un pool di indirizzi IP statico definito per la rete VM di destinazione. Se un indirizzo IP statico viene utilizzato per la rete VM, la macchina virtuale di replica verrà connessa all'ID VLAN utilizzando le impostazioni specificate per il sito di rete nella rete logica associata. La macchina virtuale riceverà il relativo indirizzo IP dal pool definito per la rete VM. Se non è definito un pool di indirizzi IP statici nella rete VM di destinazione, l'allocazione di indirizzi IP avrà esito negativo. Il pool di indirizzi IP deve essere creato nei server VMM di origine e di destinazione che si desidera utilizzare per la protezione e il ripristino.
	- **Rete VM con virtualizzazione rete Windows**: se una rete VM è configurata con questa impostazione, è necessario definire un pool statico per la rete VM di destinazione, indipendentemente dal fatto che la rete VM di origine sia configurata per utilizzare DHCP o un pool di indirizzi IP statici. Se si definisce DHCP, il server VMM di destinazione verrà utilizzato come server DHCP e fornirà un indirizzo IP dal pool definito per la rete VM di destinazione. Se per il server di origine è definito l’uso di un pool di indirizzi IP statici, il server VMM di destinazione allocherà un indirizzo IP dal pool. In entrambi i casi, l'allocazione di indirizzi IP non riuscirà se non è definito un pool di indirizzi IP statici.

#### Eseguire test

Questa procedura descrive come eseguire un failover di test per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina virtuale o server fisico nella scheda **Macchine virtuali**.

1. Selezionare **Piani di ripristino** > *nome\_pianodiripristino*. Fare clic su **Failover** > **Failover di test**.
2. Nella pagina **Conferma failover di test** specificare il modo in cui le macchine virtuali devono essere connesse alle reti al termine del failover di test.
3. Tenere traccia dello stato di avanzamento del failover nella scheda **Processi**. Quando il processo di failover raggiunge la fase conclusiva del test, fare clic su **Completa test** per terminare il failover di test.
4. Fare clic su **Note** per registrare e salvare eventuali commenti associati al failover di test.
4. Successivamente, verificare che le macchine virtuali vengano avviate correttamente
5. Dopo la verifica dell'avvio corretto delle macchine virtuali, completare il failover di test per eliminare gli elementi inclusi nell'ambiente isolato. Se è stata selezionata l'opzione per la creazione automatica delle reti VM, verranno eliminate tutte le macchine virtuali di test e le reti di test.

> [AZURE.NOTE]Se un test di failover continua per più di due settimane, verrà completato forzatamente. Eventuali elementi o macchine virtuali create automaticamente durante il failover di test verranno eliminate.


#### Preparare DHCP

Se le macchine virtuali coinvolte nel failover di test utilizzano DHCP, un server DHCP di test deve essere creato all'interno della rete isolata creata ai fini del failover di test.


### Preparare Active Directory
Per eseguire un failover di test per testare l'applicazione, è necessaria una copia dell'ambiente Active Directory di produzione nell'ambiente di test. Per altri dettagli, vedere la sezione [Considerazioni sul failover di test per Active Directory](site-recovery-active-directory.md#considerations-for-test-failover]).


### Preparare DNS

Preparare un server DNS per il failover di test come segue:

- **DHCP**: se le macchine virtuali utilizzano DHCP, l'indirizzo IP del DNS di test deve essere aggiornato nel server DHCP di test. Se si utilizza un tipo di rete di virtualizzazione rete Windows, il server VMM funge da server DHCP. Pertanto, l'indirizzo IP del DNS deve essere aggiornato nella rete di failover di test. In questo caso, le macchine virtuali effettueranno la registrazione al relativo server DNS.
- **Indirizzo statico**: se le macchine virtuali usano un indirizzo IP statico, l'indirizzo IP del server DNS di test deve essere aggiornato nella rete di failover di test. Può essere necessario aggiornare il DNS con l'indirizzo IP delle macchine virtuali di test. A tale scopo, è possibile utilizzare lo script di esempio riportato di seguito: 

	    Param(
	    [string]$Zone,
	    [string]$name,
	    [string]$IP
	    )
	    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	    $newrecord = $record.clone()
	    $newrecord.RecordData[0].IPv4Address  =  $IP
	    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## Eseguire un failover pianificato (da primario a secondario)

 Questa procedura descrive come eseguire un failover pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda **Macchine virtuali**.

1. Prima di iniziare verificare che tutte le macchine virtuali di cui eseguire il failover abbiano completato la replica iniziale.
2. Selezionare **Piani di ripristino** > *nome\_pianodiripristino*. Fare clic su **Failover** > **Failover pianificato**. 
3. Nella pagina **Conferma failover pianificato**, selezionare il percorso di origine e destinazione. Prendere nota della direzione del failover.

	- Se il funzionamento dei failover è quello previsto e tutti i server di macchine virtuali si trovano nella posizione di origine o di destinazione, i dettagli della direzione del failover sono solo a scopo informativo. 
	- Se le macchine virtuali sono attive sia nella posizione di origine che in quella di destinazione, viene visualizzato il pulsante **Cambia direzione**. Utilizzare questo pulsante per modificare e specificare la direzione in cui deve essere eseguito il failover.

5. Se si esegue il failover in Azure e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
6. Quando si avvia un failover pianificato, il primo passaggio è l’arresto delle macchine virtuali per garantire che non si verifichino perdite di dati. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover. Se si verifica un errore nel failover (in una macchina virtuale o in uno script incluso nel piano di ripristino), il failover pianificato di un piano di ripristino si interrompe. È possibile avviare nuovamente il failover.
8. Dopo la replica le macchine virtuali create sono in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover. 
9. Una volta completata la replica, le macchine virtuali vengono avviate nella posizione secondaria. 

## Eseguire un failover non pianificato

Questa procedura descrive come eseguire un failover non pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina virtuale o server fisico nella scheda **Macchine virtuali**.

1. Selezionare **Piani di ripristino** > *nome\_pianodiripristino*. Fare clic su **Failover** > **Failover non pianificato**. 
3. Nella pagina **Conferma failover non pianificato**, selezionare il percorso di origine e destinazione. Prendere nota della direzione del failover.

	- Se il funzionamento dei failover è quello previsto e tutti i server di macchine virtuali si trovano nella posizione di origine o di destinazione, i dettagli della direzione del failover sono solo a scopo informativo. 
	- Se le macchine virtuali sono attive sia nella posizione di origine che in quella di destinazione, viene visualizzato il pulsante **Cambia direzione**. Utilizzare questo pulsante per modificare e specificare la direzione in cui deve essere eseguito il failover.

4. Se si esegue il failover in Azure e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
5. Selezionare **Arresta le macchine virtuali e sincronizza i dati più recenti** per specificare che Site Recovery deve tentare di arrestare le macchine virtuali protette e sincronizzare i dati in modo che venga eseguito il faiolver dei dati più recenti. Se non si seleziona questa opzione o il tentativo ha esito negativo, il failover avverrà dall'ultimo punto di ripristino disponibile per la macchina virtuale.
6. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover. Si noti che anche se si verificano errori durante un failover non pianificato, il piano di ripristino viene eseguito fino al completamento.
7. Dopo il failover le macchine virtuali sono uno stato di **attesa d commit**. Fare clic su **Commit** per eseguire il commit del failover.
8. Se si imposta la replica per l’uso di più punti di ripristino, in Modifica punto di ripristino è possibile scegliere di utilizzare un punto di ripristino che non sia la versione più recente (per impostazione predefinita viene utilizzata la versione più recente). Dopo il commit i punti di ripristino aggiuntivi verranno rimossi.
9. Una volta completata la replica, le macchine virtuali vengono avviate ed eseguite nella posizione secondaria. Tuttavia, non sono protette e non eseguono repliche. Quando il sito primario è di nuovo disponibile con la stessa infrastruttura sottostante, fare clic su **Replica inversa** per iniziare la replica inversa. Ciò garantisce che tutti i dati vengono replicati nel sito primario e che la macchina virtuale sia nuovamente pronta per il failover. La replica inversa dopo un failover non pianificato comporta un sovraccarico nel trasferimento dei dati. Il trasferimento utilizzerà lo stesso metodo configurato per le impostazioni di replica iniziale per il cloud.

## Failback da sito secondario a primario

 Dopo il failover dalla posizione primaria alla posizione secondaria, le macchine virtuali replicate non sono protette da Site Recovery e la posizione secondaria funge da posizione primaria. Utilizzare queste procedure per eseguire il failback al sito primario originale. Questa procedura descrive come eseguire un failover pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda **Macchine virtuali**.

1. Selezionare **Piani di ripristino** > *nome\_pianodiripristino*. Fare clic su **Failover** > **Failover pianificato**.
2. Nella pagina **Conferma failover pianificato**, selezionare il percorso di origine e destinazione. Prendere nota della direzione del failover. Se il failover dal sito primario funziona nel modo previsto e tutte le macchine virtuali si trovano nella posizione secondaria, la direzione ha solo scopo informativo.
3. Se si esegue il failback da Azure, selezionare le impostazioni in **Sincronizzazione dati**:

	- **Sincronizzare i dati prima del failover**: questa opzione riduce al minimo i tempi di inattività per le macchine virtuali senza arrestarle. Effettua le seguenti operazioni:
		- Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
		- Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.
	
	> [AZURE.NOTE]È consigliabile utilizzare questa opzione se Azure è in esecuzione da un po’ di tempo (un mese o più). Questa opzione esegue la sincronizzazione senza arrestare le macchine virtuali. Non esegue una risincronizzazione, ma un failback completo. Questa opzione non esegue alcun calcolo checksum.

	- **Sincronizzare i dati solo durante il failover**: utilizzare questa opzione se l’esecuzione in Azure è in corso da poco tempo. Questa opzione è più veloce perché esegue la risincronizzazione anziché il failback completo. Esegue un calcolo del checksum veloce e carica solo i blocchi modificati.

5. Se si esegue il failover in Azure e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
5. Per impostazione predefinita viene utilizzato l'ultimo punto di ripristino, ma in **Modifica punto di ripristino** è possibile specificare un punto di ripristino diverso. 
6. Fare clic sul segno di spunta per avviare il ripristino. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover. 
7. Se è stata selezionata l'opzione per sincronizzare i dati prima del failover, dopo la sincronizzazione dati iniziale, quando si è pronti ad arrestare le macchine virtuali in Azure, fare clic su **Processi** > <planned failover job name> **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata.
8. A questo punto è possibile accedere alla macchina virtuale per verificare che sia disponibile come previsto. 
9. La macchina virtuale è in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
10. Per completare il failback fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale nel sito primario.



## Failback in una posizione alternativa

Se è stata distribuita la protezione tra un [sito Hyper-V e Azure](site-recovery-hyper-v-site-to-azure.md), è possibile eseguire il failback da Azure a una posizione alternativa locale. Ciò è utile se è necessario configurare nuovo hardware locale. Di seguito viene indicato come procedere.

1. Se si configura nuovo hardware, installare Windows Server 2012 R2 e il ruolo Hyper-V nel server.
2. Creare un commutatore di rete virtuale con lo stesso nome presente nel server originale.
3. Selezionare **Elementi protetti** -> **Gruppo protezione dati** -> <ProtectionGroupName> -> <VirtualMachineName> di cui si desidera eseguire il failback e selezionare **Failover pianificato**.
4. In **Conferma failover pianificato** selezionare **Crea macchina virtuale locale, se non esiste**. 
5. In **Nome host** selezionare il nuovo server host Hyper-V in cui si desidera collocare la macchina virtuale.
6. In Sincronizzazione dati, è consigliabile selezionare l'opzione **Sincronizzare i dati prima del failover**. Questa opzione riduce al minimo i tempi di inattività per le macchine virtuali senza arrestarle. Effettua le seguenti operazioni:

	- Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
	- Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.
	
7. Fare clic sul segno di spunta per iniziare il failover (failback).
8. Una volta completata la sincronizzazione iniziale e si è pronti per arrestare la macchina virtuale in Azure, fare clic su **Processi** > <planned failover job> > **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata.
9. È possibile accedere alla macchina virtuale locale per verificare che tutto funzioni come previsto. Fare clic su **Commit** per completare il failover.
10. Fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale in locale.

 

<!---HONumber=AcomDC_1217_2015-->
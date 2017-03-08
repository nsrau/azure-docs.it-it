---
title: "Disponibilità elevata e ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni) | Documentazione Microsoft"
description: "Implementare la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 661733edbabd61b42bfb44b4ed107b1e757c2e28
ms.lasthandoff: 03/01/2017


---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure 

La disponibilità elevata e il ripristino di emergenza sono aspetti importanti per l'esecuzione di uno o più server SAP HANA di importanza critica in Azure (istanze di grandi dimensioni). Per progettare e implementare la corretta strategia di disponibilità elevata e ripristino di emergenza è fondamentale la collaborazione con SAP, l'integratore di sistemi o Microsoft. È importante anche prendere in considerazione l'obiettivo punto di ripristino e l'obiettivo tempo di ripristino, che sono specifici dell'ambiente.

## <a name="high-availability"></a>Disponibilità elevata

Microsoft supporta i metodi di disponibilità elevata "predefiniti" di SAP HANA, che comprendono:

- **Replica di archiviazione:** la capacità del sistema di archiviazione di replicare tutti i dati in un'altra posizione (all'interno dello stesso data center o separatamente). SAP HANA funziona in modo indipendente da questo metodo.
- **Replica di sistema HANA:** la replica di tutti i dati di SAP HANA in un sistema SAP HANA separato. L'obiettivo tempo di ripristino è ridotto al minimo tramite la replica dei dati a intervalli regolari. SAP HANA supporta le modalità asincrona, sincrona in memoria e sincrona (consigliata solo per i sistemi SAP HANA all'interno della stesso data center o distanti meno di 100 km). Nell'attuale progettazione dei timestamp HANA delle istanze di grandi dimensioni, la replica di sistema HANA è utilizzabile solo per la disponibilità elevata.
- **Failover automatico dell'host:** una soluzione locale di ripristino dagli errore da usare come alternativa alla replica di sistema. Quando il nodo master diventa non disponibile, uno o più nodi SAP HANA in standby sono configurati in modalità di scalabilità orizzontale e SAP HANA esegue automaticamente il failover in un altro nodo.

Per altre informazioni sulla disponibilità elevata di SAP HANA, vedere le risorse SAP seguenti:

- [SAP HANA High-Availability Whitepaper](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html) (White paper sulla disponibilità elevata di SAP HANA)
- [SAP HANA Administration Guide](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf) (Guida all'amministrazione di SAP HANA)
- [SAP Academy Video on SAP HANA System Replication](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication) (Video della SAP Academy sulla replica di sistema di SAP HANA)
- [SAP Support Note #1999880 - FAQ on SAP HANA System Replication](https://bcs.wdf.sap.corp/sap/support/notes/1999880) (Nota di supporto SAP 1999880 - Domande frequenti sulla replica di sistema di SAP HANA)
- [SAP Support Note #2165547 - SAP HANA Backup and Restore within SAP HANA System Replication Environment](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726) (Nota di supporto SAP 2165547 - Backup e ripristino di SAP HANA nell'ambiente di replica di sistema di SAP HANA)
- [SAP Support Note #1984882 - Using SAP HANA System Replication for Hardware Exchange with Minimum/Zero Downtime](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226) (Nota di supporto SAP 1984882 - Uso della replica di sistema di SAP HANA per la sostituzione di hardware con tempo di inattività minimo o nullo)

## <a name="disaster-recovery"></a>Ripristino di emergenza

SAP HANA in Azure (istanze di grandi dimensioni) è disponibile in due aree di Azure all'interno di un'area geopolitica. Tra i due moduli per istanze di grandi dimensioni delle due diverse aree è presente una connettività di rete diretta per la replica dei dati durante il ripristino di emergenza. La replica dei dati è basata sull'infrastruttura di archiviazione. La replica non viene eseguita per impostazione predefinita. Viene eseguita per le configurazioni dei clienti che hanno ordinato il ripristino di emergenza. Nell'attuale progettazione la replica di sistema HANA non può essere usata per il ripristino di emergenza.

Tuttavia, per sfruttare i vantaggi del ripristino di emergenza, è necessario iniziare a progettare la connettività di rete alle due diverse aree di Azure in base a questo requisito. Per farlo, occorre implementare un circuito ExpressRoute di Azure per la connessione dall'infrastruttura locale all'area principale di Azure e un altro circuito per la connessione dall'infrastruttura locale all'area di ripristino di emergenza. Questa misura consente di gestire le situazioni in cui si verifica un problema all'interno di un'intera area di Azure, inclusa la posizione di un router MSEE (Microsoft Enterprise Edge).

Come seconda misura, è possibile connettere tutte le reti virtuali di Azure collegate a SAP HANA in Azure (istanze di grandi dimensioni) in una delle aree a entrambi i circuiti ExpressRoute. In questo modo è possibile gestire il caso in cui una sola delle posizioni MSEE che connette l'infrastruttura locale ad Azure risulti fuori servizio.

La figura seguente mostra la configurazione ottimale per il ripristino di emergenza:

![Configurazione ottimale per il ripristino di emergenza](./media/sap-hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

La configurazione ottimale per il ripristino di emergenza della rete è costituita da due circuiti ExpressRoute che connettono l'infrastruttura locale alle due diverse aree di Azure. Un circuito va verso l'area 1, in cui viene eseguita un'istanza di produzione. Il secondo circuito ExpressRoute passa all'area numero 2, eseguendo alcune istanze HANA non di produzione. Questo è importante se un'intera area di Azure, incluso il timestamp di MSEE e dell'istanza di grandi dimensioni, esce dalla griglia.

Come seconda misura, le diverse reti virtuali sono connesse ai vari circuiti ExpressRoute collegati a SAP HANA in Azure (istanze di grandi dimensioni). È possibile ignorare la posizione in cui un router MSEE smette di funzionare oppure ridurre l'obiettivo punto di ripristino per il ripristino di emergenza, come illustrato più avanti.

Per la configurazione del ripristino di emergenza sono previsti anche i requisiti seguenti:

- È necessario ordinare SKU di SAP HANA in Azure (istanze di grandi dimensioni) della stessa dimensione degli SKU di produzione e distribuirli nell'area di ripristino di emergenza. Queste istanze possono essere usate per eseguire istanze di QA HANA, test o sandbox.
- È necessario ordinare un profilo di ripristino di emergenza per ogni SKU di SAP HANA in Azure (istanze di grandi dimensioni) che si vuole ripristinare nel sito di ripristino di emergenza, se necessario. In questo modo vengono allocati volumi di archiviazione, che vengono usati come destinazione della replica di archiviazione dall'area di produzione all'area di ripristino di emergenza.

Una volta soddisfatti i requisiti precedenti, è responsabilità dell'utente avviare la replica di archiviazione. Nell'infrastruttura di archiviazione usata per SAP HANA in Azure (istanze di grandi dimensioni), la replica di archiviazione è basata sugli snapshot di archiviazione. Per avviare la replica di ripristino di emergenza, è necessario eseguire questi snapshot:

- Uno snapshot del LUN di avvio, come descritto in precedenza.
- Uno snapshot dei volumi correlati a HANA, come descritto in precedenza.

Dopo l'esecuzione di questi snapshot, viene effettuato il seeding di una replica iniziale dei volumi nei volumi associati al profilo di ripristino di emergenza nell'area di ripristino di emergenza.

Successivamente, il più recente snapshot di archiviazione viene utilizzato ogni ora per replicare i dati differenziali presenti nei volumi di archiviazione.

L'obiettivo punto di ripristino che è possibile ottenere con questa configurazione va da 60 a 90 minuti. Per ottenere un migliore obiettivo punto di ripristino in caso di ripristino di emergenza, è necessario copiare i backup del log delle transazioni da SAP HANA in Azure (istanze di grandi dimensioni) all'altra area di Azure. Per raggiungere questo obiettivo punto di ripristino, eseguire le operazioni seguenti:

1. Eseguire il backup del log delle transazioni di HANA il più frequentemente possibile in /hana/log/backup.
2. Copiare i backup del log delle transazioni completati in una macchina virtuale (VM) di Azure, che si trova in una rete virtuale connessa al server di SAP HANA in Azure (istanze di grandi dimensioni).
3. Da tale VM copiare il backup in una VM di una rete virtuale nell'area di ripristino di emergenza.
4. Mantenere i backup del log delle transazioni in tale area nella VM.

In caso di emergenza, dopo avere distribuito il profilo di ripristino di emergenza su un server effettivo, copiare i backup del log delle transazioni dalla VM al server di SAP HANA in Azure (istanze di grandi dimensioni), che è ora il server primario nell'area di ripristino di emergenza, e ripristinare tali backup. Questo ripristino è possibile perché lo stato di HANA nei dischi di ripristino di emergenza corrisponde a uno snapshot HANA. Questo è il punto di offset per altre operazioni di ripristino dei backup del log delle transazioni.

## <a name="backup-and-restore"></a>Backup e ripristino

Uno degli aspetti più importanti per i database operativi è quello di assicurarsi che i database possano essere protetti da diversi eventi catastrofici. Questi eventi possono avere diverse cause, da disastri naturali a semplici errori umani.

Il backup di un database con possibilità di ripristino a una temporizzazione qualsiasi, ad esempio prima dell'eliminazione di dati critici, consente di ripristinare il database a uno stato che sia il più vicino possibile a quello precedente l'interruzione.

Per ottenere risultati ottimali, è necessario eseguire due tipi di backup:

- Backup del database
- Backup dei log delle transazioni

Oltre a creare backup di database completi al livello applicazione, è possibile raggiungere risultati ancora migliori eseguendo i backup con gli snapshot di archiviazione. Anche i backup dei log sono importanti per ripristinare il database, oltre che per svuotare i log dalle transazioni di cui è già stato eseguito il commit.

SAP HANA in Azure (istanze di grandi dimensioni) offre due opzioni di backup e ripristino:

- Eseguire il backup e il ripristino manualmente. Dopo avere eseguito gli opportuni calcoli per assicurarsi che sia disponibile spazio sufficiente sui dischi, eseguire backup completi di database e log usando specifici metodi di backup (su tali dischi). Nel corso del tempo, i backup vengono copiati in Archiviazione di Azure, dopo la configurazione di un file server basato su Azure con archiviazione Standard virtualmente illimitata. In alternativa, è possibile usare l'insieme di credenziali di Backup di Azure o l'archiviazione di Azure ad accesso sporadico. Un'altra soluzione è usare uno strumento di protezione dei dati di terze parti, ad esempio Commvault, per archiviare i backup dopo averli copiati in un account di archiviazione. L'opzione di backup manuale può essere necessaria anche per i dati che devono essere archiviati più a lungo a scopo di controllo e conformità.
- Usare la funzionalità per il backup e il ripristino offerta dall'infrastruttura sottostante di SAP HANA in Azure (istanze di grandi dimensioni). Questa opzione soddisfa le esigenze di backup e rende quasi obsoleti i backup manuali, tranne quando i backup dei dati sono necessari per motivi di conformità. La parte restante di questa sezione illustra le funzionalità di backup e ripristino offerte con le istanze di grandi dimensioni HANA.

> [!NOTE]
> La tecnologia di snapshot che viene utilizzata dall'infrastruttura sottostante di HANA (istanze di grandi dimensioni) presenta una dipendenza da snapshot di SAP HANA. Gli snapshot di SAP HANA non funzionano in combinazione con i contenitori di database multi-tenant SAP HANA. Di conseguenza questo metodo di backup non può essere usato per distribuire contenitori di database multi-tenant SAP HANA.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso degli snapshot di archiviazione di SAP HANA in Azure (istanze di grandi dimensioni)

L'infrastruttura di archiviazione sottostante di SAP HANA in Azure (istanze di grandi dimensioni) supporta il concetto di snapshot di archiviazione dei volumi. Il backup e il ripristino di un determinato volume sono supportati con le considerazioni seguenti:

- In alternativa ai backup del database, vengono creati regolarmente snapshot di archiviazione dei volumi.
- Prima dell'esecuzione, lo snapshot di archiviazione avvia uno snapshot di SAP HANA. Questo snapshot di SAP HANA è il punto di installazione per i ripristini dei log finali dopo il ripristino dello snapshot di archiviazione.
- Al momento dell'esecuzione dello snapshot di archiviazione, lo snapshot di SAP HANA verrà eliminato.
- I backup dei log vengono creati di frequente e archiviati nel volume per il backup dei log o in Azure.
- Se è necessario ripristinare il database a una determinata temporizzazione, viene inviata una richiesta al supporto tecnico di Microsoft Azure (interruzione di produzione) o al team di gestione dei servizi SAP HANA in Azure per il ripristino in base a uno specifico snapshot di archiviazione, ad esempio un ripristino pianificato di un sistema sandbox allo stato originale.
- Lo snapshot di SAP HANA incluso nello snapshot di archiviazione è un punto di offset per l'applicazione dei backup di log che sono stati creati e archiviati dopo l'esecuzione dello snapshot di archiviazione.
- Questi backup dei log vengono creati per ripristinare il database a una determinata temporizzazione.

Quando si specifica il valore di backup\_name, viene creato uno snapshot dei volumi seguenti:

- hana/data
- hana/log
- hana/log\_backup (montato come backup in hana/log)
- hana/shared

### <a name="storage-snapshot-considerations"></a>Considerazioni sugli snapshot di archiviazione

>[!NOTE]
>Gli snapshot di archiviazione _non_ vengono forniti gratuitamente perché richiedono l'allocazione di spazio di archiviazione aggiuntivo.

I meccanismi specifici degli snapshot di archiviazione per SAP HANA in Azure (istanze di grandi dimensioni) comprendono:

- Nel momento in cui viene creato, uno specifico snapshot di archiviazione usa una quantità di spazio molto limitata.
- A mano a mano che il contenuto dei dati cambia e il contenuto dei file di dati di SAP HANA viene modificato nel volume di archiviazione, lo snapshot deve archiviare il contenuto dei blocchi originali.
- Le dimensioni dello snapshot di archiviazione aumentano progressivamente in proporzione alla durata.
- Quante più modifiche vengono apportate al volume del database SAP HANA per tutta la durata di uno snapshot di archiviazione, tanto più grande diventa lo spazio utilizzato da tale snapshot.

SAP HANA in Azure (istanze di grandi dimensioni) viene fornito con volumi di dimensioni fisse per l'archiviazione di dati e log. Con l'esecuzione di snapshot si utilizza spazio sui volumi ed è quindi responsabilità del cliente pianificare gli snapshot di archiviazione, nell'ambito del processo di SAP HANA in Azure (istanze di grandi dimensioni).

Le sezioni seguenti includono informazioni per l'esecuzione di questi snapshot oltre a consigli di carattere generale:

- Anche se l'hardware può supportare 255 snapshot per volume, è consigliabile rimanere ben al di sotto di questa cifra.
- Prima di eseguire snapshot di archiviazione, monitorare e tenere traccia dello spazio libero.
- Limitare il numero di snapshot di archiviazione in base allo spazio disponibile. Potrebbe essere necessario ridurre il numero di snapshot conservati o potrebbe essere necessario estendere i volumi. È possibile ordinare ulteriore spazio di archiviazione in unità di 1 TB.
- Durante determinate attività, come lo spostamento di dati in SAP HANA con strumenti di migrazione del sistema (R3load o la funzionalità per il ripristino di backup di database SAP HANA), è consigliabile non creare snapshot di archiviazione. Se viene eseguita una migrazione di sistema in un nuovo sistema SAP HANA, non è necessario creare snapshot di archiviazione.
- Durante una riorganizzazione più estesa delle tabelle di SAP HANA, evitare di creare snapshot di archiviazione, se possibile.
- Gli snapshot di archiviazione sono inoltre un prerequisito per attivare le funzionalità di ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni).

### <a name="setting-up-storage-snapshots"></a>Configurazione degli snapshot di archiviazione

1. Assicurarsi che Perl sia installato nel sistema operativo Linux sul server HANA (istanze di grandi dimensioni).
2. Modificare /etc/ssh/ssh\_config in modo da aggiungere la riga _MACs hmac-sha1_.
3. Creare un account utente di backup di SAP HANA sul nodo master per ogni istanza SAP HANA in esecuzione (se applicabile).
4. Il client HDB di SAP HANA deve essere installato in tutti i server SAP HANA (istanze di grandi dimensioni).
5. Nel primo server SAP HANA (istanze di grandi dimensioni) di ogni area è necessario creare una chiave pubblica per accedere all'infrastruttura di archiviazione sottostante che controlla la creazione di snapshot.
6. Copiare lo script azure\_hana\_backup.pl da /scripts nel percorso di **hdbsql** nell'installazione di SAP HANA.
7. Copiare il file HANABackupDetails.txt dalla cartella /scripts nello stesso percorso dello script Perl.
8. Modificare il file HANABackupDetails.txt come necessario, in base alle specifiche del cliente.

### <a name="step-1-install-sap-hana-hdbclient"></a>Passaggio 1: Installare il client HBD di SAP HANA

La distribuzione di Linux installata in SAP HANA in Azure (istanze di grandi dimensioni) include le cartelle e gli script necessari per eseguire snapshot di archiviazione di SAP HANA a scopo di backup e ripristino di emergenza. È tuttavia responsabilità del cliente installare il client HBD durante l'installazione di SAP HANA. Il client HDB e SAP HANA non vengono installati da Microsoft.

### <a name="step-2-change-etcsshsshconfig"></a>Passaggio 2: Modificare /etc/ssh/ssh\_config

Modificare /etc/ssh/ssh\_config aggiungendo la riga _MACs hmac-sha1_ come indicato di seguito:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Passaggio 3: Creare una chiave pubblica

Nel primo server SAP HANA in Azure (istanze di grandi dimensioni) in ogni area di Azure creare una chiave pubblica da usare per accedere all'infrastruttura di archiviazione in modo da poter creare gli snapshot. La chiave pubblica evita di dover usare una password per accedere alla risorsa di archiviazione e dover gestire le credenziali di accesso. In Linux, nel server SAP HANA (istanze di grandi dimensioni), eseguire il comando seguente per generare la chiave pubblica:
```
  ssh-keygen –t dsa –b 1024
```
Il nuovo percorso è _/root/.ssh/id\_dsa.pub. Non immettere una passphrase effettiva, altrimenti sarà necessario immetterla a ogni accesso. In alternativa, premere due volte **INVIO** per evitare che venga richiesta una passphrase in fase di accesso.

Verificare che la chiave pubblica sia corretta cambiando le cartelle in /root/.ssh/ e poi eseguendo il comando **ls**. Se la chiave è presente, è possibile copiarla eseguendo il comando seguente:

![La chiave pubblica viene copiata eseguendo questo comando](./media/sap-hana-overview-high-availability-disaster-recovery/image2-public-key.png)

A questo punto, contattare il team di gestione dei servizi SAP HANA in Azure e specificare la chiave, che verrà usata dal rappresentante del servizio per la registrazione nell'infrastruttura di archiviazione sottostante.

### <a name="step-4-create-an-sap-hana-user-account"></a>Passaggio 4: Creare un account utente SAP HANA

In SAP HANA Studio creare un account utente SAP HANA a scopo di backup. L'account deve disporre dei privilegi _Backup Admin_ e _Catalog Read_. In questo esempio viene creato il nome utente SCADMIN.

![Creazione di un utente in HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passaggio 5: Autorizzare l'account utente SAP HANA

Autorizzare l'account utente SAP HANA in modo che possa essere usato senza richiesta di autorizzazione ogni volta che viene eseguito lo script. Il comando `hdbuserstore` di SAP HANA consente la creazione di una chiave utente SAP HANA, che viene archiviata in uno o più nodi di SAP HANA. La chiave utente consente anche di accedere a SAP HANA senza dover gestire le password dall'interno del processo di scripting che verrà discusso più avanti.

>[!IMPORTANT]
>Eseguire il comando indicato di seguito come `_root_`. Altrimenti lo script non funzionerà correttamente.

Immettere il comando `hdbuserstore` come segue:

![Immettere il comando hdbuserstore](./media/sap-hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

Nell'esempio seguente, in cui l'utente è SCADMIN01 e il nome host è lhanad01, il comando è:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Gestire tutti gli script da un singolo server per le istanze HANA con scalabilità orizzontale. In questo esempio la chiave di SAP HANA SCADMIN01 deve essere modificata per ogni host in modo che rifletta l'host correlato alla chiave. In altre parole, l'account di backup SAP HANA viene modificato con il numero di istanza di HANA DB, **lhanad**. La chiave deve avere privilegi amministrativi sull'host a cui è assegnata e l'utente di backup per la scalabilità orizzontale deve disporre dei diritti di accesso a tutte le istanze SAP HANA.
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

### <a name="step-6-copy-items-from-the-scripts-folder"></a>Passaggio 6: Copiare elementi dalla cartella /scripts

Copiare gli elementi seguenti dalla cartella /scripts, inclusa nell'immagine gold dell'installazione, alla directory di lavoro per **hdbsql**. Per le installazioni correnti di HANA, la directory è /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
Nel caso di una configurazione con scalabilità orizzontale o OLAP, copiare gli elementi seguenti:
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
Il file HANABackupCustomerDetails.txt può essere modificato come indicato di seguito per una distribuzione con scalabilità verticale. Si tratta del file di configurazione e controllo per lo script che eseguirà gli snapshot di archiviazione. Quando le istanze sono state distribuite, il team di gestione dei servizi di SAP HANA in Azure dovrebbe avere inviato i valori di _Storage Backup Name_ e _Storage IP Address_. Non è possibile modificare la sequenza, l'ordinamento o la spaziatura per nessuna delle variabili, altrimenti lo script non funzionerà correttamente.

Per una distribuzione con scalabilità verticale, il file di configurazione ha un aspetto simile al seguente:
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
Per una configurazione con scalabilità orizzontale, il file HANABackupCustomerDetailsBW.txt ha un aspetto simile al seguente:
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
>[!NOTE]
>Attualmente solo i dettagli del nodo 1 vengono usati nell'effettivo script per lo snapshot di archiviazione HANA. Si consiglia di testare l'accesso da e verso tutti i nodi HANA in modo che, se il nodo di backup master dovesse cambiare, è già stato verificato che un altro nodo può prendere il suo posto modificando i dettagli nel nodo 1.

Per verificare le configurazioni corrette nel file di configurazione o il funzionamento della connessione alle istanze HANA, eseguire uno dei seguenti script:
- Per una configurazione con scalabilità verticale (indipendente dal carico di lavoro SAP):

 ```
testHANAConnection.pl
```
- Per una configurazioni con scalabilità orizzontale:

 ```
testHANAConnectionBW.pl
```

Verificare che l'istanza HANA master abbia accesso a tutti i server HANA necessari. Lo script non richiede parametri. Tuttavia, per la corretta esecuzione dello script, è necessario completare il file HANABackupCustomerDetails/ HANABackupCustomerDetailsBW appropriato. Poiché vengono restituiti solo i codici di errore dei comandi della shell, lo script non può eseguire il controllo degli errori di ogni istanza. Anche in questo caso lo script fornisce alcuni commenti che è utile controllare.

Per eseguire lo script:
```
 ./testHANAConnection.pl
```
 Se lo script riesce a ottenere lo stato dell'istanza HANA, mostra un messaggio che conferma la riuscita della connessione HANA.

Esiste anche un secondo tipo di script che consente di controllare se il server dell'istanza HANA master può accedere alla risorsa di archiviazione. Prima di eseguire lo script azure\_hana\_backup(\_bw).pl è necessario eseguire lo script seguente. Se un volume non contiene snapshot, non è possibile determinare se il volume è semplicemente vuoto o si è verificato un errore ssh che non consente di ottenere i dettagli dello snapshot. Per questo motivo lo script esegue due passaggi:

- Verifica che la console di archiviazione sia accessibile.
- Crea uno snapshot di test, o fittizio, per ogni volume di ogni istanza HANA.

Per questo motivo, l'istanza HANA è inclusa come argomento. Anche in questo caso non è possibile prevedere il controllo degli errori per la connessione di archiviazione, ma lo script fornisce suggerimenti utili se l'esecuzione ha esito negativo.

Lo script viene eseguito nel modo seguente:
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
Oppure viene eseguito nel modo seguente:
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
Lo script mostra anche un messaggio per segnalare che è possibile accedere in modo appropriato al tenant di archiviazione distribuita organizzato attorno ai numeri di unità logica (LUN) usati dalle istanze di server di cui si è proprietari.

Prima di eseguire i primi backup basati sugli snapshot di archiviazione, eseguire gli script seguenti per assicurarsi che la configurazione sia corretta.

Dopo l'esecuzione di questi script, è possibile eliminare gli snapshot con lo script seguente:
```
./removeTestStorageSnapshot.pl <hana instance>
```
Oppure
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="step-7-perform-on-demand-snapshots"></a>Passaggio 7: Eseguire snapshot su richiesta

Eseguire snapshot su richiesta (e pianificare snapshot regolari utilizzando cron) come descritto di seguito.

Per le configurazioni con scalabilità verticale, eseguire lo script seguente:
```
./azure_hana_backup.pl lhanad01 customer 20
```
Per le configurazioni con scalabilità orizzontale, eseguire lo script seguente:
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
Lo script per la scalabilità orizzontale esegue alcuni controlli aggiuntivi per verificare che tutti i server HANA siano accessibili e che tutte le istanze HANA restituiscano lo stato appropriato prima di procedere con la creazione di snapshot di archiviazione o SAP HANA.

Gli argomenti seguenti sono obbligatori:

- L'istanza HANA da sottoporre a backup.
- Il prefisso dello snapshot di archiviazione.
- Il numero di snapshot da mantenere per il prefisso specifico.

```
./azure_hana_backup.pl lhanad01 customer 20
```

Il processo di creazione dello snapshot di archiviazione è costituito da queste tre fasi distinte:

- Eseguire uno snapshot HANA.
- Eseguire uno snapshot di archiviazione.
- Rimuovere lo snapshot HANA.

Per eseguire lo script, chiamarlo dalla cartella dell'eseguibile HDB in cui è stato copiato. Lo script esegue sicuramente il backup dei volumi seguenti, ma anche di qualsiasi volume che include nel suo nome il nome di istanza SAP HANA esplicito.
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
Il periodo di mantenimento viene gestito rigorosamente in base al numero di snapshot specificato come parametro durante l'esecuzione dello script. Ad esempio 20, come indicato sopra. Pertanto, la quantità di tempo è calcolata in funzione del periodo di esecuzione e del numero di snapshot nella chiamata dello script. Se il numero di snapshot mantenuti supera il numero indicato come parametro nella chiamata dello script, lo snapshot di archiviazione meno recente di questa etichetta (_customer_ nel caso precedente) verrà eliminato prima dell'esecuzione di un nuovo snapshot. Ciò significa che il numero specificato come ultimo parametro della chiamata è il numero che è possibile usare per controllare il numero di snapshot.

>[!NOTE]
>Non appena si modifica l'etichetta, il conteggio viene riavviato.

È necessario includere come argomento il nome dell'istanza HANA fornito dal team di gestione dei servizi SAP HANA in Azure se si creano snapshot di ambienti multinodo. Negli ambienti a nodo singolo è sufficiente il nome nell'unità SAP HANA in Azure (istanza di grandi dimensioni), ma è comunque consigliabile specificare anche il nome dell'istanza HANA.

Inoltre è possibile eseguire il backup di volumi di avvio\LUN usando lo stesso script. È necessario eseguire il backup del volume di avvio almeno una volta quando si esegue HANA per la prima volta, anche se è consigliabile pianificare backup settimanali o nelle ore notturne per tale volume tramite cron. Anziché aggiungere il nome di un'istanza SAP HANA, inserire _boot_ come argomento dello script come indicato di seguito:
```
./azure_hana_backup boot customer 20
```
Gli stessi criteri di mantenimento si applicano anche al volume di avvio. Usare gli snapshot su richiesta, come descritto sopra, solo per casi speciali, ad esempio durante un aggiornamento di SAP Enhancement Package (EHP) o quando è necessario creare uno snapshot di archiviazione distinto.

Si consiglia di eseguire snapshot di archiviazione pianificati tramite cron, nonché di usare lo stesso script per tutti i backup e le esigenze di ripristino di emergenza, modificando l'input dello script in base ai diversi orari di backup richiesti. Gli snapshot sono tutti pianificati in modo diverso in cron, in base alla frequenza di esecuzione: oraria, ogni 12 ore, giornaliera o settimanale. La pianificazione di cron è progettata in modo da creare snapshot di archiviazione che corrispondano alle etichette di mantenimento descritte in precedenza per il backup fuori sede a lungo termine. Lo script include comandi per eseguire il backup di tutti i volumi di produzione in base alla frequenza richiesta. Il backup dei file di dati e log viene eseguito ogni ora, mentre quello del volume di avvio viene eseguito ogni giorno.

Le voci nello script di cron seguente vengono eseguite ogni ora, ogni 12 ore e ogni giorno, sempre al decimo minuto. I processi di cron vengono creati in modo da eseguire un solo snapshot di archiviazione di SAP HANA a una determinata ora, evitando così di eseguire contemporaneamente il backup orario e quello giornaliero (alle ore&12;.10). Per ottimizzare la creazione e la replica di snapshot, il team di gestione dei servizi SAP HANA in Azure fornirà suggerimenti riguardo ai tempi di esecuzione dei backup.

Di seguito è indicata la pianificazione predefinita di cron in /etc/crontab:
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
Nelle istruzioni di cron sopra indicate lo snapshot dei volumi HANA (escluso il volume di avvio) viene creato ogni ora. Di questi snapshot, ne vengono mantenuti 66. Inoltre, vengono conservati 14 snapshot con l'etichetta di 12 ore. Si possono avere snapshot orari per tre giorni, oltre a snapshot ogni 12 ore per altri quattro giorni, ovvero un'intera settimana di snapshot.

La pianificazione in cron può risultare complessa poiché in un momento specifico è consentita l'esecuzione di un solo script, a meno che questo non sia sfalsato di alcuni minuti. Se si preferiscono i backup giornalieri per l'archiviazione a lungo termine, viene eseguito uno snapshot giornaliero insieme a uno snapshot ogni 12 ore, mantenendo sette backup per ciascun tipo. In alternativa, lo snapshot con frequenza oraria viene posticipato di 10 minuti. Un solo snapshot viene mantenuto nel volume di produzione.
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
Le frequenze elencate di seguito sono solo esempi. Per ricavare il numero ottimale di snapshot, usare i criteri seguenti:

- Requisiti dell'obiettivo tempo di ripristino per il ripristino temporizzato.
- Utilizzo dello spazio.
- Requisiti dell'obiettivo punto di ripristino e dell'obiettivo tempo di ripristino per un ripristino di emergenza potenziale.
- Eventuale esecuzione di backup completi del database HANA sui dischi. Ogni volta che viene creato un backup completo del database sui dischi o che viene eseguita l'interfaccia _backint_, l'esecuzione di snapshot di archiviazione ha esito negativo. Se si pianifica l'esecuzione di backup completi del database oltre agli snapshot di archiviazione, assicurarsi che durante questo periodo l'esecuzione degli snapshot di archiviazione sia disabilitata.

>[!IMPORTANT]
> L'uso di snapshot di archiviazione per i backup di SAP HANA è valido solo se gli snapshot vengono creati insieme ai backup dei log di SAP HANA. Questi backup devono essere in grado di coprire i periodi di tempo che intercorrono tra gli snapshot di archiviazione. Se agli utenti si è garantito un ripristino temporizzato di 30 giorni, è necessario che siano soddisfatti i requisiti seguenti:

- Possibilità di accedere a uno snapshot di archiviazione di 30 giorni.
- Backup di log contigui nell'arco degli ultimi 30 giorni.

Nell'intervallo dei backup di log creare anche uno snapshot del volume dei log di backup. Assicurarsi tuttavia di eseguire backup regolari dei log in modo da poter:

- Disporre dei backup di log contigui necessari per eseguire ripristini temporizzati.
- Evitare che il volume dei log di SAP HANA esaurisca lo spazio.

Uno degli ultimi passaggi consiste nel pianificare i log di backup di SAP HANA in SAP HANA Studio. La destinazione dei log di backup di SAP HANA è il volume hana/log\_backups appositamente creato con il punto di montaggio /hana/log/backups.

![Pianificare i log di backup di SAP HANA in SAP HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

È possibile scegliere backup con una frequenza maggiore di ogni 15 minuti. Alcuni utenti eseguono il backup dei log ogni minuto, ma è sconsigliato impostare una frequenza _superiore_ a 15 minuti.

L'ultimo passaggio consiste nell'eseguire un backup basato su file (dopo l'installazione iniziale di SAP HANA) per creare una singola voce di backup che deve essere presente nel catalogo di backup. In caso contrario SAP HANA non può avviare i backup del log specificato.

![Eseguire un backup basato su file per creare una singola voce di backup](./media/sap-hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoraggio del numero e della dimensione degli snapshot sul volume di archiviazione

Su un particolare volume di archiviazione è possibile monitorare il numero di snapshot e l'utilizzo dello spazio di archiviazione degli snapshot. Il comando `ls` non mostra i file e la directory degli snapshot, diversamente dal comando `du` di Linux, che può essere eseguito con i parametri seguenti:

- `du –sh .snapshot` fornisce il totale di tutti gli snapshot all'interno della directory degli snapshot.
- `du –sh --max-depth=1` elenca tutti gli snapshot che vengono salvati nella cartella .snapshot e la dimensione di ciascuno.
- `du –hc` fornisce la dimensione totale utilizzata da tutti gli snapshot.

Usare questi comandi per assicurarsi che gli snapshot creati e archiviati non usino tutto lo spazio di archiviazione sui volumi.

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Riduzione del numero di snapshot in un server

Come illustrato in precedenza, è possibile ridurre il numero di snapshot archiviati con una determinata etichetta. Gli ultimi due parametri del comando per avviare uno snapshot sono un'etichetta e il numero di snapshot da mantenere.
```
./azure_hana_backup.pl lhanad01 customer 20
```
Nell'esempio precedente l'etichetta dello snapshot è _customer_ e il numero di snapshot con questa etichetta da mantenere è _20_. Dovendo limitare l'uso dello spazio su disco, può essere opportuno ridurre il numero di snapshot archiviati. Il modo più semplice per ridurre il numero di snapshot consiste nell'eseguire lo script con l'ultimo parametro impostato su 5:
```
./azure_hana_backup.pl lhanad01 customer 5
```
Se si esegue lo script con questa impostazione, il numero di snapshot viene ridotto e pertanto il totale degli snapshot mantenuti, compreso quello nuovo, è _5_.

 >[!NOTE]
 > Questo script consente di ridurre il numero di snapshot solo se lo snapshot precedente più recente è stato eseguito più di un'ora prima. Lo script non elimina gli snapshot che hanno meno di un'ora.

Queste restrizioni sono correlate alla funzionalità di ripristino di emergenza facoltativa offerta.

Se non si desidera più mantenere un set di snapshot con tale prefisso, è possibile eseguire lo script con _0_ come numero di snapshot da mantenere per rimuovere tutti gli snapshot corrispondenti al prefisso. La rimozione di tutti gli snapshot può tuttavia influire sulle funzionalità di ripristino di emergenza.

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Ripristino in base allo snapshot HANA più recente

Se si riscontra un problema di inattività in fase di produzione, il processo di ripristino da uno snapshot di archiviazione può essere avviato come evento imprevisto del cliente con il team di gestione dei servizi SAP HANA in Azure. Un caso imprevisto di questo tipo potrebbe richiedere estrema urgenza se i dati sono stati eliminati in un sistema di produzione e l'unico modo per recuperare i dati è quello di ripristinare il database di produzione.

Al contrario, un ripristino temporizzato può essere gestito come caso a bassa priorità e pianificato con diversi giorni di anticipo. È possibile pianificare questo ripristino con il team di gestione dei servizi SAP HANA in Azure anziché segnalare un problema ad alta priorità. Ad esempio si potrebbe pianificare un aggiornamento del software SAP con un nuovo Enhancement Package e successivamente dover eseguire il ripristino in base a uno snapshot che rappresenta lo stato precedente l'aggiornamento EHP.

Prima di eseguire la richiesta è necessario completare alcune operazioni di preparazione. In questo modo il team di gestione dei servizi SAP HANA in Azure potrà gestire la richiesta e fornire i volumi ripristinati. Sarà quindi responsabilità del cliente ripristinare il database HANA in base agli snapshot. Ecco come preparare la richiesta:

>[!NOTE]
>L'interfaccia utente potrebbe essere diversa rispetto alle seguenti schermate, a seconda della versione di SAP HANA in uso.

1. Scegliere lo snapshot da ripristinare. Viene ripristinato solo il volume hana/data, se non è diversamente specificato.

2. Arrestare l'istanza HANA.

 ![Arrestare l'istanza HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Smontare i volumi di dati in ogni nodo del database HANA. Se i volumi di dati non sono smontati, il ripristino dello snapshot avrà esito negativo.

 ![Smontare i volumi di dati in ogni nodo del database HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Inviare una richiesta al supporto tecnico di Azure per il ripristino di uno snapshot specifico.

 - Durante il ripristino: SAP HANA in Gestione servizi di Azure potrebbe chiedere di partecipare a una conferenza telefonica per evitare perdite di dati.

 - Dopo il ripristino: il team di gestione dei servizi SAP HANA in Azure invia una notifica per indicare quando è stato ripristinato lo snapshot di archiviazione.

5. Al termine del processo di ripristino montare nuovamente tutti i volumi di dati.

 ![Montare nuovamente tutti i volumi di dati](./media/sap-hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Selezionare le opzioni di ripristino in SAP HANA Studio, se non vengono automaticamente visualizzate al momento della riconnessione al database HANA tramite SAP HANA Studio. L'esempio seguente illustra un ripristino in base all'ultimo snapshot HANA. Uno snapshot di archiviazione incorpora un singolo snapshot HANA. Pertanto, se si esegue il ripristino in base all'ultimo snapshot di archiviazione, lo snapshot HANA ripristinato è quello più recente. Se invece si esegue il ripristino in base a snapshot di archiviazione precedenti, è necessario individuare lo snapshot HANA basandosi sull'ora di creazione dello snapshot di archiviazione.

 ![Selezionare le opzioni di ripristino in SAP HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Selezionare **Recover the database to a specific data backup or storage snapshot** (Ripristina il database in base a un backup di dati o a uno snapshot di archiviazione specifico).

 ![La finestra "Specify Recovery Type" (Specifica tipo di ripristino)](./media/sap-hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Scegliere **Specify backup without catalog** (Specifica il backup senza catalogo).

 ![La finestra "Specify Backup Location" (Specifica percorso backup)](./media/sap-hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Nell'elenco **Destination Type** (Tipo di destinazione) selezionare **Snapshot**.

 ![La finestra "Specify the Backup to Recover" (Specifica il backup da ripristinare)](./media/sap-hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Fare clic su **Finish** (Fine) per avviare il processo di ripristino.

 ![Fare clic su "Finish" (Fine) per avviare il processo di ripristino](./media/sap-hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. Il database HANA viene ripristinato in base allo snapshot HANA incluso nello snapshot di archiviazione.

 ![Il database HANA viene ripristinato in base allo snapshot HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Ripristino allo stato più recente

La procedura seguente ripristina lo snapshot HANA incluso nello snapshot di archiviazione. Quindi ripristina il backup del log delle transazioni allo stato più recente del database prima di ripristinare lo snapshot di archiviazione.

>[!IMPORTANT]
>Prima di procedere assicurarsi di avere una catena contigua e completa di backup dei log delle transazioni. Senza questi backup non è possibile ripristinare lo stato corrente del database.

1. Completare i passaggi da 1 a 6 della procedura precedente in "Ripristino in base allo snapshot HANA più recente".

2. Selezionare **Recover the database to its most recent state** (Ripristina il database allo stato più recente).

 ![Selezionare "Recover the database to its most recent state" (Ripristina il database allo stato più recente)](./media/sap-hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Specificare il percorso dei backup dei log di HANA più recenti. Il percorso deve contenere tutti i backup dei log delle transazioni di HANA dallo snapshot HANA allo stato più recente.

 ![Specificare il percorso dei backup dei log di HANA più recenti](./media/sap-hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Selezionare un backup come base da cui ripristinare il database. Nell'esempio specifico si tratta dello snapshot HANA incluso nello snapshot di archiviazione. Nella schermata seguente è indicato un solo snapshot.

 ![Selezionare un backup come base da cui ripristinare il database](./media/sap-hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Deselezionare la casella di controllo **Use Delta Backups** (Usa backup differenziali) se non sono stati creati backup differenziali tra l'ora di creazione dello snapshot HANA e lo stato più recente.

 ![Deselezionare la casella di controllo "Use Delta Backups" (Usa backup differenziali) se non esistono backup differenziali](./media/sap-hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Nella schermata di riepilogo fare clic su **Finish** (Fine) per avviare la procedura di ripristino.

 ![Nella schermata di riepilogo fare clic su "Finish" (Fine)](./media/sap-hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Ripristino a un'altra temporizzazione
Per eseguire il ripristino a una temporizzazione compresa tra il momento di creazione dello snapshot HANA (incluso nello snapshot di archiviazione) e un momento successivo al ripristino temporizzato dello snapshot HANA, procedere come segue:

1. Assicurarsi di avere tutti i backup dei log delle transazioni dal momento di creazione dello snapshot HANA al momento che si vuole ripristinare.
2. Iniziare la procedura riportata in "Ripristino allo stato più recente".
3. Al passaggio 2 della procedura, nella finestra **Specify Recovery Type** (Specifica tipo di ripristino), selezionare **Recover the database to the following point in time** (Ripristina il database alla temporizzazione seguente), specificare la temporizzazione e quindi completare i passaggi da 3 a 6.

## <a name="monitoring-the-execution-of-snapshots"></a>Monitorare l'esecuzione degli snapshot

È necessario monitorare l'esecuzione degli snapshot di archiviazione. Lo script che esegue uno snapshot di archiviazione scrive l'output in un file e lo salva nello stesso percorso degli script Perl. Viene creato un file separato per ogni snapshot. L'output di ogni file mostra chiaramente le diverse fasi che lo script degli snapshot esegue:

- Ricerca dei volumi per i quali è necessario creare uno snapshot
- Ricerca degli snapshot creati da tali volumi
- Eliminazione di eventuali snapshot esistenti in modo da riflettere il numero di snapshot specificato
- Creazione di uno snapshot HANA
- Creazione dello snapshot di archiviazione sui volumi
- Eliminazione dello snapshot HANA
- Rinomina dello snapshot più recente come **.0**

La parte più importante dello script è la seguente:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
In questo esempio è possibile notare come lo script registra la creazione dello snapshot HANA. Nel caso di una configurazione con scalabilità orizzontale questa procedura viene avviata sul nodo master. Il nodo master avvia la creazione sincrona degli snapshot su ciascuno dei nodi di lavoro, quindi viene creato lo snapshot di archiviazione. Dopo l'esecuzione degli snapshot di archiviazione, lo snapshot HANA viene eliminato.


---
title: "Disponibilità elevata e ripristino di emergenza di SAP HANA in Azure (istanze Large) | Documentazione Microsoft"
description: "Implementare la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze Large)."
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
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 4356e35609fa7a9727d8ad4f20fd18df01a05e84
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure 

La disponibilità elevata e il ripristino di emergenza sono aspetti importanti per l'esecuzione di uno o più server SAP HANA di importanza critica in Azure (istanze Large). Per progettare e implementare la corretta strategia di disponibilità elevata e ripristino di emergenza è fondamentale la collaborazione con SAP, l'integratore di sistemi o Microsoft. È importante anche prendere in considerazione l'obiettivo punto di ripristino e l'obiettivo tempo di ripristino, che sono specifici dell'ambiente.

Microsoft supporta alcuni metodi di disponibilità elevata "predefiniti" con le istanze Large di SAP HANA. incluse le seguenti:

- **Replica di archiviazione:** capacità del sistema di archiviazione di replicare tutti i dati in un altro indicatore di istanze Large di HANA in un'altra area di Azure. SAP HANA funziona in modo indipendente da questo metodo.
- **Replica di sistema HANA:** la replica di tutti i dati di SAP HANA in un sistema SAP HANA separato. L'obiettivo tempo di ripristino è ridotto al minimo tramite la replica dei dati a intervalli regolari. SAP HANA supporta le modalità asincrona, sincrona in memoria e sincrona (consigliata solo per i sistemi SAP HANA all'interno della stesso data center o distanti meno di 100 km). Nell'attuale progettazione degli indicatori di istanze Large di HANA, la replica di sistema HANA è utilizzabile solo per la disponibilità elevata. In base alla progettazione di rete corrente, la replica di sistema HANA non può essere usata senza il componente proxy inverso di terze parti per le configurazioni di ripristino di emergenza in un'altra area di Azure. 
- **Failover automatico dell'host:** soluzione locale di ripristino dagli errori per SAP HANA, da usare come alternativa alla replica di sistema HANA. Quando il nodo master diventa non disponibile, vengono configurati uno o più nodi SAP HANA di standby in modalità di scalabilità orizzontale e SAP HANA esegue automaticamente il failover in un nodo di standby.

SAP HANA in Azure (istanze Large) è al momento disponibile in due aree di Azure in tre diverse aree geopolitiche (Stati Uniti, Australia ed Europa). Due aree diverse che ospitano indicatori di istanze Large di HANA sono connesse con circuiti di rete dedicati distinti, usati per la replica degli snapshot di archiviazione per fornire metodi di ripristino di emergenza. La replica non è attivata per impostazione predefinita. Viene configurata per i clienti che ordinano funzionalità di ripristino di emergenza. La replica di archiviazione dipende dall'utilizzo degli snapshot di archiviazione per le istanze Large di HANA. Non è inoltre possibile scegliere un'area di Azure come area di ripristino di emergenza in un'area geopolitica diversa. 

La tabella seguente mostra i metodi di disponibilità elevata e ripristino di emergenza e le combinazioni di cui è attualmente disponibile il supporto:

| Scenario supportato nelle istanze Large di HANA | Opzione di disponibilità elevata | Opzione di ripristino di emergenza | Commenti |
| --- | --- | --- | --- |
| Nodo singolo | Non disponibile | Configurazione di ripristino di emergenza dedicata<br /> Configurazione di ripristino di emergenza multifunzione | |
| Failover automatico dell'host: N+m<br /> incluso 1+1 | Possibile con il nodo di standby che acquisisce il ruolo attivo<br /> Cambio di ruolo controllato da HANA | Configurazione di ripristino di emergenza dedicata<br /> Configurazione di ripristino di emergenza multifunzione<br /> Sincronizzazione di ripristino di emergenza tramite replica di archiviazione | Insiemi di volumi HANA collegati a tutti i nodi (n+m)<br /> Il sito di ripristino di emergenza deve avere lo stesso numero di nodi |
| Replica di sistema HANA | Possibile con configurazione nodi primario/secondario<br /> Il nodo secondario assume il ruolo primario in caso di failover<br /> Replica di sistema HANA e failover controllato dal sistema operativo | Configurazione di ripristino di emergenza dedicata<br /> Configurazione di ripristino di emergenza multifunzione<br /> Sincronizzazione di ripristino di emergenza tramite replica di archiviazione<br /> Il ripristino di emergenza tramite la replica di sistema HANA non è ancora possibile senza componenti di terze parti | Insieme separato di volumi di dischi collegati a ogni nodo<br /> Solo i volumi di dischi di replica secondaria nel sito di produzione vengono replicati nella posizione di ripristino di emergenza<br /> Nel sito di ripristino di emergenza è necessario un insieme di volumi | 

Con configurazione di ripristino di emergenza dedicata si intende una configurazione in cui l'unità di istanze Large di HANA nel sito di ripristino di emergenza non viene usata per eseguire altri carichi di lavoro o sistemi non di produzione. L'unità è passiva e viene distribuita con il solo scopo di diventare attiva in caso di failover di emergenza. Al momento non ci sono clienti con questa configurazione.

Con configurazione di ripristino di emergenza multifunzione si intende una configurazione nel sito di ripristino di emergenza in cui l'unità di istanze Large di HANA esegue un carico di lavoro non di produzione. Nel caso di un'emergenza in cui il sistema non di produzione viene arrestato, vengono avviati gli insiemi di volumi (aggiuntivi) con replica di archiviazione e l'istanza di produzione di HANA. Al momento, tutti i clienti che usano la funzionalità di ripristino di emergenza di istanze Large di HANA usano questa alternativa di configurazione. 


Per altre informazioni sulla disponibilità elevata di SAP HANA, vedere gli articoli SAP seguenti: 

- [SAP HANA High-Availability Whitepaper](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html) (White paper sulla disponibilità elevata di SAP HANA)
- [SAP HANA Administration Guide](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf) (Guida all'amministrazione di SAP HANA)
- [SAP Academy Video on SAP HANA System Replication](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication) (Video della SAP Academy sulla replica di sistema di SAP HANA)
- [SAP Support Note #1999880 - FAQ on SAP HANA System Replication](https://bcs.wdf.sap.corp/sap/support/notes/1999880) (Nota di supporto SAP 1999880 - Domande frequenti sulla replica di sistema di SAP HANA)
- [SAP Support Note #2165547 - SAP HANA Backup and Restore within SAP HANA System Replication Environment](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726) (Nota di supporto SAP 2165547 - Backup e ripristino di SAP HANA nell'ambiente di replica di sistema di SAP HANA)
- [SAP Support Note #1984882 - Using SAP HANA System Replication for Hardware Exchange with Minimum/Zero Downtime](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226) (Nota di supporto SAP 1984882 - Uso della replica di sistema di SAP HANA per la sostituzione di hardware con tempo di inattività minimo o nullo)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerazioni sulla rete per il ripristino di emergenza con istanze Large di HANA

Per sfruttare i vantaggi della funzionalità di ripristino di emergenza di istanze Large di HANA, è necessario iniziare a progettare la connettività di rete alle due diverse aree di Azure. Per farlo, occorre implementare un circuito ExpressRoute di Azure per la connessione dall'infrastruttura locale all'area principale di Azure e un altro circuito per la connessione dall'infrastruttura locale all'area di ripristino di emergenza. Questa misura consente di gestire le situazioni in cui si verifica un problema in un'intera area di Azure, inclusa la posizione di un router MSEE (Microsoft Enterprise Edge).

Come seconda misura, è possibile connettere tutte le reti virtuali di Azure connesse a SAP HANA in Azure (istanze Large) in una delle aree al circuito ExpressRoute che connette le istanze Large di HANA nell'altra area. Grazie a questa "connessione incrociata", i servizi in esecuzione in una rete virtuale di Azure nell'area 1 possono connettersi alle unità di istanze Large di HANA nell'area 2 e viceversa. In questo modo è possibile gestire il caso in cui una sola delle posizioni MSEE che connette l'infrastruttura locale ad Azure risulti fuori servizio.

Il grafico seguente illustra una configurazione resiliente di questo tipo per il ripristino di emergenza:

![Configurazione ottimale per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-for-using-hana-large-instances-storage-replication-for-disaster-recovery"></a>Altri requisiti per l'uso della replica di archiviazione di istanze Large di HANA per il ripristino di emergenza

Gli altri requisiti per una configurazione di ripristino di emergenza con istanze Large di HANA sono i seguenti:

- È necessario ordinare SKU di SAP HANA in Azure (istanze Large) della stessa dimensione degli SKU di produzione e distribuirli nell'area di ripristino di emergenza. Nelle distribuzioni dei clienti presenti fino a questo momento queste istanze vengono usate per eseguire istanze di HANA non di produzione. In questo caso, si parla di configurazioni di ripristino di emergenza multifunzione.   
- È necessario ordinare spazio di archiviazione aggiuntivo nel sito di ripristino di emergenza per ogni SKU di SAP HANA in Azure (istanze Large) che si vuole ripristinare nel sito di ripristino di emergenza, se necessario. In questo modo vengono allocati volumi di archiviazione, che vengono usati come destinazione della replica di archiviazione dall'area di Azure di produzione all'area di Azure di ripristino di emergenza.

Altre informazioni sul ripristino di emergenza sono disponibili negli ultimi capitoli del documento.
 

## <a name="backup-and-restore"></a>Backup e ripristino

Uno degli aspetti più importanti per i database operativi è quello di assicurarsi che i database possano essere protetti da diversi eventi catastrofici. Questi eventi possono avere diverse cause, da disastri naturali a semplici errori umani.

Il backup di un database con possibilità di ripristino a una temporizzazione qualsiasi, ad esempio prima dell'eliminazione di dati critici, consente di ripristinare il database a uno stato che sia il più vicino possibile a quello precedente l'interruzione.

Per ottenere risultati ottimali, è necessario eseguire due tipi di backup:

- Backup dei database, completo, incrementale o differenziale
- Backup del log delle transazioni

Oltre a creare backup di database completi al livello applicazione, è possibile raggiungere risultati ancora migliori eseguendo i backup con gli snapshot di archiviazione. Gli snapshot di archiviazione non sostituiscono comunque i backup del log delle transazioni. I backup del log delle transazioni rimangono importanti per il ripristino temporizzato del database o per svuotare i log dalle transazioni di cui è già stato eseguito il commit. Gli snapshot di archiviazione consentono di accelerare il ripristino, fornendo un'immagine di rollforward del database in modo rapido. 

SAP HANA in Azure (istanze Large) offre due opzioni di backup e ripristino:

- Eseguire il backup e il ripristino manualmente. Dopo avere eseguito gli opportuni calcoli per assicurarsi che sia disponibile spazio su disco sufficiente, eseguire backup completi di database e log usando specifici metodi di backup direttamente sui volumi collegati alle unità di istanze Large di HANA o sulle condivisioni NFS configurate in una macchina virtuale di Azure. Nel secondo caso, i clienti configurano una macchina virtuale Linux in Azure, collegano Archiviazione di Azure alla macchina virtuale e condividono l'archiviazione tramite un server NFS configurato in tale macchina virtuale. Se si esegue il backup sui volumi direttamente collegati alle unità di istanze Large di HANA, i backup devono essere copiati in un account di archiviazione di Azure (dopo aver configurato una macchina virtuale di Azure che esporta le condivisioni NFS basate su Archiviazione di Azure) oppure è possibile usare un insieme di credenziali di Backup di Azure o l'archiviazione offline sicura di Azure. Un'altra soluzione consiste nell'usare uno strumento di protezione dei dati di terze parti per archiviare i backup dopo averli copiati in un account di archiviazione di Azure. L'opzione di backup manuale può essere necessaria anche per i dati che devono essere archiviati più a lungo a scopo di controllo e conformità. In tutti i casi, i backup vengono copiati in condivisioni NFS rappresentate tramite una macchina virtuale e Archiviazione di Azure.
- Usare la funzionalità per il backup e il ripristino offerta dall'infrastruttura sottostante di SAP HANA in Azure (istanze Large). Questa opzione soddisfa la necessità di backup e ripristini veloci. La parte restante di questa sezione descrive le funzionalità di backup e ripristino offerte con le istanze Large di HANA. Viene descritta anche la relazione con le funzionalità di ripristino di emergenza offerte dalle istanze Large di HANA.

> [!NOTE]
> La tecnologia di snapshot usata dall'infrastruttura sottostante di istanze Large di HANA presenta una dipendenza dagli snapshot SAP HANA. Al momento, gli snapshot SAP HANA non funzionano in combinazione con più tenant di contenitori di database multi-tenant SAP HANA. Di conseguenza, questo metodo di backup non può essere usato se sono stati distribuiti più tenant in contenitori di database multi-tenant SAP HANA. Il metodo funziona solo se è stato distribuito un unico tenant.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso degli snapshot di archiviazione di SAP HANA in Azure (istanze Large)

L'infrastruttura di archiviazione sottostante di SAP HANA in Azure (istanze Large) supporta il concetto di snapshot di archiviazione dei volumi. Il backup e il ripristino di un determinato volume sono supportati con le considerazioni seguenti:

- Invece di backup di database completi, vengono creati snapshot dei volumi di archiviazione a intervalli frequenti.
- Al momento della generazione di uno snapshot sui volumi /hana/data, hana/log e /hana/shared (che include /usr/sap), viene avviato uno snapshot SAP HANA prima di eseguire lo snapshot di archiviazione. Questo snapshot SAP HANA è il punto di installazione per i ripristini dei log finali dopo il ripristino dello snapshot di archiviazione.
- Dopo che l'esecuzione dello snapshot di archiviazione è stata completata correttamente, lo snapshot SAP HANA viene eliminato.
- I backup del log delle transazioni vengono creati di frequente e archiviati nel volume /hana/logbackups o in Azure. Il volume /hana/logbackups contiene i backup del log delle transazioni e può essere attivato come snapshot separatamente. In tal caso, non è necessario eseguire alcuno snapshot HANA.
- Se è necessario ripristinare il database a una determinata temporizzazione, viene inviata una richiesta al supporto tecnico di Microsoft Azure (interruzione di produzione) o a SAP HANA in Azure Service Management per il ripristino in base a uno specifico snapshot di archiviazione, ad esempio un ripristino pianificato di un sistema sandbox allo stato originale.
- Lo snapshot SAP HANA incluso nello snapshot di archiviazione è un punto di offset per l'applicazione dei backup del log delle transazioni che sono stati creati e archiviati dopo la creazione dello snapshot di archiviazione.
- Questi backup del log delle transazioni vengono creati per il ripristino temporizzato del database.

I clienti hanno la possibilità di eseguire snapshot di archiviazione su tre classi diverse di volumi:

- Snapshot combinato su /hana/data, /hana/log e /hana/shared (che include /usr/sap). Questo snapshot richiede l'esecuzione di uno snapshot SAP HANA
- Snapshot separato su /hana/logbackups
- Partizione del sistema operativo (solo per il tipo I di istanze Large di HANA)


### <a name="storage-snapshot-considerations"></a>Considerazioni sugli snapshot di archiviazione

>[!NOTE]
>Gli snapshot di archiviazione utilizzano spazio di archiviazione allocato alle unità di istanze Large di HANA. È necessario considerare quindi gli aspetti seguenti per la pianificazione degli snapshot di archiviazione e mantenere un certo numero di snapshot di archiviazione. 

I meccanismi specifici degli snapshot di archiviazione per SAP HANA in Azure (istanze Large) comprendono:

- Uno snapshot di archiviazione specifico (nel momento in cui viene creato) usa una quantità di spazio limitata.
- A mano a mano che il contenuto dei dati cambia e il contenuto dei file di dati di SAP HANA viene modificato nel volume di archiviazione, lo snapshot deve archiviare il contenuto dei blocchi originali.
- Le dimensioni dello snapshot di archiviazione aumentano progressivamente in proporzione alla durata.
- Quante più modifiche vengono apportate al volume del database SAP HANA per tutta la durata di uno snapshot di archiviazione, tanto più grande diventa lo spazio utilizzato da tale snapshot.

SAP HANA in Azure (istanze Large) viene fornito con volumi di dimensioni fisse per l'archiviazione di dati e log. L'esecuzione di snapshot di questi volumi comporta il consumo dello spazio dei volumi. È quindi necessario pianificare gli snapshot di archiviazione, monitorare il consumo di spazio dei volumi di archiviazione e gestire il numero di snapshot archiviati. Solo l'utente può stabilire se disabilitare gli snapshot di archiviazione perché vengono importate grandi quantità di dati o apportate altre modifiche significative al database HANA. 


Le sezioni seguenti includono informazioni per l'esecuzione di questi snapshot oltre a consigli di carattere generale:

- Anche se l'hardware può supportare 255 snapshot per volume, è consigliabile rimanere ben al di sotto di questa cifra.
- Prima di eseguire snapshot di archiviazione, monitorare e tenere traccia dello spazio libero.
- Limitare il numero di snapshot di archiviazione in base allo spazio disponibile. Potrebbe essere necessario ridurre il numero di snapshot conservati o potrebbe essere necessario estendere i volumi. È possibile ordinare spazio di archiviazione aggiuntivo in unità da 1 TB.
- Durante determinate attività, come lo spostamento di dati in SAP HANA con gli strumenti di migrazione della piattaforma SAP (R3load) o il ripristino di database SAP HANA dai backup, è necessario disabilitare gli snapshot di archiviazione nel volume /hana/data. 
- Durante una riorganizzazione più estesa delle tabelle di SAP HANA, evitare di creare snapshot di archiviazione, se possibile.
- Gli snapshot di archiviazione sono un prerequisito per l'uso delle funzionalità di ripristino di emergenza di SAP HANA in Azure (istanze Large).

### <a name="setting-up-storage-snapshots"></a>Configurazione degli snapshot di archiviazione

I passaggi generali per configurare gli snapshot di archiviazione con le istanze Large di HANA sono simili ai seguenti:
1. Assicurarsi che Perl sia installato nel sistema operativo Linux sul server HANA (istanze Large).
2. Modificare /etc/ssh/ssh\_config in modo da aggiungere la riga _MACs hmac-sha1_.
3. Creare un account utente di backup di SAP HANA sul nodo master per ogni istanza SAP HANA in esecuzione (se applicabile).
4. Il client HDB di SAP HANA deve essere installato in tutti i server SAP HANA (istanze Large).
5. Nel primo server SAP HANA (istanze Large) di ogni area è necessario creare una chiave pubblica per accedere all'infrastruttura di archiviazione sottostante che controlla la creazione di snapshot.
6. Copiare gli script e il file di configurazione da [questa posizione di GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) nel percorso di **hdbsql** dell'installazione di SAP HANA.
7. Modificare il file HANABackupDetails.txt come necessario, in base alle specifiche del cliente.

### <a name="step-1-install-sap-hana-hdbclient"></a>Passaggio 1: Installare il client HDB di SAP HANA

Il sistema operativo Linux installato in SAP HANA in Azure (istanze Large) include le cartelle e gli script necessari per eseguire snapshot di archiviazione SAP HANA a scopo di backup e ripristino di emergenza. Verificare se sono disponibili versioni più recenti in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). La versione più recente degli script è la 2.0.
È tuttavia responsabilità dell'utente installare il client HDB SAP HANA nelle unità di istanze Large di HANA durante l'installazione di SAP HANA. Il client HDB e SAP HANA non vengono installati da Microsoft.

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

Per consentire l'accesso alle interfacce degli snapshot di archiviazione del tenant delle istanze Large di HANA, è necessario stabilire un accesso tramite chiave pubblica. Nel primo server SAP HANA in Azure (istanze Large) nel tenant creare una chiave pubblica da usare per accedere all'infrastruttura di archiviazione in modo da poter creare gli snapshot. La chiave pubblica evita di dover usare una password per accedere alle interfacce degli snapshot di archiviazione e di dover gestire le credenziali password. In Linux, nel server SAP HANA (istanze Large), eseguire il comando seguente per generare la chiave pubblica:
```
  ssh-keygen –t dsa –b 1024
```
Il nuovo percorso è _/root/.ssh/id\_dsa.pub. Non immettere una password effettiva, altrimenti sarà necessario immetterla a ogni accesso. Premere invece **INVIO** due volte per evitare che venga richiesta una password in fase di accesso.

Verificare che la chiave pubblica sia corretta cambiando le cartelle in /root/.ssh/ e poi eseguendo il comando **ls**. Se la chiave è presente, è possibile copiarla eseguendo il comando seguente:

![La chiave pubblica viene copiata eseguendo questo comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

A questo punto, contattare SAP HANA in Azure Service Management e fornire la chiave pubblica. Il rappresentante del servizio usa la chiave pubblica per la registrazione nell'infrastruttura di archiviazione sottostante, creata per il tenant delle istanze Large di HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Passaggio 4: Creare un account utente SAP HANA

Per poter avviare la creazione di snapshot SAP HANA, è necessario creare un utente in SAP HANA, che può essere usato dagli script degli snapshot di archiviazione. In SAP HANA Studio creare un account utente SAP HANA per questi scopi. L'account deve disporre dei privilegi _Backup Admin_ e _Catalog Read_. In questo esempio viene creato il nome utente SCADMIN. Il nome dell'account utente creato in HANA Studio fa distinzione tra maiuscole e minuscole.  Assicurarsi di scegliere "No" per l'opzione di richiesta di modifica della password da parte dell'utente al momento dell'accesso successivo.

![Creazione di un utente in HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passaggio 5: Autorizzare l'account utente SAP HANA

In questo passaggio è necessario autorizzare l'account utente SAP HANA creato, in modo che gli script non debbano inviare password in fase di esecuzione. Il comando `hdbuserstore` di SAP HANA consente la creazione di una chiave utente SAP HANA, che viene archiviata in uno o più nodi di SAP HANA. La chiave utente consente all'utente di accedere a SAP HANA senza dover gestire le password dall'interno del processo di scripting, illustrato più avanti.

>[!IMPORTANT]
>Eseguire il comando indicato di seguito come `root`. Altrimenti lo script non funzionerà correttamente.

Immettere il comando `hdbuserstore` come segue:

![Immettere il comando hdbuserstore](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

Nell'esempio seguente, in cui l'utente è SCADMIN01, il nome host è lhanad01 e il numero di istanza è 01, il comando è:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Se si ha una configurazione di SAP HANA con scalabilità orizzontale, è necessario gestire tutte le attività di scripting da un singolo server. In questo esempio la chiave di SAP HANA SCADMIN01 deve essere modificata per ogni host in modo che rifletta l'host correlato alla chiave. In altre parole, l'account di backup SAP HANA viene modificato con il numero di istanza del database HANA. La chiave deve avere privilegi amministrativi sull'host a cui è assegnata e l'utente di backup per la scalabilità orizzontale deve disporre dei diritti di accesso a tutte le istanze SAP HANA. Supponendo che i tre nodi con scalabilità orizzontale siano denominati lhanad01, lhanad02 e lhanad03, la sequenza di comandi sarebbe simile alla seguente:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad02:30215 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad03:30315 SCADMIN <password>
```

### <a name="step-6-get-snapshot-scripts-configure-snapshots-and-test-configuration-and-connectivity"></a>Passaggio 6: Ottenere gli script degli snapshot, configurare gli snapshot e testare la configurazione e la connettività

Scaricare la versione più recente degli script [qui](https://github.com/Azure/hana-large-instances-self-service-scripts). Copiare il file di testo e gli script scaricati nella directory di lavoro per **hdbsql**. Per le installazioni correnti di HANA, la directory è simile a /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
azure\_hana\_replication\_status.pl
azure\_hana\_snapshot\_details.pl
azure\_hana\_snapshot\_delelte.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```

Lo scopo dei diversi file e script è il seguente:

- **Azure\_hana\_backup.pl**: questo script deve essere pianificato con Cron per l'esecuzione degli snapshot di archiviazione sui volumi data/log/shared HANA, sul volume /hana/logbackups o sul sistema operativo (per SKU di tipo I di istanze Large di HANA)
- **azure\_hana\_replication\_status.pl**: questo script è progettato per fornire informazioni di base sullo stato della replica dal sito di produzione al sito di ripristino di emergenza.  Lo script viene usato per monitorare l'esecuzione della replica e visualizzare le dimensioni degli elementi di cui viene eseguita la replica.  Fornisce anche indicazioni se una replica sta richiedendo troppo tempo o se il collegamento potrebbe essere interrotto.
- **azure\_hana\_snapshot\_details.pl**: lo scopo di questo script è quello di fornire al cliente un elenco di informazioni di base su tutti gli snapshot per volume nell'ambiente. Questo script può essere eseguito nel server primario o in un'unità server nella posizione di ripristino di emergenza. Lo script fornisce le informazioni seguenti per ogni volume contenente gli snapshot: dimensioni totali degli snapshot in un volume e quindi, per ogni snapshot in un volume, informazioni su: nome dello snapshot, ora di creazione, dimensioni dello snapshot, frequenza dello snapshot e ID di backup HANA associato allo snapshot (se pertinente).
- **azure\_hana\_snapshot\_delelte.pl**: questo script è progettato per eliminare uno snapshot di archiviazione o un set di snapshot usando l'ID di backup di SAP HANA indicato in HANA Studio o in base al nome dello snapshot di archiviazione stesso.  Attualmente, l'ID di backup è collegato solo agli snapshot creati per i volumi data/log/shared HANA.  In caso contrario, se viene immesso l'ID dello snapshot, vengono cercati tutti gli snapshot corrispondenti all'ID immesso.  
- **testHANAConnection.pl**: questo script testa la connessione all'istanza di SAP HANA ed è obbligatorio per la configurazione degli snapshot di archiviazione.
- **testStorageSnapshotConnection.pl**: questo script ha due scopi. In primo luogo, assicurarsi che l'unità di istanze Large di HANA che esegue gli script abbia accesso alla macchina virtuale di archiviazione assegnata e all'interfaccia degli snapshot di archiviazione delle istanze Large di HANA. In secondo luogo, creare uno snapshot temporaneo per l'istanza di HANA che si sta testando. Questo script deve essere eseguito per ogni istanza di HANA su un server, per assicurarsi che gli script di backup funzionino come previsto.
- **removeTestStorageSnapshot.pl**: questo script consente di eliminare lo snapshot di test creato con lo script testStorageSnapshotConnection.pl. 
- **HANABackupCustomerDetails.txt**: è un file di configurazione modificabile che deve essere modificato per adattarlo alla propria configurazione di SAP HANA

 
Il file HANABackupCustomerDetails.txt è il file di configurazione e controllo per lo script che esegue gli snapshot di archiviazione. È necessario modificare il file in base alla configurazione e agli scopi specifici. Si dovrebbe aver ricevuto i valori di _Storage Backup Name_ e _Storage IP Address_ da SAP HANA in Azure Service Management al momento della distribuzione delle istanze. Non è possibile modificare la sequenza, l'ordinamento o la spaziatura di alcuna delle variabili nel file. In caso contrario, gli script non funzioneranno correttamente. È stato inoltre ricevuto l'indirizzo IP del nodo di scalabilità verticale o del nodo master (in caso di scalabilità orizzontale) da SAP HANA in Azure Service Management. Dopo l'installazione di SAP HANA, si consce anche il numero di istanza di HANA e quindi è necessario aggiungere un nome di backup al file di configurazione

Per una distribuzione con scalabilità verticale o orizzontale, il file di configurazione sarà simile al seguente dopo che sono stati inseriti il nome del backup di archiviazione e l'indirizzo IP di archiviazione. Gli altri dati che è necessario inserire nel file di configurazione sono l'indirizzo IP del nodo singolo o del nodo master, il numero di istanza di HANA e il nome del backup, che è possibile scegliere:
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA Backup Name:
```

>[!NOTE]
>Attualmente solo i dettagli del nodo 1 vengono usati nell'effettivo script per lo snapshot di archiviazione HANA. Si consiglia di testare l'accesso da e verso tutti i nodi HANA in modo che, se il nodo di backup master dovesse cambiare, è già stato verificato che un altro nodo può prendere il suo posto modificando i dettagli nel nodo 1.

Dopo aver inserito i dati di configurazione nel file HANABackupCustomerDetails.txt, è necessario verificare se le configurazioni sono corrette in relazione ai dati di istanza di HANA usando lo script testHANAConnection.pl. Questo script è indipendente da una configurazione di SAP HANA con scalabilità verticale o orizzontale

```
testHANAConnection.pl
```

Se si ha una configurazione di SAP HANA con scalabilità orizzontale, verificare che l'istanza di HANA master abbia accesso a tutti i server e le istanze di HANA necessari. Non ci sono parametri per lo script di test, ma per la corretta esecuzione è necessario completare l'aggiunta dei dati nel file di configurazione HANABackupCustomerDetails.txt. Poiché vengono restituiti solo i codici di errore dei comandi della shell, lo script non può eseguire il controllo degli errori di ogni istanza. Anche in questo caso lo script fornisce alcuni commenti che è utile controllare.

Per eseguire lo script:
```
 ./testHANAConnection.pl
```
Se lo script riesce a ottenere lo stato dell'istanza HANA, mostra un messaggio che conferma la riuscita della connessione HANA.


Il passo del test successivo consiste nel verificare la connettività all'archiviazione in base ai dati inseriti nel file di configurazione HANABackupCustomerDetails.txt e nell'eseguire uno snapshot di test.  Prima di eseguire lo script azure\_hana\_backup.pl, è necessario eseguire questo test. Se un volume non contiene snapshot, non è possibile determinare se il volume è vuoto o si è verificato un errore SSH che non consente di ottenere i dettagli dello snapshot. Per questo motivo lo script esegue due passaggi:

- Verifica che le interfacce e la macchina virtuale di archiviazione dei tenant siano accessibili per lo script, per l'esecuzione degli snapshot.
- Crea uno snapshot di test, o fittizio, per ogni volume di ogni istanza HANA.

Per questo motivo, l'istanza HANA è inclusa come argomento. In caso di errore dell'esecuzione, non è possibile fornire funzionalità di controllo degli errori per la connessione di archiviazione. In casi di questo tipo lo script fornisce però utili suggerimenti.

Lo script viene eseguito nel modo seguente:
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
Come passaggio successivo, lo script cercherà di accedere all'archiviazione usando la chiave pubblica fornita nei passaggi di configurazione precedenti e i dati configurati in HANABackupCustomerDetails.txt. In caso di esito positivo, viene visualizzato il contenuto seguente:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Se si verificano problemi di connessione alla console di archiviazione, l'output sarà simile al seguente:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Dopo aver eseguito l'accesso alle interfacce della macchina virtuale di archiviazione, lo script continua con la fase 2 e crea uno snapshot di test come illustrato di seguito per una configurazione con scalabilità orizzontale a tre nodi di SAP HANA:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Se lo snapshot di test è stato eseguito correttamente con lo script, è possibile procedere con la configurazione degli snapshot di archiviazione effettivi. In caso di esito negativo, esaminare i problemi prima di procedere. Lo snapshot di test creato deve rimanere disponibile fino a quando non vengono eseguiti i primi snapshot reali.



### <a name="step-7-perform-snapshots"></a>Passaggio 7: Eseguire gli snapshot

Dopo il completamento di tutti i passaggi di preparazione, è possibile iniziare a definire la configurazione effettiva degli snapshot di archiviazione. Lo script da pianificare funziona con le configurazioni con scalabilità verticale e con scalabilità orizzontale di SAP HANA. L'idea è quella di pianificare l'esecuzione degli script tramite Cron. 

Ci sono tre tipi di backup di snapshot che è possibile creare:
- HANA (hana): backup di snapshot combinato che copre i volumi che contengono /hana/data, /hana/log e /hana/shared (che contiene anche /usr/sap). Da questo snapshot è possibile eseguire il ripristino di un singolo file.
- Log (logs): backup di snapshot del volume /hana/logbackups. Non viene attivato nessuno snapshot HANA per eseguire questo snapshot di archiviazione. Questo volume di archiviazione è il volume che deve contenere i backup del log delle transazioni SAP HANA, eseguiti più spesso per limitare l'aumento delle dimensioni dei log e impedire la potenziale perdita di dati. Da questo snapshot è possibile eseguire il ripristino di un singolo file. Non è consigliabile ridurre la frequenza al di sotto di cinque minuti.
- Avvio (boot): snapshot del volume che contiene il LUN di avvio dell'istanza Large di HANA. Questo backup di snapshot è possibile solo con SKU di tipo I di istanze Large di HANA. Non è possibile eseguire ripristini di file singoli dallo snapshot del volume che contiene il LUN di avvio.  


La sintassi di chiamata per questi tre diversi tipi di snapshot deve essere simile alla seguente:
```
HANA backup covering /hana/data, /hana/log, /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot manual 30

```

È possibile specificare i parametri seguenti:

- Il primo parametro da specificare caratterizza il tipo di backup di snapshot. I valori consentiti sono "hana", "logs" e "boot"
- Il secondo valore è il SID HANA (ad esempio HM3). Questo parametro non è necessario per eseguire un backup del volume di avvio.
- Il terzo parametro è l'etichetta di backup per il tipo di snapshot. Si prevede che il valore sia sempre uguale per gli snapshot pianificati di un tipo specifico o con una determinata frequenza.
- Il quarto parametro definisce la conservazione degli snapshot in modo indiretto, definendo il numero di snapshot con lo stesso prefisso (etichetta) da conservare. Questo parametro è importante per l'esecuzione pianificata tramite Cron. 

In caso di scalabilità orizzontale, lo script esegue alcuni controlli aggiuntivi per verificare che tutti i server HANA siano accessibili e che tutte le istanze di HANA restituiscano lo stato appropriato prima di procedere con la creazione dello snapshot SAP HANA seguito da uno snapshot di archiviazione.

L'esecuzione dello script azure\_hana\_backup.pl crea lo snapshot di archiviazione nelle seguenti tre fasi distinte:

- Eseguire uno snapshot SAP HANA.
- Eseguire uno snapshot di archiviazione.
- Rimuovere lo snapshot SAP HANA creato prima dell'esecuzione dello snapshot di archiviazione.

Per eseguire lo script, chiamarlo dalla cartella dell'eseguibile HDB in cui è stato copiato. 

Il periodo di conservazione viene gestito in base al numero di snapshot specificato come parametro quando si esegue lo script (ad esempio 30, come indicato in precedenza). Il periodo di tempo coperto dagli snapshot di archiviazione è quindi in funzione del periodo di esecuzione e del numero di snapshot specificati come parametro quando si esegue lo script. Se il numero di snapshot conservati supera il numero indicato come parametro nella chiamata dello script, lo snapshot di archiviazione meno recente con la stessa etichetta (nel caso precedente, _manual_) viene eliminato prima dell'esecuzione di un nuovo snapshot. Ciò significa che il numero specificato come ultimo parametro della chiamata è il numero che è possibile usare per controllare il numero di snapshot conservati. Con tale parametro è possibile controllare indirettamente anche lo spazio su disco usato per gli snapshot. 

>Non appena si modifica l'etichetta, il conteggio viene riavviato. Ciò significa che è necessario fare attenzione nell'uso delle etichette.

### <a name="snapshot-strategies"></a>Strategie per gli snapshot
La frequenza degli snapshot per i diversi tipi dipende dal fatto che si usi o meno la funzionalità di ripristino di emergenza di istanze Large di HANA. Poiché la funzionalità di ripristino di emergenza di istanze Large di HANA si basa su snapshot di archiviazione, potrebbero essere applicabili alcune indicazioni speciali in termini di frequenza e periodi di esecuzione degli snapshot di archiviazione. Le considerazioni e i suggerimenti seguenti presuppongono che NON si usi la funzionalità di ripristino di emergenza di istanze Large di HANA. Si usano invece gli snapshot di archiviazione come metodo di backup ed è possibile fornire il ripristino temporizzato per gli ultimi 30 giorni. Considerate le limitazioni del numero di snapshot e di spazio, i clienti che hanno eseguito la distribuzione hanno preso in considerazione questi tipi di requisiti:

- Requisiti dell'obiettivo tempo di ripristino per il ripristino temporizzato.
- Utilizzo dello spazio.
- Requisiti relativi a obiettivo del punto di ripristino e obiettivo del tempo di ripristino in caso di ripristino di emergenza
- Eventuale esecuzione di backup completi del database HANA sui dischi. Ogni volta che viene creato un backup completo del database sui dischi o che viene eseguita l'interfaccia _backint_, l'esecuzione di snapshot di archiviazione ha esito negativo. Se si pianifica l'esecuzione di backup completi del database oltre agli snapshot di archiviazione, assicurarsi che durante questo periodo l'esecuzione degli snapshot di archiviazione sia disabilitata.
- Il numero massimo di snapshot per volume è 255.


Per i clienti che non usano la funzionalità di ripristino di emergenza di istanze Large di HANA, il periodo degli snapshot è meno frequente. In questi casi, alcuni clienti eseguono snapshot combinati su /hana/data, /hana/log e /hana/shared (che include /usr/sap) in periodi di 12 o 24 ore e conservano questi snapshot per coprire un intero mese. Lo stesso vale per gli snapshot del volume di backup del log. L'esecuzione dei backup del log delle transazioni SAP HANA sul volume di backup del log avviene invece in periodi da 5 a 15 minuti.

Si consiglia di eseguire snapshot di archiviazione pianificati tramite cron, nonché di usare lo stesso script per tutti i backup e le esigenze di ripristino di emergenza, modificando l'input dello script in base ai diversi orari di backup richiesti. Gli snapshot sono tutti pianificati in modo diverso in Cron, in base alla frequenza di esecuzione: oraria, ogni 12 ore, giornaliera o settimanale. 

Una pianificazione Cron di esempio in /etc/crontab è simile a quanto segue:
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
Nell'esempio precedente c'è uno snapshot orario combinato che copre i volumi contenenti i percorsi /hana/data, /hana/log e /hana/shared (che include /usr/sap). Questo tipo di snapshot può essere usato per un recupero temporizzato più rapido entro due giorni. Su questi volumi c'è inoltre uno snapshot giornaliero. Si ottengono così due giorni di copertura tramite gli snapshot orari più quattro settimane di copertura tramite gli snapshot giornalieri. Inoltre, per il volume di backup del log delle transazioni viene eseguito il backup una volta al giorno. Anche questi backup vengono conservati per quattro settimane. Come è possibile vedere nella terza riga di crontab, il backup del log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. Si può anche notare che i minuti di avvio dei diversi processi Cron che eseguono snapshot di archiviazione sono scaglionati, in modo che gli snapshot non vengano eseguiti tutti contemporaneamente in un determinato momento. 

Nell'esempio seguente si esegue uno snapshot combinato che copre i volumi contenenti i percorsi /hana/data, /hana/log e /hana/shared (che include /usr/sap) su base oraria. Questi snapshot vengono conservati per circa due giorni. Gli snapshot dei volumi di backup del log delle transazioni vengono eseguiti ogni cinque minuti e conservati per quattro ore. Come prima, il backup del file di log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. Lo snapshot del volume di backup del log delle transazioni viene eseguito con un ritardo di due minuti dopo l'avvio del backup del log delle transazioni. In questi due minuti, in circostanze normali il backup del log delle transazioni SAP HANA viene completato. Come prima, il backup del volume contenente il LUN di avvio viene eseguito una volta al giorno tramite uno snapshot di archiviazione e conservato per quattro settimane.

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

Il grafico illustra le sequenze dell'esempio precedente, escluso il LUN di avvio:

![Relazione tra backup e snapshot](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot.PNG)

SAP HANA esegue regolari operazioni di scrittura sul volume /hana/log per documentare le modifiche di cui viene eseguito il commit nel database. A intervalli regolari, SAP HANA scrive un punto di salvataggio nel volume /hana/data. Come specificato in crontab, viene eseguito un backup del log delle transazioni SAP HANA ogni cinque minuti. È anche possibile vedere che viene eseguito uno snapshot SAP HANA ogni ora, in seguito all'attivazione di uno snapshot di archiviazione combinato sui volumi /hana/data, /hana/log e /hana/shared. Dopo la corretta esecuzione dello snapshot HANA, viene eseguito lo snapshot di archiviazione combinato. Come indicato in crontab, ogni cinque minuti, circa due minuti dopo il backup del log delle transazioni HANA, viene eseguito lo snapshot di archiviazione sul volume /hana/logbackup.


>[!IMPORTANT]
> L'uso di snapshot di archiviazione per i backup di SAP HANA è importante solo quando gli snapshot vengono eseguiti insieme ai backup del log delle transazioni SAP HANA. Questi backup del log delle transazioni devono coprire i periodi di tempo che intercorrono tra gli snapshot di archiviazione. 

Se agli utenti si è garantito un ripristino temporizzato di 30 giorni, è necessario che siano soddisfatti i requisiti seguenti:

- Possibilità di accedere a uno snapshot di archiviazione combinato su hana/data, /hana/log e /hana/shared che non risalga a più di 30 giorni prima.
- Backup del log delle transazioni contigui che coprono il tempo tra gli snapshot di archiviazione combinati. Lo snapshot meno recente del volume di backup del log delle transazioni deve quindi risalire a 30 giorni prima. A meno che, nel frattempo, non si siano copiati i backup del log delle transazioni in un'altra condivisione NFS in Archiviazione di Azure. In tal caso, è possibile eseguire il pull dei backup del log delle transazioni precedenti da tale condivisione NFS.

Per poter usufruire di snapshot di archiviazione e di eventuale replica di archiviazione dei backup del log delle transazioni, è necessario modificare la posizione in cui SAP HANA scrive i backup del log delle transazioni. Questa modifica può essere eseguita in HANA Studio, come illustrato di seguito. Anche se SAP HANA esegue il backup automatico di segmenti di log completi, è necessario specificare un intervallo di backup del log in modo deterministico. In particolare con l'opzione di ripristino di emergenza, in genere è consigliabile eseguire i backup del log con un periodo deterministico. Nel caso seguente, è stato scelto un intervallo di backup del log di 15 minuti.

![Pianificare i log di backup di SAP HANA in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

È possibile scegliere backup con una frequenza maggiore di ogni 15 minuti. In particolare in combinazione con il ripristino di emergenza. Alcuni clienti eseguono i backup del log delle transazioni ogni cinque minuti.  

Se in precedenza non è mai stato eseguito il backup del database, il passaggio finale consiste nell'eseguire un backup del database basato su file per creare una singola voce di backup nel catalogo di backup. In caso contrario SAP HANA non può avviare i backup del log specificato.

![Eseguire un backup basato su file per creare una singola voce di backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Dopo la prima corretta esecuzione degli snapshot di archiviazione, è anche possibile eliminare lo snapshot di test eseguito nel passaggio 6. A tale scopo, è necessario eseguire lo script removeTestStorageSnapshot.pl, come illustrato di seguito:
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoraggio del numero e della dimensione degli snapshot sul volume di archiviazione

Su un particolare volume di archiviazione è possibile monitorare il numero di snapshot e l'utilizzo dello spazio di archiviazione degli snapshot. Il comando `ls` non mostra i file e la directory degli snapshot, Il comando del sistema operativo Linux `du` mostra tuttavia i dettagli di tali snapshot di archiviazione, in quanto sono archiviati negli stessi volumi. Il comando può essere usato con le opzioni seguenti:

- `du –sh .snapshot` fornisce il totale di tutti gli snapshot all'interno della directory degli snapshot.
- `du –sh --max-depth=1` elenca tutti gli snapshot salvati nella cartella ".snapshot" e le dimensioni di ognuno di essi.
- `du –hc` fornisce la dimensione totale utilizzata da tutti gli snapshot.

Usare questi comandi per assicurarsi che gli snapshot creati e archiviati non usino tutto lo spazio di archiviazione sui volumi.

>[!NOTE]
>Gli snapshot del LUN di avvio non sono visibili con i comandi precedenti.

### <a name="getting-details-of-snapshots"></a>Recupero dei dettagli degli snapshot
Per ottenere maggiori dettagli degli snapshot, è anche possibile usare lo script azure\_hana\_snapshot\_details.pl. Questo script può essere eseguito in ognuna delle posizioni, se c'è un server attivo nella posizione di ripristino di emergenza. Lo script fornisce le informazioni seguenti per ogni volume contenente gli snapshot: dimensioni totali degli snapshot in un volume e quindi, per ogni snapshot in un volume, i dettagli seguenti: nome dello snapshot, ora di creazione, dimensioni dello snapshot, frequenza dello snapshot e ID di backup HANA associato allo snapshot (se pertinente). La sintassi di esecuzione dello script è simile alla seguente:

```
./azure_hana_snapshot_details.pl 
```

Poiché lo script cerca di recuperare l'ID di backup HANA, deve connettersi all'istanza di SAP HANA e quindi richiede che il file di configurazione HANABackupCustomerDetails.txt sia impostato correttamente. L'output di due snapshot di un volume può essere simile al seguente:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-storage-snapshot"></a>Ripristino a livello di file dallo snapshot di archiviazione
Per i tipi di snapshot "hana" e "logs" è possibile accedere agli snapshot direttamente nei volumi nella directory ".snapshot". È presente una sottodirectory per ogni snapshot. Dovrebbe essere possibile copiare ogni file coperto dallo snapshot nello stato in cui si trovava al momento dello snapshot da tale sottodirectory nella struttura di directory effettiva.

>[!NOTE]
>Il ripristino di file singoli non funziona per gli snapshot del LUN di avvio. La directory ".snapshot" non è esposta nel LUN di avvio. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Riduzione del numero di snapshot in un server

Come illustrato in precedenza, è possibile ridurre il numero di snapshot archiviati con una determinata etichetta. Gli ultimi due parametri del comando per avviare uno snapshot sono un'etichetta e il numero di snapshot da mantenere.

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

Nell'esempio precedente l'etichetta dello snapshot è _customer_ e il numero di snapshot con questa etichetta da conservare è _30_. Dovendo limitare l'uso dello spazio su disco, può essere opportuno ridurre il numero di snapshot archiviati. Il modo più semplice per ridurre il numero di snapshot, ad esempio a 15, consiste nell'eseguire lo script con l'ultimo parametro impostato su 15:

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

Se si esegue lo script con questa impostazione, il numero di snapshot, incluso il nuovo snapshot di archiviazione, è _15_. I 15 snapshot più recenti vengono conservati, mentre i 15 snapshot meno recenti vengono eliminati.

 >[!NOTE]
 > Questo script riduce il numero di snapshot solo se ci sono snapshot che risalgono a più di un'ora prima. Lo script non elimina gli snapshot che hanno meno di un'ora. Queste restrizioni sono correlate alla funzionalità di ripristino di emergenza facoltativa offerta.

Se non si vuole più mantenere un set di snapshot con un'etichetta di backup specifica (hanadaily negli esempi di sintassi), è possibile eseguire lo script con _0_ come valore di conservazione per rimuovere tutti gli snapshot corrispondenti all'etichetta. La rimozione di tutti gli snapshot può tuttavia influire sulle funzionalità di ripristino di emergenza.

Una seconda possibilità per eliminare snapshot specifici consiste nell'usare lo script azure\_hana\_snapshot\_delelte.pl. Questo script è progettato per eliminare uno snapshot o un set di snapshot usando l'ID di backup di HANA indicato in HANA Studio o in base al nome dello snapshot stesso.  Attualmente, l'ID di backup è collegato solo agli snapshot creati per il tipo di snapshot "hana". I backup degli snapshot di tipo "logs" e "boot" non eseguono uno snapshot SAP HANA. Per questi snapshot non c'è quindi un ID di backup. Se viene immesso il nome dello snapshot, vengono cercati tutti gli snapshot corrispondenti al nome immesso sui diversi volumi. La sintassi di chiamata dello script è la seguente:

```
./azure_hana_snapshot_delete.pl 

```

È necessario eseguire lo script come utente _ROOT_

Se si seleziona uno snapshot, è possibile eliminare ogni snapshot singolarmente. Viene prima di tutto chiesto il volume che contiene lo snapshot e quindi il nome effettivo dello snapshot. Se lo snapshot è presente in tale volume e risale a più di un'ora prima, viene eliminato. È possibile trovare i nomi dei volumi e degli snapshot eseguendo lo script azure_hana_snapshot_details. 

>[!IMPORTANT]
>Se ci sono dati che sono presenti solo nello snapshot che si sta eliminando, eseguendo l'eliminazione tali dati vengono persi per sempre.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Ripristino in base allo snapshot HANA più recente

Se si riscontra un problema di blocco dell'ambiente di produzione, il processo di ripristino da uno snapshot di archiviazione può essere avviato come richiesta di supporto del cliente tramite il supporto di Microsoft Azure. Uno scenario imprevisto di questo tipo potrebbe richiedere estrema urgenza se i dati sono stati eliminati in un sistema di produzione e l'unico modo per recuperarli è quello di ripristinare il database di produzione.

In un caso diverso, con minor urgenza, è possibile usare un ripristino temporizzato, che può essere pianificato con diversi giorni di anticipo. È possibile pianificare questo ripristino con SAP HANA in Azure Service Management anziché segnalare un problema ad alta priorità. Ad esempio si potrebbe pianificare un aggiornamento del software SAP con un nuovo Enhancement Package e successivamente dover eseguire il ripristino in base a uno snapshot che rappresenta lo stato precedente l'aggiornamento EHP.

Prima di eseguire la richiesta è necessario completare alcune operazioni di preparazione. In questo modo SAP HANA in Azure Service Management potrà gestire la richiesta e fornire i volumi ripristinati. Sarà quindi responsabilità del cliente ripristinare il database HANA in base agli snapshot. Ecco come preparare la richiesta:

>[!NOTE]
>L'interfaccia utente potrebbe essere diversa rispetto alle seguenti schermate, a seconda della versione di SAP HANA in uso.

1. Scegliere lo snapshot da ripristinare. Viene ripristinato solo il volume hana/data, se non è diversamente specificato. 

2. Arrestare l'istanza HANA.

 ![Arrestare l'istanza HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Smontare i volumi di dati in ogni nodo del database HANA. Se i volumi di dati sono ancora montati nel sistema operativo, il ripristino dello snapshot ha esito negativo.
 ![Smontare i volumi di dati in ogni nodo del database HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Inviare una richiesta al supporto tecnico di Azure per il ripristino di uno snapshot specifico.

 - Durante il ripristino: SAP HANA in Azure Service Management potrebbe chiedere di partecipare a una conferenza telefonica per coordinarsi, verificare e confermare che venga ripristinato lo snapshot di archiviazione corretto. 

 - Dopo il ripristino: SAP HANA in Azure Service Management invia una notifica per indicare quando è stato ripristinato lo snapshot di archiviazione.

5. Al termine del processo di ripristino montare nuovamente tutti i volumi di dati.

 ![Montare nuovamente tutti i volumi di dati](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Selezionare le opzioni di ripristino in SAP HANA Studio, se non vengono automaticamente visualizzate al momento della riconnessione al database HANA tramite SAP HANA Studio. L'esempio seguente illustra un ripristino in base all'ultimo snapshot HANA. Uno snapshot di archiviazione incorpora un singolo snapshot HANA. Pertanto, se si esegue il ripristino in base all'ultimo snapshot di archiviazione, lo snapshot HANA ripristinato è quello più recente. Se invece si esegue il ripristino in base a snapshot di archiviazione precedenti, è necessario individuare lo snapshot HANA basandosi sull'ora di creazione dello snapshot di archiviazione.

 ![Selezionare le opzioni di ripristino in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Selezionare **Recover the database to a specific data backup or storage snapshot** (Ripristina il database in base a un backup di dati o a uno snapshot di archiviazione specifico).

 ![La finestra "Specify Recovery Type" (Specifica tipo di ripristino)](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Scegliere **Specify backup without catalog** (Specifica il backup senza catalogo).

 ![La finestra "Specify Backup Location" (Specifica percorso backup)](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Nell'elenco **Destination Type** (Tipo di destinazione) selezionare **Snapshot**.

 ![La finestra "Specify the Backup to Recover" (Specifica il backup da ripristinare)](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Fare clic su **Finish** (Fine) per avviare il processo di ripristino.

 ![Fare clic su "Finish" (Fine) per avviare il processo di ripristino](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. Il database HANA viene ripristinato in base allo snapshot HANA incluso nello snapshot di archiviazione.

 ![Il database HANA viene ripristinato in base allo snapshot HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Ripristino allo stato più recente

La procedura seguente ripristina lo snapshot HANA incluso nello snapshot di archiviazione. Quindi ripristina il backup del log delle transazioni allo stato più recente del database prima di ripristinare lo snapshot di archiviazione.

>[!IMPORTANT]
>Prima di procedere assicurarsi di avere una catena contigua e completa di backup dei log delle transazioni. Senza questi backup non è possibile ripristinare lo stato corrente del database.

1. Completare i passaggi da 1 a 6 della procedura precedente in "Ripristino in base allo snapshot HANA più recente".

2. Selezionare **Recover the database to its most recent state** (Ripristina il database allo stato più recente).

 ![Selezionare "Recover the database to its most recent state" (Ripristina il database allo stato più recente)](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Specificare il percorso dei backup dei log di HANA più recenti. Il percorso deve contenere tutti i backup del log delle transazioni HANA dallo snapshot HANA allo stato più recente.

 ![Specificare il percorso dei backup dei log di HANA più recenti](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Selezionare un backup come base da cui ripristinare il database. In questo esempio lo snapshot HANA nello screenshot è lo snapshot HANA incluso nello snapshot di archiviazione. 

 ![Selezionare un backup come base da cui ripristinare il database](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Deselezionare la casella di controllo **Use Delta Backups** (Usa backup differenziali) se non sono stati creati backup differenziali tra l'ora di creazione dello snapshot HANA e lo stato più recente.

 ![Deselezionare la casella di controllo "Use Delta Backups" (Usa backup differenziali) se non esistono backup differenziali](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Nella schermata di riepilogo fare clic su **Finish** (Fine) per avviare la procedura di ripristino.

 ![Nella schermata di riepilogo fare clic su "Finish" (Fine)](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Ripristino a un'altra temporizzazione
Per eseguire il ripristino a una temporizzazione compresa tra il momento di creazione dello snapshot HANA (incluso nello snapshot di archiviazione) e un momento successivo al ripristino temporizzato dello snapshot HANA, procedere come segue:

1. Assicurarsi di avere tutti i backup dei log delle transazioni dal momento di creazione dello snapshot HANA al momento che si vuole ripristinare.
2. Iniziare la procedura riportata in "Ripristino allo stato più recente".
3. Al passaggio 2 della procedura, nella finestra **Specify Recovery Type** (Specifica tipo di ripristino), selezionare **Recover the database to the following point in time** (Ripristina il database alla temporizzazione seguente), specificare la temporizzazione e quindi completare i passaggi da 3 a 6.

### <a name="monitoring-the-execution-of-snapshots"></a>Monitorare l'esecuzione degli snapshot

Quando si usano snapshot di archiviazione di istanze Large di HANA, è anche necessario monitorare l'esecuzione di questi snapshot di archiviazione. Lo script che esegue uno snapshot di archiviazione scrive l'output in un file e lo salva nello stesso percorso degli script Perl. Viene scritto un file separato per ogni snapshot di archiviazione. L'output di ogni file mostra chiaramente le diverse fasi che lo script degli snapshot esegue:

- Ricerca dei volumi per i quali è necessario creare uno snapshot
- Ricerca degli snapshot creati da tali volumi
- Eliminazione di eventuali snapshot esistenti in modo da riflettere il numero di snapshot specificato
- Creazione di uno snapshot SAP HANA
- Creazione dello snapshot di archiviazione sui volumi
- Eliminazione dello snapshot SAP HANA
- Ridenominazione dello snapshot più recente in **".0"**

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
In questo esempio è possibile notare come lo script registra la creazione dello snapshot HANA. Nel caso di una configurazione con scalabilità orizzontale questa procedura viene avviata sul nodo master. Il nodo master avvia la creazione sincrona degli snapshot SAP HANA su ciascuno dei nodi di lavoro. Quindi viene creato lo snapshot di archiviazione. Dopo l'esecuzione degli snapshot di archiviazione, lo snapshot HANA viene eliminato. Viene eseguito nuovamente l'avvio dal nodo master.


## <a name="disaster-recovery-principles"></a>Principi relativi al ripristino di emergenza
Con le istanze Large di HANA, è disponibile una funzionalità di ripristino di emergenza tra gli indicatori di istanze Large di HANA in diverse aree di Azure. Ciò significa che se si distribuiscono unità di istanze Large di HANA nell'area Stati Uniti occidentali di Azure, è possibile usare unità di istanze Large di HANA nell'area Stati Uniti orientali come unità di ripristino di emergenza. Come accennato in precedenza, il ripristino di emergenza non viene configurato automaticamente perché richiede il pagamento di un'altra unità di istanze Large di HANA anche nell'area di ripristino di emergenza. La configurazione di ripristino di emergenza funziona per le configurazioni con scalabilità verticale e con scalabilità orizzontale. 

Negli scenari distribuiti fino a questo momento i clienti hanno usato l'unità nell'area di ripristino di emergenza per eseguire sistemi non di produzione che usano un'istanza di HANA installata. Lo SKU dell'unità di istanze Large di HANA deve corrispondere a quello usato per scopi di produzione. È quindi necessario ordinare altri volumi di dischi da usare come destinazioni per la replica di archiviazione dal sistema di produzione al sito di ripristino di emergenza. I volumi che vengono attualmente replicati nel sito di ripristino di emergenza includono:

- /hana/data
- /hana/log
- /hana/shared
- /hana/log/backup (che include /usr/sap)


Alla base della funzionalità di ripristino di emergenza offerta c'è la funzionalità di replica di archiviazione fornita dall'infrastruttura di istanze Large di HANA. La funzionalità usata per l'archiviazione non è costituita da un flusso costante di modifiche replicate in modo asincrono man mano che vengono apportate all'archiviazione. Si tratta invece di un meccanismo che si basa sul fatto che gli snapshot di questi volumi vengono creati a intervalli regolari. Il differenziale tra uno snapshot già replicato e un nuovo snapshot non ancora replicato viene quindi trasferito nel sito di ripristino di emergenza in volumi di dischi di destinazione (stesse dimensioni dei volumi di produzione). La prima volta è necessario trasferire i dati completi di un volume, prima che la quantità di dati diminuisca con i differenziali tra snapshot. Di conseguenza, i volumi nel sito di ripristino di emergenza contengono tutti gli snapshot dei volumi eseguiti nel sito di produzione. Ciò consente di usare tale sistema di ripristino di emergenza per ottenere uno stato precedente al fine di recuperare i dati persi senza eseguire il rollback del sistema di produzione.

Nei casi in cui si usa la replica di sistema HANA come funzionalità di disponibilità elevata nel sito di produzione, vengono replicati solo i volumi dell'istanza di livello 2 (o replica). Questa configurazione potrebbe comportare un ritardo nella replica di archiviazione nel sito di ripristino di emergenza se si mantiene o si disattiva l'unità server di replica secondaria (livello 2) o l'istanza di SAP HANA in questa unità. 

>[!IMPORTANT]
>Come con la replica di sistema HANA multilivello, un arresto dell'unità server o dell'istanza di livello 2 di HANA blocca la replica nel sito di ripristino di emergenza quando si usa la funzionalità di ripristino di emergenza di istanze Large di HANA.


>[!NOTE]
>La funzionalità di replica di archiviazione di istanze Large di HANA esegue il mirroring/replica degli snapshot di archiviazione. Se quindi non si eseguono snapshot di archiviazione come descritto nella sezione di questo documento relativa al backup, non può venire eseguita alcuna replica nel sito di ripristino di emergenza. L'esecuzione di snapshot di archiviazione è un prerequisito per la replica di archiviazione nel sito di ripristino di emergenza.

Per ridurre al minimo l'obiettivo del punto di ripristino, vengono impostati gli intervalli di replica seguenti nel servizio di istanze Large di HANA:
- I volumi coperti dallo snapshot combinato (tipo di snapshot = "hana") vengono replicati ogni 15 minuti nelle destinazioni dei volumi di archiviazione equivalenti nel sito di ripristino di emergenza.
- Il volume di backup del log delle transazioni (tipo di snapshot = "logs") viene replicato ogni tre minuti nelle destinazioni dei volumi di archiviazione equivalenti nel sito di ripristino di emergenza.

Per ridurre al minimo l'obiettivo del punto di ripristino, è necessario impostare quanto segue
- Eseguire uno snapshot di archiviazione di tipo "hana" (vedere il passaggio 7: Eseguire gli snapshot) ogni 30 minuti-1 ora.
- Eseguire i backup del log delle transazioni SAP HANA ogni cinque minuti.
- Eseguire uno snapshot di archiviazione di tipo "logs" ogni 5-15 minuti. Con questo intervallo, dovrebbe essere possibile ottenere un obiettivo del punto di ripristino di circa 15-25 minuti.

Con questa configurazione, la sequenza di backup del log delle transazioni, snapshot di archiviazione e replica del volume di backup del log delle transazioni HANA e di /hana/data, /hana/log e /hana/shared (che include /usr/sap) potrebbe essere simile a quanto illustrato in questo grafico.

 ![Relazione tra lo snapshot di backup del log delle transazioni e snapmirror sull'asse temporale](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Per ottenere un migliore obiettivo del punto di ripristino in caso di ripristino di emergenza, è possibile copiare i backup del log delle transazioni HANA da SAP HANA in Azure (istanze Large) nell'altra area di Azure. Per ottenere questa ulteriore riduzione dell'obiettivo del punto di ripristino, eseguire i passaggi generali seguenti:

1. Eseguire il backup del log delle transazioni di HANA il più frequentemente possibile in /hana/logbackups.
2. Copiare i backup del log delle transazioni, usando rsync, dopo che sono stati completati, nella condivisione NFS ospitata da Macchine virtuali di Azure, che si trovano in reti virtuali di Azure nell'area di produzione di Azure e nelle aree di ripristino di emergenza. È necessario connettere entrambe le reti virtuali di Azure al circuito di ExpressRoute che connette le istanze Large di HANA di produzione ad Azure (vedere il grafico nella sezione di questo documento relativa ai requisiti del ripristino di emergenza). 
3. Mantenere i backup del log delle transazioni in tale area nell'archiviazione NFS esportata collegata alla macchina virtuale.
4. In caso di failover di emergenza, integrare i backup del log delle transazioni disponibili nel volume /hana/logbackups con i backup del log delle transazioni acquisiti più di recente nella condivisione NFS nel sito di ripristino di emergenza. 
5. A questo punto, è possibile avviare il ripristino del backup del log delle transazioni al backup più recente che è stato possibile salvare nell'area di ripristino di emergenza.

## <a name="disaster-recovery-fail-over-procedure"></a>Procedura di failover di ripristino di emergenza
Nel caso in cui si voglia o si debba eseguire il failover nel sito di ripristino di emergenza, viene eseguito un processo in cui è necessario interagire con SAP HANA nel servizio operazioni di Azure. I passaggi generali per il processo sono simili ai seguenti:

- Poiché si esegue un'istanza non di produzione di HANA nell'unità di ripristino di emergenza di istanze Large di HANA, è necessario arrestare questa istanza. Si presuppone la presenza di un'istanza di produzione HANA inattiva preinstallata.
- È necessario assicurarsi che non ci siano più processi SAP HANA in esecuzione. A tale scopo, è possibile usare il comando: /usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList. L'output dovrebbe mostrare il processo hdbdaemon in uno stato arrestato e nessun altro processo HANA in esecuzione o con stato avviato.
- A questo punto, è necessario controllare il nome dello snapshot o l'ID di backup di SAP HANA in cui si vuole ripristinare il sito di ripristino di emergenza. In casi di ripristino di emergenza reali, questo snapshot è in genere il più recente. Ci si potrebbe però trovare in una situazione in cui è necessario recuperare dati persi. È quindi necessario scegliere uno snapshot precedente.
- È necessario contattare SAP HANA in Azure Service Management tramite una richiesta di supporto con priorità alta e richiedere il ripristino di tale snapshot/ID di backup HANA nel sito di ripristino di emergenza. Sul lato del servizio operazioni, vengono eseguiti i passaggi seguenti:
    - La replica degli snapshot dal volume di produzione ai volumi di ripristino di emergenza viene arrestata. Questo nel caso in cui non sia già successo, a causa dell'interruzione che ha colpito il sito di produzione.
    - Lo snapshot di archiviazione o l'ID di backup scelto viene ripristinato nei volumi di ripristino di emergenza.
    - Dopo il ripristino, i volumi di ripristino di emergenza sono disponibili per essere montati nelle unità di istanze Large di HANA nell'area di ripristino di emergenza.
- Il passaggio successivo da parte del cliente consiste nel montare i volumi di ripristino di emergenza nell'unità di istanze Large di HANA nel sito di ripristino di emergenza. 
- È quindi possibile avviare l'istanza di produzione di SAP HANA fino a questo momento inattiva.
- Se si sceglie di copiare ulteriormente i log di backup del log delle transazioni per ridurre l'obiettivo del punto di ripristino, è necessario unire questi backup del log delle transazioni alla directory /hana/logbackups di ripristino di emergenza appena montata. Non sovrascrivere i backup esistenti, ma copiare semplicemente quelli più recenti che non sono stati replicati dall'archiviazione.

La sequenza di passaggi successiva comporta il ripristino dell'istanza di produzione di SAP HANA in base allo snapshot di archiviazione ripristinato e ai backup del log delle transazioni disponibili. I passaggi sono simili ai seguenti:

Modificare il percorso di backup in/hana/logbackups con SAP HANA Studio, come illustrato di seguito ![Modifica del percorso di backup per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

SAP HANA analizza i percorsi dei file di backup e suggerisce il backup del log delle transazioni più recente per il ripristino. L'analisi può richiedere alcuni minuti, fino a quando non viene visualizzata una schermata simile alla seguente: ![Elenco dei backup del log delle transazioni per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

Nel passaggio successivo è necessario modificare alcune impostazioni predefinite eseguendo queste operazioni:

- Deselezionare **Use Delta Backups** (Usa backup differenziali)
- Selezionare **Initialize Log Area** (Inizializza area log) come illustrato sotto:

 ![Impostare l'inizializzazione dell'area del log](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

Nella schermata successiva premere **Finish** (Fine) come illustrato di seguito:

 ![Completare il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Dovrebbe venire visualizzata una finestra di stato, come quella illustrata qui. Tenere presente che l'esempio si riferisce a un ripristino di emergenza di una configurazione di SAP HANA con scalabilità orizzontale a 3 nodi.

 ![Stato del ripristino](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Nei casi in cui il ripristino sembra bloccarsi nella schermata di completamento e non viene visualizzata la schermata di stato, controllare se tutte le istanze di SAP HANA nei nodi di lavoro sono in esecuzione. Se necessario, avviare manualmente le istanze di SAP HANA.


### <a name="fail-back-from-dr-to-production-site"></a>Eseguire il failback dal ripristino di emergenza al sito di produzione
Si presuppone che il failover nel sito di ripristino di emergenza sia stato causato da problemi nell'area di Azure di produzione e non dalla necessità di recuperare dati persi. Significa quindi che per un determinato periodo l'ambiente di produzione è stato spostato nel sito di ripristino di emergenza. Quando i problemi relativi al sito di produzione vengono risolti, si vuole eseguire il failback nel sito di produzione. Poiché non si vogliono perdere dati, il ritorno al sito di produzione prevede diversi passaggi e una stretta collaborazione con SAP HANA nel servizio operazioni di Azure. È compito del cliente attivare il servizio operazioni per avviare la sincronizzazione nel sito di produzione una volta risolti i problemi.

La sequenza di passaggi è simile a quanto segue:

- In SAP HANA nel servizio operazioni di Azure avviata la sincronizzazione dei volumi di archiviazione di produzione dai volumi di archiviazione di ripristino di emergenza, che rappresentano ora lo stato di produzione. In questo stato, l'unità di istanze Large di HANA nel sito di produzione viene arrestata.
- SAP HANA nel servizio operazioni di Azure monitora la replica e verifica il completamento dell'aggiornamento prima di informare il cliente
- È necessario arrestare le applicazioni che usano l'istanza di produzione di HANA nel sito di ripristino di emergenza. Eseguire quindi un backup del log delle transazioni HANA e arrestare l'istanza di HANA in esecuzione nelle unità di istanze Large di HANA nel sito di ripristino di emergenza
- Dopo che l'istanza di HANA in esecuzione nell'unità di istanze Large di HANA nel sito di ripristino di emergenza è stata arrestata, è necessario sincronizzare di nuovo manualmente i volumi dei dischi.
- SAP HANA nel servizio operazioni di Azure avvia di nuovo l'unità di istanze Large di HANA nel sito di produzione e la consegna all'utente. Assicurarsi che l'istanza di SAP HANA sia in uno stato di arresto al momento dell'avvio dell'unità di istanze Large di HANA.
- A questo punto, è necessario eseguire gli stessi passaggi di ripristino del database eseguiti in precedenza per il failover nel sito di ripristino di emergenza.

### <a name="monitoring-disaster-recovery-replication"></a>Monitoraggio della replica di ripristino di emergenza

È possibile monitorare lo stato di avanzamento della replica di archiviazione eseguendo lo script azure\_hana\_replication\_status.pl. Questo script deve essere eseguito da un'unità in esecuzione nella posizione di ripristino di emergenza, altrimenti non funziona come previsto. Lo script funziona indipendentemente dal fatto che la replica sia attiva. Lo script può essere eseguito per ogni unità di istanze Large di HANA del tenant nella posizione di ripristino di emergenza. Non può essere usato per ottenere informazioni dettagliate sul volume di avvio.

Chiamare lo script come indicato di seguito:
```
./replication_status.pl <HANA SID>
```

L'output viene suddiviso per volume nelle sezioni seguenti:  

- Stato del collegamento
- Attività di replica corrente
- Snapshot più recente replicato 
- Dimensioni dello snapshot più recente
- Tempo di ritardo corrente tra l'ultima replica snapshot completata e il momento attuale.  

Lo stato del collegamento viene visualizzato come attivo, a meno che il collegamento tra le posizioni non sia interrotto o non sia in corso un evento di failover. L'attività di replica si riferisce al fatto che sia in corso la replica dei dati oppure che i dati siano inattivi o ci siano altre attività in corso nel collegamento. L'ultimo snapshot replicato deve essere visualizzato solo come "snapmirror". Vengono quindi visualizzate le dimensioni dell'ultimo snapshot. Infine, viene visualizzato il tempo di ritardo. Il tempo di ritardo rappresenta il tempo tra l'ora pianificata per la replica e il completamento della replica. Un tempo di ritardo può superare un'ora per la replica dei dati, in particolare per la prima replica iniziale, anche se la replica è stata avviata. Il tempo di ritardo continua ad aumentare fino a quando non viene completata la replica in corso.

Di seguito è illustrato un esempio di output:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```













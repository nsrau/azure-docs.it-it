---
title: Backup e ripristino di HANA su SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Come eseguire il backup e il ripristino di HANA su SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 69417551c1c8d410f75e74a8164c8b8a223ab835
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805330"
---
# <a name="backup-and-restore"></a>Backup e ripristino

>[!IMPORTANT]
>Questa documentazione non sostituisce la documentazione relativa all'amministrazione di SAP HANA o le note su SAP. Si presuppone che il lettore abbia una solida comprensione ed esperienza in termini di amministrazione e operazioni di SAP HANA, in particolare per quanto riguarda gli argomenti di backup, ripristino, disponibilità elevata e ripristino di emergenza. In questa documentazione, vengono mostrate schermate da SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

È importante esercitarsi con i passaggi e i processi adottati nell'ambiente e con le versioni di HANA. Alcuni processi descritti in questa documentazione sono stati semplificati per una migliore comprensione generale e non devono essere usati come passaggi dettagliati per manuali operativi finali. Se si vuole creare manuali operativi per le configurazioni, è necessario testare e provare i processi e documentare quelli correlati alle configurazioni specifiche. 

Uno degli aspetti più importanti per i database operativi è garantirne la protezione da eventi catastrofici riconducibili a cause diverse, da disastri naturali a semplici errori umani.

Il backup di un database con possibilità di ripristino a una temporizzazione qualsiasi, ad esempio prima dell'eliminazione di dati critici, consente di ripristinare il database a uno stato che sia il più vicino possibile a quello precedente l'interruzione.

Per ottenere risultati ottimali, è necessario eseguire due tipi di backup:

- Backup dei database: completo, incrementale o differenziale
- Backup dei log delle transazioni

Oltre a creare backup di database completi a livello di applicazione, è possibile anche creare backup con snapshot di archiviazione. Gli snapshot di archiviazione non sostituiscono i backup del log delle transazioni, che restano tuttavia importanti per il ripristino temporizzato del database o per svuotare i log dalle transazioni di cui è già stato eseguito il commit. Gli snapshot di archiviazione consentono comunque di accelerare il ripristino, fornendo rapidamente un'immagine di rollforward del database. 

SAP HANA in Azure (istanze Large) offre due opzioni di backup e ripristino:

- Backup e ripristino manuali. Dopo avere eseguito i calcoli necessari per garantire che vi sia spazio su disco sufficiente, eseguire backup completi di database e log usando uno dei metodi di backup su disco seguenti. È possibile eseguire il backup direttamente in volumi collegati alle unità HANA in istanze Large o in condivisioni NFS configurate in una macchina virtuale di Azure. In questo secondo caso, i clienti configurano una macchina virtuale Linux in Azure, collegano Archiviazione di Azure alla macchina virtuale e condividono l'archiviazione tramite un server NFS configurato nella macchina virtuale. Se si esegue il backup sui volumi direttamente collegati alle unità di istanze Large di HANA, è necessario copiare i backup in un account di archiviazione di Azure (dopo aver configurato una macchina virtuale di Azure che esporta le condivisioni NFS basate su Archiviazione di Azure) oppure è possibile usare un insieme di credenziali di backup di Azure o l'archiviazione offline sicura di Azure. 

   Un'altra soluzione consiste nell'usare uno strumento di protezione dei dati di terze parti per archiviare i backup dopo averli copiati in un account di archiviazione di Azure. L'opzione di backup manuale può essere necessaria anche per i dati che devono essere archiviati più a lungo a scopo di controllo e conformità. In tutti i casi, i backup vengono copiati in condivisioni NFS rappresentate tramite una macchina virtuale e Archiviazione di Azure.

- Funzionalità di backup e ripristino dell'infrastruttura. È anche possibile usare la funzionalità di backup e ripristino offerta dall'infrastruttura sottostante di SAP HANA in Azure (istanze Large). Questa opzione soddisfa la necessità di backup e ripristini veloci. La parte restante di questa sezione illustra le funzionalità di backup e ripristino offerte con le istanze Large di HANA. Questa sezione descrive anche la relazione tra il backup e il ripristino e la funzionalità di ripristino di emergenza offerta da HANA in istanze Large.

> [!NOTE]
>   La tecnologia di snapshot usata dall'infrastruttura sottostante di istanze Large di HANA presenta una dipendenza dagli snapshot SAP HANA. Attualmente gli snapshot SAP HANA non funzionano in combinazione con più tenant di contenitori di database multi-tenant SAP HANA. Se viene distribuito un solo tenant, gli snapshot SAP HANA funzionano ed è possibile usare questo metodo.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso degli snapshot di archiviazione di SAP HANA in Azure (istanze di grandi dimensioni)

L'infrastruttura di archiviazione sottostante di SAP HANA in Azure (istanze Large) supporta gli snapshot di archiviazione dei volumi. Il backup e il ripristino dei volumi è supportato con le considerazioni seguenti:

- Invece di backup di database completi, vengono creati snapshot dei volumi di archiviazione a intervalli frequenti.
- Quando viene attivato uno snapshot sui volumi /hana/data e /hana/shared (che include /usr/sap), la tecnologia degli snapshot avvia uno snapshot SAP HANA prima di eseguire lo snapshot di archiviazione. Questo snapshot di SAP HANA è il punto di installazione per i ripristini dei log finali dopo il ripristino dello snapshot di archiviazione. Per la riuscita dello snapshot di HANA è necessaria un'istanza di HANA attiva.  Nello scenario HSR, lo snapshot di archiviazione non è supportato nel nodo secondario corrente se in questo non è possibile eseguire lo snapshot di HANA.
- Al termine della corretta esecuzione dello snapshot di archiviazione, lo snapshot SAP HANA viene eliminato.
- I backup del log delle transazioni vengono creati di frequente e archiviati nel volume /hana/logbackups o in Azure. È possibile attivare il volume /hana/logbackups che contiene i backup del log delle transazioni per creare separatamente uno snapshot. In questo caso, non è necessario eseguire uno snapshot HANA.
- Se è necessario ripristinare un database a un determinato punto nel tempo, richiedere il supporto di Microsoft Azure (interruzione di produzione) o SAP HANA sul ripristino di Azure a uno specifico snapshot di archiviazione. ad esempio per un ripristino pianificato di un sistema sandbox allo stato originale.
- Lo snapshot SAP HANA incluso nello snapshot di archiviazione è un punto di offset per l'applicazione dei backup del log delle transazioni che sono stati creati e archiviati dopo la creazione dello snapshot di archiviazione.
- Questi backup del log delle transazioni vengono creati per il ripristino temporizzato del database.

È possibile eseguire snapshot di archiviazione su tre classi di volumi diverse:

- Snapshot combinato su /hana/data e /hana/shared (che include /usr/sap). Questo snapshot richiede la creazione di uno snapshot SAP HANA come fase preliminare per lo snapshot di archiviazione. Lo snapshot SAP HANA verifica che il database sia in uno stato coerente dal punto di vista dell'archiviazione. Questo per il processo di ripristino che rappresenta un punto da impostare.
- Snapshot separato su /hana/logbackups.
- Una partizione del sistema operativo.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Quando si scarica il pacchetto di script di snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts), si ottiene anche la documentazione PDF relativa agli script. Ogni pacchetto di script include la relativa documentazione PDF.

## <a name="storage-snapshot-considerations"></a>Considerazioni sugli snapshot di archiviazione

>[!NOTE]
>Gli snapshot di archiviazione usano spazio di archiviazione allocato alle unità di istanze Large di HANA. È necessario considerare gli aspetti seguenti correlati alla pianificazione di snapshot di archiviazione e il numero di snapshot di archiviazione da mantenere. 

I meccanismi specifici degli snapshot di archiviazione per SAP HANA in Azure (istanze Large) comprendono:

- Uno snapshot di archiviazione specifico (nel momento in cui viene creato) usa una quantità di spazio limitata.
- Ogni volta che il contenuto dei dati cambia e il contenuto dei file di dati di SAP HANA viene modificato nel volume di archiviazione, lo snapshot deve archiviare il contenuto dei blocchi originali.
- Le dimensioni dello snapshot di archiviazione, quindi, aumentano in proporzione alla durata.
- Quante più modifiche vengono apportate al volume del database SAP HANA per tutta la durata di uno snapshot di archiviazione, tanto più grande diventa lo spazio usato dallo snapshot.

SAP HANA in Azure (istanze Large) viene fornito con volumi di dimensioni fisse per l'archiviazione di dati e log. L'esecuzione di snapshot di questi volumi comporta il consumo dello spazio dei volumi. È quindi necessario pianificare l'esecuzione degli snapshot di archiviazione, monitorare lo spazio dei volumi di archiviazione occupato e gestire il numero di snapshot archiviati. È possibile disabilitare gli snapshot di archiviazione quando vengono importate grandi quantità di dati o apportate altre modifiche significative al database HANA. 


Le sezioni seguenti includono informazioni per l'esecuzione di questi snapshot oltre a consigli di carattere generale:

- Anche se l'hardware può supportare 255 snapshot per volume, è consigliabile restare ben al di sotto di questo numero. È consigliabile un numero minore o uguale a 250.
- Prima di eseguire snapshot di archiviazione, monitorare e tenere traccia dello spazio libero.
- Limitare il numero di snapshot di archiviazione in base allo spazio disponibile. È possibile ridurre il numero di snapshot mantenuti o estendere i volumi. È possibile anche ordinare spazio di archiviazione aggiuntivo in unità da 1 TB.
- Durante particolari attività, come lo spostamento di dati in SAP HANA con gli strumenti di migrazione della piattaforma SAP (R3load) o il ripristino di database SAP HANA dai backup, è necessario disabilitare gli snapshot di archiviazione nel volume /hana/data. 
- Durante una riorganizzazione più estesa delle tabelle di SAP HANA, evitare di creare snapshot di archiviazione, se possibile.
- Gli snapshot di archiviazione sono un prerequisito per trarre vantaggio dalle funzionalità di ripristino di emergenza di SAP HANA in Azure (istanze Large).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Prerequisiti per l'uso di snapshot di archiviazione self-service

Per la corretta esecuzione dello script dello snapshot, assicurarsi che Perl sia installato nel sistema operativo Linux nel server HANA in istanze Large. Perl è preinstallato sul unità HANA istanze grandi. Per verificare la versione di Perl, usare il comando seguente:

`perl -v`

![La chiave pubblica viene copiata eseguendo questo comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurare gli snapshot di archiviazione

Per configurare gli snapshot di archiviazione con HANA in istanze Large, completare questi passaggi:
1. Assicurarsi che Perl sia installato nel sistema operativo Linux sul server HANA (istanze Large).
1. Modificare /etc/ssh/ssh\_config in modo da aggiungere la riga _MACs hmac-sha1_.
1. Creare un account utente di backup di SAP HANA sul nodo master per ogni istanza di SAP HANA in esecuzione (se applicabile).
1. Installare il client HDB di SAP HANA in tutti i server SAP HANA (istanze Large).
1. Nel primo server SAP HANA (istanze Large) di ogni area, creare una chiave pubblica per accedere all'infrastruttura di archiviazione sottostante che controlla la creazione di snapshot.
1. Copiare gli script e il file di configurazione da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) nel percorso di **hdbsql** dell'installazione di SAP HANA.
1. Modificare il file *HANABackupDetails.txt* nel modo necessario, in base alle specifiche del cliente.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Quando si scarica il pacchetto di script di snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts), si ottiene anche la documentazione PDF relativa agli script. Ogni pacchetto di script include la relativa documentazione PDF.

### <a name="consideration-for-mcod-scenarios"></a>Considerazioni per gli scenari MCOD
Se si esegue uno [scenario MCOD](https://launchpad.support.sap.com/#/notes/1681092) con più istanze di SAP HANA in un'unità HANA in istanze Large, verrà effettuato il provisioning di volumi di storage separati per ognuna delle istanze di SAP HANA. Nella versione corrente dell'automazione degli snapshot self-service non è possibile avviare snapshot separati in ogni ID sistema (SID) delle istanze HANA. La funzionalità fornisce controlli per le istanze di SAP HANA registrate del server nel file di configurazione (vedere più avanti in questo articolo) ed esegue uno snapshot simultaneo dei volumi di tutte le istanze registrate nell'unità.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passaggio 1: Installare il client HDB di SAP HANA

Il sistema operativo Linux installato su SAP HANA in Azure (istanze Large) include le cartelle e gli script necessari per eseguire snapshot di archiviazione SAP HANA per scopi di backup e ripristino di emergenza. Verificare se sono disponibili versioni più recenti in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). La versione più recente degli script è la 3.x. Diversi script potrebbero avere versioni secondarie diverse all'interno della stessa versione principale.

>[!IMPORTANT]
>Passando dalla versione 2.1 alla versione 3.x degli script, la struttura dei file di configurazione e parte della sintassi sono diverse. Vedere i callout nelle sezioni specifiche. 

È necessario installare personalmente il client HDB di SAP HANA nelle unità HANA in istanze Large durante l'installazione di SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Passaggio 2: Modificare /etc/ssh/ssh\_config

Modificare `/etc/ssh/ssh_config` aggiungendo la riga _MACs hmac-sha1_ come illustrato di seguito:
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

Per consentire l'accesso alle interfacce degli snapshot di archiviazione del tenant di HANA in istanze Large, è necessario definire una procedura di accesso tramite chiave pubblica. Nel primo server SAP HANA in Azure (istanze Large) nel tenant creare una chiave pubblica da usare per accedere all'infrastruttura di archiviazione. La creazione di una chiave pubblica evita di dover usare una password per accedere alle interfacce degli snapshot di archiviazione e di dover gestire le credenziali di tipo password. In Linux, nel server SAP HANA (istanze Large), eseguire il comando seguente per generare la chiave pubblica:
```
  ssh-keygen -t rsa –b 5120 -C ""
```

Il nuovo percorso sia **_/root/.ssh/id\_pub**. Non immettere una password effettiva, altrimenti sarà necessario immetterla a ogni accesso. Premere invece **Invio** due volte per evitare che venga richiesta una password in fase di accesso.

Verificare che la chiave pubblica sia corretta sostituendo le cartelle con **/root/.ssh/** e quindi eseguendo il comando `ls`. Se la chiave è presente, è possibile copiarla eseguendo il comando seguente:

![La chiave pubblica viene copiata eseguendo questo comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

A questo punto, contattare SAP HANA in Azure e fornire loro la chiave pubblica. Il rappresentante del servizio usa la chiave pubblica per la registrazione nell'infrastruttura di archiviazione sottostante, creata per il tenant delle istanze Large di HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Passaggio 4: Creare un account utente SAP HANA

Per avviare la creazione di snapshot SAP HANA, è necessario creare un account utente in SAP HANA che possa essere usato dagli script degli snapshot di archiviazione. Creare quindi un account utente SAP HANA in SAP HANA Studio. L'utente deve essere creato in SYSTEMDB e NON nel database SID per MDC. In un ambiente contenitore singolo, l'utente viene impostato nel database tenant. L'account deve disporre dei privilegi seguenti: **Backup Admin** e **Catalog Read**. In questo esempio, il nome utente è **SCADMIN**. Il nome dell'account utente creato in HANA Studio fa distinzione tra maiuscole e minuscole. Assicurarsi di selezionare **No** se si vuole che l'utente modifichi la password all'accesso successivo.

![Creazione di un utente in HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

In caso di distribuzioni MCOD con più istanze di SAP HANA in un'unità, questo passaggio deve essere ripetuto per ogni istanza di SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passaggio 5: Autorizzare l'account utente SAP HANA

In questo passaggio si autorizza l'account utente SAP HANA creato in modo che gli script non debbano inviare password in fase di esecuzione. Il comando `hdbuserstore` di SAP HANA consente la creazione di una chiave utente SAP HANA, che viene archiviata in uno o più nodi di SAP HANA. La chiave utente consente all'utente di accedere a SAP HANA senza dover gestire le password dall'interno del processo di scripting, descritto più avanti in questo articolo.

>[!IMPORTANT]
>Eseguire il comando seguente con l'utente previsto per l'esecuzione degli script. Altrimenti lo script non funzionerà correttamente.

Immettere il comando `hdbuserstore` come segue:

**Per un'installazione HANA non MDC**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**Per installazione HANA MDC**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

Nell'esempio seguente, l'utente è **SCADMIN01**, il nome host è **lhanad01** e il numero di istanza è **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Se si usa una distribuzione HANA MCOD con più istanze di SAP HANA in un'unità, il passaggio deve essere ripetuto per ogni istanza di SAP HANA e per l'utente della copia di backup associato nell'unità.

In presenza di una configurazione di SAP HANA con scalabilità orizzontale, è necessario gestire tutte le attività di scripting da un unico server. In questo esempio la chiave di SAP HANA **SCADMIN01** deve essere modificata per ogni host in modo da riflettere l'host correlato alla chiave. Modificare quindi l'account di backup SAP HANA con il numero di istanza del database HANA. La chiave deve avere privilegi amministrativi nell'host cui è assegnata e l'utente del backup per configurazioni con scalabilità orizzontale deve avere diritti di accesso a tutte le istanze di SAP HANA. Supponendo che i tre nodi con scalabilità orizzontale siano denominati **lhanad01**, **lhanad02** e **lhanad03**, la sequenza di comandi sarebbe simile alla seguente:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passaggio 6: Ottenere gli script degli snapshot, configurare gli snapshot e testare la configurazione e la connettività

Scaricare la versione più recente degli script da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Copiare il file di testo e gli script scaricati nella directory di lavoro per **hdbsql**. Per le installazioni correnti di HANA, la directory ha questo formato: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_test_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

Quando si usano script Perl: 

- Non modificare mai gli script, a meno che non venga richiesto da Microsoft Operations.
- Quando viene richiesto di modificare lo script o un file dei parametri, usare sempre l'editor di testo di Linux, ad esempio "vi", e non un editor di Windows come il Blocco note. L'uso di un editor di Windows può danneggiare il formato del file.
- Usare sempre gli script più recente. È possibile scaricare l'ultima versione da GitHub.
- Usare la stessa versione di script nell'orientamento orizzontale.
- Testare gli script e acquisire familiarità con i parametri necessari e con l'output dello script prima di usarlo direttamente nel sistema di produzione.
- Non modificare il nome del punto di montaggio del server fornito da Microsoft Operations. Questi script si basano su questi punti di montaggio standard disponibili per un'esecuzione corretta.


Di seguito viene indicato lo scopo dei diversi script e file:

- **azure\_hana\_backup.pl**: questo script è pianificato con l'utilità di pianificazione Cron per Linux per eseguire gli snapshot di archiviazione su dati e volumi condivisi HANA, sul volume /hana/logbackups o nel sistema operativo.
- **azure\_hana\_replication\_status.pl**: questo script fornisce informazioni di base sullo stato della replica dal sito di produzione al sito di ripristino di emergenza. Lo script consente di monitorare l'effettiva esecuzione della replica e di visualizzare le dimensioni degli elementi di cui viene eseguita la replica. Consente anche di sapere se una replica sta richiedendo troppo tempo o se il collegamento è interrotto.
- **azure\_hana\_snapshot\_details.pl**: questo script fornisce un elenco di informazioni di base su tutti gli snapshot, suddivisi per volume, presenti nell'ambiente. Questo script può essere eseguito nel server primario o in un'unità server nella posizione di ripristino di emergenza. Lo script fornisce le informazioni seguenti, suddivise per ogni volume contenente snapshot:
   * Dimensioni degli snapshot totali in un volume
   * Per ogni snapshot nel volume, vengono fornite le informazioni seguenti: 
      - Nome dello snapshot 
      - Ora di creazione 
      - Dimensioni dello snapshot
      - Frequenza dello snapshot
      - ID di backup HANA associato allo snapshot (se pertinente)
- **azure\_hana\_snapshot\_delete.pl**: questo script elimina uno snapshot di archiviazione o un set di snapshot. A questo scopo, è possibile usare l'ID backup di SAP HANA, indicato in HANA Studio, o il nome dello snapshot di archiviazione. Attualmente, l'ID di backup è collegato solo agli snapshot creati per i volumi data/log/shared HANA. In caso contrario, se viene immesso l'ID dello snapshot, vengono cercati tutti gli snapshot corrispondenti all'ID immesso.  
- **testHANAConnection.pl**: questo script testa la connessione all'istanza di SAP HANA ed è obbligatorio per configurare gli snapshot di archiviazione.
- **testStorageSnapshotConnection.pl**: questo script ha un duplice obiettivo. In primo luogo, garantisce che l'unità HANA in istanze Large che esegue gli script abbia accesso alla macchina virtuale di archiviazione assegnata e all'interfaccia degli snapshot di archiviazione di HANA in istanze Large. In secondo luogo, crea uno snapshot temporaneo per l'istanza di HANA che si sta testando. Questo script deve essere eseguito per ogni istanza di HANA su un server, per assicurarsi che gli script di backup funzionino come previsto.
- **removeTestStorageSnapshot.pl**: questo script elimina lo snapshot di test creato con lo script **testStorageSnapshotConnection.pl**.
- **azure\_hana\_dr\_failover.pl**: questo script avvia un failover di ripristino di emergenza in un'altra area. Lo script deve essere eseguito nell'unità HANA in istanze Large nell'area di ripristino di emergenza o nell'unità in cui si vuole eseguire il failover. Questo script arresta la replica di archiviazione dal lato primario al lato secondario, ripristina lo snapshot più recente nei volumi di ripristino di emergenza e fornisce i punti di montaggio per i volumi di ripristino di emergenza.
- **azure\_hana\_test\_dr\_failover.pl**: questo script esegue un failover di test nel sito di ripristino di emergenza. Diversamente dallo script azure_hana_dr_failover.pl, questa esecuzione non interrompe la replica di archiviazione dal lato primario al lato secondario. Al contrario, i cloni dei volumi di archiviazione replicata vengono creati sul lato di ripristino di emergenza e vengono forniti i punti di montaggio dei volumi clonati. 
- **HANABackupCustomerDetails.txt**: questo è un file di configurazione modificabile che deve essere cambiato per adattarsi alla configurazione di SAP HANA personale. Il file *HANABackupCustomerDetails.txt* è il file di configurazione e controllo per lo script che esegue gli snapshot di archiviazione. Modificare il file in base alla configurazione e agli scopi specifici. Viene visualizzato il **Storage Backup Name** e il **Storage IP Address** da SAP HANA in Azure durante la distribuzione delle istanze. Non è possibile modificare la sequenza, l'ordinamento o la spaziatura di alcuna delle variabili nel file. In caso contrario, gli script non vengono eseguiti correttamente. Inoltre, viene visualizzato l'indirizzo IP del nodo di scalabilità verticale o del nodo master (se orizzontale) da SAP HANA in Azure. Viene infine fornito il numero di istanze di HANA ottenuto durante l'installazione di SAP HANA. È ora necessario aggiungere un nome di backup al file di configurazione.

Per una distribuzione con scalabilità orizzontale o verticale, il file di configurazione sarà simile all'esempio seguente dopo aver immesso il nome del server dell'unità HANA in istanze Large e l'indirizzo IP del server. Completare tutti i campi necessari per ogni SID SAP HANA di cui si vuole eseguire il backup o il ripristino.

È anche possibile impostare come commento le righe delle istanze di cui non si vuole eseguire il backup per un certo periodo di tempo aggiungendo "#" davanti a un campo obbligatorio. Inoltre, non è necessario immettere tutte le istanze di SAP HANA contenute in un server se non è necessario eseguire il backup o il ripristino di un'istanza specifica. Il formato deve essere mantenuto per tutti i campi. In caso contrario, tutti gli script generano un messaggio di errore e lo script viene terminato. È possibile eliminare le necessarie righe aggiuntive delle informazioni sui SID inutilizzate dopo l'ultima istanza di SAP HANA in uso. Tutte le righe devono essere completate, impostate come commento o eliminate.

>[!IMPORTANT]
>La struttura del file è stata modificata con il passaggio dalla versione 2.1 alla versione 3.x. Se si vogliono usare gli script della versione 3.x, è necessario adattare la struttura del file di configurazione. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Per ogni istanza configurata nell'unità HANA in istanze Large o per la configurazione con scalabilità orizzontale, è necessario definire i dati in questo modo:

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
Per le configurazioni con scalabilità orizzontale e di replica di sistema HANA, ripetere questa configurazione su ciascuno dei nodi. Questa misura garantisce che in caso di errore, i backup e la replica dell'archiviazione finale possano comunque continuare a funzionare.   

Dopo aver inserito tutti i dati di configurazione nel file *HANABackupCustomerDetails.txt*, verificare che le configurazioni siano corrette in relazione ai dati delle istanze di HANA. Usare lo script `testHANAConnection.pl`, che è indipendente da una configurazione di SAP HANA con scalabilità orizzontale o verticale.

```
testHANAConnection.pl
```

Se si ha una configurazione di SAP HANA con scalabilità orizzontale, verificare che l'istanza di HANA master abbia accesso a tutti i server e le istanze di HANA necessari. Non esistono parametri per lo script di test, ma per la corretta esecuzione dello script è necessario aggiungere i propri dati nel file di configurazione *HANABackupCustomerDetails.txt*. Vengono restituiti solo i codici di errore dei comandi della shell e, pertanto, lo script non può eseguire il controllo degli errori di ogni istanza. Anche in questo caso lo script fornisce alcuni commenti che è utile controllare.

Per eseguire lo script, immettere il comando seguente:
```
 ./testHANAConnection.pl
```
Se lo script riesce a ottenere lo stato dell'istanza HANA, mostra un messaggio che conferma la riuscita della connessione HANA.


Il passaggio successivo consiste nel verificare la connettività all'archiviazione in base ai dati inseriti nel file di configurazione *HANABackupCustomerDetails.txt*, per poi eseguire uno snapshot di test. Prima di eseguire lo script `azure_hana_backup.pl`, è necessario eseguire questo test. Se un volume non contiene snapshot, non è possibile determinare se il volume è vuoto o se si è verificato un errore SSH che non permette di ottenere i dettagli dello snapshot. Per questo motivo lo script esegue due passaggi:

- Verifica che le interfacce e la macchina virtuale di archiviazione del tenant siano accessibili allo script per poter eseguire gli snapshot.
- Crea uno snapshot di test, o fittizio, per ogni volume di ogni istanza HANA.

Per questo motivo, l'istanza HANA è inclusa come argomento. In caso di errore dell'esecuzione, non è possibile fornire funzionalità di controllo degli errori per la connessione di archiviazione. Anche se non è disponibile la funzione di controllo degli errori, lo script fornisce comunque utili suggerimenti.

1. Eseguire la sequenza di comandi per realizzare questo test:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Sia il nome utente di archiviazione sia l'indirizzo IP di archiviazione sono stati forniti al trasferimento dell'unità Istanza Grande HANA.

1. Eseguire lo script di test:
   ```
    ./testStorageSnapshotConnection.pl
   ```

Lo script prova ad accedere all'archiviazione usando la chiave pubblica fornita nei passaggi di configurazione precedenti e i dati configurati nel file *HANABackupCustomerDetails.txt*. In caso di esito positivo, viene visualizzato il contenuto seguente:

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

Dopo aver eseguito l'accesso alle interfacce della macchina virtuale di archiviazione, lo script continua con la fase 2 e crea uno snapshot di test. Di seguito è riportato l'output generato con una configurazione di SAP HANA con scalabilità orizzontale a tre nodi:

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

Se lo snapshot di test è stato eseguito correttamente con lo script, è possibile procedere con la configurazione degli snapshot di archiviazione effettivi. Se l'esecuzione non riesce, analizzare i problemi prima di continuare. Lo snapshot di test deve rimanere disponibile fino a quando non vengono eseguiti i primi snapshot reali.


### <a name="step-7-perform-snapshots"></a>Passaggio 7: Eseguire gli snapshot

Al termine di tutti i passaggi di preparazione, è possibile iniziare a definire la configurazione effettiva degli snapshot di archiviazione. Lo script da pianificare funziona con le configurazioni con scalabilità verticale e con scalabilità orizzontale di SAP HANA. Per l'esecuzione periodica e regolare dello script di backup, pianificare lo script tramite l'utilità cron. 

È possibile creare tre tipi di backup di snapshot:
- **HANA**: backup di snapshot combinato che copre i volumi che contengono /hana/data e /hana/shared, che contiene anche /usr/sap. Da questo snapshot è possibile eseguire il ripristino di un singolo file.
- **Log**: backup di snapshot del volume /hana/logbackups. Non viene attivato nessuno snapshot HANA per eseguire questo snapshot di archiviazione. Questo volume di archiviazione è destinato a contenere i backup del log delle transazioni SAP HANA, che vengono eseguiti più spesso per limitare l'aumento delle dimensioni del log e impedire la possibile perdita di dati. Da questo snapshot è possibile eseguire il ripristino di un singolo file. Non ridurre la frequenza al di sotto di tre minuti.
- **Boot**: snapshot del volume che contiene il numero di unità logica (LUN, Logical Unit Number) di avvio dell'istanza Large di HANA. Questo backup di snapshot è possibile solo con SKU di tipo I di istanze Large di HANA. Non è possibile eseguire ripristini di file singoli dallo snapshot del volume che contiene il LUN di avvio.


>[!NOTE]
> La sintassi di chiamata per questi tre tipi di snapshot è cambiata con il passaggio agli script della versione 3.x, che supportano le distribuzioni MCOD. Non è più necessario specificare il SID HANA di un'istanza. È necessario verificare che le istanze di SAP HANA di un'unità siano configurate nel file di configurazione *HANABackupCustomerDetails.txt*.

>[!NOTE]
> Quando si esegue lo script per la prima volta, si possono riscontrare alcuni errori imprevisti nell'ambiente con più SID. Una nuova esecuzione dello script permette di risolvere il problema.



La nuova sintassi di chiamata per l'esecuzione di snapshot di archiviazione con lo script *azure_hana_backup.pl* ha un aspetto simile al seguente:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

I dettagli dei parametri sono i seguenti: 

- Il primo parametro caratterizza il tipo di backup di snapshot. I valori consentiti sono **hana**, **logs** e **boot**. 
- Il parametro  **\<tipo di istanza Large di HANA >** è necessario solo il backup del volume boot. Esistono due valori validi con "TypeI" o "TypeII" in base all'unità dell'Istanza Grande HANA. Per identificare il tipo di unità, vedere [Panoramica e architettura di SAP HANA (istanze Large) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Il parametro  **\<snapshot_prefix >** snapshot o etichetta di backup per il tipo di snapshot. Il parametro ha due scopi: il primo è assegnare un nome agli snapshot, in modo da poterli identificare facilmente in qualsiasi momento, mentre il secondo è permettere allo script *azure\_hana\_backup.pl* di determinare il numero di snapshot di archiviazione conservati con l'etichetta specifica. Se si pianificano due backup di snapshot di archiviazione dello stesso tipo, ad esempio **HANA**, con due etichette diverse e si stabilisce che per ogni etichetta devono essere conservati 30 snapshot, in totale saranno interessati 60 snapshot di archiviazione dei volumi. Sono consentiti solo caratteri alfanumerici ("A-Z, a-z, 0-9"), il carattere di sottolineatura ("_") e il trattino ("-"). 
- Il parametro  **\<snapshot_frequency >** è riservato agli sviluppi futuri e non ha alcun impatto. Impostarlo su 3 minuti al momento dell'esecuzione dei backup di tipo **log** e su 15 minuti quando si eseguono gli altri tipi di backup.
- Il parametro  **\<numero di snapshot conservati >** definisce il periodo di conservazione degli snapshot in modo indiretto, definendo il numero di snapshot con lo stesso prefisso (etichetta). Questo parametro è importante per le esecuzioni pianificate tramite cron. Se il numero di snapshot con lo stesso prefisso supera il numero specificato da questo parametro, lo snapshot meno recente viene eliminato prima di eseguire un nuovo snapshot di archiviazione.

In caso di scalabilità orizzontale, lo script esegue alcuni controlli aggiuntivi per verificare che sia possibile accedere a tutti i server HANA. Lo script controlla anche che tutte le istanze di HANA restituiscano lo stato appropriato, prima di procedere alla creazione di uno snapshot di SAP HANA, seguito da uno snapshot di archiviazione.

L'esecuzione dello script `azure_hana_backup.pl` crea lo snapshot di archiviazione nelle tre fasi seguenti:

1. Esegue uno snapshot SAP HANA
1. Esegue uno snapshot di archiviazione
1. Rimuove lo snapshot SAP HANA creato prima dell'esecuzione dello snapshot di archiviazione

Per eseguire lo script, chiamarlo dalla cartella dell'eseguibile HDB in cui è stato copiato. 

Il periodo di conservazione viene gestito in base al numero di snapshot specificato come parametro quando si esegue lo script. Il periodo di tempo coperto dagli snapshot di archiviazione è una funzione del periodo di esecuzione e del numero di snapshot specificati come parametro durante l'esecuzione dello script. Se il numero di snapshot conservati supera il numero indicato come parametro nella chiamata dello script, lo snapshot di archiviazione meno recente con la stessa etichetta viene eliminato prima dell'esecuzione di un nuovo snapshot. Il numero specificato come ultimo parametro della chiamata corrisponde quindi al numero che è possibile usare per controllare il numero di snapshot conservati. Con questo numero è possibile controllare indirettamente anche lo spazio su disco usato per gli snapshot. 

> [!NOTE]
>Non appena si modifica l'etichetta, il conteggio viene riavviato. L'assegnazione delle etichette deve essere effettuata con estrema attenzione per evitare l'eliminazione accidentale degli snapshot.

## <a name="snapshot-strategies"></a>Strategie per gli snapshot
Per i vari tipi disponibili, la frequenza degli snapshot varia a seconda che si usi o meno la funzionalità di ripristino di emergenza di HANA in istanze Large. Questa funzionalità è basata sugli snapshot di archiviazione, che possono richiedere l'applicazione di alcune indicazioni speciali in termini di frequenza e periodi di esecuzione degli snapshot di archiviazione. 

Le considerazioni e le indicazioni seguenti presuppongono che *non* venga usata la funzionalità di ripristino di emergenza offerta da HANA in istanze Large. Si usano invece gli snapshot di archiviazione per avere dei backup ed è possibile fornire il ripristino temporizzato per gli ultimi 30 giorni. Considerate le limitazioni in termini di numero di snapshot e spazio, i clienti hanno preso in considerazione i requisiti seguenti:

- Tempo di ripristino per un ripristino temporizzato.
- Utilizzo dello spazio.
- Obiettivo del punto di ripristino e obiettivo del tempo di ripristino per il possibile ripristino da un'emergenza.
- Eventuale esecuzione di backup completi del database HANA sui dischi. Ogni volta che viene creato un backup completo del database sui dischi o che viene eseguita l'interfaccia **backint**, l'esecuzione di snapshot di archiviazione ha esito negativo. Se si pianifica l'esecuzione di backup completi del database oltre agli snapshot di archiviazione, assicurarsi che durante questo periodo l'esecuzione degli snapshot di archiviazione sia disabilitata.
- Numero massimo di snapshot per volume (limitato a 250).


Per i clienti che non usano la funzionalità di ripristino di emergenza di istanze Large di HANA, il periodo degli snapshot è meno frequente. In questi casi, alcuni clienti eseguono snapshot combinati su /hana/data e /hana/shared (che include /usr/sap) in periodi di 12 o 24 ore e conservano gli snapshot per un mese. Lo stesso vale per gli snapshot del volume di backup del log. Tuttavia, l'esecuzione dei backup del log delle transazioni di SAP HANA nel volume di backup del log avviene invece in periodi compresi tra 5 e 15 minuti.

Gli snapshot di archiviazione pianificati vengono eseguiti al meglio usando cron. Usare lo stesso script per tutti i backup e le esigenze di ripristino di emergenza e modificare gli input dello script in modo che corrispondano ai diversi tempi di backup richiesti. Gli snapshot sono tutti pianificati in modo diverso in Cron, in base alla frequenza di esecuzione: oraria, ogni 12 ore, giornaliera o settimanale. 

Di seguito viene fornito un esempio di pianificazione cron in /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
22 12 * * *  ./azure_hana_backup.pl logs dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
Nell'esempio precedente è riportato uno snapshot orario combinato che copre i volumi contenenti i percorsi /hana/data e /hana/shared (che include /usr/sap). Usare questo tipo di snapshot per un recupero temporizzato più rapido entro i due giorni precedenti. Su questi volumi c'è inoltre uno snapshot giornaliero. Si ottengono così due giorni di copertura tramite gli snapshot orari, più quattro settimane di copertura tramite gli snapshot giornalieri. Inoltre, il backup del volume di backup del log delle transazioni viene eseguito una volta al giorno. Anche questi backup vengono conservati per quattro settimane. Come si può osservare nella terza riga di crontab, il backup del log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. I tempi di avvio dei diversi processi cron che eseguono snapshot di archiviazione sono scaglionati, in modo che gli snapshot non vengano eseguiti tutti contemporaneamente in un determinato momento. 

Nell'esempio seguente viene eseguito uno snapshot combinato che copre i volumi contenenti i percorsi /hana/data e /hana/shared (che include /usr/sap) su base oraria. Questi snapshot vengono conservati per due giorni. Gli snapshot dei volumi di backup del log delle transazioni vengono eseguiti ogni cinque minuti e conservati per quattro ore. Come indicato prima, il backup del file di log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. Lo snapshot del volume di backup del log delle transazioni viene eseguito con un ritardo di due minuti dopo l'avvio del backup del log delle transazioni. In questi due minuti, normalmente viene completato il backup del log delle transazioni SAP HANA. Come prima, il backup del volume contenente il LUN di avvio viene eseguito una volta al giorno tramite uno snapshot di archiviazione e conservato per quattro settimane.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl logs logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Il grafico seguente illustra le sequenze dell'esempio precedente, escluso il LUN di avvio:

![Relazione tra backup e snapshot](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA esegue regolari operazioni di scrittura sul volume /hana/log per documentare le modifiche di cui viene eseguito il commit nel database. A intervalli regolari, SAP HANA scrive un punto di salvataggio nel volume /hana/data. Come specificato in crontab, il backup del log delle transazioni SAP HANA viene eseguito ogni cinque minuti. È anche possibile vedere che viene eseguito uno snapshot SAP HANA ogni ora, in seguito all'attivazione di uno snapshot di archiviazione combinato sui volumi /hana/data e /hana/shared. Dopo la corretta esecuzione dello snapshot HANA, viene eseguito lo snapshot di archiviazione combinato. Come indicato in crontab, lo snapshot di archiviazione nel volume /hana/logbackup viene eseguito ogni cinque minuti, circa due minuti dopo il backup del log delle transazioni HANA.

> 

>[!IMPORTANT]
> L'uso di snapshot di archiviazione per i backup di SAP HANA è importante solo quando gli snapshot vengono eseguiti insieme ai backup del log delle transazioni SAP HANA. Questi backup del log delle transazioni devono coprire i periodi di tempo che intercorrono tra gli snapshot di archiviazione. 

Se agli utenti si è garantito un ripristino temporizzato di 30 giorni, è necessario che siano soddisfatti i requisiti seguenti:

- In casi estremi, deve essere possibile accedere a uno snapshot di archiviazione combinato su hana/data e /hana/shared di un massimo di 30 giorni.
- Predisporre backup del log delle transazioni contigui che coprano il tempo tra tutti gli snapshot di archiviazione combinati. Lo snapshot meno recente del volume di backup del log delle transazioni deve quindi risalire a 30 giorni prima, a meno che i backup del log delle transazioni non siano stati copiati in un'altra condivisione NFS nell'archiviazione di Azure. In questo caso, è possibile eseguire il pull dei backup del log delle transazioni precedenti dalla condivisione NFS.

Per poter usufruire di snapshot di archiviazione e della successiva replica di archiviazione dei backup del log delle transazioni, è necessario modificare la posizione in cui SAP HANA scrive i backup del log delle transazioni. Questa modifica può essere eseguita in HANA Studio. Anche se SAP HANA esegue il backup automatico di segmenti di log completi, è necessario specificare un intervallo di backup del log in modo deterministico. Questo vale soprattutto quando si usa l'opzione di ripristino di emergenza, perché in questo caso è preferibile eseguire i backup del log con un periodo deterministico. Nel caso seguente, sono stati impostati 15 minuti come intervallo di backup del log.

![Pianificare i log di backup di SAP HANA in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

È anche possibile scegliere backup con una frequenza maggiore di 15 minuti. Questo tipo di impostazione con frequenza maggiore viene spesso usato in combinazione con la funzionalità di ripristino di emergenza di HANA in istanze Large. Alcuni clienti eseguono i backup del log delle transazioni ogni cinque minuti.  

Se non è mai stato eseguito il backup del database, il passaggio finale consiste nell'eseguire un backup del database basato su file per creare una singola voce di backup nel catalogo di backup. In caso contrario, SAP HANA non può avviare i backup del log specificati.

![Eseguire un backup basato su file per creare una singola voce di backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Dopo la prima esecuzione corretta degli snapshot di archiviazione, è possibile eliminare lo snapshot di test eseguito nel passaggio 6. A questo scopo, eseguire lo script `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl
```

Di seguito viene fornito un esempio dell'output dello script:
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoraggio del numero e della dimensione degli snapshot sul volume di archiviazione

In un volume di archiviazione specifico è possibile monitorare il numero di snapshot e l'utilizzo dell'archiviazione degli snapshot. Il comando `ls` non mostra i file e la directory degli snapshot, mentre il comando del sistema operativo Linux `du` mostra i dettagli di questi snapshot di archiviazione, poiché sono archiviati negli stessi volumi. Il comando può essere usato con le opzioni seguenti:

- `du –sh .snapshot`: fornisce il totale di tutti gli snapshot all'interno della directory degli snapshot.
- `du –sh --max-depth=1`: elenca tutti gli snapshot salvati nella cartella **.snapshot** e le dimensioni di ciascuno.
- `du –hc`: fornisce la dimensione totale usata da tutti gli snapshot.

Usare questi comandi per assicurarsi che gli snapshot creati e archiviati non usino tutto lo spazio di archiviazione sui volumi.

>[!NOTE]
>Gli snapshot del LUN di avvio non sono visibili con i comandi precedenti.

### <a name="getting-details-of-snapshots"></a>Recupero dei dettagli degli snapshot
Per ottenere maggiori dettagli sugli snapshot, è anche possibile usare lo script `azure_hana_snapshot_details.pl`. Questo script può essere eseguito in ognuna delle posizioni, se c'è un server attivo nella posizione di ripristino di emergenza. Lo script fornisce l'output seguente per ogni volume contenente gli snapshot: 
   * Dimensioni degli snapshot totali in un volume
   * Per ogni snapshot nel volume, vengono fornite le informazioni seguenti: 
      - Nome dello snapshot 
      - Ora di creazione 
      - Dimensioni dello snapshot
      - Frequenza dello snapshot
      - ID di backup HANA associato allo snapshot (se pertinente)

Di seguito viene fornito un esempio della sintassi di esecuzione dello script:

```
./azure_hana_snapshot_details.pl 
```

Poiché lo script cerca di recuperare l'ID di backup HANA, deve connettersi all'istanza di SAP HANA. Questa connessione richiede la corretta impostazione del file di configurazione *HANABackupCustomerDetails.txt*. L'output di due snapshot in un volume può essere simile al seguente:

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



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Riduzione del numero di snapshot in un server

Come illustrato in precedenza, è possibile ridurre il numero di snapshot archiviati con una determinata etichetta. Gli ultimi due parametri del comando per avviare uno snapshot sono un'etichetta e il numero di snapshot da mantenere.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

Nell'esempio precedente l'etichetta dello snapshot è **dailyhana** e il numero di snapshot con questa etichetta da conservare è **28**. Dovendo limitare l'uso dello spazio su disco, può essere opportuno ridurre il numero di snapshot archiviati. Il modo più semplice per ridurre il numero di snapshot, ad esempio a 15, consiste nell'eseguire lo script con l'ultimo parametro impostato su **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Se si esegue lo script con questa impostazione, il numero di snapshot, incluso il nuovo snapshot di archiviazione, è 15. Vengono conservati i 15 snapshot più recenti, mentre i 15 snapshot meno recenti vengono eliminati.

 >[!NOTE]
 > Questo script riduce il numero di snapshot solo se sono presenti snapshot che risalgono a più di un'ora prima. Lo script non elimina gli snapshot che hanno meno di un'ora. Queste limitazioni sono correlate alla funzionalità di ripristino di emergenza facoltativa offerta.

Se non si vuole più conservare un set di snapshot con l'etichetta di backup **hanadaily** negli esempi di sintassi, è possibile eseguire lo script con **0** come valore di conservazione. Tutti gli snapshot che corrispondono a questa etichetta verranno quindi rimossi. Tuttavia, la rimozione di tutti gli snapshot può influire sulla funzionalità di ripristino di emergenza di HANA in istanze Large.

Una seconda opzione per eliminare snapshot specifici consiste nell'usare lo script `azure_hana_snapshot_delete.pl`. Questo script è progettato per eliminare uno snapshot o un set di snapshot usando l'ID backup di HANA indicato in HANA Studio o tramite il nome dello snapshot stesso. Attualmente, l'ID di backup è collegato solo agli snapshot creati per il tipo di snapshot **hana**. I backup di snapshot di tipo **log** e **avvio** non eseguono uno snapshot SAP HANA e di conseguenza non esiste alcun ID backup da trovare per questi snapshot. Se viene immesso il nome dello snapshot, vengono cercati tutti gli snapshot corrispondenti al nome immesso sui diversi volumi. 

Chiamare lo script necessario per specificare il SID dell'istanza di HANA usando la sintassi di chiamata dello script:

```
./azure_hana_snapshot_delete.pl <SID>

```

Eseguire lo script come utente **root**.

Se si seleziona uno snapshot, è possibile eliminare ogni snapshot singolarmente. Si specifica prima il volume che contiene lo snapshot e quindi il nome dello snapshot. Se lo snapshot è presente nel volume specificato e risale a più di un'ora prima, viene eliminato. È possibile trovare i nomi dei volumi e degli snapshot eseguendo lo script `azure_hana_snapshot_details`. 

>[!IMPORTANT]
>In caso di dati presenti solo nello snapshot in fase di eliminazione, i dati verranno persi per sempre dopo l'eliminazione dello snapshot.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>Ripristino a livello di file da uno snapshot di archiviazione
Per i tipi di snapshot **HANA** e **log**, è possibile accedere direttamente agli snapshot nei volumi nella directory **.snapshot**. È presente una sottodirectory per ogni snapshot. È possibile copiare ogni file nello stato in cui si trovava al momento dello snapshot da questa sottodirectory all'effettiva struttura di directory. La versione corrente dello script **NON** prevede uno script self-service di ripristino dello snapshot, anche se è possibile eseguire il ripristino dello snapshot nell'ambito degli script di ripristino di emergenza presso il sito di ripristino di emergenza durante il failover. È necessario contattare il team operativo Microsoft aprendo una richiesta di servizio per il ripristino dello snapshot desiderato tra gli snapshot esistenti disponibili.

>[!NOTE]
>Il ripristino di un file singolo non funziona per gli snapshot dell'avvio LUN indipendente del tipo delle unità HANA istanza grande. La directory **.snapshot** non è esposta nel LUN di avvio. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Ripristino in base allo snapshot HANA più recente

Se si riscontra un problema di blocco dell'ambiente di produzione, il processo di ripristino da uno snapshot di archiviazione può essere avviato come richiesta di supporto del cliente al supporto di Microsoft Azure. Questo è un problema di estrema urgenza se i dati sono stati eliminati in un sistema di produzione e l'unico modo per recuperarli consiste nel ripristinare il database di produzione.

In una situazione diversa, con minor urgenza, sarebbe possibile usare un ripristino temporizzato, pianificabile con diversi giorni di anticipo. È possibile pianificare questo ripristino con SAP HANA in Azure anziché segnalare un flag ad alta priorità. È possibile, ad esempio, che sia necessario pianificare un aggiornamento del software SAP con un nuovo Enhancement Package e successivamente dover eseguire il ripristino in base a uno snapshot che rappresenta lo stato precedente all'aggiornamento Enhancement Package.

Prima di eseguire la richiesta, è necessario completare alcune operazioni di preparazione. Il team SAP HANA in Azure può quindi gestire la richiesta e fornire i volumi ripristinati. L'utente dovrà quindi ripristinare il database HANA in base agli snapshot. 

Di seguito viene mostrato come preparare la richiesta:

>[!NOTE]
>È possibile che l'interfaccia utente sia diversa rispetto alle schermate seguenti, a seconda della versione di SAP HANA in uso.

1. Scegliere lo snapshot da ripristinare. Viene ripristinato solo il volume hana/data, se non diversamente specificato. 

1. Arrestare l'istanza HANA.

   ![Arrestare l'istanza HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Smontare i volumi di dati in ogni nodo del database HANA. Se i volumi di dati sono ancora montati nel sistema operativo, il ripristino dello snapshot ha esito negativo.
   ![Smontare i volumi di dati in ogni nodo del database HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Aprire una richiesta di supporto tecnico di Azure e includere le istruzioni per il ripristino di uno snapshot specifico.

   - Durante il ripristino: SAP HANA in Azure potrebbe chiedere di partecipare a una conferenza telefonica per coordinarsi, verifica e conferma che è stato ripristinato lo snapshot di archiviazione corretto. 

   - Dopo il ripristino: SAP HANA nel servizio di Azure si invia una notifica quando è stato ripristinato lo snapshot di archiviazione.

1. Al termine del processo di ripristino, montare nuovamente tutti i volumi di dati.

   ![Montare nuovamente tutti i volumi di dati](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

1. Selezionare le opzioni di ripristino in SAP HANA Studio, se non vengono automaticamente visualizzate al momento della riconnessione al database HANA tramite SAP HANA Studio. L'esempio seguente illustra un ripristino in base all'ultimo snapshot HANA. Uno snapshot di archiviazione incorpora un singolo snapshot HANA. Se si esegue il ripristino in base all'ultimo snapshot di archiviazione, quindi, lo snapshot HANA ripristinato è quello più recente. Se invece si esegue il ripristino in base a uno snapshot di archiviazione precedente, è necessario individuare lo snapshot HANA basandosi sull'ora di creazione dello snapshot di archiviazione.

   ![Selezionare le opzioni di ripristino in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

1. Selezionare **Recover the database to a specific data backup or storage snapshot** (Ripristina il database in base a un backup di dati o a uno snapshot di archiviazione specifico).

   ![Finestra "Specify Recovery Type" (Specifica tipo di ripristino)](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

1. Scegliere **Specify backup without catalog** (Specifica il backup senza catalogo).

   ![Finestra "Specify Backup Location" (Specifica percorso backup)](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

1. Nell'elenco **Destination Type** (Tipo di destinazione) selezionare **Snapshot**.

   ![La finestra "Specify the Backup to Recover" (Specifica il backup da ripristinare)](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

1. Fare clic su **Finish** (Fine) per avviare il processo di ripristino.

    ![Fare clic su Finish (Fine) per avviare il processo di ripristino](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

1. Il database HANA viene ripristinato in base allo snapshot HANA incluso nello snapshot di archiviazione.

    ![Il database HANA viene ripristinato in base allo snapshot HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>Ripristino in base allo stato più recente

La procedura seguente ripristina lo snapshot HANA incluso nello snapshot di archiviazione. Quindi ripristina il backup del log delle transazioni allo stato più recente del database prima di ripristinare lo snapshot di archiviazione.

>[!IMPORTANT]
>Prima di procedere assicurarsi di avere una catena contigua e completa di backup dei log delle transazioni. Senza questi backup non è possibile ripristinare lo stato corrente del database.

1. Completare i passaggi da 1 a 6 indicati in Ripristino in base allo snapshot HANA più recente.

1. Selezionare **Recover the database to its most recent state** (Ripristina il database allo stato più recente).

   ![Selezionare "Recover the database to its most recent state" (Ripristina il database allo stato più recente)](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

1. Specificare il percorso dei backup dei log di HANA più recenti. Il percorso deve contenere tutti i backup del log delle transazioni HANA dallo snapshot HANA allo stato più recente.

   ![Specificare il percorso dei backup dei log di HANA più recenti](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

1. Selezionare un backup come base da cui ripristinare il database. In questo esempio lo snapshot HANA nello screenshot è lo snapshot HANA incluso nello snapshot di archiviazione. 

   ![Selezionare un backup come base da cui ripristinare il database](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

1. Deselezionare la casella di controllo **Use Delta Backups** (Usa backup differenziali) se non sono stati creati backup differenziali tra l'ora di creazione dello snapshot HANA e lo stato più recente.

   ![Deselezionare la casella di controllo "Use Delta Backups" (Usa backup differenziali) se non esistono backup differenziali](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

1. Nella schermata di riepilogo fare clic su **Finish** (Fine) per avviare la procedura di ripristino.

   ![Nella schermata di riepilogo fare clic su "Finish" (Fine)](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Ripristino in base a un'altra temporizzazione
Per eseguire il ripristino a una temporizzazione compresa tra il momento di creazione dello snapshot HANA (incluso nello snapshot di archiviazione) e un momento successivo al ripristino temporizzato dello snapshot HANA, eseguire questa procedura:

1. Assicurarsi di avere tutti i backup del log delle transazioni dal momento della creazione dello snapshot HANA al momento in base al quale si vuole eseguire il ripristino.
1. Iniziare la procedura descritta in Ripristino in base allo stato più recente.
1. Nella finestra **Specify Recovery Type** (Specifica tipo di ripristino) del passaggio 2 della procedura selezionare **Recover the database to the following point in time** (Ripristina il database alla temporizzazione seguente) e quindi specificare la temporizzazione. 
1. Completare i passaggi da 3 a 6.

## <a name="monitor-the-execution-of-snapshots"></a>Monitorare l'esecuzione degli snapshot

Quando si usano snapshot di archiviazione di HANA in istanze Large, è anche necessario monitorare l'esecuzione di questi snapshot. Lo script che esegue uno snapshot di archiviazione scrive l'output in un file e quindi lo salva nello stesso percorso degli script Perl. Viene scritto un file separato per ogni snapshot di archiviazione. L'output di ogni file mostra le diverse fasi eseguite dallo script degli snapshot:

1. Trovare i volumi per i quali è necessario creare uno snapshot.
1. Trovare gli snapshot creati da questi volumi.
1. Eliminare eventuali snapshot esistenti in modo da riflettere il numero di snapshot specificato.
1. Esegue uno snapshot SAP HANA.
1. Creare lo snapshot di archiviazione sui volumi.
1. Eliminare lo snapshot SAP HANA.
1. Rinominare lo snapshot più recente in **.0**.

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
In questo esempio è possibile notare come lo script registra la creazione dello snapshot HANA. Nel caso di una configurazione con scalabilità orizzontale questa procedura viene avviata sul nodo master. Il nodo master avvia la creazione sincrona degli snapshot SAP HANA su ciascuno dei nodi di lavoro. Viene quindi creato lo snapshot di archiviazione. Dopo l'esecuzione degli snapshot di archiviazione, lo snapshot HANA viene eliminato. L'eliminazione dello snapshot HANA viene avviata dal nodo master.


## <a name="next-steps"></a>Passaggi successivi
- Visualizzare [principi relativi al ripristino di emergenza e preparazione](hana-concept-preparation.md).

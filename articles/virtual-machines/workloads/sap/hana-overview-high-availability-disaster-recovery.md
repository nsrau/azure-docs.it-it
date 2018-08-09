---
title: Disponibilità elevata e ripristino di emergenza di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Implementare la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze Large)
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
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 554991c7c0f11a095a11ae24dbb693a1a3ba50fd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430121"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure 

>[!IMPORTANT]
>Questa documentazione non sostituisce la documentazione relativa all'amministrazione di SAP HANA o le note su SAP. Si presuppone che il lettore abbia una solida comprensione ed esperienza in termini di amministrazione e operazioni di SAP HANA, in particolare per quanto riguarda gli argomenti di backup, ripristino, disponibilità elevata e ripristino di emergenza. In questa documentazione, vengono mostrate schermate da SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

È importante esercitarsi con i passaggi e i processi adottati nell'ambiente e con le versioni di HANA. Alcuni processi descritti in questa documentazione sono stati semplificati per una migliore comprensione generale e non devono essere usati come passaggi dettagliati per manuali operativi finali. Se si vuole creare manuali operativi per le configurazioni, è necessario testare e provare i processi e documentare quelli correlati alle configurazioni specifiche. 


La disponibilità elevata e il ripristino di emergenza sono aspetti fondamentali nell'esecuzione di un'istanza cruciale di SAP HANA nei server Azure (istanze Large). Per progettare e implementare le corrette strategie di disponibilità elevata e ripristino di emergenza, è fondamentale la collaborazione con SAP, il proprio integratore di sistemi o Microsoft. È anche importante prendere in considerazione l'obiettivo del punto di ripristino (RPO) e l'obiettivo del tempo di ripristino (RTO) specifici dell'ambiente.

Microsoft supporta alcune funzionalità di disponibilità elevata con le istanze Large di SAP HANA. Queste funzionalità includono:

- **Replica di archiviazione:** capacità del sistema di archiviazione di replicare tutti i dati in un altro indicatore di istanze Large di HANA in un'altra area di Azure. SAP HANA funziona in modo indipendente da questo metodo. Questa funzionalità è il meccanismo di ripristino di emergenza predefinito offerto per HANA in istanze Large.
- **Replica di sistema HANA:**: la [replica di tutti i dati di SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) in un sistema SAP HANA separato. L'obiettivo tempo di ripristino è ridotto al minimo tramite la replica dei dati a intervalli regolari. SAP HANA supporta le modalità asincrona, sincrona in memoria e sincrona, La modalità sincrona viene usata solo per i sistemi SAP HANA che si trovano all'interno dello stesso data center o a distanze inferiori a 100 km. Nell'attuale progettazione degli indicatori di HANA in istanze Large la replica di sistema HANA può essere usata solo per la disponibilità elevata all'interno di una sola area. La replica di sistema HANA richiede un componente proxy inverso o di routing di terze parti per le configurazioni di ripristino di emergenza in un'altra area di Azure. 
- **Failover automatico dell'host**: soluzione locale di ripristino dagli errori per SAP HANA, da usare come alternativa alla replica di sistema HANA. Se il nodo master diventa non disponibile, vengono configurati uno o più nodi SAP HANA di standby in modalità di scalabilità orizzontale e SAP HANA esegue automaticamente il failover in un nodo di standby.

SAP HANA in Azure (istanze Large) è disponibile in due aree di Azure all'interno di quattro diverse aree geopolitiche (Stati Uniti, Australia, Europa e Giappone). Due aree all'interno di un'area geopolitica che ospitano gli indicatori di HANA in istanze Large sono connesse a circuiti di rete dedicati separati. Questi vengono usati per la replica di snapshot di archiviazione in modo da fornire metodi di ripristino di emergenza. La replica non viene definita per impostazione predefinita, ma viene configurata per i clienti che ordinano la funzionalità di ripristino di emergenza. La replica di archiviazione dipende dall'utilizzo degli snapshot di archiviazione per le istanze Large di HANA. Non è possibile scegliere un'area di Azure come area di ripristino di emergenza in un'area geopolitica diversa. 

La tabella seguente mostra i metodi di disponibilità elevata e ripristino di emergenza e le combinazioni associate di cui è attualmente disponibile il supporto:

| Scenario supportato nelle istanze Large di HANA | Opzione di disponibilità elevata | Opzione di ripristino di emergenza | Commenti |
| --- | --- | --- | --- |
| Nodo singolo | Non disponibile. | Configurazione di ripristino di emergenza dedicata.<br /> Configurazione di ripristino di emergenza multifunzione. | |
| Failover automatico dell'host: scalabilità orizzontale (con o senza standby)<br /> incluso 1+1 | Possibile con il nodo di standby che acquisisce il ruolo attivo.<br /> Cambio di ruolo controllato da HANA. | Configurazione di ripristino di emergenza dedicata.<br /> Configurazione di ripristino di emergenza multifunzione.<br /> Sincronizzazione di ripristino di emergenza tramite replica di archiviazione. | Set di volumi HANA collegati a tutti i nodi.<br /> Il sito di ripristino di emergenza deve avere lo stesso numero di nodi. |
| Replica di sistema HANA | Possibile con la configurazione del nodo primario o secondario.<br /> Il nodo secondario assume il ruolo primario in caso di failover.<br /> Replica di sistema HANA e failover controllato dal sistema operativo. | Configurazione di ripristino di emergenza dedicata.<br /> Configurazione di ripristino di emergenza multifunzione.<br /> Sincronizzazione di ripristino di emergenza tramite replica di archiviazione.<br /> Il ripristino di emergenza tramite la replica di sistema HANA non è ancora possibile senza componenti di terze parti. | Set separato di volumi di dischi collegati a ogni nodo.<br /> Solo i volumi di dischi di replica secondaria nel sito di produzione vengono replicati nella posizione di ripristino di emergenza.<br /> Nel sito di ripristino di emergenza è necessario un set di volumi. | 

In una configurazione di ripristino di emergenza dedicata, l'unità di istanze Large di HANA nel sito di ripristino di emergenza non viene usata per eseguire altri carichi di lavoro o sistemi non di produzione. L'unità è passiva e viene distribuita solo in caso di failover di emergenza. Tuttavia, per molti clienti questa configurazione non è la scelta migliore.

Vedere [Scenari HLI supportati](hana-supported-scenario.md) per informazioni sui dettagli relativi a layout di archiviazione ed Ethernet per l'architettura.

> [!NOTE]
> [Le distribuzioni di SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (più istanze di HANA in un'unità) come scenari di sovrapposizione funzionano con i metodi HA e HR elencati nella tabella. Un'eccezione consiste nell'uso della replica di sistema HANA con un cluster di failover automatico basato su Pacemaker. Un caso simile supporta solo un'istanza HANA per unità. Per le distribuzioni [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000), funzionano solo i metodi di disponibilità elevata e ripristino di emergenza se viene distribuito più di un tenant. Se è distribuito un solo tenant, sono validi tutti i metodi elencati.  

In una configurazione di ripristino di emergenza multifunzione, l'unità di istanze Large di HANA nel sito di ripristino di emergenza esegue un carico di lavoro non di produzione. In caso di emergenza, arrestare il sistema non di produzione e montare i set di volumi (aggiuntivi) con replica di archiviazione e quindi avviare l'istanza HANA di produzione. Molti clienti che usano la funzionalità di ripristino di emergenza di HANA in istanze Large usano questa configurazione. 


Per altre informazioni sulla disponibilità elevata di SAP HANA, vedere gli articoli SAP seguenti: 

- [SAP HANA High-Availability Whitepaper](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html) (White paper sulla disponibilità elevata di SAP HANA)
- [SAP HANA Administration Guide](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf) (Guida all'amministrazione di SAP HANA)
- [SAP HANA Academy Video on SAP HANA System Replication](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication) (Video di SAP HANA Academy sulla replica di sistema di SAP HANA)
- [SAP Support Note #1999880 - FAQ on SAP HANA System Replication](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880) (Nota di supporto SAP 1999880 - Domande frequenti sulla replica di sistema di SAP HANA)
- [SAP Support Note #2165547 - SAP HANA Backup and Restore within SAP HANA System Replication Environment](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726) (Nota di supporto SAP 2165547 - Backup e ripristino di SAP HANA nell'ambiente di replica di sistema di SAP HANA)
- [SAP Support Note #1984882 - Using SAP HANA System Replication for Hardware Exchange with Minimum/Zero Downtime](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226) (Nota di supporto SAP 1984882 - Uso della replica di sistema di SAP HANA per la sostituzione di hardware con tempo di inattività minimo o nullo)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerazioni sulla rete per il ripristino di emergenza con istanze Large di HANA

Per sfruttare i vantaggi della funzionalità di ripristino di emergenza di HANA in istanze Large, è necessario progettare la connettività di rete alle due aree di Azure. È necessario implementare una connessione del circuito ExpressRoute di Azure dall'ambiente locale nell'area di Azure principale e un'altra connessione dei circuito dall'ambiente locale all'area di ripristino di emergenza. Questa misura consente di gestire le situazioni in cui si verifica un problema in un'area di Azure, anche nella posizione di un router MSEE (Microsoft Enterprise Edge).

Come seconda misura, è possibile connettere tutte le reti virtuali di Azure connesse a SAP HANA in Azure (istanze Large) in una delle aree a un circuito ExpressRoute che connette HANA in istanze Large nell'altra area. Grazie a questa *connessione incrociata*, i servizi in esecuzione in una rete virtuale di Azure nell'area 1 possono connettersi alle unità HANA in istanze Large nell'area 2 e viceversa. In questo modo, è possibile gestire il caso in cui solo una delle posizioni MSEE che si connette all'infrastruttura locale con Azure è offline.

Il grafico seguente illustra una configurazione resiliente per i casi di ripristino di emergenza:

![Configurazione ottimale per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Altri requisiti per l'uso della replica di archiviazione di HANA in istanze Large per il ripristino di emergenza

Oltre ai requisiti precedenti per una configurazione di ripristino di emergenza con HANA in istanze Large, è necessario:

- Ordinare SKU di SAP HANA in Azure (istanze Large) della stessa dimensione degli SKU di produzione e distribuirli nell'area di ripristino di emergenza. Nelle distribuzioni dei clienti attuali, queste istanze vengono usate per eseguire istanze di HANA non di produzione. Queste configurazioni sono chiamate *impostazioni di ripristino di emergenza multifunzione*.   
- Ordinare archiviazione aggiuntiva nel sito di ripristino di emergenza per ogni SKU di SAP HANA in Azure (istanze Large) che si vuole ripristinare nel sito di ripristino di emergenza. L'acquisto di spazio di archiviazione aggiuntivo consente di allocare i volumi di archiviazione, che vengono usati come destinazione della replica di archiviazione dall'area di Azure di produzione nell'area di Azure di ripristino di emergenza.
- Nel caso in cui nella replica primaria sia impostato HSR e si configuri la replica basata su archiviazione nel sito di ripristino di emergenza, è necessario acquistare risorse di archiviazione aggiuntive per quest'ultimo, in modo che i dati sia del nodo primario che di quello secondario vengano replicati nel sito di ripristino di emergenza.

 

## <a name="backup-and-restore"></a>Backup e ripristino

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

>   [!NOTE]
>   La tecnologia di snapshot usata dall'infrastruttura sottostante di istanze Large di HANA presenta una dipendenza dagli snapshot SAP HANA. Attualmente gli snapshot SAP HANA non funzionano in combinazione con più tenant di contenitori di database multi-tenant SAP HANA. Se viene distribuito un solo tenant, gli snapshot SAP HANA funzionano ed è possibile usare questo metodo.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso degli snapshot di archiviazione di SAP HANA in Azure (istanze di grandi dimensioni)

L'infrastruttura di archiviazione sottostante di SAP HANA in Azure (istanze Large) supporta gli snapshot di archiviazione dei volumi. Il backup e il ripristino dei volumi è supportato con le considerazioni seguenti:

- Invece di backup di database completi, vengono creati snapshot dei volumi di archiviazione a intervalli frequenti.
- Quando viene attivato uno snapshot sui volumi /hana/data e /hana/shared (che include /usr/sap), la tecnologia degli snapshot avvia uno snapshot SAP HANA prima di eseguire lo snapshot di archiviazione. Questo snapshot di SAP HANA è il punto di installazione per i ripristini dei log finali dopo il ripristino dello snapshot di archiviazione. Per la riuscita dello snapshot di HANA è necessaria un'istanza di HANA attiva.  Nello scenario HSR, lo snapshot di archiviazione non è supportato nel nodo secondario corrente se in questo non è possibile eseguire lo snapshot di HANA.
- Al termine della corretta esecuzione dello snapshot di archiviazione, lo snapshot SAP HANA viene eliminato.
- I backup del log delle transazioni vengono creati di frequente e archiviati nel volume /hana/logbackups o in Azure. È possibile attivare il volume /hana/logbackups che contiene i backup del log delle transazioni per creare separatamente uno snapshot. In questo caso, non è necessario eseguire uno snapshot HANA.
- Se è necessario ripristinare un database in base a un determinato momento, inviare una richiesta di ripristino temporizzato dello snapshot di archiviazione al supporto di Microsoft Azure (per un'interruzione di produzione) o a SAP HANA in Azure Service Management. ad esempio per un ripristino pianificato di un sistema sandbox allo stato originale.
- Lo snapshot SAP HANA incluso nello snapshot di archiviazione è un punto di offset per l'applicazione dei backup del log delle transazioni che sono stati creati e archiviati dopo la creazione dello snapshot di archiviazione.
- Questi backup del log delle transazioni vengono creati per il ripristino temporizzato del database.

È possibile eseguire snapshot di archiviazione su tre classi di volumi diverse:

- Snapshot combinato su /hana/data e /hana/shared (che include /usr/sap). Questo snapshot richiede la creazione di uno snapshot SAP HANA come fase preliminare per lo snapshot di archiviazione. Lo snapshot SAP HANA verifica che il database sia in uno stato coerente dal punto di vista dell'archiviazione. Questo per il processo di ripristino che rappresenta un punto da impostare.
- Snapshot separato su /hana/logbackups.
- Una partizione del sistema operativo.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="storage-snapshot-considerations"></a>Considerazioni sugli snapshot di archiviazione

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

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Prerequisiti per l'uso di snapshot di archiviazione self-service

Per la corretta esecuzione dello script dello snapshot, assicurarsi che Perl sia installato nel sistema operativo Linux nel server HANA in istanze Large. Perl è preinstallato sul unità HANA istanze grandi. Per verificare la versione di Perl, usare il comando seguente:

`perl -v`

![La chiave pubblica viene copiata eseguendo questo comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>Configurare gli snapshot di archiviazione

Per configurare gli snapshot di archiviazione con HANA in istanze Large, completare questi passaggi:
1. Assicurarsi che Perl sia installato nel sistema operativo Linux sul server HANA (istanze Large).
1. Modificare /etc/ssh/ssh\_config in modo da aggiungere la riga _MACs hmac-sha1_.
1. Creare un account utente di backup di SAP HANA sul nodo master per ogni istanza di SAP HANA in esecuzione (se applicabile).
1. Installare il client HDB di SAP HANA in tutti i server SAP HANA (istanze Large).
1. Nel primo server SAP HANA (istanze Large) di ogni area, creare una chiave pubblica per accedere all'infrastruttura di archiviazione sottostante che controlla la creazione di snapshot.
1. Copiare gli script e il file di configurazione da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) nel percorso di **hdbsql** dell'installazione di SAP HANA.
1. Modificare il file *HANABackupDetails.txt* nel modo necessario, in base alle specifiche del cliente.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

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
  ssh-keygen –t dsa –b 1024
```
Il nuovo percorso è **_/root/.ssh/id\_dsa.pub**. Non immettere una password effettiva, altrimenti sarà necessario immetterla a ogni accesso. Premere invece **Invio** due volte per evitare che venga richiesta una password in fase di accesso.

Verificare che la chiave pubblica sia corretta sostituendo le cartelle con **/root/.ssh/** e quindi eseguendo il comando `ls`. Se la chiave è presente, è possibile copiarla eseguendo il comando seguente:

![La chiave pubblica viene copiata eseguendo questo comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

A questo punto, contattare il servizio SAP HANA in Azure Service Management e fornire loro la chiave pubblica. Il rappresentante del servizio usa la chiave pubblica per la registrazione nell'infrastruttura di archiviazione sottostante, creata per il tenant delle istanze Large di HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Passaggio 4: Creare un account utente SAP HANA

Per avviare la creazione di snapshot SAP HANA, è necessario creare un account utente in SAP HANA che possa essere usato dagli script degli snapshot di archiviazione. Creare quindi un account utente SAP HANA in SAP HANA Studio. L'utente deve essere creato in SYSTEMDB e NON nel database SID per MDC. In un ambiente contenitore singolo, l'utente viene impostato nel database tenant. L'account deve disporre dei privilegi **Backup Admin** e **Catalog Read**. In questo esempio, il nome utente è **SCADMIN**. Il nome dell'account utente creato in HANA Studio fa distinzione tra maiuscole e minuscole. Assicurarsi di selezionare **No** se si vuole che l'utente modifichi la password all'accesso successivo.

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
- **testStorageSnapshotConnection.pl**: questo script ha due scopi. In primo luogo, garantisce che l'unità HANA in istanze Large che esegue gli script abbia accesso alla macchina virtuale di archiviazione assegnata e all'interfaccia degli snapshot di archiviazione di HANA in istanze Large. In secondo luogo, crea uno snapshot temporaneo per l'istanza di HANA che si sta testando. Questo script deve essere eseguito per ogni istanza di HANA su un server, per assicurarsi che gli script di backup funzionino come previsto.
- **removeTestStorageSnapshot.pl**: questo script elimina lo snapshot di test creato con lo script **testStorageSnapshotConnection.pl**.
- **azure\_hana\_dr\_failover.pl**: questo script avvia un failover di ripristino di emergenza in un'altra area. Lo script deve essere eseguito nell'unità HANA in istanze Large nell'area di ripristino di emergenza o nell'unità in cui si vuole eseguire il failover. Questo script arresta la replica di archiviazione dal lato primario al lato secondario, ripristina lo snapshot più recente nei volumi di ripristino di emergenza e fornisce i punti di montaggio per i volumi di ripristino di emergenza.
- **azure\_hana\_test\_dr\_failover.pl**: questo script esegue un failover di test nel sito di ripristino di emergenza. Diversamente dallo script azure_hana_dr_failover.pl, questa esecuzione non interrompe la replica di archiviazione dal lato primario al lato secondario. Al contrario, i cloni dei volumi di archiviazione replicata vengono creati sul lato di ripristino di emergenza e vengono forniti i punti di montaggio dei volumi clonati. 
- **HANABackupCustomerDetails.txt**: questo è un file di configurazione modificabile che deve essere modificato per essere adattato alla configurazione di SAP HANA personale. Il file *HANABackupCustomerDetails.txt* è il file di configurazione e controllo per lo script che esegue gli snapshot di archiviazione. Modificare il file in base alla configurazione e agli scopi specifici. Il **nome di backup di archiviazione** e l'**indirizzo IP di archiviazione** vengono ricevuti da SAP HANA in Azure Service Management durante la distribuzione delle istanze. Non è possibile modificare la sequenza, l'ordinamento o la spaziatura di alcuna delle variabili nel file. In caso contrario, gli script non vengono eseguiti correttamente. Si riceve inoltre l'indirizzo IP del nodo di scalabilità verticale o del nodo master (in caso di scalabilità orizzontale) da SAP HANA in Azure Service Management. Viene infine fornito il numero di istanze di HANA ottenuto durante l'installazione di SAP HANA. È ora necessario aggiungere un nome di backup al file di configurazione.

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
- **HANA**: backup di snapshot combinato in cui i volumi che contengono /hana/data e /hana/shared, che contiene anche /usr/sap, vengono gestiti dallo snapshot coordinato. Da questo snapshot è possibile eseguire il ripristino di un singolo file.
- **Log**: backup di snapshot del volume /hana/logbackups. Non viene attivato nessuno snapshot HANA per eseguire questo snapshot di archiviazione. Questo volume di archiviazione è destinato a contenere i backup del log delle transazioni SAP HANA, che vengono eseguiti più spesso per limitare l'aumento delle dimensioni del log e impedire la possibile perdita di dati. Da questo snapshot è possibile eseguire il ripristino di un singolo file. Non ridurre la frequenza al di sotto di tre minuti.
- **Avvio**: snapshot del volume che contiene il numero di unità logica (LUN) di avvio di HANA in istanze Large. Questo backup di snapshot è possibile solo con SKU di tipo I di istanze Large di HANA. Non è possibile eseguire ripristini di file singoli dallo snapshot del volume che contiene il LUN di avvio.


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
- Il parametro **<HANA Large Instance Type>** è necessario solo per i backup di volumi di avvio. Esistono due valori validi con "TypeI" o "TypeII" in base all'unità dell'Istanza Grande HANA. Per identificare il tipo di unità, vedere [Panoramica e architettura di SAP HANA (istanze Large) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Il parametro **<snapshot_prefix>** è un'etichetta di snapshot o di backup per il tipo di snapshot. Il parametro ha due scopi: il primo è assegnare un nome agli snapshot, in modo da poterli identificare facilmente in qualsiasi momento, mentre il secondo è permettere allo script *azure\_hana\_backup.pl* di determinare il numero di snapshot di archiviazione conservati con l'etichetta specifica. Se si pianificano due backup di snapshot di archiviazione dello stesso tipo, ad esempio **HANA**, con due etichette diverse e si stabilisce che per ogni etichetta devono essere conservati 30 snapshot, in totale saranno interessati 60 snapshot di archiviazione dei volumi. Sono consentiti solo caratteri alfanumerici ("A-Z, a-z, 0-9"), il carattere di sottolineatura ("_") e il trattino ("-"). 
- Il parametro **< snapshot_frequency >** è riservato agli sviluppi futuri e non ha alcun impatto. Impostarlo su 3 minuti al momento dell'esecuzione dei backup di tipo **log** e su 15 minuti quando si eseguono gli altri tipi di backup.
- Il parametro **<number of snapshots retained>** definisce la conservazione degli snapshot in modo indiretto, definendo il numero di snapshot con lo stesso prefisso (etichetta). Questo parametro è importante per le esecuzioni pianificate tramite cron. Se il numero di snapshot con lo stesso prefisso supera il numero specificato da questo parametro, lo snapshot meno recente viene eliminato prima di eseguire un nuovo snapshot di archiviazione.

In caso di scalabilità orizzontale, lo script esegue alcuni controlli aggiuntivi per verificare che sia possibile accedere a tutti i server HANA. Lo script controlla anche che tutte le istanze di HANA restituiscano lo stato appropriato, prima di procedere alla creazione di uno snapshot di SAP HANA, seguito da uno snapshot di archiviazione.

L'esecuzione dello script `azure_hana_backup.pl` crea lo snapshot di archiviazione nelle tre fasi seguenti:

1. Esegue uno snapshot SAP HANA
1. Esegue uno snapshot di archiviazione
1. Rimuove lo snapshot SAP HANA creato prima dell'esecuzione dello snapshot di archiviazione

Per eseguire lo script, chiamarlo dalla cartella dell'eseguibile HDB in cui è stato copiato. 

Il periodo di conservazione viene gestito in base al numero di snapshot specificato come parametro quando si esegue lo script. Il periodo di tempo coperto dagli snapshot di archiviazione è una funzione del periodo di esecuzione e del numero di snapshot specificati come parametro durante l'esecuzione dello script. Se il numero di snapshot conservati supera il numero indicato come parametro nella chiamata dello script, lo snapshot di archiviazione meno recente con la stessa etichetta viene eliminato prima dell'esecuzione di un nuovo snapshot. Il numero specificato come ultimo parametro della chiamata corrisponde quindi al numero che è possibile usare per controllare il numero di snapshot conservati. Con questo numero è possibile controllare indirettamente anche lo spazio su disco usato per gli snapshot. 

> [!NOTE]
>Non appena si modifica l'etichetta, il conteggio viene riavviato. L'assegnazione delle etichette deve essere effettuata con estrema attenzione per evitare l'eliminazione accidentale degli snapshot.

### <a name="snapshot-strategies"></a>Strategie per gli snapshot
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
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
Nell'esempio precedente è riportato uno snapshot orario combinato che copre i volumi contenenti i percorsi /hana/data e /hana/shared (che include /usr/sap). Usare questo tipo di snapshot per un recupero temporizzato più rapido entro i due giorni precedenti. Su questi volumi c'è inoltre uno snapshot giornaliero. Si ottengono così due giorni di copertura tramite gli snapshot orari, più quattro settimane di copertura tramite gli snapshot giornalieri. Inoltre, il backup del volume di backup del log delle transazioni viene eseguito una volta al giorno. Anche questi backup vengono conservati per quattro settimane. Come si può osservare nella terza riga di crontab, il backup del log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. I tempi di avvio dei diversi processi cron che eseguono snapshot di archiviazione sono scaglionati, in modo che gli snapshot non vengano eseguiti tutti contemporaneamente in un determinato momento. 

Nell'esempio seguente viene eseguito uno snapshot combinato che copre i volumi contenenti i percorsi /hana/data e /hana/shared (che include /usr/sap) su base oraria. Questi snapshot vengono conservati per due giorni. Gli snapshot dei volumi di backup del log delle transazioni vengono eseguiti ogni cinque minuti e conservati per quattro ore. Come indicato prima, il backup del file di log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. Lo snapshot del volume di backup del log delle transazioni viene eseguito con un ritardo di due minuti dopo l'avvio del backup del log delle transazioni. In questi due minuti, normalmente viene completato il backup del log delle transazioni SAP HANA. Come prima, il backup del volume contenente il LUN di avvio viene eseguito una volta al giorno tramite uno snapshot di archiviazione e conservato per quattro settimane.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
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


### <a name="file-level-restore-from-a-storage-snapshot"></a>Ripristino a livello di file da uno snapshot di archiviazione
Per i tipi di snapshot **HANA** e **log**, è possibile accedere direttamente agli snapshot nei volumi nella directory **.snapshot**. È presente una sottodirectory per ogni snapshot. È possibile copiare ogni file nello stato in cui si trovava al momento dello snapshot da questa sottodirectory all'effettiva struttura di directory. La versione corrente dello script **NON** prevede uno script self-service di ripristino dello snapshot, anche se è possibile eseguire il ripristino dello snapshot nell'ambito degli script di ripristino di emergenza presso il sito di ripristino di emergenza durante il failover. È necessario contattare il team operativo Microsoft aprendo una richiesta di servizio per il ripristino dello snapshot desiderato tra gli snapshot esistenti disponibili.

>[!NOTE]
>Il ripristino di un file singolo non funziona per gli snapshot dell'avvio LUN indipendente del tipo delle unità HANA istanza grande. La directory **.snapshot** non è esposta nel LUN di avvio. 


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

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>Ripristino in base allo snapshot HANA più recente

Se si riscontra un problema di blocco dell'ambiente di produzione, il processo di ripristino da uno snapshot di archiviazione può essere avviato come richiesta di supporto del cliente al supporto di Microsoft Azure. Questo è un problema di estrema urgenza se i dati sono stati eliminati in un sistema di produzione e l'unico modo per recuperarli consiste nel ripristinare il database di produzione.

In una situazione diversa, con minor urgenza, sarebbe possibile usare un ripristino temporizzato, pianificabile con diversi giorni di anticipo. È possibile pianificare questo ripristino con SAP HANA in Azure Service Management anziché generare un flag ad alta priorità. È possibile, ad esempio, che sia necessario pianificare un aggiornamento del software SAP con un nuovo Enhancement Package e successivamente dover eseguire il ripristino in base a uno snapshot che rappresenta lo stato precedente all'aggiornamento Enhancement Package.

Prima di eseguire la richiesta, è necessario completare alcune operazioni di preparazione. In questo modo, il servizio SAP HANA in Azure Service Management potrà gestire la richiesta e fornire i volumi ripristinati. L'utente dovrà quindi ripristinare il database HANA in base agli snapshot. 

Di seguito viene mostrato come preparare la richiesta:

>[!NOTE]
>È possibile che l'interfaccia utente sia diversa rispetto alle schermate seguenti, a seconda della versione di SAP HANA in uso.

1. Scegliere lo snapshot da ripristinare. Viene ripristinato solo il volume hana/data, se non diversamente specificato. 

1. Arrestare l'istanza HANA.

 ![Arrestare l'istanza HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Smontare i volumi di dati in ogni nodo del database HANA. Se i volumi di dati sono ancora montati nel sistema operativo, il ripristino dello snapshot ha esito negativo.
 ![Smontare i volumi di dati in ogni nodo del database HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Aprire una richiesta di supporto tecnico di Azure e includere le istruzioni per il ripristino di uno snapshot specifico.

 - Durante il ripristino: è possibile che il servizio SAP HANA in Azure Service Management chieda di partecipare a una conferenza telefonica per coordinarsi, verificare e confermare che venga ripristinato lo snapshot di archiviazione corretto. 

 - Dopo il ripristino: il team di gestione dei servizi SAP HANA in Azure invia una notifica per indicare quando è stato ripristinato lo snapshot di archiviazione.

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

1. Completare i passaggi da 1 a 6 indicati in [Ripristino in base allo snapshot HANA più recente](#recovering-to-the-most-recent-hana-snapshot).

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
1. Iniziare la procedura descritta in [Ripristino in base allo stato più recente](#recovering-to-the-most-recent-state).
1. Nella finestra **Specify Recovery Type** (Specifica tipo di ripristino) del passaggio 2 della procedura selezionare **Recover the database to the following point in time** (Ripristina il database alla temporizzazione seguente) e quindi specificare la temporizzazione. 
1. Completare i passaggi da 3 a 6.

### <a name="monitor-the-execution-of-snapshots"></a>Monitorare l'esecuzione degli snapshot

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


## <a name="disaster-recovery-principles"></a>Principi relativi al ripristino di emergenza
HANA in istanze Large offre una funzionalità di ripristino di emergenza tra gli indicatori di HANA in istanze Large in diverse aree di Azure. Ad esempio, se si distribuiscono unità HANA in istanze Large nell'area Stati Uniti occidentali di Azure, è possibile usare unità HANA in istanze Large nell'area Stati Uniti orientali come unità di ripristino di emergenza. Come accennato in precedenza, il ripristino di emergenza non viene configurato automaticamente poiché richiede il pagamento di un'altra unità di istanze Large di HANA nell'area di ripristino di emergenza. La configurazione di ripristino di emergenza funziona per le configurazioni con scalabilità verticale e con scalabilità orizzontale. 

Negli scenari distribuiti fino ad ora, i clienti hanno usato l'unità nell'area di ripristino di emergenza per eseguire sistemi non di produzione che usano un'istanza di HANA installata. Lo SKU dell'unità di istanze Large di HANA deve corrispondere a quello usato per scopi di produzione. L'immagine seguente mostra l'aspetto che avrà la configurazione dei dischi tra l'unità server nell'area di produzione di Azure e l'area di ripristino di emergenza:

![Configurazione di ripristino di emergenza dal punto di vista dei dischi](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Come illustrato in questo grafico di sintesi, è necessario ordinare un secondo set di volumi di dischi. I volumi di dischi di destinazione hanno le stesse dimensioni dei volumi di produzione per l'istanza di produzione nell'unità di ripristino di emergenza. I volumi di dischi, inoltre, sono associati all'unità server di istanze Large di HANA nel sito di ripristino di emergenza. Dall'area di produzione al sito di ripristino di emergenza vengono replicati i volumi seguenti:

- /hana/data
- /hana/logbackups 
- /hana/shared (include /usr/sap)

Il volume /hana/log non viene replicato perché il log delle transazioni SAP HANA non è necessario nella modalità con cui viene eseguito il ripristino da questi volumi. 

Alla base della funzionalità di ripristino di emergenza offerta vi è la funzionalità di replica dell'archiviazione fornita dall'infrastruttura HANA in istanze Large. La funzionalità usata per l'archiviazione non è costituita da un flusso costante di modifiche replicate in modo asincrono man mano che vengono apportate al volume di archiviazione. Si tratta invece di un meccanismo basato sul fatto che gli snapshot di questi volumi vengono creati a intervalli regolari. Il differenziale tra uno snapshot già replicato e un nuovo snapshot non ancora replicato viene quindi trasferito nel sito di ripristino di emergenza in volumi di dischi di destinazione.  Questi snapshot vengono archiviati nei volumi e, nel caso di un failover di ripristino di emergenza, devono essere ripristinati nei volumi.  

Il primo trasferimento dei dati completi del volume deve essere eseguito prima che la quantità di dati sia inferiore ai differenziali tra gli snapshot. I volumi nel sito di ripristino di emergenza contengono quindi tutti gli snapshot dei volumi eseguiti nel sito di produzione. È infine possibile usare il sistema di ripristino di emergenza per ottenere uno stato precedente per il recupero dei dati persi, senza eseguire il rollback del sistema di produzione.

In caso di distribuzioni MCOD con più istanze di SAP HANA indipendenti in un'unità HANA in istanze Large, è previsto che per tutte le istanze di SAP HANA sia replicata l'archiviazione sul lato di ripristino di emergenza.

Nei casi in cui si usa la replica di sistema HANA come funzionalità a disponibilità elevata nel sito di produzione e la replica basata sull'archiviazione per il sito di ripristino di emergenza, vengono replicati i volumi di entrambi i nodi dal sito primario all'istanza di ripristino di emergenza. È necessario acquistare risorse di archiviazione aggiuntive (con la stessa dimensione del nodo primario) per il sito di ripristino di emergenza per consentire la replica di entrambi, primario e secondario, nel sito di ripristino di emergenza. 



>[!NOTE]
>La funzionalità di replica dell'archiviazione di HANA in istanze Large esegue il mirroring e la replica degli snapshot di archiviazione. Se non si eseguono snapshot di archiviazione come descritto nella sezione [Backup e ripristino](#backup-and-restore) di questo articolo, non è possibile eseguire alcuna replica nel sito di ripristino di emergenza. L'esecuzione di snapshot di archiviazione è un prerequisito per la replica di archiviazione nel sito di ripristino di emergenza.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparazione dello scenario di ripristino di emergenza
In questo scenario è presente un sistema di produzione in esecuzione in HANA in istanze Large nell'area di Azure di produzione. Per i passaggi che seguono, si presupponga che il SID del sistema HANA è "PRD" e che è presente un sistema non di produzione in esecuzione in HANA in istanze Large nell'area di Azure di ripristino di emergenza. Nel secondo caso, si supponga che il SID è "TST". L'immagine seguente mostra questa configurazione:

![Avvio della configurazione di ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Se l'istanza del server non è già stata ordinata con il set di volumi di archiviazione aggiuntivo, SAP HANA in Azure Service Management collega il set di volumi aggiuntivo come destinazione per la replica di produzione all'unità HANA in istanze Large in cui viene eseguita l'istanza HANA TST. A questo scopo, è necessario fornire il SID dell'istanza di produzione HANA. Quando il servizio SAP HANA in Azure Service Management avrà confermato l'associazione di questi volumi, sarà necessario montare i volumi nell'unità di istanze Large di HANA.

![Passaggio successivo della configurazione di ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Il passaggio successivo consiste nell'installare la seconda istanza di SAP HANA nell'unità HANA in istanze Large nell'area di Azure di ripristino di emergenza in cui viene eseguita l'istanza HANA TST. L'istanza di SAP HANA appena installata deve avere lo stesso SID e gli utenti creati devono avere lo stesso UID e ID gruppo dell'istanza di produzione. Se l'installazione ha esito positivo, è necessario:

- Eseguire il passaggio 2 della preparazione degli snapshot di archiviazione descritto in precedenza in questo articolo.
- Se non è ancora stato fatto, creare una chiave pubblica per l'unità HANA in istanze Large. Vedere il passaggio 3 della preparazione degli snapshot di archiviazione descritto in precedenza in questo articolo.
- Mantenere il *HANABackupCustomerDetails.txt* con la nuova istanza HANA ed eseguire il test che verifichi che la connettività nell'account di archiviazione funzioni correttamente.  
- Arrestare l'istanza di SAP HANA appena installata nell'unità HANA in istanze Large nell'area di Azure di ripristino di emergenza.
- Smontare i volumi PRD e contattare il servizio SAP HANA in Azure Service Management. I volumi non possono rimanere montati nell'unità poiché non devono risultare accessibili durante il funzionamento come destinazione della replica di archiviazione.  

![Passaggio della configurazione di ripristino di emergenza prima di definire la replica](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Il team operativo stabilisce la relazione di replica tra i volumi PRD nell'area di Azure di produzione e i volumi PRD nell'area di Azure di ripristino di emergenza.

>[!IMPORTANT]
>Il volume /hana/log non viene replicato perché non è necessario ripristinare nel sito di ripristino di emergenza il database SAP HANA replicato in uno stato coerente.

Configurare quindi la pianificazione dei backup degli snapshot di archiviazione per conseguire gli obiettivi RTO e RPO definiti in caso di emergenza. Per ridurre al minimo l'obiettivo del punto di ripristino (RPO), impostare gli intervalli di replica seguenti nel servizio di istanze Large di HANA:
- Per i volumi gestiti dallo snapshot combinato (tipo di snapshot **HANA**), impostare la replica ogni 15 minuti nelle destinazioni dei volumi di archiviazione equivalenti nel sito di ripristino di emergenza.
- Per il volume di backup del log delle transazioni (tipo di snapshot **log**) impostare la replica ogni tre minuti nelle destinazioni dei volumi di archiviazione equivalenti nel sito di ripristino di emergenza.

Per ridurre al minimo l'obiettivo del punto di ripristino, impostare quanto segue:
- Eseguire uno snapshot di archiviazione di tipo **hana** (vedere il "Passaggio 7: Eseguire gli snapshot") ogni 30/60 minuti.
- Eseguire i backup del log delle transazioni SAP HANA ogni cinque minuti.
- Eseguire uno snapshot di archiviazione di tipo **logs** ogni 5-15 minuti. Con questo intervallo, dovrebbe essere possibile ottenere un valore RPO di circa 15-25 minuti.

Con questa configurazione, la sequenza dei backup del log delle transazioni, degli snapshot di archiviazione e dalla replica del volume di backup del log delle transazioni HANA e dei volumi /hana/data e /hana/shared (che include /usr/sap) dovrebbe essere simile ai dati mostrati in questo grafico:

 ![Relazione tra uno snapshot dei backup del log delle transazioni e uno snapmirror su un asse temporale](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Per ottenere un valore RPO migliore in caso di ripristino di emergenza, è possibile copiare i backup del log delle transazioni HANA da SAP HANA in Azure (istanze Large) all'altra area di Azure. Per ottenere questa ulteriore riduzione del valore RPO, completare questi passaggi:

1. Eseguire il backup del log delle transazioni di HANA il più frequentemente possibile in /hana/logbackups.
1. Usare rsync per copiare i backup del log delle transazioni nelle macchine virtuali di Azure ospitate nella condivisione NFS, che si trovano in reti virtuali di Azure nell'area di produzione di Azure e nelle aree di ripristino di emergenza. È necessario connettere entrambe le reti virtuali di Azure al circuito che stabilisce la connessione tra le istanze Large di HANA di produzione e Azure. Vedere il grafico disponibile nella sezione [Considerazioni sulla rete per il ripristino di emergenza con istanze Large di HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances). 
1. Mantenere i backup del log delle transazioni nell'area nella macchina virtuale collegata all'archiviazione NFS esportata.
1. In caso di failover di emergenza, integrare i backup del log delle transazioni disponibili nel volume /hana/logbackups con i backup del log delle transazioni acquisiti più di recente nella condivisione NFS nel sito di ripristino di emergenza. 
1. Avviare il ripristino del backup del log delle transazioni in base al backup più recente che è stato possibile salvare nell'area di ripristino di emergenza.

Quando le operazioni di HANA in istanze Large confermano la configurazione della relazione di replica e si avvia l'esecuzione dei backup degli snapshot di archiviazione, viene avviata anche la replica dei dati.

![Passaggio della configurazione di ripristino di emergenza prima di definire la replica](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Durante la replica, gli snapshot nei volumi PRD presenti nelle aree di Azure di ripristino di emergenza non vengono replicati, ma vengono solo archiviati. Se i volumi vengono montati con questo stato, rappresentano lo stato in cui sono stati smontati dopo l'installazione dell'istanza PRD di SAP HANA nell'unità server presente nell'area di Azure di ripristino di emergenza. Rappresentano anche i backup di archiviazione non ancora ripristinati.

In caso di failover, è possibile anche scegliere di ripristinare uno snapshot di archiviazione precedente anziché lo snapshot di archiviazione più recente.

## <a name="disaster-recovery-failover-procedure"></a>Procedura di failover di ripristino di emergenza
Esistono due casi diversi da prendere in considerazione per il failover nel sito di ripristino di emergenza:

- È necessario riportare il database SAP HANA allo stato più recente dei dati. In questo caso, è disponibile uno script self-service che permette di eseguire il failover senza contattare Microsoft. Tuttavia, per il failback sarà necessario operare insieme a Microsoft.
- Si vuole ripristinare uno snapshot di archiviazione che non è lo snapshot più recente replicato. In questo caso, è necessario operare insieme a Microsoft. 

>[!NOTE]
>È necessario eseguire i passaggi seguenti nell'unità HANA in istanze Large, che rappresenta l'unità di ripristino di emergenza. 
 
Per ripristinare gli snapshot di archiviazione replicata più recenti, completare i passaggi seguenti: 

1. Arrestare l'istanza non di produzione di HANA nell'unità di ripristino di emergenza di HANA in istanze Large in esecuzione. Il motivo è la presenza di un'istanza di produzione HANA inattiva preinstallata.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. A questo scopo, usare il comando `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. L'output dovrebbe mostrare il processo **hdbdaemon** con stato arrestato e nessun altro processo HANA con stato in esecuzione o avviato.
1. Nell'unità HANA in istanze Large del sito di ripristino di emergenza eseguire lo script *azure_hana_dr_failover.pl*. Lo script richiede che venga ripristinato un tipo di SID SAP HANA. Quando viene richiesto, digitare uno o l'unico SID SAP HANA che è stato replicato e che viene mantenuto nel file *HANABackupCustomerDetails.txt* nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 

      Se si vuole eseguire il failover di più istanze di SAP HANA, è necessario eseguire lo script diverse volte. Quando viene richiesto, digitare il SID SAP HANA di cui si vuole eseguire il failover e il ripristino. Al termine, lo script mostra un elenco di punti di montaggio dei volumi che vengono aggiunti all'unità HANA in istanze Large. Questo elenco include anche i volumi di ripristino di emergenza ripristinati.

1. Montare i volumi di ripristino di emergenza ripristinati usando i comandi del sistema operativo Linux nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si sceglie di copiare i log di backup del log delle transazioni per ridurre il valore RPO, è necessario unire questi backup del log delle transazioni nella nuova directory /hana/logbackups di ripristino di emergenza appena montata. Non sovrascrivere i backup esistenti, ma copiare quelli più recenti che non sono stati replicati con l'ultima replica di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file degli snapshot replicati nel volume /hana/shared/PRD nell'area di Azure di ripristino di emergenza. 

È possibile eseguire anche il test del failover del ripristino di emergenza senza conseguenze per la relazione di replica effettiva. Per eseguire un failover di test, seguire i passaggi 1 e 2 precedenti e quindi continuare con il passaggio 3 seguente.

>[!IMPORTANT]
>*Non* eseguire transazioni di produzione nell'istanza creata nel sito di ripristino di emergenza tramite il processo di **test di un failover** con lo script presentato nel passaggio 3. Il comando crea un set di volumi che non hanno relazione con il sito primario. Di conseguenza, *non* sarà possibile eseguire una nuova sincronizzazione con il sito primario. 

Passaggio 3 per il test del failover:

Nell'unità HANA in istanze Large del sito di ripristino di emergenza eseguire lo script **azure_hana_test_dr_failover.pl**. Questo script *non* interrompe la relazione di replica tra il sito primario e il sito di ripristino di emergenza. Al contrario, questo script clona i volumi di archiviazione di ripristino di emergenza. Dopo che il processo di clonazione ha esito positivo, i volumi clonati vengono ripristinati allo stato dello snapshot più recente e quindi montati nell'unità di ripristino di emergenza. Lo script richiede che venga ripristinato un tipo di SID SAP HANA. Digitare uno o l'unico SID SAP HANA che è stato replicato e che viene mantenuto nel file *HANABackupCustomerDetails.txt* nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 

Se si vuole testare più istanze di SAP HANA, è necessario eseguire lo script più volte. Quando viene richiesto, digitare il SID SAP HANA dell'istanza da testare per il failover. Al termine, lo script mostra un elenco di punti di montaggio dei volumi che vengono aggiunti all'unità HANA in istanze Large. Questo elenco comprende anche i volumi di ripristino di emergenza clonati.

Continuare con il passaggio 4.

   >[!NOTE]
   >Se è necessario eseguire il failover nel sito di ripristino di emergenza per salvare alcuni dati che sono stati eliminati ore prima e di conseguenza i volumi di ripristino di emergenza devono essere impostati su uno snapshot più recente, è necessario usare questa procedura. 

1. Arrestare l'istanza non di produzione di HANA nell'unità di ripristino di emergenza di HANA in istanze Large in esecuzione. Il motivo è la presenza di un'istanza di produzione HANA inattiva preinstallata.
1. Assicurarsi che nessun processo SAP HANA sia più in esecuzione. A questo scopo, usare il comando `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. L'output dovrebbe mostrare il processo **hdbdaemon** in uno stato arrestato e nessun altro processo HANA in esecuzione o con stato avviato.
1. Determinare con quale nome di snapshot o ID backup di SAP HANA si vuole ripristinare il sito di ripristino di emergenza. In casi di ripristino di emergenza reali, questo snapshot è in genere il più recente. Nel caso in cui sia necessario recuperare dati persi, selezionare uno snapshot precedente.
1. Contattare il supporto di Azure tramite una richiesta di supporto ad alta priorità. Richiedere il ripristino dello snapshot (con il nome e la data dello snapshot) o l'ID backup di HANA nel sito di ripristino di emergenza. Il valore predefinito prevede che il lato operazioni ripristini solo il volume /hana/data. Se si vuole ripristinare anche i volumi hana/logbackups, è necessario indicarlo in modo specifico. *Non ripristinare il volume /hana/shared.* È preferibile invece scegliere file specifici, ad esempio global.ini, dalla directory **.snapshot** e dalle relative sottodirectory dopo aver rimontato il volume /hana/shared per PRD. 

   Sul lato operazioni si verificano i passaggi seguenti:

   a. Viene arrestata la replica degli snapshot dal volume di produzione ai volumi di ripristino di emergenza. È possibile che questa interruzione si sia già verificata se un'interruzione di corrente del sito di produzione è il motivo per cui è stato necessario eseguire la procedura di ripristino di emergenza.
   
   b. Il nome dello snapshot di archiviazione o con l'ID di backup selezionato viene ripristinato nei volumi di ripristino di emergenza.
   
   c. Dopo il ripristino, i volumi di ripristino di emergenza sono disponibili per essere montati nelle unità HANA in istanze Large nell'area di ripristino di emergenza.
      
1. Montare i volumi di ripristino di emergenza nell'unità HANA in istanze Large nel sito di ripristino di emergenza. 
1. Avviare l'istanza di produzione di SAP HANA inattiva.
1. Se si sceglie di copiare i log di backup del log delle transazioni per ridurre il valore RPO, è necessario unire questi backup del log delle transazioni nella nuova directory /hana/logbackups di ripristino di emergenza appena montata. Non sovrascrivere i backup esistenti, ma copiare quelli più recenti che non sono stati replicati con l'ultima replica di uno snapshot di archiviazione.
1. È anche possibile ripristinare singoli file degli snapshot replicati nel volume /hana/shared/PRD nell'area di Azure di ripristino di emergenza.

La sequenza di passaggi successiva prevede il ripristino dell'istanza di produzione di SAP HANA in base allo snapshot di archiviazione ripristinato e ai backup del log delle transazioni disponibili:

1. Modificare il percorso di backup in **/hana/logbackups** con SAP HANA Studio.
   ![Modificare il percorso di backup per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA analizza i percorsi dei file di backup e suggerisce il backup del log delle transazioni più recente per il ripristino. L'analisi può richiedere alcuni minuti, fino a quando non viene visualizzata una schermata simile alla seguente: ![Elenco dei backup del log delle transazioni per il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Modificare alcune delle impostazioni predefinite:

      - Deselezionare **Use Delta Backups** (Usa backup differenziali).
      - Selezionare **Initialize Log Area** (Inizializza area log).

   ![Impostare l'opzione Initialize log area (Inizializza area log)](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selezionare **Fine**.

   ![Completare il ripristino di emergenza](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Dovrebbe venire visualizzata una finestra di stato, come quella illustrata qui. Tenere presente che l'esempio si riferisce a un ripristino di emergenza di una configurazione di SAP HANA con scalabilità orizzontale a tre nodi.

![Stato del ripristino](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se il ripristino sembra bloccarsi nella schermata di **completamento** e non viene visualizzata la schermata di stato, verificare che tutte le istanze di SAP HANA nei nodi di lavoro siano in esecuzione. Se necessario, avviare manualmente le istanze di SAP HANA.


### <a name="failback-from-a-dr-to-a-production-site"></a>Failback da un sito di ripristino di emergenza a un sito di produzione
È possibile eseguire il failback dal ripristino di emergenza a un sito di produzione. Si consideri uno scenario in cui il failover nel sito di ripristino di emergenza è stato causato da problemi nell'area di Azure di produzione e non dalla necessità di recuperare dati persi. Per un periodo di tempo il carico di lavoro di produzione SAP è stato eseguito nel sito di ripristino di emergenza. Quando i problemi relativi al sito di produzione vengono risolti, si vuole eseguire il failback nel sito di produzione. Poiché non si vogliono perdere dati, tuttavia, il ritorno al sito di produzione prevede diversi passaggi e una stretta collaborazione con il servizio operazioni di SAP HANA in Azure. Una volta risolti i problemi, è necessario sollecitare personalmente il team operativo perché avvii la sincronizzazione di nuovo nel sito di produzione.

Questa è la sequenza dei passaggi da eseguire:

1. Il team operativo di SAP HANA in Azure avvia la sincronizzazione dei volumi di archiviazione di produzione dai volumi di archiviazione di ripristino di emergenza, che rappresentano ora lo stato di produzione. In questo stato, l'unità di istanze Large di HANA nel sito di produzione viene arrestata.
1. Il team operativo di SAP HANA in Azure monitora la replica e verifica il completamento dell'aggiornamento prima di informare il cliente.
1. È necessario arrestare le applicazioni che usano l'istanza di produzione di HANA nel sito di ripristino di emergenza. Eseguire quindi un backup del log delle transazioni HANA. Arrestare quindi l'istanza di HANA in esecuzione nelle unità HANA in istanze Large nel sito di ripristino di emergenza.
1. Dopo l'arresto dell'istanza di HANA in esecuzione nell'unità HANA in istanze Large nel sito di ripristino di emergenza, il team operativo sincronizza di nuovo i volumi dei dischi manualmente.
1. Il servizio operazioni di SAP HANA in Azure avvia nuovamente l'unità di istanze Large di HANA nel sito di produzione e la consegna all'utente. Assicurarsi che l'istanza di SAP HANA sia in uno stato di arresto nel momento in cui viene avviata l'unità HANA in istanze Large.
1. Eseguire gli stessi passaggi di ripristino del database eseguiti in precedenza per il failover nel sito di ripristino di emergenza.

### <a name="monitor-disaster-recovery-replication"></a>Monitorare la replica di ripristino di emergenza

È possibile monitorare lo stato di avanzamento della replica di archiviazione eseguendo lo script `azure_hana_replication_status.pl`. Per poter funzionare nel modo previsto, questo script deve essere eseguito da un'unità in esecuzione nella posizione di ripristino di emergenza. Lo script funziona indipendentemente dal fatto che la replica sia o meno attiva. Lo script può essere eseguito per ogni unità di istanze Large di HANA del tenant nella posizione di ripristino di emergenza. Non può essere usato per ottenere informazioni dettagliate sul volume di avvio.

Chiamare lo script con questo comando:
```
./replication_status.pl <HANA SID>
```

L'output viene suddiviso per volume nelle sezioni seguenti:  

- Stato del collegamento
- Attività di replica corrente
- Snapshot più recente replicato 
- Dimensioni dello snapshot più recente
- Tempo di ritardo corrente tra snapshot, ovvero tra l'ultima replica snapshot completata e il momento attuale

Lo stato del collegamento viene visualizzato come **attivo**, a meno che il collegamento tra le posizioni non sia stato interrotto o non sia in corso un evento di failover. L'attività di replica si riferisce al fatto che sia in corso la replica dei dati oppure che i dati siano inattivi o ci siano altre attività in corso nel collegamento. L'ultimo snapshot replicato dovrebbe essere visualizzato solo come `snapmirror…`. Vengono quindi visualizzate le dimensioni dell'ultimo snapshot. Infine, viene visualizzato il tempo di ritardo. Il tempo di ritardo rappresenta il tempo tra la replica pianificata e il completamento della replica. Un tempo di ritardo può superare un'ora per la replica dei dati, in particolare per la replica iniziale, anche se la replica è stata avviata. Il tempo di ritardo continua ad aumentare fino al termine della replica in corso.

Di seguito viene fornito un esempio dell'output:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```













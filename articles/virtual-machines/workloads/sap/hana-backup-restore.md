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
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707246"
---
# <a name="backup-and-restore"></a>Backup e ripristino

>[!IMPORTANT]
>Questa documentazione non sostituisce la documentazione relativa all'amministrazione di SAP HANA o le note su SAP. Si presuppone che il lettore abbia una solida comprensione ed esperienza in termini di amministrazione e operazioni di SAP HANA, in particolare per quanto riguarda gli argomenti di backup, ripristino, disponibilità elevata e ripristino di emergenza. In questa documentazione, vengono mostrate schermate da SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

È importante esercitarsi con i passaggi e i processi adottati nell'ambiente e con le versioni di HANA. Alcuni processi descritti in questa documentazione sono stati semplificati per una migliore comprensione generale e non devono essere usati come passaggi dettagliati per manuali operativi finali. Se si vuole creare manuali operativi per le configurazioni, è necessario testare e provare i processi e documentare quelli correlati alle configurazioni specifiche. 

Uno degli aspetti più importanti per i database operativi è garantirne la protezione da eventi catastrofici riconducibili a cause diverse, da disastri naturali a semplici errori umani.

Il backup di un database con possibilità di ripristino a una temporizzazione qualsiasi, ad esempio prima dell'eliminazione di dati critici, consente di ripristinare il database a uno stato che sia il più vicino possibile a quello precedente l'interruzione.

Per ottenere tale funzionalità da ripristinare, è necessario eseguire due tipi di backup:

- Backup dei database: completo, incrementale o differenziale
- Backup dei log delle transazioni

Oltre a creare backup di database completi a livello di applicazione, è possibile anche creare backup con snapshot di archiviazione. Gli snapshot di archiviazione non sostituiscono i backup del log delle transazioni, che restano tuttavia importanti per il ripristino temporizzato del database o per svuotare i log dalle transazioni di cui è già stato eseguito il commit. Gli snapshot di archiviazione consentono comunque di accelerare il ripristino, fornendo rapidamente un'immagine di rollforward del database. 

SAP HANA in Azure (istanze Large) offre due opzioni di backup e ripristino:

- Backup e ripristino manuali. Dopo avere verificato che vi sia spazio su disco sufficiente, eseguire backup completi del database e log usando uno dei seguenti metodi di backup del disco. È possibile eseguire il backup direttamente in volumi collegati alle unità HANA in istanze Large o in condivisioni NFS configurate in una macchina virtuale di Azure. In questo secondo caso, i clienti configurano una macchina virtuale Linux in Azure, collegano Archiviazione di Azure alla macchina virtuale e condividono l'archiviazione tramite un server NFS configurato nella macchina virtuale. Se si esegue il backup sui volumi direttamente collegati alle unità di istanze Large di HANA, è necessario copiare i backup in un account di archiviazione di Azure (dopo aver configurato una macchina virtuale di Azure che esporta le condivisioni NFS basate su Archiviazione di Azure) oppure è possibile usare un insieme di credenziali di backup di Azure o l'archiviazione offline sicura di Azure. 

   Un'altra soluzione consiste nell'usare uno strumento di protezione dei dati di terze parti per archiviare i backup dopo averli copiati in un account di archiviazione di Azure. L'opzione di backup manuale può essere necessaria anche per i dati che devono essere archiviati più a lungo a scopo di controllo e conformità. In tutti i casi, i backup vengono copiati in condivisioni NFS rappresentate tramite una macchina virtuale e Archiviazione di Azure.

- Funzionalità di backup e ripristino dell'infrastruttura. È anche possibile usare la funzionalità di backup e ripristino offerta dall'infrastruttura sottostante di SAP HANA in Azure (istanze Large). Questa opzione soddisfa la necessità di backup e ripristini veloci. La parte restante di questa sezione illustra le funzionalità di backup e ripristino offerte con le istanze Large di HANA. Questa sezione descrive anche la relazione tra il backup e il ripristino e la funzionalità di ripristino di emergenza offerta da HANA in istanze Large.

> [!NOTE]
>   La tecnologia di snapshot usata dall'infrastruttura sottostante di istanze Large di HANA presenta una dipendenza dagli snapshot SAP HANA. Attualmente gli snapshot SAP HANA non funzionano in combinazione con più tenant di contenitori di database multi-tenant SAP HANA. Se viene distribuito un solo tenant, gli snapshot SAP HANA funzionano ed è possibile usare questo metodo.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso degli snapshot di archiviazione di SAP HANA in Azure (istanze di grandi dimensioni)

L'infrastruttura di archiviazione sottostante di SAP HANA in Azure (istanze Large) supporta gli snapshot di archiviazione dei volumi. Il backup e il ripristino dei volumi è supportato con le considerazioni seguenti:

- Invece di backup di database completi, vengono creati snapshot dei volumi di archiviazione a intervalli frequenti.
- Quando viene attivato uno snapshot sui volumi /hana/data e /hana/shared (che include /usr/sap), la tecnologia degli snapshot avvia uno snapshot SAP HANA prima di eseguire lo snapshot di archiviazione. Questo snapshot di SAP HANA è il punto di installazione per i ripristini dei log finali dopo il ripristino dello snapshot di archiviazione. Per lo snapshot HANA abbia esito positivo, è necessario un'istanza HANA attiva.  Nello scenario HSR, lo snapshot di archiviazione non è supportato nel nodo secondario corrente se in questo non è possibile eseguire lo snapshot di HANA.
- Al termine della corretta esecuzione dello snapshot di archiviazione, lo snapshot SAP HANA viene eliminato.
- I backup del log delle transazioni vengono creati di frequente e archiviati nel volume /hana/logbackups o in Azure. È possibile attivare il volume /hana/logbackups che contiene i backup del log delle transazioni per creare separatamente uno snapshot. In questo caso, non è necessario eseguire uno snapshot HANA.
- Se è necessario ripristinare un database a un determinato punto nel tempo, richiedere il supporto di Microsoft Azure (interruzione di produzione) o SAP HANA sul ripristino di Azure a uno specifico snapshot di archiviazione. ad esempio per un ripristino pianificato di un sistema sandbox allo stato originale.
- Lo snapshot SAP HANA incluso nello snapshot di archiviazione è un punto di offset per l'applicazione dei backup del log delle transazioni che sono stati creati e archiviati dopo la creazione dello snapshot di archiviazione.
- Questi backup del log delle transazioni vengono creati per il ripristino temporizzato del database.

È possibile eseguire snapshot di archiviazione su tre classi di volumi diverse:

- Snapshot combinato su /hana/data e /hana/shared (che include /usr/sap). Questo snapshot richiede la creazione di uno snapshot SAP HANA come fase preliminare per lo snapshot di archiviazione. Lo snapshot SAP HANA verifica che il database sia in uno stato coerente dal punto di vista dell'archiviazione. Questo per il processo di ripristino che rappresenta un punto da impostare.
- Snapshot separato su /hana/logbackups.
- Una partizione del sistema operativo.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Quando si scarica il pacchetto di script di snapshot dal [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), otterrai i tre file di cui uno è una documentazione PDF per la funzionalità fornita. Assicurarsi che procedere lungo le istruzioni nel capitolo 'Recupero degli snapshot strumenti' quando il set di strumenti di download.

## <a name="storage-snapshot-considerations"></a>Considerazioni sugli snapshot di archiviazione

>[!NOTE]
>Gli snapshot di archiviazione usano spazio di archiviazione allocato alle unità di istanze Large di HANA. È necessario considerare gli aspetti seguenti correlati alla pianificazione di snapshot di archiviazione e il numero di snapshot di archiviazione da mantenere. 

I meccanismi specifici degli snapshot di archiviazione per SAP HANA in Azure (istanze Large) comprendono:

- Uno snapshot di archiviazione specifico (nel momento in cui viene creato) usa una quantità di spazio limitata.
- Ogni volta che il contenuto dei dati cambia e il contenuto dei file di dati di SAP HANA viene modificato nel volume di archiviazione, lo snapshot deve archiviare il contenuto dei blocchi originali.
- Le dimensioni dello snapshot di archiviazione, quindi, aumentano in proporzione alla durata.
- Quante più modifiche vengono apportate al volume del database SAP HANA per tutta la durata di uno snapshot di archiviazione, tanto più grande diventa lo spazio usato dallo snapshot.

SAP HANA in Azure (istanze Large) viene fornito con volumi di dimensioni fisse per l'archiviazione di dati e log. L'esecuzione di snapshot di questi volumi comporta il consumo dello spazio dei volumi. È necessario determinare, durante la pianificazione degli snapshot di archiviazione. monitorare lo spazio dei volumi di archiviazione occupato e gestire il numero di snapshot archiviati. È possibile disabilitare gli snapshot di archiviazione quando vengono importate grandi quantità di dati o apportate altre modifiche significative al database HANA. 


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
1. Copiare gli script e il file di configurazione da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) nel percorso di **hdbsql** dell'installazione di SAP HANA.
1. Modificare il file *HANABackupDetails.txt* nel modo necessario, in base alle specifiche del cliente.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Per i passaggi dettagliati sopra elencati, fare riferimento a [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>Considerazioni per gli scenari MCOD
Se si esegue uno [scenario MCOD](https://launchpad.support.sap.com/#/notes/1681092) con più istanze di SAP HANA in un'unità HANA in istanze Large, verrà effettuato il provisioning di volumi di storage separati per ognuna delle istanze di SAP HANA. Per informazioni dettagliate sulle MDC e altre considerazioni, consultare [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) capitolo **'Aspetti importanti da ricordare'**.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passaggio 1: Installare il client HDB di SAP HANA

Il sistema operativo Linux installato su SAP HANA in Azure (istanze Large) include le cartelle e gli script necessari per eseguire snapshot di archiviazione SAP HANA per scopi di backup e ripristino di emergenza. Verificare se sono disponibili versioni più recenti in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). La versione più recente degli script è 4.0. Diversi script potrebbero avere versioni secondarie diverse all'interno della stessa versione principale.

È necessario installare personalmente il client HDB di SAP HANA nelle unità HANA in istanze Large durante l'installazione di SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Passaggio 2: Modificare /etc/ssh/ssh\_config

Questo passaggio è descritto in dettaglio nel controllo per le versioni più recenti nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) capitolo **'Abilitazione della comunicazione con l'archiviazione'**


### <a name="step-3-create-a-public-key"></a>Passaggio 3: Creare una chiave pubblica

Per consentire l'accesso alle interfacce degli snapshot di archiviazione del tenant di HANA in istanze Large, è necessario definire una procedura di accesso tramite chiave pubblica. Nel primo server SAP HANA in Azure (istanze Large) nel tenant creare una chiave pubblica da usare per accedere all'infrastruttura di archiviazione. La creazione di una chiave pubblica evita di dover usare una password per accedere alle interfacce degli snapshot di archiviazione e di dover gestire le credenziali di tipo password. I passaggi esatti come generare pubblico chiave è descritto nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) nel capitolo **'Abilitazione della comunicazione con l'archiviazione'**


### <a name="step-4-create-an-sap-hana-user-account"></a>Passaggio 4: Creare un account utente SAP HANA

Per avviare la creazione di snapshot SAP HANA, è necessario creare un account utente in SAP HANA che possa essere usato dagli script degli snapshot di archiviazione. Creare quindi un account utente SAP HANA in SAP HANA Studio. L'utente deve essere creato in SYSTEMDB e NON nel database SID per MDC. Nell'ambiente singolo contenitore, l'utente viene creato nel database del tenant. L'account deve disporre dei privilegi seguenti: **Backup Admin** e **Catalog Read**. Per i passaggi esatti impostare l'utente e usare l'utente, leggere [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) capitolo **'Abilitazione della comunicazione con SAP HANA'**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passaggio 5: Autorizzare l'account utente SAP HANA

In questo passaggio si autorizza l'account utente SAP HANA creato in modo che gli script non debbano inviare password in fase di esecuzione. Il comando `hdbuserstore` di SAP HANA consente la creazione di una chiave utente SAP HANA, che viene archiviata in uno o più nodi di SAP HANA. La chiave utente consente all'utente di accedere a SAP HANA senza dover gestire le password dall'interno del processo di scripting, descritto più avanti in questo articolo.

>[!IMPORTANT]
>Eseguire questi comandi di configurazione con lo stesso contesto utente che in cui vengono eseguiti i comandi di snapshot. In caso contrario, i comandi di snapshot non funzionano correttamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passaggio 6: Ottenere gli script degli snapshot, configurare gli snapshot e testare la configurazione e la connettività

Scaricare la versione più recente degli script da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Il modo in cui che gli script sono dovrà essere installato è stato modificato majorly con versione 4.0 di script. Per informazioni dettagliate precise, leggere [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) nel capitolo **'Abilitazione della comunicazione con SAP HANA'**

Per la sequenza esatta dei comandi, vedere il capitolo **'Facile installazione di strumenti di snapshot (impostazione predefinita)'** del documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Si consiglia l'utilizzo dell'installazione predefinita. Se si desidera eseguire l'aggiornamento dalla versione 3.x a 4.0, vedere la sezione **'L'aggiornamento di un'installazione esistente'** dei [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Per disinstallare il set di 4.0 strumenti, seguire le istruzioni in **'La disinstallazione degli strumenti di snapshot'** nelle [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Non dimenticare di eseguire i passaggi descritti in **'Il programma di installazione completa di strumenti di snapshot di'** dei [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Lo scopo dei file e script diversi come che sia state eseguite è elencato e descritte in dettaglio in **'Che cosa sono questi strumenti snapshot'?** del documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Prima di configurare gli strumenti di snapshot, assicurarsi che anche configurato percorsi di backup di HANA e le impostazioni correttamente come descritto nella **'Configurazione di SAP HANA'** del documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

La configurazione del set di strumenti snapshot è descritto dettagliatamente **'File di configurazione - hanabackupcustomerdetails. txt'** del documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="testing-connectivity-with-sap-hana"></a>Verifica la connettività di SAP HANA

Dopo aver inserito tutti i dati di configurazione nel file *HANABackupCustomerDetails.txt*, verificare che le configurazioni siano corrette in relazione ai dati delle istanze di HANA. Usare lo script `testHANAConnection`, che è indipendente da una configurazione di SAP HANA con scalabilità orizzontale o verticale.

Per informazioni dettagliate, vedere **'Verifica di connettività con SAP HANA - testHANAConnection'** del documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>Test di connettività dell'archiviazione

Il passaggio successivo consiste nel verificare la connettività all'archiviazione in base ai dati inseriti nel file di configurazione *HANABackupCustomerDetails.txt*, per poi eseguire uno snapshot di test. Prima di eseguire il `azure_hana_backup` comando, è necessario eseguire questo test. La sequenza di comandi per questo test è elencata nella **'Verifica di connettività con l'archiviazione - testStorageSnapshotConnection'** del documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Dopo aver eseguito l'accesso alle interfacce della macchina virtuale di archiviazione, lo script continua con la fase 2 e crea uno snapshot di test. Di seguito è riportato l'output generato con una configurazione di SAP HANA con scalabilità orizzontale a tre nodi:

Se lo snapshot di test è stato eseguito correttamente con lo script, è possibile procedere con la pianificazione degli snapshot di archiviazione effettiva. Se l'esecuzione non riesce, analizzare i problemi prima di continuare. Lo snapshot di test deve rimanere disponibile fino a quando non vengono eseguiti i primi snapshot reali.


### <a name="step-7-perform-snapshots"></a>Passaggio 7: Eseguire gli snapshot

Al termine i passaggi di preparazione, è possibile iniziare a configurare e pianificare gli snapshot di archiviazione effettivo. Lo script da pianificare funziona con le configurazioni con scalabilità verticale e con scalabilità orizzontale di SAP HANA. Per l'esecuzione periodica e regolare dello script di backup, pianificare lo script tramite l'utilità cron. 

Per la sintassi del comando esatto e funzionalità, leggere **'Backup di snapshot Esegui - azure_hana_backup'** del documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).  

L'esecuzione dello script `azure_hana_backup` crea lo snapshot di archiviazione nelle tre fasi seguenti:

1. Esegue uno snapshot SAP HANA
1. Esegue uno snapshot di archiviazione
1. Rimuove lo snapshot SAP HANA creato prima dell'esecuzione dello snapshot di archiviazione

Per eseguire lo script, chiamarlo dalla cartella dell'eseguibile HDB in cui è stato copiato. 

Il periodo di conservazione viene gestito in base al numero di snapshot specificato come parametro quando si esegue lo script. Il periodo di tempo coperto dagli snapshot di archiviazione è una funzione del periodo di esecuzione e del numero di snapshot specificati come parametro durante l'esecuzione dello script. Se il numero di snapshot conservati supera il numero indicato come parametro nella chiamata dello script, lo snapshot di archiviazione meno recente con la stessa etichetta viene eliminato prima dell'esecuzione di un nuovo snapshot. Il numero specificato come ultimo parametro della chiamata corrisponde quindi al numero che è possibile usare per controllare il numero di snapshot conservati. Con questo numero è possibile controllare indirettamente anche lo spazio su disco usato per gli snapshot. 


## <a name="snapshot-strategies"></a>Strategie per gli snapshot
Per i vari tipi disponibili, la frequenza degli snapshot varia a seconda che si usi o meno la funzionalità di ripristino di emergenza di HANA in istanze Large. Questa funzionalità è basata sugli snapshot di archiviazione, che possono richiedere l'applicazione di alcune indicazioni speciali in termini di frequenza e periodi di esecuzione degli snapshot di archiviazione. 

Le considerazioni e le indicazioni seguenti presuppongono che *non* venga usata la funzionalità di ripristino di emergenza offerta da HANA in istanze Large. Si usano invece gli snapshot di archiviazione per avere dei backup ed è possibile fornire il ripristino temporizzato per gli ultimi 30 giorni. Considerate le limitazioni in termini di numero di snapshot e spazio, i clienti hanno preso in considerazione i requisiti seguenti:

- Tempo di ripristino per un ripristino temporizzato.
- Utilizzo dello spazio.
- Obiettivo del punto di ripristino e obiettivo del tempo di ripristino per il possibile ripristino da un'emergenza.
- Eventuale esecuzione di backup completi del database HANA sui dischi. Ogni volta che viene creato un backup completo del database sui dischi o che viene eseguita l'interfaccia **backint**, l'esecuzione di snapshot di archiviazione ha esito negativo. Se si pianifica l'esecuzione di backup completi del database oltre agli snapshot di archiviazione, assicurarsi che durante questo periodo l'esecuzione degli snapshot di archiviazione sia disabilitata.
- Numero massimo di snapshot per volume (limitato a 250).

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Per i clienti che non usano la funzionalità di ripristino di emergenza di istanze Large di HANA, il periodo degli snapshot è meno frequente. In questi casi, alcuni clienti eseguono snapshot combinati su /hana/data e /hana/shared (che include /usr/sap) in periodi di 12 o 24 ore e conservano gli snapshot per un mese. Lo stesso vale per gli snapshot del volume di backup del log. Tuttavia, l'esecuzione del backup del log delle transazioni SAP HANA sul volume di backup del log avviene in periodi da 5 a 15 minuti.

Gli snapshot di archiviazione pianificati vengono eseguiti al meglio usando cron. Usare lo stesso script per tutti i backup e le esigenze di ripristino di emergenza e modificare gli input dello script in modo che corrispondano ai diversi tempi di backup richiesti. Gli snapshot sono tutti pianificati in modo diverso in Cron, in base alla frequenza di esecuzione: oraria, ogni 12 ore, giornaliera o settimanale. 

Di seguito viene fornito un esempio di pianificazione cron in /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Nell'esempio precedente, è uno snapshot orario combinato che copre i volumi contenenti i dati Hana e /hana/shared/SID (include usr/sap) posizioni. Usare questo tipo di snapshot per un recupero temporizzato più rapido entro i due giorni precedenti. Su questi volumi c'è inoltre uno snapshot giornaliero. Si ottengono così due giorni di copertura tramite gli snapshot orari, più quattro settimane di copertura tramite gli snapshot giornalieri. Inoltre, il backup del volume di backup del log delle transazioni viene eseguito una volta al giorno. Anche questi backup vengono conservati per quattro settimane. Come si può osservare nella terza riga di crontab, il backup del log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. I tempi di avvio dei diversi processi cron che eseguono snapshot di archiviazione sono scaglionati, in modo che gli snapshot non vengano eseguiti tutti contemporaneamente in un determinato momento. 

Nell'esempio seguente, si esegue uno snapshot combinato che copre i volumi che contengono il/hana/data e /hana/shared/SID (inclusi usr/sap) posizioni su base oraria. Questi snapshot vengono conservati per due giorni. Gli snapshot dei volumi di backup del log delle transazioni vengono eseguiti ogni cinque minuti e conservati per quattro ore. Come indicato prima, il backup del file di log delle transazioni HANA è pianificato per l'esecuzione ogni cinque minuti. Lo snapshot del volume di backup del log delle transazioni viene eseguito con un ritardo di due minuti dopo l'avvio del backup del log delle transazioni. In questi due minuti, normalmente viene completato il backup del log delle transazioni SAP HANA. Come prima, il backup del volume contenente il LUN di avvio viene eseguito una volta al giorno tramite uno snapshot di archiviazione e conservato per quattro settimane.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Il grafico seguente illustra le sequenze dell'esempio precedente, escluso il LUN di avvio:

![Relazione tra backup e snapshot](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA esegue regolari operazioni di scrittura sul volume /hana/log per documentare le modifiche di cui viene eseguito il commit nel database. A intervalli regolari, SAP HANA scrive un punto di salvataggio nel volume /hana/data. Come specificato in crontab, il backup del log delle transazioni SAP HANA viene eseguito ogni cinque minuti. Noterete anche che uno snapshot SAP HANA viene eseguito ogni ora, in seguito all'attivazione di uno snapshot di archiviazione combinato sui volumi /hana/data e /hana/shared/SID. Dopo la corretta esecuzione dello snapshot HANA, viene eseguito lo snapshot di archiviazione combinato. Come indicato in crontab, lo snapshot di archiviazione nel volume /hana/logbackup viene eseguito ogni cinque minuti, circa due minuti dopo il backup del log delle transazioni HANA.

> 

>[!IMPORTANT]
> L'uso di snapshot di archiviazione per i backup di SAP HANA è importante solo quando gli snapshot vengono eseguiti insieme ai backup del log delle transazioni SAP HANA. Questi backup del log delle transazioni devono coprire i periodi di tempo che intercorrono tra gli snapshot di archiviazione. 

Se agli utenti si è garantito un ripristino temporizzato di 30 giorni, è necessario che siano soddisfatti i requisiti seguenti:

- In casi estremi, accedere a una risorsa di archiviazione combinato su /hana/data e /hana/shared/SID che ha 30 giorni di snapshot.
- Predisporre backup del log delle transazioni contigui che coprano il tempo tra tutti gli snapshot di archiviazione combinati. Lo snapshot meno recente del volume di backup del log delle transazioni deve quindi risalire a 30 giorni prima, a meno che i backup del log delle transazioni non siano stati copiati in un'altra condivisione NFS nell'archiviazione di Azure. In questo caso, è possibile eseguire il pull dei backup del log delle transazioni precedenti dalla condivisione NFS.

Per poter usufruire di snapshot di archiviazione e della successiva replica di archiviazione dei backup del log delle transazioni, è necessario modificare la posizione in cui SAP HANA scrive i backup del log delle transazioni. Questa modifica può essere eseguita in HANA Studio. Anche se SAP HANA esegue il backup automatico di segmenti di log completi, è necessario specificare un intervallo di backup del log in modo deterministico. Questo vale soprattutto quando si usa l'opzione di ripristino di emergenza, perché in questo caso è preferibile eseguire i backup del log con un periodo deterministico. Nel caso seguente, sono stati impostati 15 minuti come intervallo di backup del log.

![Pianificare i log di backup di SAP HANA in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

È anche possibile scegliere backup con una frequenza maggiore di 15 minuti. Questo tipo di impostazione con frequenza maggiore viene spesso usato in combinazione con la funzionalità di ripristino di emergenza di HANA in istanze Large. Alcuni clienti eseguono i backup del log delle transazioni ogni cinque minuti.  

Se non è mai stato eseguito il backup del database, il passaggio finale consiste nell'eseguire un backup del database basato su file per creare una singola voce di backup nel catalogo di backup. In caso contrario, SAP HANA non può avviare i backup del log specificati.

![Eseguire un backup basato su file per creare una singola voce di backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Dopo che sono stati eseguiti i primi snapshot di archiviazione con esito positivo, è necessario eliminare lo snapshot di test che è stato eseguito nel passaggio 6. Lettura **'Remove test snapshots - removeTestStorageSnapshot'** nel documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) per informazioni dettagliate. 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoraggio del numero e della dimensione degli snapshot sul volume di archiviazione

In un volume di archiviazione specifico è possibile monitorare il numero di snapshot e l'utilizzo dell'archiviazione degli snapshot. Il comando `ls` non mostra i file e la directory degli snapshot, mentre il comando del sistema operativo Linux `du` mostra i dettagli di questi snapshot di archiviazione, poiché sono archiviati negli stessi volumi. Il comando può essere usato con le opzioni seguenti:

- `du –sh .snapshot`: fornisce il totale di tutti gli snapshot all'interno della directory degli snapshot.
- `du –sh --max-depth=1`: elenca tutti gli snapshot salvati nella cartella **.snapshot** e le dimensioni di ciascuno.
- `du –hc`: fornisce la dimensione totale usata da tutti gli snapshot.

Usare questi comandi per assicurarsi che gli snapshot creati e archiviati non usino tutto lo spazio di archiviazione sui volumi.

>[!NOTE]
>Gli snapshot del LUN di avvio non sono visibili con i comandi precedenti.

### <a name="getting-details-of-snapshots"></a>Recupero dei dettagli degli snapshot
Per ottenere maggiori dettagli sugli snapshot, è anche possibile usare lo script `azure_hana_snapshot_details`. Questo script può essere eseguito in ognuna delle posizioni, se c'è un server attivo nella posizione di ripristino di emergenza. Lo script fornisce l'output seguente per ogni volume contenente gli snapshot: 
   * Dimensioni degli snapshot totali in un volume
   * Per ogni snapshot nel volume, vengono fornite le informazioni seguenti: 
      - Nome dello snapshot 
      - Ora di creazione 
      - Dimensioni dello snapshot
      - Frequenza dello snapshot
      - ID di backup HANA associato allo snapshot (se pertinente)

Per informazioni sulla sintassi dei controlli effettuati dalla comandi e output **'Di elencare gli snapshot - azure_hana_snapshot_details'** nel documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Riduzione del numero di snapshot in un server

Come illustrato in precedenza, è possibile ridurre il numero di snapshot archiviati con una determinata etichetta. Gli ultimi due parametri del comando per avviare uno snapshot sono un'etichetta e il numero di snapshot da mantenere.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Nell'esempio precedente l'etichetta dello snapshot è **dailyhana** e il numero di snapshot con questa etichetta da conservare è **28**. Dovendo limitare l'uso dello spazio su disco, può essere opportuno ridurre il numero di snapshot archiviati. Il modo più semplice per ridurre il numero di snapshot, ad esempio a 15, consiste nell'eseguire lo script con l'ultimo parametro impostato su **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se si esegue lo script con questa impostazione, il numero di snapshot, incluso il nuovo snapshot di archiviazione, è 15. Vengono conservati i 15 snapshot più recenti, mentre i 15 snapshot meno recenti vengono eliminati.

 >[!NOTE]
 > Questo script riduce il numero di snapshot solo se sono presenti snapshot che risalgono a più di un'ora prima. Lo script non elimina gli snapshot che hanno meno di un'ora. Queste limitazioni sono correlate alla funzionalità di ripristino di emergenza facoltativa offerta.

Se non si desidera mantenere un set di snapshot con il prefisso di backup non è più **dailyhana** negli esempi di sintassi, è possibile eseguire lo script con **0** come numero di snapshot. Tutti gli snapshot che corrispondono a questa etichetta verranno quindi rimossi. Tuttavia, la rimozione di tutti gli snapshot può influire sulla funzionalità di ripristino di emergenza di HANA in istanze Large.

Una seconda opzione per eliminare snapshot specifici consiste nell'usare lo script `azure_hana_snapshot_delete`. Questo script è progettato per eliminare uno snapshot o un set di snapshot usando l'ID backup di HANA indicato in HANA Studio o tramite il nome dello snapshot stesso. Attualmente, l'ID di backup è collegato solo agli snapshot creati per il tipo di snapshot **hana**. I backup di snapshot di tipo **log** e **avvio** non eseguono uno snapshot SAP HANA e di conseguenza non esiste alcun ID backup da trovare per questi snapshot. Se viene immesso il nome dello snapshot, vengono cercati tutti gli snapshot corrispondenti al nome immesso sui diversi volumi. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Per informazioni sullo script, vedere **'Di eliminare uno snapshot - azure_hana_snapshot_delete'** nel documento [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Eseguire lo script come utente **root**.

>[!IMPORTANT]
>In caso di dati presenti solo nello snapshot in fase di eliminazione, i dati verranno persi per sempre dopo l'eliminazione dello snapshot.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>Ripristino a livello di file da uno snapshot di archiviazione

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Per i tipi di snapshot **HANA** e **log**, è possibile accedere direttamente agli snapshot nei volumi nella directory **.snapshot**. È presente una sottodirectory per ogni snapshot. È possibile copiare ogni file nello stato in cui si trovava al momento dello snapshot da questa sottodirectory all'effettiva struttura di directory. Nella versione corrente dello script, è presente **NO** Ripristina script fornito per il ripristino dello snapshot come un self-service (anche se il ripristino di snapshot può essere eseguito come parte del ripristino di emergenza self-service di script nel sito di ripristino di emergenza durante il failover). È necessario contattare il team operativo Microsoft aprendo una richiesta di servizio per il ripristino dello snapshot desiderato tra gli snapshot esistenti disponibili.

>[!NOTE]
>Il ripristino di un file singolo non funziona per gli snapshot dell'avvio LUN indipendente del tipo delle unità HANA istanza grande. La directory **.snapshot** non è esposta nel LUN di avvio. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Ripristino in base allo snapshot HANA più recente

Se si riscontra un problema di blocco dell'ambiente di produzione, il processo di ripristino da uno snapshot di archiviazione può essere avviato come richiesta di supporto del cliente al supporto di Microsoft Azure. Questo è un problema di estrema urgenza se i dati sono stati eliminati in un sistema di produzione e l'unico modo per recuperarli consiste nel ripristinare il database di produzione.

In una situazione diversa, con minor urgenza, sarebbe possibile usare un ripristino temporizzato, pianificabile con diversi giorni di anticipo. È possibile pianificare questo ripristino con SAP HANA in Azure anziché segnalare un flag ad alta priorità. È possibile, ad esempio, che sia necessario pianificare un aggiornamento del software SAP con un nuovo Enhancement Package e successivamente dover eseguire il ripristino in base a uno snapshot che rappresenta lo stato precedente all'aggiornamento Enhancement Package.

Prima di eseguire la richiesta, è necessario completare alcune operazioni di preparazione. Il team SAP HANA in Azure può quindi gestire la richiesta e fornire i volumi ripristinati. L'utente dovrà quindi ripristinare il database HANA in base agli snapshot.

Le possibilità per ottenere uno snapshot ripristinato con il nuovo set di strumenti sono descritti nella sezione **'Come eseguire il ripristino di uno snapshot'** del documento [manuale Recovery Guide per SAP HANA in Azure da uno Snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Di seguito viene mostrato come preparare la richiesta:

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



Un'altra possibilità per ottenere, ad esempio, stati ripristinati da uno snapshot di archiviazione file di dati di SAP HANA è documentata nel passaggio 7 del documento [manuale Recovery Guide per SAP HANA in Azure da uno Snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Il documento [manuale Recovery Guide per SAP HANA in Azure da uno Snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) viene illustrata la sequenza di ripristino da backup di snapshot. Usare la documentazione per l'esecuzione di un ripristino. 

>[!Note]
>Passaggio 7 non è necessario eseguire se è stato ottenuto lo snapshot di ripristino da Microsoft operations.


### <a name="recover-to-another-point-in-time"></a>Ripristino in base a un'altra temporizzazione
Il documento [manuale Recovery Guide per SAP HANA in Azure da uno Snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) Mostra la sequenza di ripristino per un determinato punto nel tempo nella sezione **"Ripristina il database fino al punto seguente nel tempo"**. Usare la documentazione per l'esecuzione di un ripristino a un determinato punto nel tempo. 


## <a name="next-steps"></a>Passaggi successivi
- Visualizzare [principi relativi al ripristino di emergenza e preparazione](hana-concept-preparation.md).

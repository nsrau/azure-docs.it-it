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
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64987909"
---
# <a name="backup-and-restore"></a>Backup e ripristino

>[!IMPORTANT]
>Questo articolo non è una sostituzione per la documentazione relativa all'amministrazione di SAP HANA o SAP note. È probabile che si dispone di una solida comprensione ed esperienza nell'amministrazione di SAP HANA e le operazioni, in particolare per il backup, ripristino, disponibilità elevata e ripristino di emergenza. In questo articolo, vengono visualizzate schermate da SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

È importante esercitarsi con i passaggi e i processi adottati nell'ambiente e con le versioni di HANA. Alcuni processi descritti in questo articolo sono stati semplificati per una migliore comprensione generale. Essi non sono lo scopo di essere utilizzati come passaggi dettagliati per manuali operativi finali. Se si desidera creare manuali operativi per le configurazioni, test e i processi e documentare i processi correlati alle configurazioni specifiche. 

Uno degli aspetti più importanti dei database operativi è per la protezione da eventi catastrofici. riconducibili a cause diverse, da disastri naturali a semplici errori umani.

Backup di database, con la possibilità di ripristinare in qualsiasi punto nel tempo, a, ad esempio prima di un utente eliminato i dati critici, consente di ripristinare in uno stato che è più vicino possibile al modo in cui si è verificato prima dell'interruzione.

Per ottenere la possibilità di ripristino è necessario eseguire due tipi di backup:

- Backup dei database: completo, incrementale o differenziale
- Backup dei log delle transazioni

Oltre a creare backup di database completi a livello di applicazione, è possibile anche creare backup con snapshot di archiviazione. Gli snapshot di archiviazione non sostituiscono i backup del log delle transazioni. che restano tuttavia importanti per il ripristino temporizzato del database o per svuotare i log dalle transazioni di cui è già stato eseguito il commit. Gli snapshot di archiviazione possono accelerare il ripristino, fornendo rapidamente un'immagine di rollforward del database. 

SAP HANA in Azure (istanze Large) offre due opzioni di backup e ripristino:

- **Eseguire questa operazione manualmente (fai da te).** Dopo aver apportato che vi sia spazio su disco sufficiente, eseguire completo del database e backup del log usando uno dei seguenti metodi di backup del disco. È possibile eseguire il backup direttamente sui volumi collegati alle unità di istanze Large di HANA o per le condivisioni NFS configurate in una macchina virtuale di Azure (VM). In questo secondo caso, i clienti configurano una macchina virtuale Linux in Azure, collegano Archiviazione di Azure alla macchina virtuale e condividono l'archiviazione tramite un server NFS configurato nella macchina virtuale. Se si esegue il backup sui volumi direttamente collegati alle unità di istanze Large di HANA, copiare i backup a un account di archiviazione di Azure. Eseguire questa operazione dopo aver configurato una macchina virtuale di Azure che esporta le condivisioni NFS basate su archiviazione di Azure. È anche possibile usare un insieme di credenziali di Backup di Azure o l'archiviazione di Azure ad accesso sporadico. 

   Un'altra opzione consiste nell'utilizzare uno strumento di protezione dei dati di terze parti per archiviare i backup dopo che sono stati copiati in un account di archiviazione di Azure. L'opzione di backup manuale potrebbe essere necessaria anche per i dati che si devono archiviare per lunghi periodi di tempo per scopi di controllo e conformità. In tutti i casi, i backup vengono copiati in condivisioni NFS rappresentate tramite una macchina virtuale e Archiviazione di Azure.

- **Infrastruttura di backup e ripristino delle funzionalità.** È anche possibile usare il backup e ripristino delle funzionalità che fornisce l'infrastruttura sottostante di SAP HANA in Azure (istanze Large). Questa opzione soddisfa la necessità di backup e ripristini veloci. La parte restante di questa sezione illustra le funzionalità di backup e ripristino offerte con le istanze Large di HANA. Questa sezione descrive anche la relazione dotati di backup e ripristino di emergenza offerte funzionalità di ripristino per istanze Large di HANA.

> [!NOTE]
>   La tecnologia di snapshot che viene utilizzata dall'infrastruttura sottostante di istanze Large di HANA presenta una dipendenza dagli snapshot SAP HANA. A questo punto, gli snapshot SAP HANA non funzionano in combinazione con più tenant di contenitori di database multi-tenant SAP HANA. Solo se è stato distribuito un solo tenant, gli snapshot SAP HANA funzionano ed è possibile usare questo metodo.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usare gli snapshot di archiviazione di SAP HANA in Azure (istanze Large)

L'infrastruttura di archiviazione sottostante di SAP HANA in Azure (istanze Large) supporta gli snapshot di archiviazione dei volumi. Il backup e il ripristino dei volumi è supportato con le considerazioni seguenti:

- Invece di backup di database completi, vengono creati snapshot dei volumi di archiviazione a intervalli frequenti.
- Quando uno snapshot viene attivato su /hana/data e /hana/shared, che include /usr/sap, volumi, la tecnologia di snapshot avvia uno snapshot prima di SAP HANA viene eseguito lo snapshot di archiviazione. Questo snapshot di SAP HANA è il punto di installazione per i ripristini dei log finali dopo il ripristino dello snapshot di archiviazione. Per uno snapshot HANA abbia esito positivo, è necessario un'istanza HANA attiva. In uno scenario con HSR, uno snapshot di archiviazione non è supportato in un nodo secondario corrente in cui non è possibile eseguire uno snapshot HANA.
- Dopo lo snapshot di archiviazione viene eseguito correttamente, lo snapshot SAP HANA viene eliminato.
- I backup del log delle transazioni vengono creati di frequente e archiviati nel volume /hana/logbackups o in Azure. È possibile attivare il volume /hana/logbackups che contiene i backup del log delle transazioni per creare separatamente uno snapshot. In tal caso, non devi eseguire uno snapshot HANA.
- Se è necessario ripristinare un database a un determinato punto nel tempo, per un'interruzione di produzione, richiesta di supporto di Microsoft Azure o SAP HANA sul ripristino di Azure a uno specifico snapshot di archiviazione. ad esempio per un ripristino pianificato di un sistema sandbox allo stato originale.
- Lo snapshot SAP HANA incluso nello snapshot di archiviazione è un punto di offset per l'applicazione di backup del log delle transazioni eseguiti a cui sono state archiviate dopo che è stato creato lo snapshot di archiviazione.
- Questi backup del log delle transazioni vengono creati per il ripristino temporizzato del database.

È possibile eseguire snapshot di archiviazione che tre classi di volumi di destinazione:

- Snapshot combinato su hana/data e/hana/condiviso, che include /usr/sap. Questo snapshot richiede la creazione di uno snapshot SAP HANA come fase preliminare per lo snapshot di archiviazione. Lo snapshot SAP HANA garantisce che il database è in uno stato coerente dal punto di vista dell'archiviazione. Per il processo di ripristino che è un punto per configurare in.
- Snapshot separato su /hana/logbackups.
- Una partizione del sistema operativo.

Per ottenere gli script degli snapshot e la documentazione più recente, vedere [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Quando si scarica il pacchetto di script di snapshot dalla [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), si ottiene tre file. Uno dei file è documentato in un file PDF per la funzionalità fornita. Dopo aver scaricato il set di strumenti, seguire le istruzioni in "ottenere gli strumenti di snapshot".

## <a name="storage-snapshot-considerations"></a>Considerazioni sugli snapshot di archiviazione

>[!NOTE]
>Gli snapshot di archiviazione usano spazio di archiviazione allocato alle unità di istanze Large di HANA. Prendere in considerazione i seguenti aspetti di pianificazione degli snapshot di archiviazione e quanti snapshot di archiviazione da mantenere. 

I meccanismi specifici degli snapshot di archiviazione per SAP HANA in Azure (istanze Large) comprendono:

- Uno snapshot di archiviazione specifico nel punto in cui viene creato ora consuma spazio limitata.
- Contenuto dei dati cambia e il contenuto nei dati di SAP HANA file modificato nel volume di archiviazione, lo snapshot deve archiviare il contenuto dei blocchi originali e le modifiche ai dati.
- Le dimensioni dello snapshot di archiviazione, quindi, aumentano in proporzione alla durata.
- Quante più modifiche vengono apportate al volume del database SAP HANA per tutta la durata di uno snapshot di archiviazione, tanto più grande diventa lo spazio usato dallo snapshot.

SAP HANA in Azure (istanze Large) viene fornito con volumi di dimensioni fisse per l'archiviazione di dati e log. L'esecuzione di snapshot di questi volumi comporta il consumo dello spazio dei volumi. È necessario:

- Determinare la frequenza di pianificazione degli snapshot di archiviazione.
- Monitorare il consumo dello spazio dei volumi di archiviazione. 
- Gestire il numero di snapshot archiviati. 

È possibile disabilitare gli snapshot di archiviazione quando vengono importate grandi quantità di dati o apportate altre modifiche significative al database HANA. 


Le sezioni seguenti forniscono informazioni per l'esecuzione di questi snapshot e vengono incluse le indicazioni generali:

- Anche se l'hardware può supportare 255 snapshot per volume, è consigliabile rimanere ben di sotto di questo numero. La raccomandazione è minore o uguale a 250.
- Prima di eseguire snapshot di archiviazione, monitorare e tenere traccia dello spazio libero.
- Limitare il numero di snapshot di archiviazione in base allo spazio disponibile. È possibile ridurre il numero di snapshot mantenuti o estendere i volumi. È possibile anche ordinare spazio di archiviazione aggiuntivo in unità da 1 TB.
- Durante particolari attività, come lo spostamento di dati in SAP HANA con gli strumenti di migrazione della piattaforma SAP (R3load) o il ripristino di database SAP HANA dai backup, è necessario disabilitare gli snapshot di archiviazione nel volume /hana/data. 
- Durante la riorganizzazione più grande delle tabelle di SAP HANA, se possibile evitare gli snapshot di archiviazione.
- Gli snapshot di archiviazione sono un prerequisito per trarre vantaggio dalle funzionalità di ripristino di emergenza di SAP HANA in Azure (istanze Large).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Prerequisiti per l'uso di snapshot di archiviazione self-service

Per assicurarsi che lo script di snapshot viene eseguito correttamente, assicurarsi che Perl sia installato nel sistema operativo Linux nel server di istanze Large di HANA. Perl è preinstallato nell'unità di istanze Large di HANA. Per verificare la versione di Perl, usare il comando seguente:

`perl -v`

![La chiave pubblica viene copiata eseguendo questo comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurare gli snapshot di archiviazione

Per configurare gli snapshot di archiviazione con le istanze Large di HANA, seguire questa procedura.
1. Assicurarsi che Perl sia installato nel sistema operativo Linux sul server HANA (istanze Large).
1. Modificare /etc/ssh/ssh\_config in modo da aggiungere la riga _MACs hmac-sha1_.
1. Creare un account utente per il backup di SAP HANA sul nodo master per ogni istanza di SAP HANA che viene eseguito, se applicabile.
1. Installare il client HDB di SAP HANA in tutti i server SAP HANA (istanze Large).
1. Nel primo server SAP HANA (istanze Large) di ogni area, creare una chiave pubblica per accedere all'infrastruttura di archiviazione sottostante che controlla la creazione di snapshot.
1. Copiare gli script e il file di configurazione da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) nel percorso di **hdbsql** dell'installazione di SAP HANA.
1. Modificare il file *HANABackupDetails.txt* nel modo necessario, in base alle specifiche del cliente.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Per i passaggi elencati in precedenza, vedere [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Considerazioni per gli scenari MCOD
Se si esegue un' [scenario MCOD](https://launchpad.support.sap.com/#/notes/1681092) con più istanze di SAP HANA in un'unità di istanze Large di HANA, si dispone di volumi di archiviazione separati sottoposti a provisioning per ognuna delle istanze di SAP HANA. Per altre informazioni su MDC e altre considerazioni, vedere "Informazioni importanti da ricordare" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passaggio 1: Installare il client HDB di SAP HANA

Il sistema operativo Linux installato in SAP HANA in Azure (istanze Large) include le cartelle e gli script necessari per eseguire snapshot di archiviazione SAP HANA backup e ripristino di emergenza a scopo di ripristino. Verificare se sono disponibili versioni più recenti in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). La versione più recente degli script è 4.0. Diversi script potrebbero avere versioni secondarie diverse all'interno della stessa versione principale.

È responsabilità del cliente installare il client HDB di SAP HANA nelle unità di istanze Large di HANA durante l'installazione di SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Passaggio 2: Modificare /etc/ssh/ssh\_config

Questo passaggio è descritto in "Abilitare la comunicazione con archiviazione" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>Passaggio 3: Creare una chiave pubblica

Per abilitare l'accesso alle interfacce degli snapshot di archiviazione del tenant di istanze Large di HANA, definire una procedura di accesso tramite chiave pubblica. 

Nel primo server SAP HANA in Azure (istanze Large) nel tenant, creare una chiave pubblica per accedere all'infrastruttura di archiviazione. Con una chiave pubblica, non è necessaria una password per accedere a interfacce degli snapshot di archiviazione. Inoltre, non è necessario gestire le credenziali di password con una chiave pubblica. 

Per generare una chiave pubblica, vedere "Abilitare la comunicazione con archiviazione" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Passaggio 4: Creare un account utente SAP HANA

Per avviare la creazione di snapshot di SAP HANA, creare un account utente in SAP HANA che è possibile usare gli script degli snapshot di archiviazione. Creare quindi un account utente SAP HANA in SAP HANA Studio. L'utente deve essere creato sotto il SYSTEMDB e *non* presenti nel database di SID per MDC. Nell'ambiente singolo contenitore, l'utente viene creato nel database del tenant. Questo account deve disporre **Backup Admin** e **Catalog Read** privilegi. 

Per configurare e usare un account utente, vedere la sezione "Abilitare la comunicazione con SAP HANA" in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passaggio 5: Autorizzare l'account utente SAP HANA

In questo passaggio si autorizza l'account utente SAP HANA creato in modo che gli script non debbano inviare password in fase di esecuzione. Il comando di SAP HANA `hdbuserstore` consente la creazione di una chiave utente SAP HANA. La chiave viene archiviata in uno o più nodi SAP HANA. La chiave utente consente all'utente di accedere a SAP HANA senza dover gestire le password dall'interno del processo di scripting, descritto più avanti in questo articolo.

>[!IMPORTANT]
>Eseguire questi comandi di configurazione con lo stesso contesto utente in cui vengono eseguiti i comandi di snapshot. In caso contrario, i comandi di snapshot non funzioneranno correttamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passaggio 6: Ottenere gli script degli snapshot, configurare gli snapshot e testare la configurazione e la connettività

Scaricare la versione più recente degli script da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Il modo in cui che sono installati gli script modificato con versione 4.0 di script. Per altre informazioni, vedere "Abilitare la comunicazione con SAP HANA" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Per la sequenza esatta dei comandi, vedere "Installazione semplice di strumenti di snapshot (impostazione predefinita)" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). È consigliabile l'uso dell'installazione predefinita. 

Per eseguire l'aggiornamento dalla versione 3.x a 4.0, vedere "Aggiornare un'installazione esistente" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Per disinstallare il set di 4.0 strumenti, vedere "Disinstallazione degli strumenti di snapshot" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Non dimenticare di eseguire la procedura descritta in "Il programma di installazione completa di strumenti di snapshot" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Lo scopo dei file e script diversi è stato installato è descritta in "Che cosa sono questi strumenti snapshot?" nelle [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Prima di configurare gli strumenti di snapshot, assicurarsi che è configurato anche percorsi di backup di HANA e le impostazioni in modo corretto. Per altre informazioni, vedere "Configurazione di SAP HANA" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

La configurazione del set di strumento di snapshot è descritta in "File di configurazione - hanabackupcustomerdetails. txt" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Testare la connettività con SAP HANA

Dopo aver inserito tutti i dati di configurazione nel file *HANABackupCustomerDetails.txt*, verificare che le configurazioni siano corrette in relazione ai dati delle istanze di HANA. Usare lo script `testHANAConnection`, che è indipendente da una configurazione di SAP HANA con scalabilità orizzontale o verticale.

Per altre informazioni, vedere "Verificare la connettività con SAP HANA - testHANAConnection" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Testare la connettività di archiviazione

È il passaggio successivo per verificare la connettività alla risorsa di archiviazione in base ai dati inseriti nel *hanabackupcustomerdetails. txt* file di configurazione. Quindi eseguire uno snapshot di test. Prima di eseguire il `azure_hana_backup` comando, è necessario eseguire questo test. Per la sequenza di comandi per questo test, vedere "Verificare la connettività con l'archiviazione - testStorageSnapshotConnection" "nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Dopo aver eseguito l'accesso alle interfacce della macchina virtuale di archiviazione, lo script continua con la fase 2 e crea uno snapshot di test. Di seguito è riportato l'output per una configurazione di scalabilità orizzontale di tre nodi di SAP HANA.

Se lo snapshot di test viene eseguito correttamente con lo script, è possibile pianificare gli snapshot di archiviazione effettivo. Se non è riuscita, analizzare i problemi prima di proseguire. Lo snapshot di test deve rimanere disponibile fino a quando non vengono eseguiti i primi snapshot reali.


### <a name="step-7-perform-snapshots"></a>Passaggio 7: Eseguire gli snapshot

Al termine i passaggi di preparazione, è possibile iniziare a configurare e pianificare gli snapshot di archiviazione effettivo. Lo script da pianificare funziona con le configurazioni con scalabilità verticale e con scalabilità orizzontale di SAP HANA. Per l'esecuzione periodica e regolare dello script di backup, pianificare lo script tramite l'utilità cron. 

Per la sintassi del comando esatto e funzionalità, vedere "Backup di snapshot Esegui - azure_hana_backup" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Quando lo script `azure_hana_backup` viene eseguita, crea la snapshot di archiviazione nelle seguenti tre fasi:

1. Viene eseguito uno snapshot SAP HANA.
1. Viene eseguito uno snapshot di archiviazione.
1. Rimuove lo snapshot SAP HANA creato prima è stato eseguito lo snapshot di archiviazione.

Per eseguire lo script, chiamarlo dalla cartella dell'eseguibile HDB in cui è stato copiato. 

Il periodo di conservazione viene gestito in base al numero di snapshot specificato come parametro quando si esegue lo script. La quantità di tempo coperto dagli snapshot di archiviazione è una funzione del periodo di esecuzione e del numero di snapshot specificato come parametro durante l'esecuzione dello script. 

Se il numero di snapshot conservati supera il numero indicato come un parametro nella chiamata dello script, lo snapshot di archiviazione meno recente della stessa etichetta viene eliminato prima dell'esecuzione di un nuovo snapshot. Il numero specificato come ultimo parametro della chiamata corrisponde quindi al numero che è possibile usare per controllare il numero di snapshot conservati. Con questo numero, è anche possibile controllare indirettamente, lo spazio su disco che viene usato per gli snapshot. 


## <a name="snapshot-strategies"></a>Strategie per gli snapshot
Per i vari tipi disponibili, la frequenza degli snapshot varia a seconda che si usi o meno la funzionalità di ripristino di emergenza di HANA in istanze Large. Questa funzionalità è basata sugli snapshot di archiviazione, che possono richiedere l'applicazione di alcune indicazioni speciali in termini di frequenza e periodi di esecuzione degli snapshot di archiviazione. 

Le considerazioni e le indicazioni seguenti presuppongono che *non* venga usata la funzionalità di ripristino di emergenza offerta da HANA in istanze Large. Si usano invece gli snapshot di archiviazione per avere dei backup ed è possibile fornire il ripristino temporizzato per gli ultimi 30 giorni. Considerate le limitazioni del numero di snapshot e di spazio, prendere in considerazione i requisiti seguenti:

- Tempo di ripristino per un ripristino temporizzato.
- Utilizzo dello spazio.
- Obiettivo del punto di ripristino e obiettivo del tempo di ripristino per il possibile ripristino da un'emergenza.
- Eventuale esecuzione di backup completi del database HANA sui dischi. Ogni volta che viene creato un backup completo del database sui dischi o che viene eseguita l'interfaccia **backint**, l'esecuzione di snapshot di archiviazione ha esito negativo. Se si prevede di eseguire backup completi del database oltre agli snapshot di archiviazione, assicurarsi che l'esecuzione degli snapshot di archiviazione sia disabilitata durante questo periodo.
- Il numero massimo di snapshot per volume, che è limitato a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se non si usa la funzionalità di ripristino di emergenza di istanze Large di HANA, il periodo degli snapshot è meno frequente. In questi casi, eseguono snapshot combinati su /hana/data e /hana/shared, che include /usr/sap, in periodi di 12 o 24 ore. Mantenere gli snapshot per un mese. Lo stesso vale per gli snapshot del volume di backup del log. L'esecuzione del backup del log delle transazioni SAP HANA sul volume di backup del log si verifica in 5 minuti a periodi di 15 minuti.

Gli snapshot di archiviazione pianificati vengono eseguiti al meglio usando cron. Usare lo stesso script per tutti i backup e ripristino di emergenza desiderate. Modificare gli input dello script in base ai vari orari di backup richiesti. Questi snapshot sono tutti pianificati in modo diverso in cron, a seconda del tempo di esecuzione. Può essere su base oraria, ogni 12 ore, giornaliera o settimanale. 

L'esempio seguente illustra una pianificazione cron in /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Nell'esempio precedente, uno snapshot orario combinato copre i volumi che contengono /hana/data e /hana/shared/SID, che include /usr/sap, posizioni. Usare questo tipo di snapshot per un recupero temporizzato più rapido entro i due giorni precedenti. È inoltre disponibile uno snapshot sui volumi giornaliero. Si ottengono così due giorni di copertura tramite gli snapshot orari più quattro settimane di copertura tramite gli snapshot giornalieri. Il volume di backup del log delle transazioni viene inoltre eseguito ogni giorno. Questi backup vengono conservati per quattro settimane. 

Come si vedere nella terza riga di crontab, il backup del log delle transazioni HANA è pianificato per eseguire ogni 5 minuti. Le ore di inizio dei diversi processi cron che eseguono snapshot di archiviazione sono scaglionate. In questo modo, gli snapshot non vengono eseguiti tutti contemporaneamente in un determinato punto nel tempo. 

Nell'esempio seguente, si esegue uno snapshot combinato che copre i volumi che contengono /hana/data e /hana/shared/SID, che include /usr/sap, posizioni su base oraria. Questi snapshot vengono conservati per due giorni. Gli snapshot dei volumi di backup del log delle transazioni eseguito ogni 5 minuti e conservati per quattro ore. Come prima, il backup dei file di log delle transazioni HANA è pianificato per eseguire ogni 5 minuti. 

Lo snapshot del volume di backup del log delle transazioni viene eseguito con un ritardo di due minuti dopo l'avvio del backup del log delle transazioni. In circostanze normali, il backup del log delle transazioni SAP HANA viene completata entro i 2 minuti. Come prima, il backup del volume contenente il LUN di avvio viene eseguito una volta al giorno tramite uno snapshot di archiviazione e conservato per quattro settimane.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Il grafico seguente illustra le sequenze dell'esempio precedente. È escluso il LUN di avvio.

![Relazione tra backup e snapshot](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA esegue regolari operazioni di scrittura sul volume /hana/log per documentare le modifiche di cui viene eseguito il commit nel database. A intervalli regolari, SAP HANA scrive un punto di salvataggio nel volume /hana/data. Come specificato in crontab, un backup del log delle transazioni SAP HANA viene eseguito ogni 5 minuti. 

È anche possibile vedere che uno snapshot SAP HANA viene eseguito ogni ora in seguito all'attivazione di una risorsa di archiviazione combinato sui volumi /hana/data e /hana/shared/SID di snapshot. Dopo lo snapshot HANA ha esito positivo, lo spazio di archiviazione combinato di snapshot viene eseguito. Come indicato in crontab, viene eseguito lo snapshot di archiviazione sul volume /hana/logbackup ogni cinque minuti, circa due minuti dopo il backup del log delle transazioni HANA.

> 

>[!IMPORTANT]
> L'uso di snapshot di archiviazione per i backup di SAP HANA è importante solo quando gli snapshot vengono eseguiti insieme ai backup del log delle transazioni SAP HANA. Questi backup del log delle transazioni devono coprire i periodi di tempo che intercorrono tra gli snapshot di archiviazione. 

Se agli utenti si è garantito un ripristino temporizzato di 30 giorni, è necessario che siano soddisfatti i requisiti seguenti:

- Accedere a uno snapshot di archiviazione combinato su /hana/data e /hana/shared/SID che ha 30 giorni, in casi estremi. 
- Predisporre backup del log delle transazioni contigui che coprano il tempo tra tutti gli snapshot di archiviazione combinati. Lo snapshot meno recente del volume di backup del log delle transazioni deve quindi risalire a 30 giorni prima, Ciò non avviene se si copia il backup del log delle transazioni in un'altra condivisione NFS in archiviazione di Azure. In questo caso, è possibile eseguire il pull dei backup del log delle transazioni precedenti dalla condivisione NFS.

Per poter usufruire di snapshot di archiviazione e la replica di archiviazione finale possano comunque dei backup del log delle transazioni, modificare il percorso in cui SAP HANA scrive i backup del log delle transazioni. Questa modifica può essere eseguita in HANA Studio. 

Anche se SAP HANA esegue automaticamente il backup di segmenti di log completi, specificare un intervallo di backup del log per essere deterministiche. Ciò vale soprattutto quando si utilizza l'opzione di ripristino di emergenza, poiché in genere si desidera eseguire il backup del log con un periodo deterministico. Nel caso seguente, 15 minuti è impostato l'intervallo di backup del log.

![Pianificare i log di backup di SAP HANA in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

È anche possibile scegliere backup con frequenza maggiore di 15 minuti. Questo tipo di impostazione con frequenza maggiore viene spesso usato in combinazione con la funzionalità di ripristino di emergenza di HANA in istanze Large. Alcuni clienti eseguono i backup del log delle transazioni ogni cinque minuti.

Se non è mai stato eseguito il backup del database, il passaggio finale consiste nell'eseguire un backup del database basato su file per creare una singola voce di backup nel catalogo di backup. In caso contrario, SAP HANA non è possibile avviare i backup del log specificato.

![Eseguire un backup basato su file per creare una singola voce di backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Dopo l'esecuzione degli snapshot di archiviazione con esito positivo prima, eliminare lo snapshot di test che sono stati eseguiti nel passaggio 6. Per altre informazioni, vedere "Remove test snapshots - removeTestStorageSnapshot" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorare il numero e dimensioni degli snapshot sul volume del disco

In un volume di archiviazione specifico è possibile monitorare il numero di snapshot e l'utilizzo dell'archiviazione degli snapshot. Il comando `ls` non mostra i file e la directory degli snapshot, Il comando del sistema operativo Linux `du` Mostra i dettagli di questi snapshot di archiviazione perché vengono archiviati negli stessi volumi. Usare il comando con le opzioni seguenti:

- `du –sh .snapshot`: fornisce il totale di tutti gli snapshot all'interno della directory degli snapshot.
- `du –sh --max-depth=1`: elenca tutti gli snapshot salvati nella cartella **.snapshot** e le dimensioni di ciascuno.
- `du –hc`: fornisce la dimensione totale usata da tutti gli snapshot.

Usare i comandi seguenti per assicurarsi che gli snapshot vengono creati e archiviati non utilizzino tutte l'archiviazione sui volumi.

>[!NOTE]
>Gli snapshot del LUN di avvio non sono visibili con i comandi precedenti.

### <a name="get-details-of-snapshots"></a>Ottenere i dettagli di snapshot
Per ottenere altri dettagli sugli snapshot, usare lo script `azure_hana_snapshot_details`. È possibile eseguire questo script in ognuna delle posizioni se è presente un server attivo nella posizione di ripristino di emergenza. Lo script fornisce l'output seguente per ogni volume contenente gli snapshot: 
   * Dimensioni degli snapshot totali in un volume
   * Per ogni snapshot nel volume, vengono fornite le informazioni seguenti: 
      - Nome dello snapshot 
      - Ora di creazione 
      - Dimensioni dello snapshot
      - Frequenza dello snapshot
      - ID di backup HANA associato allo snapshot (se pertinente)

Per informazioni sulla sintassi del comando e gli output, vedere "Elencare gli snapshot - azure_hana_snapshot_details" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Ridurre il numero di snapshot in un server

Come descritto in precedenza, è possibile ridurre il numero di snapshot archiviati una determinata etichetta. Gli ultimi due parametri del comando per avviare uno snapshot sono un'etichetta e il numero di snapshot da mantenere.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Nell'esempio precedente, l'etichetta dello snapshot viene **dailyhana**. È il numero di snapshot con questa etichetta da conservare **28**. Dovendo limitare l'uso dello spazio su disco, può essere opportuno ridurre il numero di snapshot archiviati. Un modo semplice per ridurre il numero di snapshot a 15, ad esempio, consiste nell'eseguire lo script con l'ultimo parametro impostato su **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se si esegue lo script con questa impostazione, il numero di snapshot, che include il nuovo snapshot di archiviazione, è 15. Vengono conservati i 15 snapshot più recenti, mentre i 15 snapshot meno recenti vengono eliminati.

 >[!NOTE]
 > Questo script riduce il numero di snapshot solo se sono presenti snapshot più di un'ora prima. Lo script non elimina gli snapshot che hanno meno di un'ora prima. Queste limitazioni sono correlate alla funzionalità di ripristino di emergenza facoltativa offerta.

Se non si desidera mantenere un set di snapshot con il prefisso di backup non è più **dailyhana** negli esempi di sintassi, eseguire lo script con **0** come numero di snapshot. Quindi vengono rimossi tutti gli snapshot che corrispondono a tale etichetta. Rimozione di tutti gli snapshot può influire sulle funzionalità di funzionalità di ripristino di emergenza di istanze Large di HANA.

Una seconda opzione per eliminare snapshot specifici consiste nell'usare lo script `azure_hana_snapshot_delete`. Questo script è progettato per eliminare uno snapshot o un set di snapshot usando l'ID di backup di HANA indicato in HANA Studio o tramite il nome dello snapshot stesso. Attualmente, l'ID di backup è collegato solo agli snapshot creati per il tipo di snapshot **hana**. I backup del tipo di snapshot **registri** e **avvio** non eseguono uno snapshot, SAP HANA, pertanto non presenta alcun ID backup da trovare per questi snapshot. Se viene immesso il nome dello snapshot, vengono cercati tutti gli snapshot corrispondenti al nome immesso sui diversi volumi. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Per altre informazioni sullo script, vedere "Elimina uno snapshot - azure_hana_snapshot_delete" nella [per gli snapshot strumenti Microsoft per SAP HANA in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Eseguire lo script come utente **radice**.

>[!IMPORTANT]
>Se sono presenti solo nello snapshot di dati si intende eliminare, dopo l'eliminazione dello snapshot, che i dati vengono persi per sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Ripristino a livello di file da uno snapshot di archiviazione

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Per i tipi di snapshot **HANA** e **log**, è possibile accedere direttamente agli snapshot nei volumi nella directory **.snapshot**. È presente una sottodirectory per ogni snapshot. Copiare ogni file nello stato di cui che si trovava al momento dello snapshot dalla sottodirectory alla struttura di directory effettiva. 

Nella versione corrente dello script, è presente *alcun* Ripristina script fornito per il ripristino dello snapshot come Self-Service. Ripristino dello snapshot può essere eseguito come parte degli script di ripristino di emergenza self-service nel sito di ripristino di emergenza durante il failover. Per ripristinare uno snapshot desiderato dagli snapshot disponibili esistenti, è necessario contattare il team operativo di Microsoft, aprire una richiesta di servizio.

>[!NOTE]
>Ripristino file singolo non funziona per gli snapshot dell'avvio LUN indipendente del tipo di unità di istanze Large di HANA. Il **snapshot** directory non è esposta nel LUN di avvio. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Ripristino in base allo snapshot HANA più recente

In uno scenario di produzione-down, il processo di ripristino da uno snapshot di archiviazione può essere avviato come imprevisto del cliente con supporto di Microsoft Azure. È una questione di estrema urgenza se i dati sono stati eliminati in un sistema di produzione e l'unico modo per recuperarlo è ripristinare il database di produzione.

In una situazione diversa, con minor urgenza, sarebbe possibile usare un ripristino temporizzato, pianificabile con diversi giorni di anticipo. È possibile pianificare questo ripristino con SAP HANA in Azure anziché segnalare un flag ad alta priorità. Ad esempio, si potrebbe prevede di eseguire l'aggiornamento del software SAP applicando un nuovo enhancement package. e successivamente dover eseguire il ripristino in base a uno snapshot che rappresenta lo stato precedente all'aggiornamento Enhancement Package.

Prima di eseguire la richiesta, è necessario completare alcune operazioni di preparazione. Il team SAP HANA in Azure può quindi gestire la richiesta e fornire i volumi ripristinati. L'utente dovrà quindi ripristinare il database HANA in base agli snapshot.

Per le possibilità per ottenere uno snapshot ripristinato con il nuovo set di strumenti, vedere "Procedura ripristinare uno snapshot" nella [Guida di ripristino manuale di SAP HANA in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Per preparare per la richiesta, seguire questa procedura.

1. Scegliere lo snapshot da ripristinare. Viene ripristinato solo il volume hana/data, se non diversamente specificato. 

1. Arrestare l'istanza HANA.

   ![Arrestare l'istanza HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Smontare i volumi di dati in ogni nodo del database HANA. Se i volumi di dati sono ancora montati nel sistema operativo, il ripristino dello snapshot ha esito negativo.

   ![Smontare i volumi di dati in ogni nodo del database HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Aprire una richiesta di supporto tecnico di Azure e includere le istruzioni per il ripristino di uno snapshot specifico:

   - Durante il ripristino: SAP HANA nel servizio di Azure potrebbe chiedere di partecipare a una conferenza telefonica per coordinare, verificare e confermare che è stato ripristinato lo snapshot di archiviazione corretto. 

   - Dopo il ripristino: SAP HANA nel servizio di Azure si invia una notifica quando viene ripristinato lo snapshot di archiviazione.

1. Al termine del processo di ripristino, montare nuovamente tutti i volumi di dati.

   ![Montare nuovamente tutti i volumi di dati](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Un'altra possibilità per ottenere, ad esempio, recuperati da uno snapshot di archiviazione, i file di dati SAP HANA è documentata nel passaggio 7 [Guida di ripristino manuale di SAP HANA in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Per ripristinare da un backup di snapshot, vedere [Guida di ripristino manuale di SAP HANA in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Se lo snapshot è stato ripristinato da operazioni di Microsoft, non devi eseguire il passaggio 7.


### <a name="recover-to-another-point-in-time"></a>Ripristino in base a un'altra temporizzazione
Per ripristinare un determinato punto nel tempo, vedere la sezione "Ripristinare il database fino al punto seguente nel tempo" in [Guida di ripristino manuale di SAP HANA in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Passaggi successivi
- Visualizzare [principi relativi al ripristino di emergenza e preparazione](hana-concept-preparation.md).

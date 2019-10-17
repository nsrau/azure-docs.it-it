---
title: Backup e ripristino di HANA su SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Come eseguire il backup e il ripristino di HANA su SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430085"
---
# <a name="backup-and-restore"></a>Eseguire backup e ripristino

>[!IMPORTANT]
>Questo articolo non sostituisce la documentazione di amministrazione SAP HANA o le note SAP. Ci si aspetta che l'utente abbia una conoscenza approfondita di e le competenze in SAP HANA amministrazione e operazioni, specialmente per il backup, il ripristino, la disponibilità elevata e il ripristino di emergenza. In questo articolo vengono visualizzate le schermate di SAP HANA Studio. Contenuto, struttura e natura delle schermate degli strumenti di amministrazione SAP e gli strumenti stessi possono variare nelle diverse versioni di SAP HANA.

È importante esercitarsi con i passaggi e i processi adottati nell'ambiente e con le versioni di HANA. Alcuni processi descritti in questo articolo sono semplificati per una migliore comprensione generale. Non sono destinate a essere usate come passaggi dettagliati per i manuali di operazioni finali. Se si desidera creare manuali operativi per le configurazioni, testare e verificare i processi e documentare i processi correlati alle configurazioni specifiche. 

Uno degli aspetti più importanti dei database operativi è la protezione da eventi irreversibili. riconducibili a cause diverse, da disastri naturali a semplici errori umani.

Il backup di un database, con la possibilità di ripristinarlo in qualsiasi punto nel tempo, ad esempio prima dell'eliminazione di dati critici, consente il ripristino a uno stato che sia il più vicino possibile al modo in cui si trovava prima dell'errore.

Per ottenere la funzionalità di ripristino, è necessario eseguire due tipi di backup:

- Backup dei database: completo, incrementale o differenziale
- Backup dei log delle transazioni

Oltre a creare backup di database completi a livello di applicazione, è possibile anche creare backup con snapshot di archiviazione. Gli snapshot di archiviazione non sostituiscono i backup del log delle transazioni. che restano tuttavia importanti per il ripristino temporizzato del database o per svuotare i log dalle transazioni di cui è già stato eseguito il commit. Gli snapshot di archiviazione possono accelerare il ripristino fornendo rapidamente un'immagine di rollforward del database. 

SAP HANA in Azure (istanze Large) offre due opzioni di backup e ripristino:

- **Fai da te (fai da te).** Dopo avere verificato che lo spazio su disco è sufficiente, eseguire backup completi del database e del log usando uno dei metodi di backup su disco seguenti. È possibile eseguire il backup direttamente nei volumi collegati alle unità di istanze large di HANA o in condivisioni NFS configurate in una macchina virtuale (VM) di Azure. In questo secondo caso, i clienti configurano una macchina virtuale Linux in Azure, collegano Archiviazione di Azure alla macchina virtuale e condividono l'archiviazione tramite un server NFS configurato nella macchina virtuale. Se si esegue il backup su volumi collegati direttamente alle unità di istanze large di HANA, copiare i backup in un account di archiviazione di Azure. Eseguire questa operazione dopo aver configurato una macchina virtuale di Azure che esporta le condivisioni NFS basate su archiviazione di Azure. È anche possibile usare un insieme di credenziali di backup di Azure o Azure Cold Storage. 

   Un'altra opzione consiste nell'usare uno strumento di protezione dei dati di terze parti per archiviare i backup dopo che sono stati copiati in un account di archiviazione di Azure. L'opzione di backup DIY potrebbe essere necessaria anche per i dati che è necessario archiviare per periodi di tempo più lunghi a scopo di controllo e conformità. In tutti i casi, i backup vengono copiati in condivisioni NFS rappresentate tramite una macchina virtuale e Archiviazione di Azure.

- **Funzionalità di backup e ripristino dell'infrastruttura.** È anche possibile usare la funzionalità di backup e ripristino fornita dall'infrastruttura sottostante di SAP HANA in Azure (istanze large). Questa opzione soddisfa la necessità di backup e ripristini veloci. La parte restante di questa sezione illustra le funzionalità di backup e ripristino offerte con le istanze Large di HANA. In questa sezione viene inoltre illustrata la relazione tra backup e ripristino e la funzionalità di ripristino di emergenza offerta da HANA in istanze large.

> [!NOTE]
>   La tecnologia snapshot usata dall'infrastruttura sottostante delle istanze large di HANA presenta una dipendenza da snapshot SAP HANA. A questo punto, SAP HANA snapshot non funzionano in combinazione con più tenant di SAP HANA contenitori di database multi-tenant. Se viene distribuito un solo tenant, SAP HANA gli snapshot funzionano ed è possibile usare questo metodo.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usare gli snapshot di archiviazione di SAP HANA in Azure (istanze large)

L'infrastruttura di archiviazione sottostante di SAP HANA in Azure (istanze Large) supporta gli snapshot di archiviazione dei volumi. Il backup e il ripristino dei volumi è supportato con le considerazioni seguenti:

- Invece di backup di database completi, vengono creati snapshot dei volumi di archiviazione a intervalli frequenti.
- Quando uno snapshot viene attivato su/Hana/data e/Hana/Shared, che include/usr/SAP, i volumi, la tecnologia snapshot avvia uno snapshot SAP HANA prima di eseguire lo snapshot di archiviazione. Questo snapshot di SAP HANA è il punto di installazione per i ripristini dei log finali dopo il ripristino dello snapshot di archiviazione. Affinché uno snapshot HANA abbia esito positivo, è necessaria un'istanza di HANA attiva. In uno scenario HSR, uno snapshot di archiviazione non è supportato in un nodo secondario corrente in cui non è possibile eseguire uno snapshot HANA.
- Dopo l'esecuzione corretta dello snapshot di archiviazione, lo snapshot SAP HANA viene eliminato.
- I backup del log delle transazioni vengono creati di frequente e archiviati nel volume /hana/logbackups o in Azure. È possibile attivare il volume /hana/logbackups che contiene i backup del log delle transazioni per creare separatamente uno snapshot. In tal caso, non è necessario eseguire uno snapshot HANA.
- Se è necessario ripristinare un database a un determinato momento, per un'interruzione di produzione richiedere che Microsoft Azure supporto o SAP HANA in Azure Restore in uno snapshot di archiviazione specifico. ad esempio per un ripristino pianificato di un sistema sandbox allo stato originale.
- Lo snapshot SAP HANA incluso nello snapshot di archiviazione è un punto di offset per l'applicazione dei backup del log delle transazioni che sono stati eseguiti e che sono stati archiviati dopo che è stato eseguito lo snapshot di archiviazione.
- Questi backup del log delle transazioni vengono creati per il ripristino temporizzato del database.

È possibile eseguire snapshot di archiviazione destinati a tre classi di volumi:

- Uno snapshot combinato su/Hana/data e/Hana/Shared, che include/usr/SAP. Questo snapshot richiede la creazione di uno snapshot SAP HANA come fase preliminare per lo snapshot di archiviazione. Lo snapshot SAP HANA garantisce che il database sia in uno stato coerente dal punto di vista dell'archiviazione. Per il processo di ripristino, questo è un punto da configurare in.
- Snapshot separato su /hana/logbackups.
- Una partizione del sistema operativo.

Per ottenere gli script e la documentazione di snapshot più recenti, vedere [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Quando si Scarica il pacchetto di script di snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), si ottengono tre file. Uno dei file è documentato in un file PDF per la funzionalità fornita. Dopo aver scaricato il set di strumenti, seguire le istruzioni riportate in "ottenere gli strumenti per gli snapshot".

## <a name="storage-snapshot-considerations"></a>Considerazioni sugli snapshot di archiviazione

>[!NOTE]
>Gli snapshot di archiviazione utilizzano lo spazio di archiviazione allocato alle unità di istanze large di HANA. Considerare i seguenti aspetti della pianificazione degli snapshot di archiviazione e del numero di snapshot di archiviazione da conservare. 

I meccanismi specifici degli snapshot di archiviazione per SAP HANA in Azure (istanze Large) comprendono:

- Uno snapshot di archiviazione specifico nel momento in cui viene utilizzato USA poca quantità di spazio di archiviazione.
- Quando il contenuto dei dati cambia e il contenuto dei file di dati SAP HANA cambia nel volume di archiviazione, lo snapshot deve archiviare il contenuto del blocco originale e i dati cambiano.
- Le dimensioni dello snapshot di archiviazione, quindi, aumentano in proporzione alla durata.
- Quante più modifiche vengono apportate al volume del database SAP HANA per tutta la durata di uno snapshot di archiviazione, tanto più grande diventa lo spazio usato dallo snapshot.

SAP HANA in Azure (istanze Large) viene fornito con volumi di dimensioni fisse per l'archiviazione di dati e log. L'esecuzione di snapshot di questi volumi comporta il consumo dello spazio dei volumi. È necessario:

- Determinare quando pianificare gli snapshot di archiviazione.
- Monitorare l'utilizzo dello spazio dei volumi di archiviazione. 
- Gestire il numero di snapshot archiviati. 

È possibile disabilitare gli snapshot di archiviazione quando vengono importate grandi quantità di dati o apportate altre modifiche significative al database HANA. 


Le sezioni seguenti forniscono informazioni per l'esecuzione di questi snapshot e includono raccomandazioni generali:

- Sebbene l'hardware possa sostenere 255 snapshot per volume, è consigliabile rimanere ben al di sotto di questo numero. La raccomandazione è 250 o meno.
- Prima di eseguire snapshot di archiviazione, monitorare e tenere traccia dello spazio libero.
- Limitare il numero di snapshot di archiviazione in base allo spazio disponibile. È possibile ridurre il numero di snapshot mantenuti o estendere i volumi. È possibile anche ordinare spazio di archiviazione aggiuntivo in unità da 1 TB.
- Durante particolari attività, come lo spostamento di dati in SAP HANA con gli strumenti di migrazione della piattaforma SAP (R3load) o il ripristino di database SAP HANA dai backup, è necessario disabilitare gli snapshot di archiviazione nel volume /hana/data. 
- Durante le riorganizzazioni più grandi delle tabelle SAP HANA, evitare gli snapshot di archiviazione, se possibile.
- Gli snapshot di archiviazione sono un prerequisito per trarre vantaggio dalle funzionalità di ripristino di emergenza di SAP HANA in Azure (istanze Large).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Prerequisiti per l'uso di snapshot di archiviazione self-service

Per assicurarsi che lo script di snapshot venga eseguito correttamente, assicurarsi che Perl sia installato nel sistema operativo Linux nel server di istanze large di HANA. Perl viene preinstallato nell'unità di istanze large di HANA. Per verificare la versione di Perl, usare il comando seguente:

`perl -v`

![La chiave pubblica viene copiata eseguendo questo comando](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Configurare gli snapshot di archiviazione

Per configurare gli snapshot di archiviazione con le istanze large di HANA, seguire questa procedura.
1. Assicurarsi che Perl sia installato nel sistema operativo Linux sul server HANA (istanze Large).
1. Modificare /etc/ssh/ssh\_config in modo da aggiungere la riga _MACs hmac-sha1_.
1. Creare un account SAP HANA backup utente sul nodo master per ogni istanza di SAP HANA eseguita, se applicabile.
1. Installare il client HDB di SAP HANA in tutti i server SAP HANA (istanze Large).
1. Nel primo server SAP HANA (istanze Large) di ogni area, creare una chiave pubblica per accedere all'infrastruttura di archiviazione sottostante che controlla la creazione di snapshot.
1. Copiare gli script e il file di configurazione da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) nel percorso di **hdbsql** dell'installazione di SAP HANA.
1. Modificare il file *HANABackupDetails.txt* nel modo necessario, in base alle specifiche del cliente.

Ottenere gli script e la documentazione più recenti sugli snapshot da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Per i passaggi elencati in precedenza, vedere [Microsoft snapshot Tools per SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Considerazioni per gli scenari MCOD
Se si esegue uno [scenario MCOD](https://launchpad.support.sap.com/#/notes/1681092) con più istanze di SAP Hana in un'unità di istanze large di Hana, è stato effettuato il provisioning di volumi di archiviazione separati per ognuna delle istanze di SAP Hana. Per ulteriori informazioni su MDC e altre considerazioni, vedere "aspetti importanti da ricordare" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Passaggio 1: Installare il client HDB di SAP HANA

Il sistema operativo Linux installato in SAP HANA in Azure (istanze large) include le cartelle e gli script necessari per eseguire SAP HANA snapshot di archiviazione a scopo di backup e ripristino di emergenza. Verificare se sono disponibili versioni più recenti in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

È responsabilità dell'utente installare il client HDB SAP HANA nelle unità di istanze large di HANA durante l'installazione di SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Passaggio 2: Modificare /etc/ssh/ssh\_config

Questo passaggio è descritto in "abilitare la comunicazione con l'archiviazione" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Passaggio 3: Creare una chiave pubblica

Per consentire l'accesso alle interfacce degli snapshot di archiviazione del tenant delle istanze large di HANA, stabilire una procedura di accesso tramite una chiave pubblica. 

Nel primo SAP HANA nel server di Azure (istanze large) nel tenant, creare una chiave pubblica per accedere all'infrastruttura di archiviazione. Con una chiave pubblica, non è necessaria una password per accedere alle interfacce degli snapshot di archiviazione. Non è inoltre necessario mantenere le credenziali della password con una chiave pubblica. 

Per generare una chiave pubblica, vedere "abilitare la comunicazione con l'archiviazione" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Passaggio 4: Creare un account utente SAP HANA

Per avviare la creazione di SAP HANA snapshot, creare un account utente in SAP HANA che gli script di snapshot di archiviazione possano usare. Creare quindi un account utente SAP HANA in SAP HANA Studio. L'utente deve essere creato in SYSTEMDB e *non* nel database SID per MDC. Nell'ambiente del singolo contenitore l'utente viene creato nel database tenant. Questo account deve disporre dei privilegi di **amministratore di backup** e di **lettura del catalogo** . 

Per configurare e usare un account utente, vedere "abilitare la comunicazione con SAP HANA" in [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Passaggio 5: Autorizzare l'account utente SAP HANA

In questo passaggio si autorizza l'account utente SAP HANA creato in modo che gli script non debbano inviare password in fase di esecuzione. Il comando SAP HANA `hdbuserstore` consente la creazione di una chiave utente SAP HANA. La chiave viene archiviata in uno o più nodi SAP HANA. La chiave utente consente all'utente di accedere a SAP HANA senza dover gestire le password dall'interno del processo di scripting, descritto più avanti in questo articolo.

>[!IMPORTANT]
>Eseguire questi comandi di configurazione con lo stesso contesto utente in cui vengono eseguiti i comandi snapshot. In caso contrario, i comandi snapshot non funzioneranno correttamente.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Passaggio 6: Ottenere gli script degli snapshot, configurare gli snapshot e testare la configurazione e la connettività

Scaricare la versione più recente degli script da [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). La modalità di installazione degli script è cambiata con la versione 4,1 degli script. Per ulteriori informazioni, vedere "abilitare la comunicazione con SAP HANA" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Per la sequenza esatta dei comandi, vedere l'argomento relativo all'installazione semplificata di strumenti di snapshot (impostazione predefinita) in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Si consiglia di utilizzare l'installazione predefinita. 

Per eseguire l'aggiornamento dalla versione 3. x alla 4,1, vedere "aggiornare un'installazione esistente" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Per disinstallare il set di strumenti 4,1, vedere la sezione relativa alla disinstallazione degli strumenti di snapshot in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Non dimenticare di eseguire i passaggi descritti in "installazione completa di strumenti snapshot" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Lo scopo dei diversi script e file come sono stati installati è descritto in "quali sono gli strumenti di snapshot?" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Prima di configurare gli strumenti di snapshot, verificare che le impostazioni e i percorsi di backup di HANA siano stati configurati correttamente. Per ulteriori informazioni, vedere "configurazione di SAP HANA" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

La configurazione del set di strumenti snapshot è descritta in "config file-HANABackupCustomerDetails. txt" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Testare la connettività con SAP HANA

Dopo aver inserito tutti i dati di configurazione nel file *HANABackupCustomerDetails.txt*, verificare che le configurazioni siano corrette in relazione ai dati delle istanze di HANA. Usare lo script `testHANAConnection`, che è indipendente da una configurazione di SAP HANA con scalabilità orizzontale o verticale.

Per ulteriori informazioni, vedere "verifica della connettività con SAP HANA-testHANAConnection" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testare la connettività di archiviazione

Il passaggio successivo del test consiste nel verificare la connettività all'archiviazione in base ai dati inseriti nel file di configurazione *HANABackupCustomerDetails. txt* . Quindi, eseguire uno snapshot di test. Prima di eseguire il comando `azure_hana_backup`, è necessario eseguire questo test. Per la sequenza di comandi per questo test, vedere "verifica della connettività con storage-testStorageSnapshotConnection" "in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Dopo aver eseguito l'accesso alle interfacce della macchina virtuale di archiviazione, lo script continua con la fase 2 e crea uno snapshot di test. L'output è riportato di seguito per una configurazione con scalabilità orizzontale a tre nodi di SAP HANA.

Se lo snapshot di test viene eseguito correttamente con lo script, è possibile pianificare gli snapshot di archiviazione effettivi. In caso contrario, analizzare i problemi prima di procedere. Lo snapshot di test deve rimanere disponibile fino a quando non vengono eseguiti i primi snapshot reali.


### <a name="step-7-perform-snapshots"></a>Passaggio 7: Eseguire gli snapshot

Al termine della procedura di preparazione, è possibile iniziare a configurare e pianificare gli snapshot di archiviazione effettivi. Lo script da pianificare funziona con le configurazioni con scalabilità verticale e con scalabilità orizzontale di SAP HANA. Per l'esecuzione periodica e regolare dello script di backup, pianificare lo script tramite l'utilità cron. 

Per la sintassi e la funzionalità del comando esatte, vedere "eseguire il backup di snapshot-azure_hana_backup" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Quando viene eseguito lo script `azure_hana_backup`, viene creato lo snapshot di archiviazione nelle tre fasi seguenti:

1. Esegue uno snapshot SAP HANA.
1. Esegue uno snapshot di archiviazione.
1. Rimuove lo snapshot SAP HANA creato prima dell'esecuzione dello snapshot di archiviazione.

Per eseguire lo script, chiamarlo dalla cartella dell'eseguibile HDB in cui è stato copiato. 

Il periodo di memorizzazione viene gestito con il numero di snapshot inviati come parametro durante l'esecuzione dello script. L'intervallo di tempo coperto dagli snapshot di archiviazione è una funzione del periodo di esecuzione e del numero di snapshot inviati come parametro durante l'esecuzione dello script. 

Se il numero di snapshot conservati supera il numero indicato come parametro nella chiamata dello script, lo snapshot di archiviazione meno recente della stessa etichetta viene eliminato prima dell'esecuzione di un nuovo snapshot. Il numero specificato come ultimo parametro della chiamata corrisponde quindi al numero che è possibile usare per controllare il numero di snapshot conservati. Con questo numero, è anche possibile controllare, indirettamente, lo spazio su disco usato per gli snapshot. 


## <a name="snapshot-strategies"></a>Strategie per gli snapshot
Per i vari tipi disponibili, la frequenza degli snapshot varia a seconda che si usi o meno la funzionalità di ripristino di emergenza di HANA in istanze Large. Questa funzionalità è basata sugli snapshot di archiviazione, che possono richiedere l'applicazione di alcune indicazioni speciali in termini di frequenza e periodi di esecuzione degli snapshot di archiviazione. 

Le considerazioni e le indicazioni seguenti presuppongono che *non* venga usata la funzionalità di ripristino di emergenza offerta da HANA in istanze Large. Si usano invece gli snapshot di archiviazione per avere dei backup ed è possibile fornire il ripristino temporizzato per gli ultimi 30 giorni. Date le limitazioni del numero di snapshot e dello spazio, considerare i requisiti seguenti:

- Tempo di ripristino per un ripristino temporizzato.
- Utilizzo dello spazio.
- Obiettivo del punto di ripristino e obiettivo del tempo di ripristino per il possibile ripristino da un'emergenza.
- Eventuale esecuzione di backup completi del database HANA sui dischi. Ogni volta che viene creato un backup completo del database sui dischi o che viene eseguita l'interfaccia **backint**, l'esecuzione di snapshot di archiviazione ha esito negativo. Se si prevede di eseguire backup completi del database all'inizio degli snapshot di archiviazione, assicurarsi che durante questo periodo l'esecuzione degli snapshot di archiviazione sia disabilitata.
- Il numero di snapshot per volume, limitato a 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Se non si usa la funzionalità di ripristino di emergenza di istanze large di HANA, il periodo di snapshot è meno frequente. In questi casi, eseguire gli snapshot combinati su/Hana/data e/Hana/Shared, che include/usr/SAP, in periodi di 12 o 24 ore. Mantieni gli snapshot per un mese. Lo stesso vale per gli snapshot del volume di backup del log. L'esecuzione di SAP HANA backup del log delle transazioni rispetto al volume di backup del log avviene in periodi da 5 a 15 minuti.

Gli snapshot di archiviazione pianificati vengono eseguiti al meglio usando cron. Usare lo stesso script per tutti i backup e le esigenze di ripristino di emergenza. Modificare gli input dello script in base ai vari orari di backup richiesti. Questi snapshot sono tutti pianificati in modo diverso in cron, a seconda del tempo di esecuzione. Può essere oraria, ogni 12 ore, ogni giorno o ogni settimana. 

Nell'esempio seguente viene illustrata una pianificazione cron in/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Nell'esempio precedente, uno snapshot combinato ogni ora copre i volumi che contengono/Hana/data e/hana/shared/SID, che include/usr/SAP, percorsi. Usare questo tipo di snapshot per un recupero temporizzato più rapido entro i due giorni precedenti. In questi volumi è disponibile anche uno snapshot giornaliero. Sono quindi disponibili due giorni di copertura per snapshot orari più quattro settimane di copertura tramite snapshot giornalieri. Viene inoltre eseguito il backup giornaliero del volume di backup del log delle transazioni. Questi backup vengono conservati per quattro settimane. 

Come è possibile vedere nella terza riga di crontab, il backup del log delle transazioni HANA è pianificato per l'esecuzione ogni 5 minuti. Gli orari di inizio dei diversi processi cron che eseguono snapshot di archiviazione sono scaglionati. In questo modo, gli snapshot non vengono eseguiti tutti contemporaneamente in un determinato momento. 

Nell'esempio seguente viene eseguito uno snapshot combinato che copre i volumi che contengono/Hana/data e/hana/shared/SID, che include/usr/SAP, percorsi su base oraria. Questi snapshot vengono conservati per due giorni. Gli snapshot dei volumi di backup del log delle transazioni vengono eseguiti in base a 5 minuti e vengono conservati per quattro ore. Come in precedenza, il backup del file di log delle transazioni HANA è pianificato per l'esecuzione ogni 5 minuti. 

Lo snapshot del volume di backup del log delle transazioni viene eseguito con un ritardo di due minuti dopo l'avvio del backup del log delle transazioni. In circostanze normali, il backup del log delle transazioni SAP HANA termina entro questi 2 minuti. Come prima, il backup del volume contenente il LUN di avvio viene eseguito una volta al giorno tramite uno snapshot di archiviazione e conservato per quattro settimane.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Il grafico seguente illustra le sequenze dell'esempio precedente. Il LUN di avvio è escluso.

![Relazione tra backup e snapshot](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA esegue regolari operazioni di scrittura sul volume /hana/log per documentare le modifiche di cui viene eseguito il commit nel database. A intervalli regolari, SAP HANA scrive un punto di salvataggio nel volume /hana/data. Come specificato in crontab, un backup del log delle transazioni SAP HANA viene eseguito ogni 5 minuti. 

Si noterà anche che uno snapshot SAP HANA viene eseguito ogni ora in seguito all'attivazione di uno snapshot di archiviazione combinato sui volumi/Hana/data e/hana/shared/SID. Una volta completato lo snapshot HANA, viene eseguito lo snapshot di archiviazione combinato. Come indicato in crontab, lo snapshot di archiviazione nel volume/Hana/Logbackup viene eseguito ogni 5 minuti, circa 2 minuti dopo il backup del log delle transazioni HANA.

> 

>[!IMPORTANT]
> L'uso di snapshot di archiviazione per i backup di SAP HANA è importante solo quando gli snapshot vengono eseguiti insieme ai backup del log delle transazioni SAP HANA. Questi backup del log delle transazioni devono coprire i periodi di tempo che intercorrono tra gli snapshot di archiviazione. 

Se agli utenti si è garantito un ripristino temporizzato di 30 giorni, è necessario che siano soddisfatti i requisiti seguenti:

- Accedere a uno snapshot di archiviazione combinato su/Hana/data e/hana/shared/SID 30 giorni fa, in casi estremi. 
- Predisporre backup del log delle transazioni contigui che coprano il tempo tra tutti gli snapshot di archiviazione combinati. Lo snapshot meno recente del volume di backup del log delle transazioni deve quindi risalire a 30 giorni prima, Questo non avviene se si copiano i backup del log delle transazioni in un'altra condivisione NFS presente in archiviazione di Azure. In questo caso, è possibile eseguire il pull dei backup del log delle transazioni precedenti dalla condivisione NFS.

Per sfruttare i vantaggi degli snapshot di archiviazione e della replica di archiviazione finale dei backup del log delle transazioni, modificare il percorso in cui SAP HANA scrive i backup del log delle transazioni. Questa modifica può essere eseguita in HANA Studio. 

Sebbene SAP HANA Esegui il backup automatico di segmenti di log completi, specificare un intervallo di backup del log deterministico. Ciò vale soprattutto quando si usa l'opzione di ripristino di emergenza perché in genere si desidera eseguire i backup del log con un periodo deterministico. Nel caso seguente, viene impostato 15 minuti come intervallo di backup del log.

![Pianificare i log di backup di SAP HANA in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

È anche possibile scegliere backup più frequenti di ogni 15 minuti. Questo tipo di impostazione con frequenza maggiore viene spesso usato in combinazione con la funzionalità di ripristino di emergenza di HANA in istanze Large. Alcuni clienti eseguono i backup del log delle transazioni ogni cinque minuti.

Se non è mai stato eseguito il backup del database, il passaggio finale consiste nell'eseguire un backup del database basato su file per creare una singola voce di backup nel catalogo di backup. In caso contrario, SAP HANA non può avviare i backup del log specificati.

![Eseguire un backup basato su file per creare una singola voce di backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Al termine dell'esecuzione dei primi snapshot di archiviazione riusciti, eliminare lo snapshot di test eseguito nel passaggio 6. Per ulteriori informazioni, vedere la sezione relativa alla rimozione di snapshot di test-removeTestStorageSnapshot in [Microsoft snapshot Tools per SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorare il numero e le dimensioni degli snapshot nel volume del disco

In un volume di archiviazione specifico è possibile monitorare il numero di snapshot e l'utilizzo dell'archiviazione degli snapshot. Il comando `ls` non mostra i file e la directory degli snapshot, Il comando del sistema operativo Linux `du` Mostra i dettagli relativi agli snapshot di archiviazione perché sono archiviati negli stessi volumi. Usare il comando con le opzioni seguenti:

- `du –sh .snapshot`: fornisce il totale di tutti gli snapshot all'interno della directory degli snapshot.
- `du –sh --max-depth=1`: elenca tutti gli snapshot salvati nella cartella **.snapshot** e le dimensioni di ciascuno.
- `du –hc`: fornisce la dimensione totale usata da tutti gli snapshot.

Usare questi comandi per assicurarsi che gli snapshot che vengono eseguiti e archiviati non utilizzino tutta l'archiviazione sui volumi.

>[!NOTE]
>Gli snapshot del LUN di avvio non sono visibili con i comandi precedenti.

### <a name="get-details-of-snapshots"></a>Ottenere i dettagli degli snapshot
Per ottenere altre informazioni sugli snapshot, usare lo script `azure_hana_snapshot_details`. È possibile eseguire questo script in uno dei due percorsi se è presente un server attivo nella posizione di ripristino di emergenza. Lo script fornisce l'output seguente per ogni volume contenente gli snapshot: 
   * Dimensioni degli snapshot totali in un volume
   * Per ogni snapshot nel volume, vengono fornite le informazioni seguenti: 
      - Nome dello snapshot 
      - Ora di creazione 
      - Dimensioni dello snapshot
      - Frequenza dello snapshot
      - ID di backup HANA associato allo snapshot (se pertinente)

Per la sintassi del comando e degli output, vedere "list Snapshots-azure_hana_snapshot_details" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Ridurre il numero di snapshot in un server

Come spiegato in precedenza, è possibile ridurre il numero di determinate etichette degli snapshot archiviati. Gli ultimi due parametri del comando per avviare uno snapshot sono un'etichetta e il numero di snapshot da mantenere.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Nell'esempio precedente l'etichetta dello snapshot è **dailyhana**. Il numero di snapshot con questa etichetta da mantenere è **28**. Dovendo limitare l'uso dello spazio su disco, può essere opportuno ridurre il numero di snapshot archiviati. Un modo semplice per ridurre il numero di snapshot a 15, ad esempio, consiste nell'eseguire lo script con l'ultimo parametro impostato su **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Se si esegue lo script con questa impostazione, il numero di snapshot, incluso il nuovo snapshot di archiviazione, è 15. Vengono conservati i 15 snapshot più recenti, mentre i 15 snapshot meno recenti vengono eliminati.

 >[!NOTE]
 > Questo script riduce il numero di snapshot solo se sono presenti snapshot più di un'ora precedente. Lo script non elimina gli snapshot che hanno meno di un'ora precedente. Queste limitazioni sono correlate alla funzionalità di ripristino di emergenza facoltativa offerta.

Se non si vuole più mantenere un set di snapshot con il prefisso di backup **dailyhana** negli esempi di sintassi, eseguire lo script con **0** come numero di conservazione. Verranno rimossi tutti gli snapshot che corrispondono a tale etichetta. La rimozione di tutti gli snapshot può influire sulle funzionalità di funzionalità di ripristino di emergenza di istanze large di HANA.

Una seconda opzione per eliminare snapshot specifici consiste nell'usare lo script `azure_hana_snapshot_delete`. Questo script è progettato per eliminare uno snapshot o un set di snapshot usando l'ID di backup di HANA indicato in HANA Studio o tramite il nome dello snapshot stesso. Attualmente, l'ID di backup è collegato solo agli snapshot creati per il tipo di snapshot **hana**. I backup di snapshot dei **log** di tipo e dell' **avvio** non eseguono uno snapshot di SAP Hana, quindi non è disponibile alcun ID di backup per tali snapshot. Se viene immesso il nome dello snapshot, vengono cercati tutti gli snapshot corrispondenti al nome immesso sui diversi volumi. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Per altre informazioni sullo script, vedere "eliminare uno snapshot-azure_hana_snapshot_delete" in [Microsoft snapshot Tools for SAP Hana in Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Eseguire lo script come utente **root**.

>[!IMPORTANT]
>Se sono presenti dati che esistono solo nello snapshot che si intende eliminare, dopo l'eliminazione dello snapshot, i dati andranno persi per sempre.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Ripristino a livello di file da uno snapshot di archiviazione

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Per i tipi di snapshot **HANA** e **log**, è possibile accedere direttamente agli snapshot nei volumi nella directory **.snapshot**. È presente una sottodirectory per ogni snapshot. Copiare ogni file nello stato in cui si trovava al momento dello snapshot da tale sottodirectory nella struttura di directory effettiva. 

Nella versione corrente dello script non è stato fornito *alcuno* script di ripristino per il ripristino dello snapshot come self-service. Il ripristino dello snapshot può essere eseguito come parte degli script di ripristino di emergenza self-service nel sito di ripristino di emergenza durante il failover. Per ripristinare uno snapshot desiderato dagli snapshot disponibili esistenti, è necessario contattare il team operativo Microsoft aprendo una richiesta di servizio.

>[!NOTE]
>Il ripristino di un singolo file non funziona per gli snapshot del LUN di avvio indipendentemente dal tipo di unità di istanze large di HANA. La directory **. snapshot** non è esposta nel LUN di avvio. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Ripristino in base allo snapshot HANA più recente

In uno scenario di produzione, il processo di ripristino da uno snapshot di archiviazione può essere avviato come evento imprevisto del cliente con supporto Microsoft Azure. Si tratta di una situazione molto urgente se i dati sono stati eliminati in un sistema di produzione e l'unico modo per recuperarli è ripristinare il database di produzione.

In una situazione diversa, con minor urgenza, sarebbe possibile usare un ripristino temporizzato, pianificabile con diversi giorni di anticipo. È possibile pianificare questo ripristino con SAP HANA in Azure anziché generare un flag ad alta priorità. Ad esempio, è possibile pianificare l'aggiornamento del software SAP applicando un nuovo pacchetto di miglioramento. e successivamente dover eseguire il ripristino in base a uno snapshot che rappresenta lo stato precedente all'aggiornamento Enhancement Package.

Prima di eseguire la richiesta, è necessario completare alcune operazioni di preparazione. Il SAP HANA del team di Azure può quindi gestire la richiesta e fornire i volumi ripristinati. L'utente dovrà quindi ripristinare il database HANA in base agli snapshot.

Per la possibilità di ripristinare uno snapshot con il nuovo set di strumenti, vedere "come ripristinare uno snapshot" nella [Guida al ripristino manuale per SAP Hana in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Per preparare la richiesta, attenersi alla seguente procedura.

1. Scegliere lo snapshot da ripristinare. Viene ripristinato solo il volume hana/data, se non diversamente specificato. 

1. Arrestare l'istanza HANA.

   ![Arrestare l'istanza HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Smontare i volumi di dati in ogni nodo del database HANA. Se i volumi di dati sono ancora montati nel sistema operativo, il ripristino dello snapshot ha esito negativo.

   ![Smontare i volumi di dati in ogni nodo del database HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Aprire una richiesta di supporto tecnico di Azure e includere le istruzioni sul ripristino di uno snapshot specifico:

   - Durante il ripristino: SAP HANA nel servizio di Azure potrebbe chiedere di partecipare a una conferenza telefonica per coordinare, verificare e confermare che venga ripristinato lo snapshot di archiviazione corretto. 

   - Dopo il ripristino: SAP HANA nel servizio di Azure invia una notifica quando viene ripristinato lo snapshot di archiviazione.

1. Al termine del processo di ripristino, montare nuovamente tutti i volumi di dati.

   ![Montare nuovamente tutti i volumi di dati](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Un'altra possibilità per ottenere, ad esempio, SAP HANA file di dati recuperati da uno snapshot di archiviazione, è documentata nel passaggio 7 nella [Guida al ripristino manuale per SAP Hana in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Per eseguire il ripristino da un backup di snapshot, vedere [Guida al ripristino manuale per SAP Hana in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Se lo snapshot è stato ripristinato da operazioni Microsoft, non è necessario eseguire il passaggio 7.


### <a name="recover-to-another-point-in-time"></a>Ripristino in base a un'altra temporizzazione
Per eseguire il ripristino fino a un determinato punto nel tempo, vedere "ripristinare il database fino al punto nel tempo seguente" nella [Guida al ripristino manuale per SAP Hana in Azure da uno snapshot di archiviazione](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Passaggi successivi
- Vedere [principi e preparazione per il ripristino di emergenza](hana-concept-preparation.md).

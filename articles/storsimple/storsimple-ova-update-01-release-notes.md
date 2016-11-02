<properties 
   pageTitle="Note sulla versione degli aggiornamenti per l'array virtuale StorSimple | Microsoft Azure"
   description="Vengono illustrati i principali problemi in sospeso e le soluzioni per l'array virtuale StorSimple che esegue l'Aggiornamento 0.2 e 0.1."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/16/2016"
   ms.author="alkohli" />

# Note sulla versione dell'Aggiornamento 0.2 e 0.1 per l'array virtuale StorSimple

## Panoramica

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple. L'array virtuale di Microsoft Azure StorSimple è noto anche come dispositivo virtuale locale StorSimple o dispositivo virtuale StorSimple.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il dispositivo virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'Aggiornamento 0.2 corrisponde alla versione del software **10.0.10280.0**; l'Aggiornamento 0.1 corrisponde invece alla versione **10.0.10279.0**. Nelle sezioni seguenti sono elencate le modifiche per ogni aggiornamento.

> [AZURE.NOTE] Gli aggiornamenti comportano il riavvio del dispositivo. Se sono in corso operazioni di I/O, il dispositivo va incontro a tempi di inattività.

## Problemi risolti nell'Aggiornamento 0.2
L'Aggiornamento 0.2 include tutte le modifiche introdotte dall'Aggiornamento 0.1, con in più la correzione descritta nella tabella seguente:

Funzionalità | Problema |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Aggiornamenti | Nell'ultima versione gli aggiornamenti non venivano rilevati automaticamente nel portale di Azure classico, pertanto per installarli era necessario utilizzare l'interfaccia utente Web locale. Tale problema è stato corretto in questa versione. Installando l'Aggiornamento 0.2, è possibile installare gli aggiornamenti futuri tramite il portale di Azure classico.                       

## Novità dell'aggiornamento 0.1

L'aggiornamento 0.1 contiene le correzioni di bug e i miglioramenti seguenti.

- **Miglioramento della resilienza per le interruzioni del cloud**: questa versione include diverse correzioni di bug per il ripristino di emergenza, il backup e la suddivisione in livelli in caso di interruzione della connettività al cloud. 

- **Miglioramento delle prestazioni di ripristino**: questa versione include correzioni di bug che riducono significativamente il tempo necessario per il completamento dei processi di ripristino.

- **Ottimizzazione del recupero automatizzato dello spazio**: quando vengono eliminati dati in volumi con thin provisioning, i blocchi di archiviazione non usati devono essere recuperati. Questa versione ha migliorato il processo di recupero dello spazio dal cloud, in modo che lo spazio non usato risulti disponibile con maggiore rapidità rispetto alle versioni precedenti.

- **Nuove immagini di dischi virtuali**: sono ora disponibili nuovi file VHD, VHDX e VMDK tramite il portale di Azure classico. È possibile scaricare queste immagini per eseguire il provisioning dei nuovi dispositivi con aggiornamento 0.1.

- **Miglioramento della precisione dello stato dei processi nel portale**: nella versione precedente del software, la segnalazione dello stato del processo nel portale non era granulare. Questo problema è stato risolto in questa versione.

- **Esperienza di aggiunta al dominio**: correzione di bug relativi all'aggiunta al dominio e alla ridenominazione del dispositivo.


## Problemi risolti nell'aggiornamento 0.1

La tabella seguente fornisce un riepilogo dei problemi risolti in questa versione.

| No. | Funzionalità | Problema |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | VMDK | In alcune versioni di VMware, il disco del sistema operativo era considerato come sparse che generava avvisi e interrompeva le normali operazioni. Questo bug è stato risolto in questa versione. |
| 2 | Server iSCSI | Nell'ultima versione, all'utente veniva richiesto di specificare un gateway per ogni interfaccia di rete abilitata del dispositivo virtuale StorSimple. Questo comportamento è stato modificato in questa versione in modo che l'utente debba configurare almeno un gateway per tutte le interfacce di rete abilitate. |
| 3 | Pacchetto di supporto | Nella versione precedente del software, la raccolta dei pacchetti di supporto aveva esito negativo se le dimensioni del pacchetto superavano 1 GB. Tale problema è stato corretto in questa versione. |
| 4 | Accesso al cloud | Nell'ultima versione, quando l'array virtuale StorSimple non disponeva della connettività di rete e veniva riavviato, l'interfaccia utente locale evidenziava problemi di connettività. Questo problema è stato risolto in questa versione. |
| 5 | Grafici di monitoraggio | Nella versione precedente, dopo un failover del dispositivo, i grafici di utilizzo della capacità cloud visualizzavano valori non corretti nel portale di Azure classico. Questo problema è stato risolto nella versione corrente. |



## Problemi noti nell'aggiornamento 0.1

La tabella seguente fornisce un riepilogo dei problemi noti per l'array virtuale StorSimple e include i problemi delle versioni precedenti. **I problemi della versione corrente sono contrassegnati con un asterisco. Quasi tutti i problemi descritti in questo elenco sono riportati dalla versione di disponibilità generale dell'array virtuale StorSimple.**


| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Aggiornamenti | È impossibile aggiornare i dispositivi virtuali creati nella versione di anteprima in una versione supportata di disponibilità generale. | Questi dispositivi virtuali devono essere sottoposti a failover per la versione di disponibilità generale tramite un flusso di lavoro di ripristino di emergenza. |
| **2.** | Disco dati sottoposto a provisioning | Dopo il provisioning di un disco dati di una determinata dimensione specificata e la creazione di un dispositivo virtuale StorSimple corrispondente, non si deve espandere o compattare il disco dati. Il tentativo di eseguire questa operazione comportare la perdita di tutti i dati nei livelli locali del dispositivo. | |
| **3.** | Criteri di gruppo | Nei dispositivi aggiunti a un dominio, l'applicazione di criteri di gruppo può influire negativamente sul funzionamento dei dispositivi. | Assicurarsi che l'array virtuale sia nella propria unità organizzativa (OU) per Active Directory e che ad esso non siano applicati oggetti Criteri di gruppo (GPO).|
| **4.** | Interfaccia utente Web locale | Se sono abilitate le funzionalità di sicurezza avanzate di Internet Explorer, alcune pagine dell'interfaccia utente Web locale come Risoluzione dei problemi o Manutenzione potrebbero non funzionare correttamente. Anche i pulsanti di queste pagine potrebbero non funzionare. | Disattivare le funzionalità di protezione avanzata di Internet Explorer.|
| **5.** | Interfaccia utente Web locale | In una macchina virtuale Hyper-V, le interfacce di rete nell'interfaccia utente Web vengono visualizzate come interfacce da 10 Gbps. | Questo comportamento è una reflection di Hyper-V. Hyper-V visualizza sempre 10 Gbps per le schede di rete virtuale. |
| **6.** | Volumi o condivisioni a livelli | Il blocco dell'intervallo di byte per le applicazioni che operano con i volumi a livelli di StorSimple non è supportato. Se il blocco dell'intervallo di byte è abilitato, la suddivisione in livelli di StorSimple non funziona. | Tra le misure consigliate: <br></br>Disabilitare il blocco dell'intervallo di byte nella logica dell'applicazione.<br></br>Scegliere di inserire i dati per l'applicazione in volumi aggiunti in locale anziché in volumi a livelli.<br></br>*Avvertenza*: se l'uso di volumi aggiunti in locale e il blocco dell'intervallo di byte sono abilitati, il volume aggiunto in locale può essere online anche prima del completamento del ripristino. In questi casi, se è in corso un ripristino, è necessario attenderne il completamento. |
| **7.** | Condivisioni a livelli | Lavorare con file di grandi dimensioni può comportare una suddivisione in livelli lenta. | Durante l'uso di file di grandi dimensioni, è consigliabile che il file più grande sia più piccolo del 3% rispetto alle dimensioni della condivisione. |
| **8.** | Capacità usata per le condivisioni | È possibile riscontrare il consumo di condivisioni in assenza di dati nella condivisione. Questo accade perché la capacità usata per le condivisioni include i metadati. | |
| **9.** | Ripristino di emergenza | È possibile eseguire solo il ripristino di emergenza di un file server nello stesso dominio del dispositivo di origine. Il ripristino di emergenza su un dispositivo di destinazione in un dominio diverso non è supportato in questa versione. | Questo aspetto sarà implementato in una versione successiva. |
| **10.** | Azure PowerShell | I dispositivi virtuali StorSimple non possono essere gestiti tramite Azure PowerShell in questa versione. | La gestione dei dispositivi virtuali deve essere eseguita tramite il portale di Azure classico e l'interfaccia utente Web locale. |
| **11.** | Modifica della password | La console del dispositivo array virtuale accetta solo input nel formato della tastiera inglese (Stati Uniti). | |
| **12.** | CHAP | Le credenziali di autenticazione CHAP create non possono essere rimosse. Se si modificano le credenziali di autenticazione CHAP, per rendere effettive le modifiche è necessario portare i volumi offline e quindi riportarli online. | Questi problemi verranno risolti in una versione successiva. |
| **13.** | Server iSCSI | Le risorse di archiviazione usate visualizzate per un volume iSCSI possono essere diverse nel servizio StorSimple Manager e nell'host iSCSI. | L'host iSCSI ha la visualizzazione del file system.<br></br>Il dispositivo vede i blocchi allocati quando il volume aveva raggiunto le dimensioni massime.|
| **14.** | File server* | Se un file contenuto in una cartella è associato a un flusso di dati alternativi (ADS), sul flusso non viene eseguito il backup o il ripristino tramite ripristino di emergenza, clonazione e ripristino a livello di elemento.| |


## Passaggio successivo

[Installare aggiornamenti](storsimple-ova-install-update-01.md) nell'array virtuale StorSimple

<!---HONumber=AcomDC_0622_2016-->
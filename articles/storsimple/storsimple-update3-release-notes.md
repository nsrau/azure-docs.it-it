<properties 
   pageTitle="Note sulla versione dell'aggiornamento 3 di StorSimple serie 8000 | Microsoft Azure"
   description="Vengono descritte le nuove funzionalità, i problemi e le soluzioni alternative dell'aggiornamento 3 per StorSimple serie 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />  
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/06/2016"
   ms.author="alkohli" />  

# Note sulla versione dell'aggiornamento 3 di StorSimple serie 8000  

## Overview

Nelle note sulla versione seguenti vengono descritte le nuove funzionalità e sono indicati i problemi critici non risolti relativi all'aggiornamento 3 di StorSimple serie 8000. Contengono inoltre un elenco degli aggiornamenti software di StorSimple inclusi in questa versione.

L'aggiornamento 3 può essere applicato a qualsiasi dispositivo StorSimple che esegue la versione in disponibilità generale del software o una versione di aggiornamento compresa tra 0.1 e 2.2. La versione del dispositivo associata all'aggiornamento 3 è 6.3.9600.17759.

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.

>[AZURE.IMPORTANT]
> 
> - L'aggiornamento 3 include software per dispositivi, firmware e driver LSI e aggiornamenti Storport e Spaceport. L'installazione dell'aggiornamento richiede circa 1,5-2 ore.

> - Per le nuove versioni è possibile che gli aggiornamenti non siano immediatamente visibili perché viene effettuata un'implementazione graduale degli aggiornamenti. Attendere alcuni giorni e quindi provare a cercare nuovamente gli aggiornamenti, perché verranno presto resi disponibili.


## Novità dell'aggiornamento 3

L'aggiornamento 3 include gli importanti aggiornamenti e correzioni di bug descritti di seguito.

 
- **Modifiche al recupero dello spazio automatico**: a partire dall'aggiornamento 3, gli algoritmi di recupero dello spazio vengono eseguiti nel controller in standby del sistema, con il risultato di un'esecuzione più rapida. Per ulteriori informazioni sulle porte necessarie per il recupero dello spazio, vedere i [requisiti di rete di StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

- **Miglioramenti delle prestazioni**: l'aggiornamento 3 ha migliorato le prestazioni di lettura e scrittura nel cloud.

- **Miglioramenti relativi alla migrazione**: in questa versione sono stati corretti svariati bug e introdotti diversi miglioramenti nella funzionalità di migrazione da dispositivi della serie 5000/7000 a dispositivi della serie 8000. Per altre informazioni su come usare la funzionalità di migrazione, vedere l'articolo sulla [migrazione da dispositivi della serie 5000/7000 a dispositivi della serie 8000](https://www.microsoft.com/it-IT/download/details.aspx?id=47322).

- **Correzioni relative al monitoraggio**: in questa versione sono stati risolti i bug correlati a grafici di monitoraggio, dashboard del servizio e dashboard del dispositivo.


## Problemi risolti nell'aggiornamento 3

Le tabelle seguenti offrono un riepilogo dei problemi risolti nell'aggiornamento 3.

| No | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1 | Migrazione dei dati lato host | Nella versione precedente l'appliance cloud StorSimple si disconnetteva durante la migrazione dei dati sul lato host. Tale problema è stato corretto in questa versione. | No | Sì |
| 2 | Volumi aggiunti in locale | Nella versione precedente i volumi aggiunti in locale presentavano problemi correlati a errori di I/O, di conversione dei volumi e di percorso dati. In questa versione i problemi sono stati corretti una volta individuata la causa radice. | Sì | No |
| 3 | Monitoraggio | Erano presenti svariati problemi relativi alle unità gerarchiche e al monitoraggio; nei grafici relativi ai dashboard dispositivo erano inoltre visualizzate informazioni errate sui volumi aggiunti in locale. Tali problemi sono stati risolti in questa versione. | Sì | No |
| 4 | I/O a scrittura intensiva | Durante l'uso di StorSimple per carichi di lavoro con operazioni di scrittura intensiva, si presentava un bug insolito in cui il working set veniva suddiviso in livelli nel cloud. Tale bug è stato risolto in questa versione. | Sì | Sì |
| 5 | Backup | In rari casi, nelle versioni precedenti del software, quando l'utente eseguiva il backup di un clone remoto, si verificavano errori nel cloud e l'operazione non riusciva. In questa versione il problema è stato risolto e l'operazione viene completata correttamente. | Sì | Sì |
| 6 | Criterio di backup | In rari casi, nelle versioni precedenti del software, era presente un bug relativo all'eliminazione dei criteri di backup. Tale problema è stato corretto in questa versione. | Sì | Sì |



## Problemi noti nell'aggiornamento 3

Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.

| di serie | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum disco | In rari casi, se la maggior parte dei dischi nello chassis EBOD di un dispositivo 8600 è disconnessa generando un'assenza di quorum disco, il pool di archiviazione sarà offline. Il pool rimarrà in tale stato anche se i dischi vengono riconnessi. | Sarà necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft per i passaggi successivi. | Sì | No |
| 2 | ID controller non corretto | Quando viene eseguita la sostituzione di un controller, il controller 0 potrebbe essere visualizzato come controller 1. Durante la sostituzione del controller, quando l'immagine viene caricata dal nodo peer, l'ID del controller può presentarsi inizialmente come ID del controller peer. In rari casi, questo comportamento può verificarsi anche dopo un riavvio del sistema. | Non è necessaria alcuna azione da parte dell’utente. Questa situazione si risolverà dopo la sostituzione del controller. | Sì | No |
| 3 | Account di archiviazione | L’utilizzo del servizio di archiviazione per eliminare l'account di archiviazione non è supportato. Tale operazione causerebbe una situazione in cui non è possibile recuperare i dati dell'utente.| | Sì | Sì |
| 4 | Failover del dispositivo | I failover multipli di un contenitore di volumi dallo stesso dispositivo di origine verso dispositivi di destinazione diversi non sono supportati. Il failover da un singolo dispositivo inattivo a più dispositivi causerà la perdita della proprietà dei dati dei contenitori di volumi sul primo dispositivo sottoposto a failover. Dopo un tale failover, questi contenitori di volumi appariranno o si comporteranno in maniera diversa quando vengono visualizzati nel portale di Azure classico. | | Sì | No |
| 5 | Installare | Durante l’installazione dell'adattatore StorSimple per SharePoint è necessario fornire un IP del dispositivo affinché l'installazione possa essere completata correttamente. | | Sì | No |
| 6 | Proxy Web | Se nella configurazione del proxy Web è specificato il protocollo HTTPS, la comunicazione tra dispositivo e servizio ne sarà interessata e il dispositivo verrà portato offline. Nel processo, inoltre, verranno generati pacchetti di supporto, consumando risorse significative sul dispositivo. | Assicurarsi che l'URL del proxy Web abbia HTTP come protocollo specificato. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
| 7 | Proxy Web | Se si configura e si abilita il proxy Web su un dispositivo registrato, è necessario riavviare il controller attivo sul dispositivo. | | Sì | No |
| 8 | Elevata latenza del cloud ed elevato carico di lavoro I/O | Quando il dispositivo StorSimple rileva una combinazione di latenze cloud molto elevate (nell’ordine di secondi) e carico di lavoro I/O elevato, i volumi del dispositivo entrano in uno stato con funzionalità ridotte e gli I/O potrebbero non riuscire a causa di un errore di "dispositivo non pronto". | In questo caso è necessario riavviare manualmente i controller del dispositivo o eseguire un failover del dispositivo per risolvere il problema. | Sì | No |
| 9 | Azure PowerShell | Quando si utilizza il cmdlet di StorSimple **Get-AzureStorSimpleStorageAccountCredential | Select-Object -First 1 -Wait** per selezionare il primo oggetto in modo tale da poterne creare uno nuovo **VolumeContainer**, il cmdlet restituisce tutti gli oggetti. | Mettere il cmdlet tra parentesi nel modo seguente: **(Get-Azure-StorSimpleStorageAccountCredential) | Select-Object -First 1 -Wait** | Sì | Sì |
| 10| Migrazione | Quando, per la migrazione, vengono passati più contenitori del volume, ETA per il backup più recente è accurato solo per il primo contenitore del volume. Inoltre, la migrazione parallela verrà avviata dopo la migrazione dei primi quattro backup nel primo contenitore del volume. | Si consiglia di migrare un contenitore del volume alla volta. | Sì | No |
| 11| Migrazione | Dopo il ripristino, i volumi non vengono aggiunti ai criteri di backup o al gruppo di dischi virtuali. | È necessario aggiungerli a un criterio di backup per creare i backup. | Sì | Sì |
| 12| Migrazione | Al termine della migrazione, il dispositivo di serie 5000/7000 non deve accedere ai contenitori di dati migrati. | Dopo il completamento e l'approvazione della migrazione, è consigliabile eliminare i contenitori di dati migrati. | Sì | No |
| 13| Clonazione e ripristino di emergenza | Un dispositivo StorSimple che esegue l'aggiornamento 1 non può clonare o eseguire un ripristino di emergenza su un dispositivo che esegue software precedente a tale aggiornamento. | È necessario aggiornare il dispositivo di destinazione all'aggiornamento 1 per consentire queste operazioni | Sì | Sì |
| 14 | Migrazione | La configurazione del backup per la migrazione potrebbe non riuscire in un dispositivo di serie 5000-7000 quando sono presenti gruppi di volumi senza volumi associati. | Eliminare tutti i gruppi di volumi vuoti senza volumi associati e poi tentare nuovamente il backup della configurazione.| Sì | No |
| 15 | Cmdlet di Azure PowerShell e volumi aggiunti in locale | Non è possibile creare un volume aggiunto in locale tramite i cmdlet di Azure PowerShell. I volumi creati tramite Azure PowerShell verranno suddivisi in livelli. |Usare sempre il servizio StorSimple Manager per configurare i volumi aggiunti in locale.| Sì | No |
| 16 |Spazio disponibile per i volumi aggiunti in locale | Se si elimina un volume aggiunto in locale, lo spazio disponibile per i nuovi volumi potrebbe non essere immediatamente aggiornato. Il servizio StorSimple Manager aggiorna lo spazio locale disponibile circa ogni ora.| Attendere un'ora prima di creare il nuovo volume. | Sì | No |
| 17 | Volumi aggiunti in locale | Il processo di ripristino espone il backup dell'istantanea temporaneo in Catalogo Backup, ma solo per la durata del processo di ripristino. Espone inoltre un gruppo di dischi virtuali con prefisso **tmpCollection** nella pagina **Criteri di backup**, ma solo per la durata del processo di ripristino. | Questo comportamento può verificarsi se il processo di ripristino dispone solo di volumi associati in locale o di una combinazione di volumi associati in locale e a livelli. Se il processo di ripristino include solo i volumi a livelli, questo comportamento non si verificherà. Non è necessario alcun intervento dell'utente. | Sì | No |
| 18 | Volumi aggiunti in locale | Se si annulla un processo di ripristino e si verifica un failover del controller subito dopo, il processo di ripristino visualizzerà lo stato **Non riuscito** anziché **Annullato**. Se un processo di ripristino ha esito negativo e si verifica un failover del controller subito dopo, il processo di ripristino visualizzerà lo stato **Annullato** anziché **Non riuscito**. | Questo comportamento può verificarsi se il processo di ripristino dispone solo di volumi associati in locale o di una combinazione di volumi associati in locale e a livelli. Se il processo di ripristino include solo i volumi a livelli, questo comportamento non si verificherà. Non è necessario alcun intervento dell'utente. | Sì | No |
| 19 |Volumi aggiunti in locale | Se si annulla un processo di ripristino o se un ripristino ha esito negativo e quindi si verifica un failover del controller, viene visualizzato un processo di ripristino aggiuntivo nella pagina **Processi**. | Questo comportamento può verificarsi se il processo di ripristino dispone solo di volumi associati in locale o di una combinazione di volumi associati in locale e a livelli. Se il processo di ripristino include solo i volumi a livelli, questo comportamento non si verificherà. Non è necessario alcun intervento dell'utente. | Sì | No |
| 20 |Volumi aggiunti in locale | Se si cerca di convertire un volume a livelli (creato e clonato con l'aggiornamento 1.2 o precedente) in un volume aggiunto in locale e il dispositivo sta esaurendo lo spazio o si verifica un'interruzione del cloud, i cloni possono risultare danneggiati.| Questo problema si verifica solo con i volumi che sono stati creati e clonati con software precedente all'aggiornamento 2.1. Si tratta di uno scenario poco frequente.|
| 21 | Conversione del volume | Non aggiornare i record di controllo di accesso collegati a un volume mentre è in corso una conversione del volume (da volume a livelli a volume aggiunto in locale o viceversa). L'aggiornamento dei record di controllo di accesso potrebbe causare il danneggiamento dei dati. | Se necessario, aggiornare i record di controllo di accesso prima della conversione del volume e non eseguire altri aggiornamenti dei record di controllo di accesso mentre la conversione è in corso. |


## Aggiornamenti firmware e controller presenti nell'aggiornamento 3

Questa versione dispone degli aggiornamenti del firmware e del driver LSI. Per altre informazioni su come installare gli aggiornamenti per firmware e driver LSI, vedere l'articolo sull'[installazione dell'aggiornamento 3](storsimple-install-update-3.md) nel dispositivo StorSimple.

 
## Aggiornamenti del dispositivo virtuale nell'aggiornamento 3

Questo aggiornamento non può essere applicato all'appliance cloud StorSimple (ovvero il dispositivo virtuale). Sarà necessario creare nuovi dispositivi virtuali.


## Passaggio successivo

Informazioni su come [installare l'aggiornamento 3](storsimple-install-update-3.md) nel dispositivo StorSimple.

<!---HONumber=AcomDC_0914_2016-->
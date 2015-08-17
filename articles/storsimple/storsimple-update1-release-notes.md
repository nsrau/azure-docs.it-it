<properties 
    pageTitle="Note sulla versione dell'aggiornamento 1 di StorSimple serie 8000"
    description="Vengono descritte le nuove funzionalità, i problemi e le soluzioni alternative dell'aggiornamento 1 per StorSimple serie 8000."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="adinah"
    editor="tysonn" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="07/15/2015"
    ms.author="v-sharos" />

# Note sulla versione dell'aggiornamento 1 di StorSimple serie 8000  

## Panoramica

Nelle note sulla versione seguenti vengono descritte le nuove funzionalità e sono indicati i problemi critici non risolti relativi all'aggiornamento 1 di StorSimple serie 8000. Contengono inoltre un elenco degli aggiornamenti software e firmware di StorSimple inclusi in questa versione. Si tratta della prima versione principale dopo la versione di rilascio di StorSimple serie 8000 resa disponibile a livello generale a luglio 2014.

Questo aggiornamento modifica il software del dispositivo all'aggiornamento 1 di StorSimple serie 8000. Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple. Per altre informazioni, vedere la procedura [Installazione dell'aggiornamento 1 sul dispositivo StorSimple](storsimple-install-update-1.md).

Prima di distribuire gli aggiornamenti nella soluzione StorSimple, esaminare le informazioni contenute nelle note sulla versione.

>[AZURE.IMPORTANT]
> 
- È stata rilasciata una patch critica, aggiornamento 1.1, il 23 giugno. Questa patch risolve un problema nel motore di backup. Se è stato applicato l'aggiornamento 1 prima del 23 giugno e si usa attualmente la versione del software **6.3.9600.17491**, assicurarsi di applicare questo aggiornamento critico per evitare eventuali problemi con i backup. Dopo aver installato l'aggiornamento, la versione del software diventerà **6.3.9600.17521**.
- Se è stato creato un dispositivo virtuale tra il 27 maggio e il 10 luglio nella versione del software 6.3.9600.17481, creare un nuovo dispositivo virtuale ed eseguire il failover di tutti i volumi del dispositivo virtuale precedente a quello nuovo. (Questo perché non è possibile aggiornare il dispositivo virtuale precedente). Se non si crea un nuovo dispositivo virtuale, è possibile che i backup non si avviino. Per procedure di ripristino di emergenza e failover, visitare[Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).
- Per installare l'aggiornamento 1, usare il servizio StorSimple Manager e non Windows PowerShell per StorSimple.
- Questa versione include, inoltre, gli aggiornamenti firmware del disco che possono essere applicati solo quando il dispositivo è in modalità di manutenzione. Si tratta di aggiornamenti improvvisi che causano un tempo di inattività del dispositivo. È possibile applicare questi aggiornamenti durante la manutenzione pianificata.
- Sono necessarie circa 5-10 ore per installare questo aggiornamento (inclusi gli aggiornamenti di Windows). 
- Per le nuove versioni è possibile che gli aggiornamenti non siano immediatamente visibili perché viene effettuata un'implementazione graduale degli aggiornamenti. Provare a cercare nuovamente gli aggiornamenti dopo qualche giorno perché verranno presto resi disponibili.

## Novità dell'aggiornamento 1

L'aggiornamento contiene i seguenti nuovi miglioramenti e funzionalità:

- **Migrazione da dispositivi di serie 5000-7000 alla serie 8000** - Questa versione introduce una nuova funzionalità di migrazione che consente all'utente del dispositivo StorSimple serie 5000-7000 di migrare i dati a un dispositivo fisico StorSimple serie 8000 o a un'appliance virtuale 1100. La funzionalità di migrazione dispone di due proposte di valore principali:                                                                  

    - **Continuità aziendale**, attivando la migrazione dei dati esistenti nei dispositivi di serie 5000-7000 ai dispositivi di serie 8000.
    - **Miglioramenti delle funzionalità offerte dalle appliance serie 8000**, come la gestione centralizzata efficiente di più appliance tramite il servizio StorSimple Manager, migliore classe di hardware e aggiornamento di firmware, appliance virtuali, mobilità dei dati e funzionalità nei piani d’azione futuri.

    Consultare la [Guida alla migrazione](http://www.microsoft.com/download/details.aspx?id=47322) per dettagli su come eseguire la migrazione di un dispositivo StorSimple serie 5000-7000 a un dispositivo di serie 8000.

- **Disponibilità nel portale di Azure Government** – StorSimple è ora disponibile nel portale di Azure Government. Vedere la procedura [Distribuire un dispositivo StorSimple nel portale di Azure Government](storsimple-deployment-walkthrough-gov.md).

- **Supporto per altri provider di servizi cloud** – Altri provider di servizi cloud supportati sono Amazon S3, Amazon S3 con RRS, HP e OpenStack (beta).

- **Aggiornamento alle API di archiviazione più recenti** - Con questa versione, StorSimple è stato aggiornato con le API del servizio di archiviazione di Azure più recenti. I dispositivi StorSimple serie 8000 che eseguono GA, utilizzano versioni delle API del servizio di archiviazione di Azure precedenti al 12 febbraio 2012. Come indicato nell’[annuncio sulla rimozione delle versioni del servizio di archiviazione](http://azure.microsoft.com/blog/2014/08/04/microsoft-azure-storage-service-version-removal/), entro il 10 dicembre 2015 tali API saranno deprecate. È necessario applicare l'aggiornamento 1 di StorSimple serie 8000 prima del 9 dicembre 2015. In caso contrario, i dispositivi StorSimple smetteranno di funzionare correttamente.

- **Supporto per l'archiviazione con ridondanza della zona (ZRS) ** – Con l'aggiornamento alla versione più recente delle API di archiviazione, StorSimple serie 8000 supporta l'archiviazione con ridondanza della zona (ZRS) oltre all'archiviazione con ridondanza locale (LRS) e all'archiviazione con ridondanza geografica (GRS). Fare riferimento a questo [articolo sulle opzioni di ridondanza di archiviazione di Azure](../storage/storage-redundancy.md) per i dettagli sull’archiviazione ZRS.

- **Esperienza di distribuzione iniziale e aggiornamento migliorata** - In questa versione sono stati migliorati i processi di installazione e aggiornamento. L'installazione tramite configurazione guidata è stata migliorata per fornire commenti e suggerimenti all'utente qualora la configurazione di rete e le impostazioni del firewall non siano corrette. Per facilitare la risoluzione dei problemi di rete del dispositivo sono stati forniti dei cmdlet diagnostici aggiuntivi. Vedere l’[articolo sulla risoluzione dei problemi di distribuzione](storsimple-troubleshoot-deployment.md) per ulteriori informazioni sui nuovi cmdlet diagnostici per la risoluzione dei problemi.

## Problemi risolti nell'aggiornamento 1

Nella seguente tabella è disponibile un riepilogo dei problemi risolti in questo aggiornamento.

| No. | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell per StorSimple | Quando un utente accede in remoto al dispositivo StorSimple tramite Windows PowerShell per StorSimple e poi avvia l'installazione guidata, si verifica un arresto anomalo non appena si inserisce l'IP Data 0. Nell'aggiornamento 1, questo bug è stato corretto. | Sì | Sì |
| 2 | Ripristino delle impostazioni predefinite | In alcuni casi, quando si esegue un ripristino delle impostazioni predefinite, il dispositivo StorSimple si blocca e l’utente visualizza il messaggio: **Ripristino delle impostazioni predefinite in corso (fase 8)**. Ciò si verifica se si preme CTRL + C durante l'esecuzione del cmdlet. Il bug è stato corretto.| Sì | No |
| 3 | Ripristino delle impostazioni predefinite | In caso di ripristino non riuscito delle impostazioni predefinite del controller doppio, era possibile procedere con la registrazione del dispositivo. Ciò determinava una configurazione di sistema non supportata. Nell'aggiornamento 1, viene visualizzato un messaggio di errore e la registrazione è bloccata su un dispositivo in cui il ripristino delle impostazioni predefinite non è riuscito. | Sì | No |
| 4 | Ripristino delle impostazioni predefinite | In alcuni casi, sono stati generati avvisi falsi positivi di mancata corrispondenza. Sui dispositivi che eseguono l'aggiornamento 1 non verranno più generati avvisi di mancata corrispondenza non corretti. | Sì | No |
| 5 | Ripristino delle impostazioni predefinite | Se un ripristino delle impostazioni di fabbrica veniva interrotto prima del completamento, il dispositivo entrava in modalità di ripristino e non consentiva l'accesso a Windows PowerShell per StorSimple. Il bug è stato corretto. | Sì | No |
| 6 | Ripristino di emergenza | È stato corretto un bug che impediva il ripristino d'emergenza durante l'individuazione di backup nel dispositivo di destinazione. | Sì | Sì |
| 7 | LED di monitoraggio | In alcuni casi, i LED di monitoraggio posti nella parte posteriore dell’appliance non indicavano lo stato corretto. Il LED blu è stato disattivato. I LED DATA 0 e 1 lampeggiavano anche quando le interfacce non erano configurate. Il problema è stato risolto e i LED di monitoraggio indicano lo stato corretto. | Sì | No |
| 8 | Interfacce di rete | Nelle versioni precedenti, un dispositivo StorSimple configurato con un gateway non indirizzabile poteva disconnettersi. In questa versione, la metrica di routing per Data 0 è stata effettuata al minimo; pertanto, anche se altre interfacce di rete sono abilitate per il cloud, tutto il traffico cloud dal dispositivo verrà indirizzato tramite Data 0. | Sì | Sì | 
| 9 | Backups | Un bug nell'aggiornamento 1 (versione software 6.3.9600.17491) che ha causato errori nei backup dopo 24 giorni è stato risolto nella versione patch aggiornamento 1.1 (versione software 6.3.9600.17521). | Sì | Sì |

## Problemi noti nell'aggiornamento 1

Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.

| No. | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Quorum disco | In rari casi, se la maggior parte dei dischi nello chassis EBOD di un dispositivo 8600 è disconnessa generando un’assenza di quorum disco, il pool di archiviazione sarà offline e rimarrà in tale stato anche se i dischi vengono riconnessi. | Sarà necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft per i passaggi successivi. | Sì | No |
| 2 | ID controller non corretto | Quando viene eseguita la sostituzione di un controller, il controller 0 potrebbe essere visualizzato come controller 1. Durante la sostituzione del controller, quando l'immagine viene caricata dal nodo peer, l'ID del controller può presentarsi inizialmente come ID del controller peer. In rari casi, questo comportamento può verificarsi anche dopo un riavvio del sistema. | Non è necessaria alcuna azione da parte dell’utente. Questa situazione si risolverà dopo la sostituzione del controller. | Sì | No |
| 3 | Account di archiviazione | L’utilizzo del servizio di archiviazione per eliminare l'account di archiviazione non è supportato. Tale operazione causerebbe una situazione in cui non è possibile recuperare i dati dell'utente. | Sì | Sì |
| 4 | Failover del dispositivo | I failover multipli di un contenitore di volumi dallo stesso dispositivo di origine verso dispositivi di destinazione diversi non sono supportati. Il failover da un singolo dispositivo inattivo a più dispositivi causerà la perdita della proprietà dei dati dei contenitori di volumi sul primo dispositivo sottoposto a failover. Dopo un tale failover, questi contenitori di volumi appariranno o si comporteranno in maniera diversa quando vengono visualizzati nel portale di gestione. | | Sì | No |
| 5 | Installare | Durante l’installazione dell'adattatore StorSimple per SharePoint è necessario fornire un IP del dispositivo affinché l'installazione possa essere completata correttamente. | | Sì | No |
| 6 | Proxy Web | Se nella configurazione del proxy Web è specificato il protocollo HTTPS, la comunicazione tra dispositivo e servizio ne sarà interessata e il dispositivo verrà portato offline. Nel processo, inoltre, verranno generati pacchetti di supporto, consumando risorse significative sul dispositivo. | Assicurarsi che l'URL del proxy Web abbia HTTP come protocollo specificato. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](https://msdn.microsoft.com/library/azure/dn764937.aspx). | Sì | No |
| 7 | Proxy Web | Se si configura e si abilita il proxy Web su un dispositivo registrato, è necessario riavviare il controller attivo sul dispositivo. | | Sì | No |
| 8 | Elevata latenza del cloud ed elevato carico di lavoro I/O | Quando il dispositivo StorSimple rileva una combinazione di latenze cloud molto elevate (nell’ordine di secondi) e carico di lavoro I/O elevato, i volumi del dispositivo entrano in uno stato con funzionalità ridotte e gli I/O potrebbero non riuscire a causa di un errore di "dispositivo non pronto". | In questo caso è necessario riavviare manualmente i controller del dispositivo o eseguire un failover del dispositivo per risolvere il problema. | Sì | No |
| 9 | Azure PowerShell | Quando si utilizza il cmdlet di StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** per selezionare il primo oggetto in modo tale da poterne creare uno nuovo **VolumeContainer**, il cmdlet restituisce tutti gli oggetti. | Mettere il cmdlet tra parentesi nel modo seguente: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** | Sì | Sì |
| 10| Migrazione | Quando, per la migrazione, vengono passati più contenitori del volume, ETA per il backup più recente è accurato solo per il primo contenitore del volume. Inoltre, la migrazione parallela verrà avviata dopo la migrazione dei primi quattro backup nel primo contenitore del volume. | Si consiglia di migrare un contenitore del volume alla volta. | Sì | No |
| 11| Migrazione | Dopo il ripristino, i volumi non vengono aggiunti ai criteri di backup o al gruppo di dischi virtuali. | È necessario aggiungerli a un criterio di backup per creare i backup. | Sì | Sì |
| 12| Migrazione | Al termine della migrazione, il dispositivo di serie 5000/7000 non deve accedere ai contenitori di dati migrati. | Dopo il completamento e l'approvazione della migrazione, è consigliabile eliminare i contenitori di dati migrati. | Sì | No |
| 13| Clonazione e ripristino di emergenza | Un dispositivo StorSimple che esegue l'aggiornamento 1 non può clonare o eseguire un ripristino di emergenza su un dispositivo che esegue software precedente a tale aggiornamento. | È necessario aggiornare il dispositivo di destinazione all'aggiornamento 1 per consentire queste operazioni | Sì | Sì |
| 14 | Migrazione | La configurazione del backup per la migrazione potrebbe non riuscire in un dispositivo di serie 5000-7000 quando sono presenti gruppi di volumi senza volumi associati. | Eliminare tutti i gruppi di volumi vuoti senza volumi associati e poi tentare nuovamente il backup della configurazione.| Sì | No |

## Aggiornamenti del dispositivo fisico nell'aggiornamento 1

Quando questi aggiornamenti vengono applicati a un dispositivo fisico, la versione del software passa a 6.3.9600.17521.

## Aggiornamenti firmware e controller SAS presenti nell'aggiornamento 1

Questa versione aggiorna il driver e il firmware per il controller SAS del dispositivo fisico. Aggiorna, inoltre, il firmware del disco sul dispositivo.
 
- Per altre informazioni sull'aggiornamento del controller SAS, vedere [Aggiornamento 1 per i controller SAS LSI nell’appliance Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 

- Per altre informazioni sull’aggiornamento del firmware, vedere [Aggiornamento 1 del firmware per l’appliance Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063414).

- Per altre informazioni sull’aggiornamento del firmware del disco, vedere [Aggiornamento 1 del firmware del disco per l’appliance Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416).
 
## Aggiornamenti del dispositivo virtuale nell'aggiornamento 1

Impossibile applicare questo aggiornamento per il dispositivo virtuale. Tuttavia, tutti i dispositivi virtuali creati dopo il 10 luglio saranno automaticamente in questa versione.

## Passaggi successivi

- [Installare l'aggiornamento 1 sul dispositivo](storsimple-install-update-1.md)
 

<!---HONumber=August15_HO6-->
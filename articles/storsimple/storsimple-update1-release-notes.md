---
title: Note sulla versione dell'aggiornamento 1.2 di StorSimple serie 8000 | Microsoft Docs
description: Vengono descritte le nuove funzionalità, i problemi e le soluzioni alternative dell'aggiornamento 1.2 per StorSimple serie 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/18/2016
ms.author: alkohli

---
# Note sulla versione dell'aggiornamento 1.2 di StorSimple serie 8000
## Overview
Nelle note sulla versione seguenti vengono descritte le nuove funzionalità e sono indicati i problemi critici non risolti relativi all'aggiornamento 1.2 di StorSimple serie 8000. Le note contengono inoltre un elenco degli aggiornamenti del firmware del software, del driver e del disco di StorSimple inclusi in questa versione.

L’aggiornamento 1.2 può essere applicato a qualsiasi dispositivo StorSimple che esegue il software Release (GA), aggiornamento 0.1, aggiornamento 0.2 o aggiornamento 0.3. L’aggiornamento 1.2 non è disponibile se il dispositivo esegue l’aggiornamento 1 o 1.1. Se il dispositivo esegue Release (GA), [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per assistenza relativa all'installazione dell'aggiornamento.

Nella tabella seguente vengono elencate le versioni del software del dispositivo corrispondenti agli aggiornamenti 1, 1.1 e 1.2.

| Se si esegue l’aggiornamento... | questa è la versione del software del dispositivo. |
| --- | --- |
| Aggiornamento 1.2 |6\.3.9600.17584 |
| Aggiornamento 1.1 |6\.3.9600.17521 |
| Aggiornamento 1.0 |6\.3.9600.17491 |

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple. Per altre informazioni, vedere la procedura [Installazione dell'aggiornamento 1.2 sul dispositivo StorSimple](storsimple-install-update-1.md).

> [!IMPORTANT]
> * Sono necessarie circa 5-10 ore per installare questo aggiornamento (inclusi gli aggiornamenti di Windows).
> * L’aggiornamento 1.2 dispone di aggiornamenti del software, del driver LSI e del firmware del disco. Per installarlo, seguire le istruzioni in [Installare l’aggiornamento 1.2 sul dispositivo StorSimple](storsimple-install-update-1.md).
> * Per le nuove versioni è possibile che gli aggiornamenti non siano immediatamente visibili perché viene effettuata un'implementazione graduale degli aggiornamenti. Provare a cercare nuovamente gli aggiornamenti dopo qualche giorno perché verranno presto resi disponibili.
> 
> 

## Novità dell'aggiornamento 1.2
Queste funzionalità sono state rilasciate precedentemente con l'aggiornamento 1 che è stato reso disponibile per un numero limitato di utenti. Con l’aggiornamento versione 1.2, la maggior parte degli utenti StorSimple noterà i miglioramenti e le nuove funzionalità seguenti:

* **Migrazione da dispositivi di serie 5000-7000 alla serie 8000** - Questa versione introduce una nuova funzionalità di migrazione che consente agli utenti del dispositivo StorSimple serie 5000-7000 di migrare i dati a un dispositivo fisico StorSimple serie 8000 o a un'appliance virtuale. La funzionalità di migrazione dispone di due proposte di valore principali:
  
  * **Continuità aziendale**, attivando la migrazione dei dati esistenti nei dispositivi di serie 5000-7000 ai dispositivi di serie 8000.
  * **Miglioramenti delle funzionalità offerte dalle appliance serie 8000**, come la gestione centralizzata efficiente di più appliance tramite il servizio StorSimple Manager, migliore classe di hardware e aggiornamento di firmware, appliance virtuali, mobilità dei dati e funzionalità nei piani d’azione futuri.
    
    Consultare la [Guida alla migrazione](http://www.microsoft.com/download/details.aspx?id=47322) per dettagli su come eseguire la migrazione di un dispositivo StorSimple serie 5000-7000 a un dispositivo di serie 8000.
* **Disponibilità nel portale di Azure Government** – StorSimple è ora disponibile nel portale di Azure Government. Vedere la procedura [Distribuire un dispositivo StorSimple nel portale di Azure Government](storsimple-deployment-walkthrough-gov.md).
* **Supporto per altri provider di servizi cloud** – Altri provider di servizi cloud supportati sono Amazon S3, Amazon S3 con RRS, HP e OpenStack (beta).
* **Aggiornamento alle API di archiviazione più recenti** - Con questa versione, StorSimple è stato aggiornato con le API del servizio di archiviazione di Azure più recenti. I dispositivi StorSimple serie 8000 che eseguono le versioni del software del pre-aggiornamento 1 (Release, 0.1, 0.2 e 0.3) utilizzano versioni delle API del servizio di archiviazione di Azure precedenti al 17 luglio 2009. Come indicato nell’[annuncio sulla rimozione delle versioni del servizio di archiviazione](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx) aggiornato, entro il 1 agosto 2016 tali API saranno deprecate. È necessario applicare l'aggiornamento 1 di StorSimple serie 8000 prima del 1 agosto 2016. In caso contrario, i dispositivi StorSimple smetteranno di funzionare correttamente.
* **Supporto per l'archiviazione con ridondanza della zona (ZRS)** – Con l'aggiornamento alla versione più recente delle API di archiviazione, StorSimple serie 8000 supporta l'archiviazione con ridondanza della zona (ZRS) oltre all'archiviazione con ridondanza locale (LRS) e all'archiviazione con ridondanza geografica (GRS). Fare riferimento a questo [articolo sulle opzioni di ridondanza di archiviazione di Azure](../storage/storage-redundancy.md) per i dettagli sull’archiviazione ZRS.
* **Esperienza di distribuzione iniziale e aggiornamento migliorata** - In questa versione sono stati migliorati i processi di installazione e aggiornamento. L'installazione tramite configurazione guidata è stata migliorata per fornire commenti e suggerimenti all'utente qualora la configurazione di rete e le impostazioni del firewall non siano corrette. Per facilitare la risoluzione dei problemi di rete del dispositivo sono stati forniti dei cmdlet diagnostici aggiuntivi. Vedere l’[articolo sulla risoluzione dei problemi di distribuzione](storsimple-troubleshoot-deployment.md) per ulteriori informazioni sui nuovi cmdlet diagnostici per la risoluzione dei problemi.

## Problemi risolti nell'aggiornamento 1.2
La seguente tabella fornisce un riepilogo dei problemi risolti nell'aggiornamento 1.2, 1.1 e 1.

| di serie | Funzionalità | Problema | Risolto nell'aggiornamento | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell per StorSimple |Quando un utente accede in remoto al dispositivo StorSimple tramite Windows PowerShell per StorSimple e poi avvia l'installazione guidata, si verifica un arresto anomalo non appena si inserisce l'IP Data 0. Nell'aggiornamento 1, questo bug è stato corretto. |Aggiornamento 1 |Sì |Sì |
| 2 |Ripristino delle impostazioni predefinite |In alcuni casi, quando si esegue un ripristino delle impostazioni predefinite, il dispositivo StorSimple si blocca e l’utente visualizza il messaggio: **Ripristino delle impostazioni predefinite in corso (fase 8)**. Ciò si verifica se si preme CTRL + C durante l'esecuzione del cmdlet. Il bug è stato corretto. |Aggiornamento 1 |Sì |No |
| 3 |Ripristino delle impostazioni predefinite |In caso di ripristino non riuscito delle impostazioni predefinite del controller doppio, era possibile procedere con la registrazione del dispositivo. Ciò determinava una configurazione di sistema non supportata. Nell'aggiornamento 1, viene visualizzato un messaggio di errore e la registrazione è bloccata su un dispositivo in cui il ripristino delle impostazioni predefinite non è riuscito. |Aggiornamento 1 |Sì |No |
| 4 |Ripristino delle impostazioni predefinite |In alcuni casi, sono stati generati avvisi falsi positivi di mancata corrispondenza. Sui dispositivi che eseguono l'aggiornamento 1 non verranno più generati avvisi di mancata corrispondenza non corretti. |Aggiornamento 1 |Sì |No |
| 5 |Ripristino delle impostazioni predefinite |Se un ripristino delle impostazioni di fabbrica veniva interrotto prima del completamento, il dispositivo entrava in modalità di ripristino e non consentiva l'accesso a Windows PowerShell per StorSimple. Il bug è stato corretto. |Aggiornamento 1 |Sì |No |
| 6 |Ripristino di emergenza |È stato corretto un bug che impediva il ripristino d'emergenza durante l'individuazione di backup nel dispositivo di destinazione. |Aggiornamento 1 |Sì |Sì |
| 7 |LED di monitoraggio |In alcuni casi, i LED di monitoraggio posti nella parte posteriore dell’appliance non indicavano lo stato corretto. Il LED blu è stato disattivato. I LED DATA 0 e 1 lampeggiavano anche quando le interfacce non erano configurate. Il problema è stato risolto e i LED di monitoraggio indicano lo stato corretto. |Aggiornamento 1 |Sì |No |
| 8 |LED di monitoraggio |In alcuni casi, dopo aver applicato l'aggiornamento 1, la luce blu sul controller attivo si disattiva rendendo difficile identificare il controller attivo. Questo problema è stato risolto in questa versione patch. |Aggiornamento 1.2 |Sì |No |
| 9 |Interfacce di rete |Nelle versioni precedenti, un dispositivo StorSimple configurato con un gateway non indirizzabile poteva disconnettersi. In questa versione, la metrica di routing per Data 0 è stata effettuata al minimo; pertanto, anche se altre interfacce di rete sono abilitate per il cloud, tutto il traffico cloud dal dispositivo verrà indirizzato tramite Data 0. |Aggiornamento 1 |Sì |Sì |
| 10 |Backup |Un bug nell'aggiornamento 1 che ha causato errori nei backup dopo 24 giorni è stato risolto nella versione patch dell’aggiornamento 1.1. |Aggiornamento 1.1 |Sì |Sì |
| 11 |Backup |Un bug nelle versioni precedenti che comprometteva le prestazioni degli snapshot cloud con bassi tassi di cambio. Questo bug è stato risolto in questa versione patch. |Aggiornamento 1.2 |Sì |Sì |
| 12 |Aggiornamenti |Un bug nell'aggiornamento 1 che segnalava un aggiornamento non riuscito e che ha determinato il passaggio dei controller alla modalità di ripristino, è stato risolto in questa versione patch. |Aggiornamento 1.2 |Sì |Sì |

## Problemi noti nell'aggiornamento 1.2
Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.

| di serie | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
| --- | --- | --- | --- | --- | --- |
| 1 |Quorum disco |In rari casi, se la maggior parte dei dischi nello chassis EBOD di un dispositivo 8600 è disconnessa generando un’assenza di quorum disco, il pool di archiviazione sarà offline e rimarrà in tale stato anche se i dischi vengono riconnessi. |Sarà necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft per i passaggi successivi. |Sì |No |
| 2 |ID controller non corretto |Quando viene eseguita la sostituzione di un controller, il controller 0 potrebbe essere visualizzato come controller 1. Durante la sostituzione del controller, quando l'immagine viene caricata dal nodo peer, l'ID del controller può presentarsi inizialmente come ID del controller peer. In rari casi, questo comportamento può verificarsi anche dopo un riavvio del sistema. |Non è necessaria alcuna azione da parte dell’utente. Questa situazione si risolverà dopo la sostituzione del controller. |Sì |No |
| 3 |Account di archiviazione |L’utilizzo del servizio di archiviazione per eliminare l'account di archiviazione non è supportato. Tale operazione causerebbe una situazione in cui non è possibile recuperare i dati dell'utente. |Sì |Sì | |
| 4 |Failover del dispositivo |I failover multipli di un contenitore di volumi dallo stesso dispositivo di origine verso dispositivi di destinazione diversi non sono supportati. Il failover del dispositivo da un singolo dispositivo inattivo a più dispositivi causerà la perdita della proprietà dei dati dei contenitori di volumi sul primo dispositivo sottoposto a failover. Dopo un tale failover, questi contenitori di volumi appariranno o si comporteranno in maniera diversa quando vengono visualizzati nel portale di Azure classico. | |Sì |No |
| 5 |Installare |Durante l’installazione dell'adattatore StorSimple per SharePoint è necessario fornire un IP del dispositivo affinché l'installazione possa essere completata correttamente. | |Sì |No |
| 6 |Proxy Web |Se nella configurazione del proxy Web è specificato il protocollo HTTPS, la comunicazione tra dispositivo e servizio ne sarà interessata e il dispositivo verrà portato offline. Nel processo, inoltre, verranno generati pacchetti di supporto, consumando risorse significative sul dispositivo. |Assicurarsi che l'URL del proxy Web abbia HTTP come protocollo specificato. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](storsimple-configure-web-proxy.md). |Sì |No |
| 7 |Proxy Web |Se si configura e si abilita il proxy Web su un dispositivo registrato, è necessario riavviare il controller attivo sul dispositivo. | |Sì |No |
| 8 |Elevata latenza del cloud ed elevato carico di lavoro I/O |Quando il dispositivo StorSimple rileva una combinazione di latenze cloud molto elevate (nell’ordine di secondi) e carico di lavoro I/O elevato, i volumi del dispositivo entrano in uno stato con funzionalità ridotte e gli I/O potrebbero non riuscire a causa di un errore di "dispositivo non pronto". |In questo caso è necessario riavviare manualmente i controller del dispositivo o eseguire un failover del dispositivo per risolvere il problema. |Sì |No |
| 9 |Azure PowerShell |Quando si utilizza il cmdlet di StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** per selezionare il primo oggetto in modo tale da poterne creare uno nuovo **VolumeContainer**, il cmdlet restituisce tutti gli oggetti. |Mettere il cmdlet tra parentesi nel modo seguente: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Sì |Sì |
| 10 |Migrazione |Quando, per la migrazione, vengono passati più contenitori del volume, ETA per il backup più recente è accurato solo per il primo contenitore del volume. Inoltre, la migrazione parallela verrà avviata dopo la migrazione dei primi quattro backup nel primo contenitore del volume. |Si consiglia di migrare un contenitore del volume alla volta. |Sì |No |
| 11 |Migrazione |Dopo il ripristino, i volumi non vengono aggiunti ai criteri di backup o al gruppo di dischi virtuali. |È necessario aggiungerli a un criterio di backup per creare i backup. |Sì |Sì |
| 12 |Migrazione |Al termine della migrazione, il dispositivo di serie 5000/7000 non deve accedere ai contenitori di dati migrati. |Dopo il completamento e l'approvazione della migrazione, è consigliabile eliminare i contenitori di dati migrati. |Sì |No |
| 13 |Clonazione e ripristino di emergenza |Un dispositivo StorSimple che esegue l'aggiornamento 1 non può clonare o eseguire un ripristino di emergenza su un dispositivo che esegue software precedente a tale aggiornamento. |È necessario aggiornare il dispositivo di destinazione all'aggiornamento 1 per consentire queste operazioni |Sì |Sì |
| 14 |Migrazione |La configurazione del backup per la migrazione potrebbe non riuscire in un dispositivo di serie 5000-7000 quando sono presenti gruppi di volumi senza volumi associati. |Eliminare tutti i gruppi di volumi vuoti senza volumi associati e poi tentare nuovamente il backup della configurazione. |Sì |No |

## Aggiornamenti del dispositivo fisico nell'aggiornamento 1.2
Se l’aggiornamento 1.2 della patch viene applicato a un dispositivo fisico (che esegue versioni precedenti all’aggiornamento 1), la versione del software viene modificata con 6.3.9600.17584.

## Aggiornamenti firmware e controller presenti nell'aggiornamento 1.2
Questa versione aggiorna il driver e il firmware del disco sul dispositivo.

* Per altre informazioni sull'aggiornamento del controller SAS, vedere [Aggiornamento 1 per i controller SAS LSI nell’appliance Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005).
* Per altre informazioni sull’aggiornamento del firmware del disco, vedere [Aggiornamento 1 del firmware del disco per l’appliance Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416).

## Aggiornamenti del dispositivo virtuale nell'aggiornamento 1.2
Impossibile applicare questo aggiornamento per il dispositivo virtuale. Sarà necessario creare nuovi dispositivi virtuali.

## Passaggi successivi
* [Installare l'aggiornamento 1.2 nel dispositivo](storsimple-install-update-1.md).

<!---HONumber=AcomDC_0824_2016-->
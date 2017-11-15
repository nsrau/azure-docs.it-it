---
title: Note sulla versione dell'aggiornamento 2.2 di StorSimple serie 8000 | Documentazione Microsoft
description: "Illustra le nuove funzionalità, i problemi e le soluzioni alternative per l'aggiornamento 2.2 per StorSimple serie 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 06c14bdd24dd24a98b3838a2ba73b657ce56785a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Note sulla versione dell'aggiornamento 2.2 di StorSimple serie 8000
> [!NOTE]
> Il portale classico per StorSimple è deprecato. Le istanze di Gestione dispositivi StorSimple passeranno automaticamente al nuovo portale di Azure in base alla pianificazione per la funzionalità deprecata. Si riceveranno un messaggio di posta elettronica e una notifica del portale relativi a questo passaggio. Anche questo documento verrà ritirato a breve. Per domande relative al passaggio, vedere [Domande frequenti: spostamento nel portale di Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Panoramica
Le note sulla versione seguenti illustrano le nuove funzionalità e indicano i problemi critici non risolti relativi all'aggiornamento 2.2 di StorSimple serie 8000. Contengono inoltre un elenco degli aggiornamenti software di StorSimple inclusi in questa versione.

L'aggiornamento 2.2 può essere applicato a qualsiasi dispositivo StorSimple che esegue la versione in disponibilità generale del software o una versione di aggiornamento compresa tra 0.1 e 2.1. La versione del dispositivo associata all'aggiornamento 2.2 è 6.3.9600.17708.

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.

> [!IMPORTANT]
> * L'aggiornamento 2.2 include aggiornamenti solo per il software. L'installazione dell'aggiornamento richiede circa 1,5-2 ore. 
> * Se si esegue ancora l'aggiornamento 2.1, si consiglia di applicare l'aggiornamento 2.2 appena possibile.
> * Per le nuove versioni è possibile che gli aggiornamenti non siano immediatamente visibili perché viene effettuata un'implementazione graduale degli aggiornamenti. Attendere alcuni giorni e quindi provare a cercare nuovamente gli aggiornamenti, perché verranno presto resi disponibili.
> 
> 

## <a name="whats-new-in-update-22"></a>Novità dell'aggiornamento 2.2
L'aggiornamento 2.2 include gli importanti aggiornamenti seguenti.

* **Ottimizzazione del recupero automatizzato dello spazio** : quando vengono eliminati dati in volumi con thin provisioning, i blocchi di archiviazione non usati devono essere recuperati. Questa versione ha migliorato il processo di recupero dello spazio dal cloud, in modo che lo spazio non usato risulti disponibile con maggiore rapidità rispetto alle versioni precedenti.
* **Miglioramenti delle prestazioni dello snapshot** : l'aggiornamento 2.2 ha migliorato il tempo di elaborazione di uno snapshot cloud in determinati scenari in cui vengono usati volumi di grandi dimensioni e che includono una quantità minima di dati o nessun dato da modificare. Questo miglioramento risulta ad esempio utile per uno scenario relativo ai volumi di archiviazione.
* **Protezione avanzata per la raccolta di pacchetti per il supporto** : sono stati apportati miglioramenti ala modalità di raccolta e caricamento di pacchetti per il supporto in questa versione. 
* **Miglioramenti per l'affidabilità degli aggiornamenti** : questa versione include correzioni di bug che consentono di ottenere un incremento dell'affidabilità degli aggiornamenti.

## <a name="issues-fixed-in-update-22"></a>Problemi risolti nell'aggiornamento 2.2
Le seguenti tabelle forniscono un riepilogo dei problemi risolti nell’aggiornamento 2.2 e 2.1.    

| No | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
| --- | --- | --- | --- | --- |
| 1 |Prestazioni dell'host |Nella versione precedente sono stati rilevati problemi di prestazioni lato host durante la creazione di un volume aggiunto localmente e durante la conversione di un volume a livelli in un volume aggiunto localmente. Questi problemi sono stati risolti in questa versione, consentendo quindi un miglioramento delle prestazioni dell'host durante le procedure di creazione e conversione del volume. |Sì |No |
| 2 |Volumi aggiunti in locale |In rari casi, è possibile che si verifichi un arresto anomalo del sistema durante la creazione di un volume aggiunto localmente. Tale bug è stato risolto in questa versione. |Sì |No |
| 3 |Suddivisione in livelli |Si sono verificati alcuni arresti anomali quando i metadati per i dispositivi cloud StorSimple (8010 e 8020) sono stati suddivisi in livelli sul cloud. Tale problema è stato corretto in questa versione. |No |Sì |
| 4 |Creazione di snapshot |Si sono verificati problemi correlati alla creazione di snapshot incrementali in scenari con volumi di grandi dimensioni e varianza dati minima o nulla. Tali problemi sono stati risolti in questa versione. |Sì |Sì |
| 5 |Autenticazione Openstack |Quando si usa Openstack come provider di servizi cloud, l'utente rileva un bug poco frequente correlato all'autenticazione, in cui il parser JSON ha provocato un arresto anomalo. Tale bug è stato risolto in questa versione. |Sì |No |
| 6 |Copia lato host |Nelle versioni precedenti del software è stato rilevato un bug poco frequente correlato agli intervalli ODX durante la copia di dati da un volume a un altro. A causa di questo errore, si verifica il failover del controller ed è possibile che il sistema passi alla modalità di ripristino. Il bug è stato risolto in questa versione. |sì |No |
| 7 |Strumentazione gestione Windows (WMI) |Nelle versioni precedenti del software sono state rilevate alcune istanze dell'errore del proxy Web con l'eccezione "<ManagementException> Errore caricamento provider". Questo bug è stato attribuito a una perdita di memoria di WMI ed è stato ora risolto. |Sì |No |
| 8 |Aggiornamento |In alcuni rari casi, nelle versioni precedenti del software l'utente ha ricevuto un errore "CisPowershellHcsscripterror" durante il tentativo di analisi o di installazione di aggiornamenti. Tale problema è stato corretto in questa versione. |Sì |Sì |
| 9 |Pacchetto di supporto |In questa versione sono stati apportati miglioramenti alla modalità di raccolta e caricamento di pacchetti per il supporto. |Sì |Sì |

## <a name="known-issues-in-update-22"></a>Problemi noti nell'aggiornamento 2.2
Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.

| No. | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
| --- | --- | --- | --- | --- | --- |
| 1 |Quorum disco |In rari casi, se la maggior parte dei dischi nello chassis EBOD di un dispositivo 8600 è disconnessa generando un'assenza di quorum disco, il pool di archiviazione sarà offline. Il pool rimarrà in tale stato anche se i dischi vengono riconnessi. |Sarà necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto tecnico Microsoft per i passaggi successivi. |Sì |No |
| 2 |ID controller non corretto |Quando viene eseguita la sostituzione di un controller, il controller 0 potrebbe essere visualizzato come controller 1. Durante la sostituzione del controller, quando l'immagine viene caricata dal nodo peer, l'ID del controller può presentarsi inizialmente come ID del controller peer. In rari casi, questo comportamento può verificarsi anche dopo un riavvio del sistema. |Non è necessaria alcuna azione da parte dell’utente. Questa situazione si risolverà dopo la sostituzione del controller. |Sì |No |
| 3 |Account di archiviazione |L’utilizzo del servizio di archiviazione per eliminare l'account di archiviazione non è supportato. Tale operazione causerebbe una situazione in cui non è possibile recuperare i dati dell'utente. | |Sì |Sì |
| 4 |Failover del dispositivo |I failover multipli di un contenitore di volumi dallo stesso dispositivo di origine verso dispositivi di destinazione diversi non sono supportati. Il failover da un singolo dispositivo inattivo a più dispositivi causerà la perdita della proprietà dei dati dei contenitori di volumi sul primo dispositivo sottoposto a failover. Dopo un tale failover, questi contenitori di volumi appariranno o si comporteranno in maniera diversa quando vengono visualizzati nel portale di Azure classico. | |Sì |No |
| 5 |Installare |Durante l’installazione dell'adattatore StorSimple per SharePoint è necessario fornire un IP del dispositivo affinché l'installazione possa essere completata correttamente. | |Sì |No |
| 6 |Proxy Web |Se nella configurazione del proxy Web è specificato il protocollo HTTPS, la comunicazione tra dispositivo e servizio ne sarà interessata e il dispositivo verrà portato offline. Nel processo, inoltre, verranno generati pacchetti di supporto, consumando risorse significative sul dispositivo. |Assicurarsi che l'URL del proxy Web abbia HTTP come protocollo specificato. Per ulteriori informazioni, andare a [Configurare il proxy Web per il dispositivo](storsimple-configure-web-proxy.md). |Sì |No |
| 7 |Proxy Web |Se si configura e si abilita il proxy Web su un dispositivo registrato, è necessario riavviare il controller attivo sul dispositivo. | |Sì |No |
| 8 |Elevata latenza del cloud ed elevato carico di lavoro I/O |Quando il dispositivo StorSimple rileva una combinazione di latenze cloud molto elevate (nell’ordine di secondi) e carico di lavoro I/O elevato, i volumi del dispositivo entrano in uno stato con funzionalità ridotte e gli I/O potrebbero non riuscire a causa di un errore di "dispositivo non pronto". |In questo caso è necessario riavviare manualmente i controller del dispositivo o eseguire un failover del dispositivo per risolvere  il problema. |Sì |No |
| 9 |Azure PowerShell |Quando si utilizza il cmdlet di StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** per selezionare il primo oggetto in modo tale da poter creare un oggetto **VolumeContainer** nuovo, il cmdlet restituisce tutti gli oggetti. |Racchiudere il cmdlet tra parentesi nel modo seguente: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Sì |Sì |
| 10 |Migrazione |Quando, per la migrazione, vengono passati più contenitori del volume, ETA per il backup più recente è accurato solo per il primo contenitore del volume. Inoltre, la migrazione parallela verrà avviata dopo la migrazione dei primi quattro backup nel primo contenitore del volume. |Si consiglia di migrare un contenitore del volume alla volta. |Sì |No |
| 11 |Migrazione |Dopo il ripristino, i volumi non vengono aggiunti ai criteri di backup o al gruppo di dischi virtuali. |È necessario aggiungerli a un criterio di backup per creare i backup. |Sì |Sì |
| 12 |Migrazione |Al termine della migrazione, il dispositivo di serie 5000/7000 non deve accedere ai contenitori di dati migrati. |Dopo il completamento e l'approvazione della migrazione, è consigliabile eliminare i contenitori di dati migrati. |Sì |No |
| 13 |Clonazione e ripristino di emergenza |Un dispositivo StorSimple che esegue l'aggiornamento 1 non può clonare o eseguire un ripristino di emergenza su un dispositivo che esegue software precedente a tale aggiornamento. |È necessario aggiornare il dispositivo di destinazione all'aggiornamento 1 per consentire queste operazioni |Sì |Sì |
| 14 |Migrazione |La configurazione del backup per la migrazione potrebbe non riuscire in un dispositivo di serie 5000-7000 quando sono presenti gruppi di volumi senza volumi associati. |Eliminare tutti i gruppi di volumi vuoti senza volumi associati e poi tentare nuovamente il backup della configurazione. |Sì |No |
| 15 |Cmdlet di Azure PowerShell e volumi aggiunti in locale |Non è possibile creare un volume aggiunto in locale tramite i cmdlet di Azure PowerShell. I volumi creati tramite Azure PowerShell verranno suddivisi in livelli. |Usare sempre il servizio StorSimple Manager per configurare i volumi aggiunti in locale. |Sì |No |
| 16 |Spazio disponibile per i volumi aggiunti in locale |Se si elimina un volume aggiunto in locale, lo spazio disponibile per i nuovi volumi potrebbe non essere immediatamente aggiornato. Il servizio StorSimple Manager aggiorna lo spazio locale disponibile circa ogni ora. |Attendere un'ora prima di creare il nuovo volume. |Sì |No |
| 17 |Volumi aggiunti in locale |Il processo di ripristino espone il backup dell'istantanea temporaneo in Catalogo Backup, ma solo per la durata del processo di ripristino. Espone inoltre un gruppo di dischi virtuali con prefisso **tmpCollection** nella pagina **Criteri di backup**, ma solo per la durata del processo di ripristino. |Questo comportamento può verificarsi se il processo di ripristino dispone solo di volumi associati in locale o di una combinazione di volumi associati in locale e a livelli. Se il processo di ripristino include solo i volumi a livelli, questo comportamento non si verificherà. Non è necessario alcun intervento dell'utente. |Sì |No |
| 18 |Volumi aggiunti in locale |Se si annulla un processo di ripristino e si verifica un failover del controller subito dopo, il processo di ripristino visualizzerà lo stato **Non riuscito** anziché **Annullato**. Se un processo di ripristino ha esito negativo e si verifica un failover del controller subito dopo, il processo di ripristino visualizzerà lo stato **Annullato** anziché **Non riuscito**. |Questo comportamento può verificarsi se il processo di ripristino dispone solo di volumi associati in locale o di una combinazione di volumi associati in locale e a livelli. Se il processo di ripristino include solo i volumi a livelli, questo comportamento non si verificherà. Non è necessario alcun intervento dell'utente. |Sì |No |
| 19 |Volumi aggiunti in locale |Se si annulla un processo di ripristino o se un ripristino ha esito negativo e quindi si verifica un failover del controller, viene visualizzato un processo di ripristino aggiuntivo nella pagina **Processi** . |Questo comportamento può verificarsi se il processo di ripristino dispone solo di volumi associati in locale o di una combinazione di volumi associati in locale e a livelli. Se il processo di ripristino include solo i volumi a livelli, questo comportamento non si verificherà. Non è necessario alcun intervento dell'utente. |Sì |No |
| 20 |Volumi aggiunti in locale |Se si cerca di convertire un volume a livelli (creato e clonato con l'aggiornamento 1.2 o precedente) in un volume aggiunto in locale e il dispositivo sta esaurendo lo spazio o si verifica un'interruzione del cloud, i cloni possono risultare danneggiati. |Questo problema si verifica solo con i volumi che sono stati creati e clonati con software precedente all'aggiornamento 2.1. Si tratta di uno scenario poco frequente. | | |
| 21 |Conversione del volume |Non aggiornare i record di controllo di accesso collegati a un volume mentre è in corso una conversione del volume (da volume a livelli a volume aggiunto in locale o viceversa). L'aggiornamento dei record di controllo di accesso potrebbe causare il danneggiamento dei dati. |Se necessario, aggiornare i record di controllo di accesso prima della conversione del volume e non eseguire altri aggiornamenti dei record di controllo di accesso mentre la conversione è in corso. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Aggiornamenti firmware e controller presenti nell'aggiornamento 2.2
Questa versione include aggiornamenti solo per il software. Se tuttavia si esegue l'aggiornamento da una versione precedente all'aggiornamento 2, sarà necessario installare aggiornamenti per driver, Storport, Spaceport e in alcuni casi per firmware del disco nel dispositivo.

Per altre informazioni su come installare gli aggiornamenti per driver, Storport, Spaceport e firmware del disco, vedere [Installare l'aggiornamento 2.2](storsimple-install-update-21.md) nel dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aggiornamenti del dispositivo virtuale nell'aggiornamento 2.2
Impossibile applicare questo aggiornamento per il dispositivo virtuale. Sarà necessario creare nuovi dispositivi virtuali. 

## <a name="next-step"></a>Passaggio successivo
Informazioni su come [Installare l'aggiornamento 2.2](storsimple-install-update-21.md) nel dispositivo StorSimple.


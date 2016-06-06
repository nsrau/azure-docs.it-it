<properties 
   pageTitle="Note sulla versione dell'aggiornamento 2 di StorSimple serie 8000 | Microsoft Azure"
   description="Descrive le nuove funzionalità, i problemi e le soluzioni alternative dell'aggiornamento 2 per StorSimple serie 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# Note sulla versione dell'aggiornamento 2 di StorSimple serie 8000  

## Panoramica

Le note sulla versione seguenti descrivono le nuove funzionalità e identificano i problemi critici non risolti relativi all'aggiornamento 2 di StorSimple serie 8000. Le note contengono anche un elenco degli aggiornamenti del software, del driver e del firmware del disco di StorSimple inclusi in questa versione.

L'aggiornamento 2 può essere applicato a qualsiasi dispositivo StorSimple che esegue la versione in disponibilità generale del software o una versione di aggiornamento compresa tra 0.1 e 1.2. La versione del dispositivo associata all'aggiornamento 2 è 6.3.9600.17673.

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.

>[AZURE.IMPORTANT]
> 
- Sono necessarie circa 4-7 ore per installare questo aggiornamento (inclusi gli aggiornamenti di Windows). 
- L'aggiornamento 2 contiene aggiornamenti del software, del driver LSI e del firmware SSD.
- Per le nuove versioni è possibile che gli aggiornamenti non siano immediatamente visibili perché viene effettuata un'implementazione graduale degli aggiornamenti. Attendere alcuni giorni e quindi provare a cercare nuovamente gli aggiornamenti, perché verranno presto resi disponibili.


## Novità dell'aggiornamento 2

L'aggiornamento 2 introduce le nuove funzionalità seguenti:

- **Volumi aggiunti in locale**: nelle versioni precedenti di StorSimple serie 8000, i blocchi di dati erano suddivisi in livelli nel cloud in base all'uso. Non vi era alcun modo di garantire che i blocchi rimanessero in locale. Nell'aggiornamento 2, quando si crea un volume è possibile designarlo come aggiunto in locale e i dati primari di tale volume non verranno suddivisi in livelli nel cloud. Gli snapshot dei volumi aggiunti in locale vengono sempre copiati nel cloud per il backup, in modo che il cloud possa essere usato a scopi di ripristino di emergenza e mobilità dei dati. È anche possibile modificare il tipo di volume (ovvero convertire i volumi a livelli in volumi aggiunti in locale e i volumi aggiunti in locale in volumi a livelli). 

- **Miglioramenti del dispositivo virtuale StorSimple**: in precedenza, StorSimple serie 8000 posizionava il dispositivo virtuale come soluzione di sviluppo/test o di ripristino di emergenza. Era disponibile un solo modello di dispositivo virtuale (modello 1100). L'aggiornamento 2 introduce due modelli di dispositivi virtuali:

     - 8010 (in precedenza denominato 1100): nessuna modifica; ha una capacità di 30 TB e usa l'archiviazione standard di Azure.
     - 8020: ha una capacità di 64 TB e usa l'archiviazione Premium di Azure per migliorare le prestazioni.

    È presente un singolo disco rigido virtuale per entrambi i modelli di dispositivi virtuali (8010/8020). Quando viene avviato per la prima volta, il dispositivo virtuale rileva i parametri di piattaforma e applica la versione del modello corretta.

- **Miglioramenti di rete**: l'aggiornamento 2 include i miglioramenti di rete seguenti:

     - È possibile abilitare più schede di interfaccia di rete per il cloud in modo che possa verificarsi il failover se una scheda di interfaccia di rete presenta un problema.
     - Miglioramenti di routing, con metriche fisse per blocchi abilitati per il cloud.
     - Nuovo tentativo online delle risorse con errori prima di un failover.
     - Nuovi avvisi per gli errori del servizio.

- **Miglioramenti dell'aggiornamento**: nell'aggiornamento 1.2 e versioni precedenti, StorSimple serie 8000 veniva aggiornato tramite due canali: Windows Update per clustering, iSCSI e così via e Microsoft Update per i file binari e il firmware. L'aggiornamento 2 usa Microsoft Update per tutti i pacchetti di aggiornamento. Questo dovrebbe ridurre il tempo necessario per l'applicazione di patch o l'esecuzione di failover.

- **Aggiornamenti del firmware**: sono inclusi gli aggiornamenti del firmware seguenti:
    - LSI: lsi\_sas2.sys versione del prodotto 2.00.72.10
    - Solo unità SSD (non sono presenti aggiornamenti dell'unità disco rigido): XMGG, XGEG, KZ50, F6C2 e VR08

- **Supporto proattivo**: l'aggiornamento 2 consente a Microsoft di estrarre informazioni di diagnostica aggiuntive dal dispositivo. Quando il team addetto alle operazioni identifica i dispositivi che presentano problemi, sono disponibili più modi di raccogliere informazioni dal dispositivo e diagnosticare i problemi. **Accettando l'aggiornamento 2, si acconsente a partecipare al supporto proattivo**.
 

## Problemi risolti nell'aggiornamento 2

Le tabelle seguenti offrono un riepilogo dei problemi risolti nell'aggiornamento 2.

| No. | Funzionalità | Problema | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Interfacce di rete | Dopo aver eseguito l'aggiornamento 1, il servizio StorSimple Manager segnalava che le porte Data2 e Data3 restituivano un errore su un controller. Il problema è stato risolto. | Sì | No |
| 2 | Aggiornamenti | Dopo aver eseguito l'aggiornamento 1, si sono attivati degli avvisi acustici nel portale di Azure classico in più dispositivi. Il problema è stato risolto. | Sì | No |
| 3 | Autenticazione Openstack | Quando si usa Openstack come provider di servizi cloud, potrebbe venire visualizzato un errore che informa che la stringa di autenticazione cloud è troppo lunga. Questo problema è stato risolto. | Sì | No |


## Problemi noti nell'aggiornamento 2

Nella tabella seguente viene fornito un riepilogo dei problemi noti in questa versione.

| No. | Funzionalità | Problema | Commenti/Soluzione alternativa | Si applica a un dispositivo fisico | Si applica a un dispositivo virtuale |
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
| 20 |Volumi aggiunti in locale | Se si cerca di convertire un volume a livelli (creato e clonato con l'aggiornamento 1.2 o precedente) in un volume aggiunto in locale e il dispositivo sta esaurendo lo spazio o si verifica un'interruzione del cloud, i cloni possono risultare danneggiati.| Questo problema si verifica solo con i volumi che sono stati creati e clonati con software precedente all'aggiornamento 2. Si tratta di uno scenario poco frequente.|
| 21 | Conversione del volume | Non aggiornare i record di controllo di accesso collegati a un volume mentre è in corso una conversione del volume (da volume a livelli a volume aggiunto in locale o viceversa). L'aggiornamento dei record di controllo di accesso potrebbe causare il danneggiamento dei dati. | Se necessario, aggiornare i record di controllo di accesso prima della conversione del volume e non eseguire altri aggiornamenti dei record di controllo di accesso mentre la conversione è in corso. |

## Aggiornamenti del controller e del firmware presenti nell'aggiornamento 2

Questa versione aggiorna il driver e il firmware del disco sul dispositivo.
 
- Per altre informazioni sugli aggiornamenti del firmware LSI, vedere l'articolo della Microsoft Knowledge Base 3121900. 
- Per altre informazioni sugli aggiornamenti del firmware del disco, vedere l'articolo della Microsoft Knowledge Base 3121899.
 
## Aggiornamenti del dispositivo virtuale nell'aggiornamento 2

Impossibile applicare questo aggiornamento per il dispositivo virtuale. Sarà necessario creare nuovi dispositivi virtuali.

## Passaggio successivo

Informazioni su come [installare l'aggiornamento 2](storsimple-install-update-2.md) nel dispositivo StorSimple.

<!---HONumber=AcomDC_0525_2016-->
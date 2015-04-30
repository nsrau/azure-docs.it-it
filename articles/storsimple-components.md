<properties 
   pageTitle="Che cosa sono i componenti StorSimple?" 
   description="Descrive i dispositivi, i servizi e le tecnologie di gestione StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="02/17/2015"
   ms.author="v-sharos@microsoft.com"/>


# Che cosa sono i componenti StorSimple? 

Microsoft Azure StorSimple è una soluzione di archiviazione integrata che gestisce le attività di archiviazione tra i dispositivi locali e l'archiviazione cloud di Microsoft Azure.  StorSimple è stato progettato per ridurre i costi e semplificare la gestione dell'archiviazione, migliorare le funzionalità e l'efficienza del ripristino di emergenza e assicurare la mobilità dei dati.

Le sezioni seguenti descrivono i componenti di Microsoft Azure StorSimple e illustrano il modo in cui la soluzione organizza i dati, alloca le risorse di archiviazione e facilita la gestione dell'archiviazione e la protezione dei dati. 

> [AZURE.NOTE] Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure si applicano solo ai dispositivi di StorSimple serie 8000. Per informazioni sui dispositivi serie 7000, vedere: [Guida di StorSimple](http://onlinehelp.storsimple.com/).

## Dispositivo StorSimple

Il dispositivo Microsoft Azure StorSimple è un array di archiviazione ibrido locale che fornisce archiviazione primaria e accesso iSCSI ai dati archiviati altrove. Gestisce le comunicazioni con l'archiviazione cloud e consente di garantire la sicurezza e la riservatezza di tutti i dati archiviati nella soluzione Microsoft Azure StorSimple.

Il dispositivo StorSimple include unità SSD (Solid State Drive) e unità disco rigido, oltre al supporto per il clustering e il failover automatico. Contiene un processore condiviso, uno spazio di archiviazione condiviso e due controller con mirroring. Ciascun controller fornisce quanto segue:

- Connessione a un computer host
- Fino a sei porte di rete per la connessione alla rete LAN (Local Area Network)
- Monitoraggio hardware
- Memoria NVRAM (Non-Volatile Random Access Memory), per mantenere le informazioni anche in caso di interruzione dell'alimentazione
- Aggiornamento compatibile con cluster per gestire gli aggiornamenti software dei server in un cluster di failover, in modo che gli aggiornamenti abbiano un impatto minimo o nullo sulla disponibilità dei servizi
- Servizio cluster, con funzionamento analogo a quello di un cluster back-end, in grado di fornire disponibilità elevata e ridurre al minimo gli effetti negativi provocati da un eventuale errore o disconnessione di un'unità disco rigido o SSD

Solo uno dei due controller è sempre attivo. Il caso di guasto del controller attivo, il secondo si attiva automaticamente. 

Per altre informazioni vedere [Dispositivi StorSimple](https://msdn.microsoft.com/library/azure/dn772363.aspx).

## Dispositivo virtuale StorSimple

È possibile usare StorSimple per creare un dispositivo virtuale che replica l'architettura e le funzionalità del dispositivo di archiviazione ibrido fisico. 

Il dispositivo virtuale StorSimple (anche noto come appliance virtuale StorSimple) viene eseguito in un unico nodo in una macchina virtuale di Azure. Un dispositivo virtuale può essere creato solo in una macchina virtuale di Azure e non in un dispositivo StorSimple o in un server locale. Un dispositivo virtuale StorSimple si distingue da un dispositivo StorSimple fisico per le seguenti caratteristiche: 

- Il dispositivo virtuale ha una sola interfaccia di rete, mentre il dispositivo fisico ne ha sei. 
- Il dispositivo virtuale può essere registrato durante la configurazione del dispositivo e non con un'attività separata.
- Non è possibile rigenerare la chiave DEK del servizio da un dispositivo virtuale. Durante il rollover della chiave, l'utente rigenera la chiave nel dispositivo fisico, quindi aggiorna il dispositivo virtuale con la nuova chiave.
- Se è necessario applicare aggiornamenti al dispositivo virtuale, potrebbero esserci dei tempi di inattività. Questo inconveniente non si verifica con un dispositivo StorSimple fisico.

È consigliabile usare il dispositivo virtuale StorSimple per gli scenari di ripristino di emergenza in cui non è disponibile un dispositivo fisico, ad esempio durante l'esecuzione di test o di piccole distribuzioni pilota.

Per altre informazioni vedere [Dispositivo virtuale StorSimple](https://msdn.microsoft.com/library/azure/dn772390.aspx).


## Tecnologie di gestione dell'archiviazione
 
Oltre al dispositivo StorSimple e al dispositivo virtuale dedicati, Microsoft Azure StorSimple usa le seguenti tecnologie software per fornire un accesso rapido ai dati e ridurre il consumo di risorse di archiviazione:

- Suddivisione automatica in livelli dell'archiviazione 
- Thin provisioning 
- Deduplicazione e compressione 

### Suddivisione automatica in livelli dell'archiviazione

Microsoft Azure StorSimple organizza automaticamente i dati in livelli logici in base all'uso corrente, alla data di creazione e alle relazioni con altri dati. I dati più attivi vengono archiviati in locale, mentre quelli meno attivi e non attivi vengono automaticamente migrati nel cloud. Il diagramma seguente illustra questo approccio all'archiviazione.
 
![Livelli di archiviazione StorSimple](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**Livelli di archiviazione di StorSimple**

Per abilitare l'accesso rapido, StorSimple archivia i dati molto attivi (hot data) nelle unità SSD del dispositivo StorSimple. Archivia invece i dati usati occasionalmente (warm data) nelle unità disco rigido del dispositivo o nei server del data center. Infine, i dati inattivi, i dati di backup e quelli conservati per scopi di archiviazione o conformità vengono spostati nel cloud. 

StorSimple ordina e riorganizza i dati e le assegnazioni delle risorse di archiviazione in base ai cambiamenti dei modelli di utilizzo. Alcune informazioni, ad esempio, possono diventare meno attive nel corso del tempo. Con il diminuire della frequenza di uso, tali informazioni vengono prima migrate dalle unità SSD alle unità disco rigido e infine vengono spostate nel cloud. Se ritornano attive, le informazioni vengono di nuovo migrate nel dispositivo di archiviazione.

### Thin provisioning

Il thin provisioning è una tecnologia di virtualizzazione in cui le risorse di archiviazione disponibili risultano maggiori delle risorse fisiche. Anziché riservare in anticipo spazio di archiviazione sufficiente, StorSimple usa il thin provisioning per allocare esattamente la quantità di spazio necessaria per soddisfare i requisiti correnti. La natura elastica dell'archiviazione cloud facilita questo approccio, consentendo a StorSimple di aumentare o diminuire lo spazio cloud in base alle variazioni nella domanda. 

### Deduplicazione e compressione

Microsoft Azure StorSimple usa tecniche di deduplicazione e compressione dei dati per ridurre ulteriormente i requisiti di archiviazione.

La deduplicazione riduce la quantità complessiva di dati archiviati eliminando la ridondanza nel set di dati archiviato. Se i dati subiscono delle variazioni, StorSimple ignora quelli non modificati e acquisisce soltanto le differenze. StorSimple riduce inoltre la quantità di dati archiviati identificando e rimuovendo le informazioni non necessarie. 

## Windows PowerShell per StorSimple

Windows PowerShell per StorSimple fornisce un'interfaccia della riga di comando che è possibile usare per creare e gestire il servizio Microsoft Azure StorSimple e configurare e monitorare i dispositivi StorSimple. Si tratta di un'interfaccia della riga di comando basata su Windows PowerShell, che include cmdlet dedicati per la gestione del dispositivo StorSimple. Windows PowerShell per StorSimple offre funzionalità che consentono di:

- Registrare un dispositivo.
- Configurare l'interfaccia di rete in un dispositivo.
- Installare determinati tipi di aggiornamento.
- Risolvere i problemi del dispositivo tramite l'accesso alla sessione di supporto.
- Modificare lo stato del dispositivo.

È possibile accedere a Windows PowerShell per StorSimple da una console seriale (in un computer host connesso direttamente al dispositivo) o da una posizione remota usando la gestione remota di Windows PowerShell. Alcune attività di Windows PowerShell per StorSimple, ad esempio la registrazione iniziale del dispositivo, possono essere eseguite soltanto sulla console seriale. 

Per altre informazioni, vedere [Windows PowerShell per StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx).

## Servizio StorSimple Manager

Microsoft Azure StorSimple fornisce l'accesso a un'interfaccia utente basata sul Web (il servizio StorSimple Manager) che consente di gestire in modo centralizzato il data center e l'archiviazione cloud. È possibile usare il servizio StorSimple Manager per eseguire le attività seguenti:

- Configurare le impostazioni di sistema per i dispositivi StorSimple.
- Configurare e gestire le impostazioni di sicurezza per i dispositivi StorSimple.
- Configurare le credenziale e le proprietà cloud.
- Configurare e gestire volumi su un server.
- Configurare gruppi di volumi.
- Eseguire il backup e il ripristino dei dati.
- Monitorare le prestazioni.
- Rivedere le impostazioni di sistema e identificare possibili problemi.

È possibile usare il servizio StorSimple Manager per eseguire tutte le attività amministrative, ad eccezione di quelle che richiedono un tempo di inattività del sistema, ad esempio la configurazione iniziale o l'installazione di aggiornamenti.

Per altre informazioni vedere [Servizio StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx).

## Gestione snapshot StorSimple

Gestione snapshot StorSimple è uno snap-in di Microsoft Management Console (MMC) che consente di creare copie di backup coerenti e temporizzate di dati locali o cloud. Lo snap-in viene eseguito su un host basato su Windows Server. Gestione snapshot StorSimple può essere usato per:

- Configurare, eseguire il backup ed eliminare volumi.
- Configurare gruppi di volumi per assicurarsi che i dati di cui è stato eseguito il backup siano coerenti con l'applicazione.
- Gestire i criteri di backup in modo che il backup venga eseguito secondo una pianificazione prestabilita e che i dati vengano archiviati in una posizione designata (in locale o sul cloud).
- Ripristinare volumi e singoli file.

I backup vengono acquisiti come snapshot: questo significa che vengono registrate solo le modifiche apportate dopo l'ultimo snapshot, richiedendo uno spazio di archiviazione notevolmente ridotto rispetto a quello necessario per i backup completi. A seconda delle necessità, è possibile creare pianificazioni dei backup o eseguire backup immediati. È possibile usare Gestione snapshot StorSimple anche per definire criteri di conservazione che controllano il numero di snapshot da salvare. Se in un secondo tempo è necessario ripristinare i dati da un backup, Gestione snapshot StorSimple consente di effettuare una selezione dal catalogo degli snapshot locali o cloud. 

Se si verifica un'emergenza o è necessario ripristinare i dati per un altro motivo, Gestione snapshot StorSimple esegue il ripristino in modo incrementale, in base alla necessità di disponibilità dei dati. Il ripristino dei dati non richiede l'arresto dell'intero sistema durante il recupero di un file, la sostituzione di un componente o lo spostamento delle operazioni in un altro sito.

Per altre informazioni vedere [Gestione snapshot StorSimple](https://msdn.microsoft.com/library/azure/dn772365.aspx).

## Adattatore StorSimple per SharePoint

Microsoft Azure StorSimple include l'adattatore StorSimple per SharePoint, un componente opzionale che estende in modo trasparente le funzionalità di archiviazione e protezione dati di StorSimple alle server farm di SharePoint. L'adattatore funziona con un provider RBS (Remote Blob Storage) e la funzionalità RBS di SQL Server, consentendo di spostare i BLOB in un server di cui il sistema Microsoft Azure StorSimple ha eseguito il backup. Microsoft Azure StorSimple archivia quindi i dati BLOB localmente o nel cloud, in base all'utilizzo.

L'adattatore StorSimple per SharePoint viene gestito dal portale Amministrazione centrale SharePoint. Di conseguenza, la gestione di SharePoint rimane centralizzata e l'intera archiviazione risulta essere nella farm di SharePoint.

Per altre informazioni, vedere [Adattatore StorSimple per SharePoint](https://msdn.microsoft.com/library/azure/dn757737.aspx). 


## Passaggi successivi

Rivedere le [note sulla versione di StorSimple](https://msdn.microsoft.com/library/azure/dn772367.aspx)





<!--HONumber=52-->
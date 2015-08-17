<properties 
   pageTitle="Informazioni su StorSimple" 
   description="Descrive l'architettura e le funzionalità di StorSimple e vengono descritti i componenti di StorSimple." 
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
   ms.date="08/03/2015"
   ms.author="v-sharos@microsoft.com"/>


# Informazioni su StorSimple 

## Panoramica

Microsoft Azure StorSimple è una soluzione efficiente, dai costi contenuti e gestibile che elimina molti problemi e spese associati agli archivi dell'organizzazione e alla protezione dei dati. Usa un dispositivo proprietario (il dispositivo Microsoft Azure StorSimple), integrazione con servizi cloud e un set di strumenti di gestione integrati per offrire una semplice visualizzazione di tutti gli archivi dell'organizzazione, inclusa l'archiviazione cloud.

StorSimple utilizza la suddivisione in livelli di archiviazione per gestire i dati archiviati in vari supporti di memorizzazione. Il working set corrente è archiviato localmente su unità a stato solido, i dati utilizzati meno frequentemente vengono archiviati su unità a disco rigido e i dati di archiviazione vengono spostati nel cloud. Inoltre, StorSimple utilizza la deduplicazione e la compressione per ridurre la quantità di spazio di archiviazione che i dati utilizzano. Il processo di suddivisione in livelli di archiviazione avviene nel modo seguente:

1. Un amministratore di sistema configura un account di archiviazione cloud di Microsoft Azure.
2. L'amministratore utilizza la console seriale e il servizio StorSimple Manager (in esecuzione nel portale di gestione di Azure) per configurare il dispositivo e il file server, creando volumi e criteri di protezione dei dati. Il file server locale utilizza l'interfaccia iSCSI (Internet Small Computer System Interface) per accedere al dispositivo StorSimple.
3. Inizialmente, StorSimple archivia i dati nel livello SSD rapido del dispositivo.
4. Quando il livello SSD raggiunge il limite di capacità, StorSimple deduplica e comprime i blocchi di dati meno recenti, quindi li sposta nel livello HDD.
5. Quando il livello HDD raggiunge il limite di capacità, StorSimple esegue la crittografia dei blocchi di dati meno recenti e li invia in modo sicuro all'account di archiviazione di Microsoft Azure tramite HTTPS.
6. Microsoft Azure crea più repliche dei dati nel data center e in un data center remoto, assicurando che i dati possono essere recuperati in caso di emergenza. 
7. Quando il file server richiede i dati archiviati nel cloud, StorSimple li restituisce facilmente e ne archivia una copia nel livello SSD del dispositivo StorSimple.

Oltre alla gestione dell'archiviazione, le funzioni di protezione dei dati di StorSimple consentono di creare backup su richiesta e pianificati e di archiviarli in locale o nel cloud. I backup vengono eseguiti sotto forma di snapshot incrementali, il che significa che possono essere creati e ripristinati rapidamente. Gli snapshot cloud possono essere fondamentali in scenari di ripristino di emergenza perché sostituiscono i sistemi di archiviazione secondaria (come il backup su nastro) e consentono di ripristinare i dati nel data center o in siti alternativi, se necessario.

>[AZURE.NOTE]StorSimple serie 8000 con aggiornamento del software 1 o seguenti supporta Amazon S3 con RRS, HP, e i servizi cloud di OpenStack, nonché Microsoft Azure. (È comunque necessario un account di archiviazione di Microsoft Azure per la gestione dei dispositivi) Per ulteriori informazioni, vedere [Configurare un nuovo account di archiviazione](storsimple-deployment-walkthrough.md#configure-a-new-storage-account) in [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u1.md).

## Vantaggi dell'uso di StorSimple

Microsoft Azure StorSimple offre i vantaggi seguenti:

- **Integrazione trasparente**: Microsoft Azure StorSimple usa il protocollo iSCSI per collegare in modo invisibile le strutture di archiviazione dati. Questo assicura che i dati archiviati nel cloud, nel data center o in server remoti appaiano come archiviati in un'unica posizione.
- **Riduzione dei costi di archiviazione** – Microsoft Azure StorSimple alloca una quantità di memoria locale o cloud sufficiente a soddisfare le richieste correnti ed estende la memoria cloud solo quando è necessario. Riduce ulteriormente i requisiti e le spese di archiviazione eliminando le versioni ridondanti degli stessi dati (deduplicazione) e usando la compressione.
- **Gestione dell'archiviazione semplificata** – Microsoft Azure StorSimple offre strumenti di amministrazione di sistema che è possibile usare per configurare e gestire i dati archiviati in locale, su un server remoto e nel cloud. Inoltre, è possibile gestire backup e ripristino di funzioni da uno snap-in Microsoft Management Console (MMC). StorSimple fornisce un’interfaccia opzionale separata che è possibile usare per estendere i servizi di gestione dei dati di StorSimple al contenuto archiviato su server di SharePoint. 
- **Miglioramento del ripristino di emergenza e della conformità** – Microsoft Azure StorSimple non richiede tempi di recupero estesi. Ripristina invece i dati quando è necessario. Le normali operazioni possono quindi continuare con un'interruzione minima. È anche possibile configurare criteri per specificare le pianificazioni dei backup e la conservazione dei dati.
- **Mobilità dei dati** – I dati caricati nei servizi cloud di Microsoft Azure sono accessibili da altri siti a scopo di ripristino e migrazione. È anche possibile usare StorSimple per configurare dispositivi virtuali StorSimple in macchine virtuali (VM, Virtual Machine) in esecuzione in Microsoft Azure. Le VM possono quindi usare i dispositivi virtuali per accedere ai dati archiviati a scopo di test o recupero. 

Il diagramma seguente offre una visualizzazione generale della soluzione Microsoft Azure StorSimple.

![Architettura di StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Architettura di Microsoft Azure StorSimple**

## Componenti di StorSimple

La soluzione Microsoft Azure StorSimple include i componenti seguenti:

- **Dispositivo Microsoft Azure StorSimple** – Array di archiviazione ibrido locale contenente unità SSD e unità di disco rigido, oltre a controller ridondanti e funzionalità di failover automatico. I controller gestiscono il tiering di archiviazione, inserendo i dati attualmente usati (o hot data) nella risorsa di archiviazione locale (nel dispositivo o nei server locali) e spostando nel cloud i dati usati meno di frequente.
- **Dispositivo virtuale StorSimple** – Noto anche come dispositivo virtuale StorSimple, è una versione software del dispositivo StorSimple che replica l'architettura e le funzionalità del dispositivo di archiviazione ibrida fisica. Il dispositivo virtuale StorSimple viene eseguito su un singolo nodo in una macchina virtuale di Azure. Il dispositivo virtuale è adatto a essere usato in scenari di test e di piccoli progetti pilota. Non è possibile creare un dispositivo virtuale StorSimple su un dispositivo StorSimple o un server locale.
- **Windows PowerShell per StorSimple **– Un’interfaccia della riga di comando che può essere usata per gestire il dispositivo StorSimple. Windows PowerShell per StorSimple include funzionalità che permettono di registrare il dispositivo StorSimple, configurare l'interfaccia di rete nel dispositivo, installare determinati tipi di aggiornamento, risolvere i problemi del dispositivo tramite l'accesso alla sessione di supporto e modificare lo stato del dispositivo. È possibile accedere a Windows PowerShell per StorSimple connettendosi alla console seriale o usando la comunicazione remota di Windows PowerShell.
- **i cmdlet PowerShell di azure StorSimple** – un insieme di cmdlet Windows PowerShell che consente di automatizzare le attività a livello di servizio e la migrazione dalla riga di comando. Per altre informazioni sui cmdlet di Azure PowerShell per StorSimple, visitare la [Documentazione di riferimento relativa ai cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **Servizio StorSimple Manager** – un’estensione del portale di gestione di Azure che consente di gestire un dispositivo StorSimple o un dispositivo virtuale StorSimple da una singola interfaccia Web. È possibile usare il servizio StorSimple Manager per creare e gestire servizi, visualizzare e gestire dispositivi, visualizzare avvisi, gestire volumi e visualizzare e gestire criteri di backup e il catalogo di backup.
- **Gestione snapshot StorSimple **– Snap-in MMC (Microsoft Management Console) che usa gruppi di volumi e il servizio Copia Shadow del volume di Windows per generare backup coerenti con l'applicazione. È possibile usare Gestione snapshot StorSimple anche per creare pianificazioni di backup e clonare o ripristinare volumi. 
- **Adattatore StorSimple per SharePoint** – Uno strumento che estende in modo trasparente la risorsa di archiviazione di Microsoft Azure StorSimple e la protezione dei dati al server farm SharePoint, rendendo la risorsa di archiviazione di StorSimple visualizzabile e gestibile dal portale di amministrazione di SharePoint.

## Passaggi successivi

Leggere [Panoramica delle soluzioni StorSimple](http://www.microsoft.com/it-it/server-cloud/products/storsimple/resources.aspx) e visualizzare il [chalk talk StorSimple](http://www.microsoft.com/it-it/server-cloud/products/storsimple/Features.aspx).

Ulteriori informazioni su [componenti e terminologia di StorSimple](storsimple-components.md).


 

<!---HONumber=August15_HO6-->
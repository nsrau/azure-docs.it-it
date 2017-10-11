---
title: Procedure consigliate per l'array virtuale StorSimple | Microsoft Docs
description: Descrive le procedure consigliate per la distribuzione e la gestione dell'array virtuale StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2017
ms.author: alkohli
ms.openlocfilehash: 264764c5e9c32574d97beb2cc3c1bb1cfb555568
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="storsimple-virtual-array-best-practices"></a>Procedure consigliate per l'array virtuale StorSimple
## <a name="overview"></a>Panoramica
Array virtuale Microsoft Azure StorSimple è una soluzione di archiviazione integrata che consente di gestire le attività di archiviazione tra un dispositivo virtuale locale eseguito in un hypervisor e la risorsa di archiviazione cloud di Microsoft Azure. L'array virtuale StorSimple è un'alternativa efficace e conveniente all'array fisico serie 8000. L'array virtuale può essere eseguito nell'infrastruttura di hypervisor esistente, supporta i protocolli iSCSI e SMB ed è adatto per scenari con uffici remoti o succursali. Per altre informazioni sulle soluzioni StorSimple, vedere [Microsoft Azure StorSimple Overview](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx)(Panoramica di Microsoft Azure StorSimple).

Questo articolo descrive le procedure consigliate implementate durante l'installazione, la distribuzione e la gestione iniziali dell'array virtuale StorSimple. Le procedure consigliate forniscono le linee guida convalidate per l'installazione e la gestione dell'array virtuale. L'articolo è destinato agli amministratori IT che si occupano della distribuzione e gestione di array virtuali nei data center.

È consigliabile una revisione periodica delle procedure consigliate per assicurare che, quando vengono apportate modifiche al flusso di installazione o delle attività operative, il dispositivo continui a essere conforme. In caso di problemi durante l'implementazione di queste procedure consigliate relative all'array virtuale, [Contattare il supporto tecnico Microsoft](storsimple-virtual-array-log-support-ticket.md) per assistenza.

## <a name="configuration-best-practices"></a>Procedure consigliate per la configurazione
Queste procedure consigliate forniscono le linee guida che occorre seguire durante l'installazione e la distribuzione iniziali degli array virtuali. Sono incluse le procedure consigliate relative a provisioning della macchina virtuale, impostazioni di Criteri di gruppo, ridimensionamento, configurazione della rete, configurazione degli account di archiviazione e creazione di condivisioni e volumi per l'array virtuale. 

### <a name="provisioning"></a>Provisioning
L'array virtuale StorSimple è una macchina virtuale con provisioning nell'hypervisor (Hyper-V o VMware) del server host. Quando si effettua il provisioning della macchina virtuale, assicurarsi che l'host sia in grado di dedicare risorse sufficienti. Per altre informazioni, vedere i [requisiti minimi di risorse](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) per il provisioning di un array.

Implementare le procedure consigliate seguenti durante il provisioning di un array virtuale:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo di macchina virtuale** |**Generazione 2** per l'uso con Windows Server 2012 o versione successiva e un'immagine *VHDX* . <br></br> **Generazione 1** per l'uso con Windows Server 2008 o versione successiva e un'immagine *VHD* . |Quando si usa un'immagine *VMDK* , usare macchine virtuali versione da 8 a 11. |
| **Tipo di memoria** |Configurare come **memoria statica**. <br></br> Non usare l'opzione per la **memoria dinamica** . | |
| **Tipo di disco dati** |Effettuare il provisioning come **espansione dinamica**.<br></br> **dimensioni fisse** richiedono molto tempo. <br></br> Non usare l'opzione di **differenziazione** . |Usare l'opzione per il **thin provisioning** . |
| **Modifica dei dischi dati** |L'espansione o la riduzione non è consentita. Il tentativo di eseguire questa operazione comporta la perdita di tutti i dati locali nel dispositivo. |L'espansione o la riduzione non è consentita. Il tentativo di eseguire questa operazione comporta la perdita di tutti i dati locali nel dispositivo. |

### <a name="sizing"></a>Ridimensionamento
Quando si ridimensiona l'array virtuale StorSimple, considerare i fattori seguenti:

* Prenotazione locale per volumi o condivisioni. Circa il 12% dello spazio viene riservato a livello locale per ogni volume o condivisione a livelli con provisioning. Anche circa il 10% dello spazio è riservato per un volume aggiunto in locale per il file system.
* Sovraccarico di snapshot. Circa il 15% dello spazio a livello locale è riservato per gli snapshot.
* Necessità di ripristini. Se l'esecuzione del ripristino fa parte di una nuova operazione, il ridimensionamento deve tenere conto dello spazio necessario per il ripristino. Il ripristino viene eseguito in una condivisione o un volume con dimensioni uguali.
* È necessario allocare una parte del buffer per un'eventuale crescita imprevista.

In base ai fattori precedenti, i requisiti per il ridimensionamento possono essere rappresentati dall'equazione seguente:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Gli esempi seguenti illustrano come è possibile ridimensionare un array virtuale secondo le esigenze.

#### <a name="example-1"></a>Esempio 1:
Nell'array virtuale si vuole poter eseguire queste operazioni

* Effettuare il provisioning di un volume o una condivisione a livelli di 2 TB.
* Effettuare il provisioning di un volume o una condivisione a livelli di 1 TB.
* Effettuare il provisioning di un volume o una condivisione aggiunta in locale di 300 GB.

Per i volumi o le condivisioni precedenti, si calcoleranno i requisiti di spazio a livello locale.

Prima di tutto, per ogni volume o condivisione a livelli la prenotazione locale sarà uguale al 12% delle dimensioni del volume o della condivisione. Per il volume o la condivisione aggiunti in locale, la prenotazione locale corrisponde al 10% delle dimensioni del volume o della condivisione aggiunti in locale (oltre alle dimensioni con provisioning). In questo esempio sono necessari

* Prenotazione locale di 240 GB, per un volume o una condivisione a livelli di 2 TB.
* Prenotazione locale di 120 GB (per un volume o una condivisione a livelli di 1 TB).
* 330 GB per un volume o una condivisione aggiunti in locale (con l'aggiunta del 10% della prenotazione locale ai 300 GB delle dimensioni con provisioning)

Lo spazio totale richiesto a livello locale fino a questo punto è: 240 GB + 120 GB + 330 GB = 690 GB.

È quindi necessaria una quantità di spazio a livello locale pari alla singola prenotazione più grande. Questa quantità aggiuntiva viene usata nel caso in cui sia necessario eseguire il ripristino da uno snapshot cloud. In questo esempio la prenotazione locale più grande è di 330 GB (inclusa la prenotazione per il file system), quindi sarà necessario aggiungere questo spazio ai 690 GB: 690 GB + 330 GB = 1020 GB.
Se sono stati eseguiti altri ripristini successivi, è sempre possibile liberare spazio dall'operazione di ripristino precedente.

Fino a questo punto è quindi necessario il 15% dello spazio locale totale per archiviare gli snapshot locali, perciò è disponibile solo l'85%. In questo esempio saranno circa 1020 GB = 0,85&ast;TB del disco dati con provisioning. Di conseguenza, il disco dati con provisioning sarà pari a (1020&ast;(1/0,85)) = 1200 GB = 1,20 TB ~ 1,25 TB (con arrotondamento al quartile più vicino)

Considerando la crescita imprevista e i nuovi ripristini, è consigliabile effettuare il provisioning di un disco locale di circa 1,25-1,5 TB.

> [!NOTE]
> È anche consigliabile effettuare il thin provisioning del disco locale, perché lo spazio di ripristino è necessario solo quando si vogliono ripristinare dati più vecchi di cinque giorni. Il ripristino a livello di elemento consente di ripristinare i dati degli ultimi cinque giorni senza richiedere spazio aggiuntivo per il ripristino.


#### <a name="example-2"></a>Esempio 2
Nell'array virtuale si vuole poter eseguire queste operazioni

* Effettuare il provisioning di un volume a livelli di 2 TB.
* Effettuare il provisioning di un volume aggiunto in locale di 300 GB.

In base al 12% della prenotazione di spazio locale per i volumi o le condivisioni a livelli e al 10% per i volumi o le condivisioni aggiunte in locale, sono necessari

* Prenotazione locale di 240 GB (per un volume o una condivisione a livelli di 2 TB).
* 330 GB per un volume o una condivisione aggiunti in locale (con l'aggiunta del 10% della prenotazione locale ai 300 GB dello spazio con provisioning)

Lo spazio totale richiesto a livello locale è: 240 GB + 330 GB + 570 GB

Lo spazio locale minimo necessario per il ripristino è 330 GB.

Il 15% del disco totale viene usato per archiviare gli snapshot, perciò è disponibile solo lo 0,85. Le dimensioni del disco sono quindi (900&ast;(1/0,85)) = 1,06 TB ~ 1,25 TB (con arrotondamento al quartile più vicino)

Considerando l'eventuale crescita imprevista, è consigliabile effettuare il provisioning di un disco locale di 1,25-1,5 TB.

### <a name="group-policy"></a>Criteri di gruppo
Criteri di gruppo è un'infrastruttura che consente di implementare configurazioni specifiche per utenti e computer. Le impostazioni di Criteri di gruppo sono contenute in oggetti Criteri di gruppo collegati ai contenitori di Servizi di dominio Active Directory seguenti: siti, domini o unità organizzative (OU). 

Se l'array virtuale è aggiunto al dominio, è possibile applicarvi oggetti Criteri di gruppo. Questi oggetti Criteri di gruppo possono installare applicazioni, ad esempio un software antivirus che può influire negativamente sul funzionamento dell'array virtuale StorSimple.

È quindi consigliabile:

* Assicurarsi che l'array virtuale sia nella propria unità organizzativa (OU) per Active Directory.
* Assicurarsi che nessun oggetto Criteri di gruppo venga applicato all'array virtuale. È possibile bloccare l'ereditarietà per assicurarsi che l'array virtuale (nodo figlio) non erediti automaticamente alcun oggetto Criteri di gruppo dall'oggetto padre. Per altre informazioni, vedere [Bloccare l'ereditarietà](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Rete
La configurazione di rete per l'array virtuale viene eseguita tramite l'interfaccia utente Web locale. Un'interfaccia di rete virtuale è abilitata tramite l'hypervisor in cui viene effettuato il provisioning dell'array virtuale. Usare la pagina [Impostazioni di rete](storsimple-virtual-array-deploy3-fs-setup.md) per configurare l'indirizzo IP, la subnet e il gateway dell'interfaccia di rete virtuale.  È inoltre possibile configurare il server DNS primario e secondario, le impostazioni dell'ora e le impostazioni proxy facoltative per il dispositivo. La maggior parte della configurazione di rete è un'operazione singola. Prima di distribuire l'array virtuale, vedere la sezione [Requisiti di rete](storsimple-ova-system-requirements.md#networking-requirements) per StorSimple.

Quando si distribuisce l'array virtuale, è consigliabile seguire queste procedure consigliate:

* Assicurarsi che la rete in cui viene distribuito l'array virtuale abbia sempre la capacità di dedicare una larghezza di banda Internet di almeno 5 Mbps.
  
  * La larghezza di banda Internet necessaria varia a seconda delle caratteristiche del carico di lavoro e della frequenza di modifica dei dati.
  * La modifica di dati che può essere gestita è direttamente proporzionale alla larghezza di banda Internet. Ad esempio, quando si esegue un backup, una larghezza di banda 5 Mbps può supportare una modifica dei dati di circa 18 GB in 8 ore. Con una larghezza di banda quattro volte maggiore (20 Mbps), è possibile gestire una modifica dei dati quadrupla (72 GB).
* Verificare che la connettività Internet sia sempre disponibile. Le connessioni Internet sporadiche o non affidabili ai dispositivi possono comportare la perdita di accesso ai dati nel cloud e di conseguenza una configurazione non supportata.
* Se si prevede di distribuire il dispositivo come server iSCSI:
  
  * È consigliabile disabilitare l'opzione **Ottenere automaticamente l'indirizzo IP** (DHCP).
  * Configurare gli indirizzi IP statici. È necessario configurare un server DNS primario e secondario.
  * Se si definiscono più interfacce di rete nell'array virtuale, solo la prima interfaccia di rete, per impostazione predefinita **Ethernet**, può connettersi al cloud. Per controllare il tipo di traffico, è possibile creare più interfacce di rete virtuali nell'array virtuale, configurato come server iSCSI, e connettere queste interfacce a subnet diverse.
* Per limitare solo la larghezza di banda cloud, usata dall'array virtuale, configurare la limitazione nel router o nel firewall. Se si definisce la limitazione nell'hypervisor, saranno limitati tutti i protocolli inclusi iSCSI e SMB invece della sola larghezza di banda cloud.
* Assicurarsi che sia abilitata la sincronizzazione dell'ora per hypervisor. Se si usa Hyper-V, selezionare l'array virtuale nella console di gestione di Hyper-V, passare a **Impostazioni &gt; Servizi di integrazione** e assicurarsi che **Sincronizzazione ora** sia selezionata.

### <a name="storage-accounts"></a>Account di archiviazione
L'array virtuale StorSimple può essere associato a un singolo account di archiviazione. Questo account di archiviazione può essere un account di archiviazione generato automaticamente, un account nella stessa sottoscrizione del servizio o un account di archiviazione correlato a un'altra sottoscrizione. Per altre informazioni, vedere come [gestire gli account di archiviazione per l'array virtuale](storsimple-virtual-array-manage-storage-accounts.md).

Usare le raccomandazioni seguenti per gli account di archiviazione associati all'array virtuale.

* Quando si collegano più array virtuali a un singolo account di archiviazione, tenere conto della capacità massima (64 TB) per un array virtuale e delle dimensioni massime (500 TB) per un account di archiviazione. Questo limita a circa 7 il numero di array virtuali di dimensioni intere che è possibile associare a tale account di archiviazione.
* Quando creare un nuovo account di archiviazione
  
  * È consigliabile crearlo nell'area più vicina all'ufficio remoto o alla succursale in cui viene distribuito l'array virtuale StorSimple per ridurre al minimo le latenze.
  * Tenere presente che non è possibile spostare un account di archiviazione tra aree diverse. Non è inoltre possibile spostare un servizio tra sottoscrizioni.
  * Usare un account di archiviazione che implementa la ridondanza tra i data center. L'archiviazione con ridondanza geografica, l'archiviazione con ridondanza della zona e l'archiviazione con ridondanza locale sono tutte supportate per l'uso con l'array virtuale. Per altre informazioni sui diversi tipi di account di archiviazione, vedere [Replica di Archiviazione di Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Condivisioni e volumi
Per l'array virtuale StorSimple è possibile effettuare il provisioning di condivisioni quando è configurato come un file server e di volumi quando è configurato come server iSCSI. Le procedure consigliate per la creazione di condivisioni e volumi sono correlate alle dimensioni e al tipo configurato.

#### <a name="volumeshare-size"></a>Dimensioni di volumi o condivisioni
Nell'array virtuale è possibile effettuare il provisioning di condivisioni quando è configurato come un file server e di volumi quando è configurato come server iSCSI. Le procedure consigliate per la creazione di condivisioni e volumi sono correlate alle dimensioni e al tipo configurato. 

Tenere presenti le procedure consigliate seguenti quando si effettua il provisioning di condivisioni o volumi nel dispositivo virtuale.

* Le dimensioni dei file in relazione alle dimensioni con cui è stato effettuato il provisioning di una condivisione a livelli possono compromettere le prestazioni della suddivisione in livelli. L'utilizzo di file di grandi dimensioni può comportare il rallentamento della suddivisione in livelli. Durante l'uso di file di grandi dimensioni, è consigliabile che il file più grande sia più piccolo del 3% rispetto alle dimensioni della condivisione.
* Nell'array virtuale è possibile creare un massimo di 16 volumi o condivisioni. Per i limiti delle dimensioni dei volumi o delle condivisioni a livelli e aggiunti localmente, fare riferimento sempre ai [limiti di array virtuale StorSimple](storsimple-ova-limits.md).
* Quando si crea un volume, tenere conto anche dell'utilizzo di dati previsto, nonché della crescita futura. Il volume non può essere espanso in un secondo momento.
* Dopo aver creato il volume, non sarà possibile ridurne le dimensioni in StorSimple.
* Quando si scrive in un volume a livelli in StorSimple, le operazioni di I/O vengono limitate quando i dati del volume raggiungono una determinata soglia, relativamente allo spazio locale riservato per il volume. Se si continua a scrivere in questo volume, le operazioni di I/O rallentano in modo significativo. Anche se è possibile scrivere in un volume a livelli oltre la capacità di cui è stato effettuato il provisioning (non viene attivamente impedito all'utente di scrivere oltre tale capacità), viene visualizzato un messaggio di notifica per segnalare il superamento della sottoscrizione. Dopo aver visualizzato l'avviso, è fondamentale adottare misure correttive, ad esempio eliminare i dati del volume. L'espansione di un volume non è attualmente supportata.
* Per i casi d'uso di ripristino di emergenza, poiché il numero di condivisioni o volumi consentiti è pari a 16 e il numero massimo di condivisioni o volumi che possono essere elaborati in parallelo è 16, il numero di condivisioni o volumi non ha un impatto su RPO e RTO.

#### <a name="volumeshare-type"></a>Tipo di volumi o condivisioni
StorSimple supporta due tipi di volumi o condivisioni in base all'utilizzo: aggiunta in locale e a livelli. Per i volumi o le condivisioni aggiunte in locale viene effettuato il thick provisioning, mentre per i volumi o le condivisioni a livelli viene effettuato il thin provisioning. Dopo la creazione, non è possibile convertire una condivisione o un volume aggiunto in locale nel tipo a livelli o *viceversa*.

È consigliabile implementare le procedure consigliate seguenti quando si configurano volumi o condivisioni di StorSimple:

* Prima di creare un volume, identificare il tipo di volume in base ai carichi di lavoro che si intende distribuire. Usare volumi aggiunti in locale per i carichi di lavoro che richiedono garanzie locali per i dati, anche durante un'interruzione del servizio cloud, e latenze cloud basse. Dopo aver creato un volume nell'array virtuale, non è possibile modificare il tipo di volume da aggiunto in locale al tipo a livelli o *viceversa*. Ad esempio, creare volumi aggiunti in locale quando si distribuiscono carichi di lavoro SQL o carichi di lavoro che ospitano macchine virtuali e usare invece volumi a livelli per carichi di lavoro di condivisione file.
* Quando si utilizzano file di dimensioni elevate, selezionare l'opzione per i dati di archiviazione usati con minore frequenza. Quando si abilita questa opzione per accelerare il trasferimento dei dati nel cloud, vengono usate dimensioni del blocco di deduplicazione maggiori pari a 512 KB.

#### <a name="volume-format"></a>Formato del volume
Dopo aver creato i volumi StorSimple nel server iSCSI, è necessario inizializzarli, montarli e formattarli. Questa operazione viene eseguita nell'host connesso al dispositivo StorSimple. Per montare e formattare i volumi nell'host StorSimple, è opportuno seguire queste procedure consigliate.

* Eseguire una formattazione veloce in tutti i volumi StorSimple.
* Quando si formatta un volume StorSimple, usare dimensioni dell'unità di allocazione di 64 KB. Il valore predefinito è 4 KB. Le dimensioni dell'unità di allocazione di 64 KB sono basate su test eseguiti internamente per carichi di lavoro StorSimple comuni e altri carichi di lavoro.
* Quando si usa l'array virtuale StorSimple configurato come server iSCSI, non usare volumi con spanning o i dischi dinamici perché questi volumi o dischi non sono supportati da StorSimple.

#### <a name="share-access"></a>Accesso alle condivisioni
Quando si creano condivisioni nel file server dell'array virtuale, seguire queste linee guida:

* Quando si crea una condivisione, assegnare un gruppo di utenti come amministratore della condivisione, invece di un singolo utente.
* Dopo aver creato la condivisione, è possibile gestire le autorizzazioni NTFS modificando le condivisioni in Esplora risorse.

#### <a name="volume-access"></a>Accesso ai volumi
Quando si configurano i volumi iSCSI nell'array virtuale StorSimple, è importante controllare l'accesso dove necessario. Per determinare quali server host possono accedere ai volumi, creare e associare i record di controllo di accesso (ACR) ai volumi StorSimple.

Usare le procedure consigliate seguenti quando si configurano ACR per i volumi StorSimple:

* Associare sempre almeno un ACT a un volume.

* Quando si assegnano più ACR a un volume, assicurarsi che il volume non sia esposto in modo tale da consentire l'accesso contemporaneo da parte di più host non cluster. Se sono stati assegnato più ACR a un volume, viene visualizzato un messaggio di avviso che richiede di verificare la configurazione.

### <a name="data-security-and-encryption"></a>Sicurezza e crittografia dei dati
L'array virtuale StorSimple include funzionalità di sicurezza e crittografia dei dati che assicurano la riservatezza e l'integrità dei dati. Quando si usano queste funzionalità, è opportuno seguire queste procedure consigliate: 

* Definire una chiave di crittografia per l'archiviazione cloud per generare la crittografia AES-256 prima dell'invio dei dati dall'array virtuale al cloud. Questa chiave non è necessaria se i dati sono crittografati inizialmente. La chiave può essere generata e protetta usando un sistema di gestione delle chiavi, ad esempio l' [insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-whatis.md).
* Quando si configura l'account di archiviazione tramite il servizio StorSimple Manager, assicurarsi di abilitare la modalità SSL per creare un canale sicuro per la comunicazione di rete tra il dispositivo StorSimple e il cloud.
* Rigenerare periodicamente le chiavi per gli account di archiviazione, mediante l'accesso al servizio di archiviazione di Azure, per tenere conto delle eventuali modifiche relative all'accesso in base alle modifiche apportate all'elenco di amministratori.
* I dati nell'array virtuale vengono compressi e deduplicati prima di essere inviati ad Azure. Non è consigliabile usare il servizio ruolo Deduplicazione dati nell'host Windows Server.

## <a name="operational-best-practices"></a>Procedure operative consigliate
Le procedure operative consigliate sono linee guida che è opportuno seguire durante la gestione o il funzionamento quotidiano dell'array virtuale. Queste procedure riguardano attività di gestione specifiche, ad esempio l'esecuzione di backup, il ripristino da un set di backup, l'esecuzione di un failover, la disattivazione e l'eliminazione dell'array, l'utilizzo del sistema di monitoraggio e l'integrità e l'esecuzione di analisi antivirus nell'array virtuale.

### <a name="backups"></a>Backup
Il backup dei dati nell'array virtuale viene eseguito nel cloud in due modi, un backup giornaliero automatico predefinito dell'intero dispositivo che inizia alle 22.30 o un backup manuale su richiesta. Per impostazione predefinita, il dispositivo crea automaticamente snapshot cloud giornalieri di tutti i dati che contiene. Per altre informazioni, vedere [Backup dell'array virtuale StorSimple](storsimple-virtual-array-backup.md).

Non è possibile modificare le impostazioni di frequenza e conservazione associate ai backup predefiniti, ma si può configurare l'ora in cui vengono avviati ogni giorno i backup giornalieri. Quando si configura l'ora di inizio dei backup automatizzati, è consigliabile:

* Pianificare i backup per le ore non di punta. L'ora di inizio del backup non deve coincidere con numerose operazioni di I/O dell'host.
* Avviare un backup manuale su richiesta quando si prevede di eseguire un failover del dispositivo o prima della finestra di manutenzione, per proteggere i dati nell'array virtuale.

### <a name="restore"></a>Ripristino
È possibile eseguire il ripristino da un set di backup in due modi: eseguire il ripristino in un altro volume o un'altra condivisione file o eseguire un ripristino a livello di elemento, disponibile solo in un array virtuale configurato come file server. Il ripristino a livello di elemento consente un ripristino granulare di file e cartelle da un backup cloud di tutte le condivisioni nel dispositivo StorSimple. Per altre informazioni, vedere [Ripristinare da un backup dell'array virtuale StorSimple](storsimple-virtual-array-clone.md).

Quando si esegue un ripristino, tenere presenti queste linee guida:

* L'array virtuale StorSimple non supporta il ripristino sul posto. Si può tuttavia farlo facilmente con un processo in due passaggi: liberare spazio nell'array virtuale e quindi ripristinarlo in un altro volume o un'altra condivisione.
* Quando si esegue il ripristino da un volume locale, tenere presente che sarà un'operazione a esecuzione prolungata. Anche se il volume può tornare rapidamente online, i dati continuano a essere idratati in background.
* Il tipo di volume rimane invariato durante il processo di ripristino. Un volume a livelli viene ripristinato in un altro volume a livelli e un volume aggiunto in locale in un altro volume aggiunto in locale.
* Quando si tenta di ripristinare un volume o una condivisione da un set di backup, anche se il processo di ripristino non riesce, è comunque possibile creare nel portale un volume o una condivisione di destinazione. È importante eliminare questo volume o questa condivisione di destinazione inutilizzata nel portale, per ridurre al minimo eventuali problemi futuri causati da questo elemento.

### <a name="failover-and-disaster-recovery"></a>Failover e ripristino di emergenza
Il failover di un dispositivo consente di eseguire la migrazione dei dati da un dispositivo di *origine* nel data center a un altro dispositivo di *destinazione* situato nella stessa località geografica o in una diversa. Il failover del dispositivo interessa tutto il dispositivo. Durante il failover, la proprietà dei dati del cloud passa dal dispositivo di origine al dispositivo di destinazione.

Per l'array virtuale StorSimple è possibile eseguire il failover solo in un altro array virtuale gestito dallo stesso servizio StorSimple Manager. Non è consentito eseguire un failover in un dispositivo serie 8000 o un array gestito da un servizio StorSimple Manager diverso da quello del dispositivo di origine. Per altre considerazioni sul failover, vedere i [prerequisiti per il failover del dispositivo](storsimple-virtual-array-failover-dr.md).

Quando si esegue un failover per l'array virtuale, tenere presente quanto segue:

* Per un failover pianificato è consigliabile portare offline tutti i volumi o le condivisioni prima di avviare il failover. Seguire le istruzioni specifiche del sistema operativo per portare offline i volumi o le condivisioni prima nell'host e quindi nel dispositivo virtuale.
* Per il ripristino di emergenza di un file server, è consigliabile aggiungere il dispositivo di destinazione allo stesso dominio del dispositivo di origine, in modo da risolvere automaticamente le autorizzazioni di condivisione. In questa versione è supportato solo il failover su un dispositivo di destinazione nello stesso dominio.
* Al termine del ripristino di emergenza, il dispositivo di origine viene eliminato automaticamente. Anche se il dispositivo non è più disponibile, la macchina virtuale sottoposta a provisioning nel sistema host usa ancora risorse. È consigliabile eliminare la macchina virtuale dal sistema host per evitare l'accumularsi dei costi.
* Si noti che anche se il failover non riesce, **i dati sono sempre al sicuro nel cloud**. Prendere in considerazione i tre scenari seguenti in cui il failover non è stato completato correttamente:
  
  * Si è verificato un errore nelle fasi iniziali del failover, ad esempio quando vengono eseguite le verifiche preliminari del ripristino di emergenza. In questo caso, il dispositivo di destinazione è ancora utilizzabile. È possibile riprovare il failover nello stesso dispositivo di destinazione.
  * Si è verificato un errore durante il processo di failover effettivo. In questo caso, il dispositivo di destinazione è contrassegnato come inutilizzabile. È necessario eseguire il provisioning e configurare un altro array virtuale di destinazione e usarlo per il failover.
  * Il failover è stato completato, dopo di che il dispositivo di origine è stato eliminato, ma il dispositivo di destinazione presenta problemi e non è possibile accedere ai dati. I dati sono comunque al sicuro nel cloud e possono essere facilmente recuperati creando un altro array virtuale, che potrà quindi essere usato come un dispositivo di destinazione per il ripristino di emergenza.

### <a name="deactivate"></a>Disattivare
Quando si disattiva un array virtuale StorSimple, si interrompe la connessione tra il dispositivo e il servizio StorSimple Manager corrispondente. La disattivazione è un'operazione **permanente** e non può essere annullata. Un dispositivo disattivato non può essere registrato di nuovo con il servizio StorSimple Manager. Per altre informazioni, vedere [Disattivare ed eliminare un dispositivo StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Quando si disattiva l'array virtuale, tenere in considerazione le procedure consigliate seguenti:

* Creare uno snapshot cloud di tutti i dati prima di disattivare un dispositivo virtuale. Quando si disattiva un array virtuale, tutti i dati nel dispositivo locale vanno persi. La creazione di uno snapshot consentirà di recuperare tutti i dati in una fase successiva.
* Prima di disattivare un array virtuale StorSimple, assicurarsi di arrestare o eliminare i client e gli host che dipendono da tale dispositivo.
* Eliminare un dispositivo disattivato se non lo si usa più, in modo da evitare l'accumularsi di addebiti.

### <a name="monitoring"></a>Monitoraggio
Per assicurarsi che l'array virtuale StorSimple sia costantemente in uno stato integro, è necessario monitorarlo e assicurarsi di ricevere informazioni dal sistema, inclusi gli avvisi. Per monitorare l'integrità complessiva dell'array virtuale, implementare le procedure consigliate seguenti:

* Configurare il monitoraggio per tenere traccia dell'utilizzo del disco, sia del disco dati dell'array virtuale sia di quello del sistema operativo. Se è in esecuzione Hyper-V, è possibile usare una combinazione di System Center Virtual Machine Manager (SCVMM) e System Center Operations Manager (SCOM) per monitorare gli host di virtualizzazione.
* Configurare notifiche tramite posta elettronica nell'array virtuale per l'invio di avvisi in presenza di determinati livelli di utilizzo.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Applicazioni di analisi antivirus e ricerca nell'indice
Un array virtuale StorSimple può creare automaticamente livelli di dati dal livello locale al cloud di Microsoft Azure. Quando si usa un'applicazione, ad esempio per la ricerca nell'indice o l'analisi antivirus, per analizzare i dati archiviati in StorSimple, è necessario fare attenzione che non venga eseguito l'accesso ai dati nel cloud e non ne venga effettuato il pull nel livello locale.

Quando si configurano la scansione di virus o ricerca nell'indice nell'array virtuale, è opportuno implementare le procedure consigliate seguenti:

* Disabilitare qualsiasi operazione di analisi completa configurata automaticamente.
* Per i volumi a livelli configurare l'applicazione di analisi antivirus o di ricerca dell'indice per l'esecuzione di una ricerca incrementale. Questa impostazione consente di analizzare solo i nuovi dati che probabilmente risiedono nel livello locale. Durante un'operazione incrementale non viene eseguito l'accesso ai dati a livelli nel cloud.
* Assicurarsi di avere configurato le impostazioni e i filtri di ricerca corretti in modo che vengano analizzati solo i tipi di file desiderati. Ad esempio, file di immagine (JPEG, GIF e TIFF) e disegni di progettazione non devono essere analizzati durante la ricompilazione dell'indice completa o incrementale.

Se si usa il processo di indicizzazione di Windows, seguire queste linee guida:

* Non usare l'indicizzatore di Windows per i volumi a livelli, perché richiama grandi quantità di dati (TB) dal cloud se l'indice deve essere ricompilato frequentemente. La ricompilazione dell'indice recupera tutti i tipi di file per indicizzarne il contenuto.
* Usare il processo di indicizzazione di Windows per i volumi aggiunti in locale, perché per compilare l'indice verrà eseguito l'accesso solo ai dati nei livelli locali, non ai dati cloud.

### <a name="byte-range-locking"></a>Blocco di intervalli di byte
Le applicazioni possono bloccare un intervallo di byte specificato all'interno dei file. Se per le applicazioni che scrivono in StorSimple è abilitato il blocco di intervalli di byte, la suddivisione in livelli non funziona nell'array virtuale. Perché la suddivisione in livelli funzioni, tutte le aree del file a cui viene eseguito l'accesso devono essere sbloccate. Il blocco di intervalli di byte non è supportato con i volumi a livelli nell'array virtuale.

Ecco alcune misure consigliate per mitigare gli effetti del blocco di intervalli di byte:

* Disattivare il blocco dell'intervallo di byte nella logica dell'applicazione.
* Usare volumi aggiunti in locale, invece che a livelli, per i dati associati all'applicazione. I volumi aggiunti in locale non vengono suddivisi in livelli nel cloud.
* Quando si usano volumi aggiunti in locale con abilitato il blocco di intervalli di byte, è possibile che il volume torni online prima del completamento del ripristino. In questi casi, è necessario attendere che il ripristino venga completato.

## <a name="multiple-arrays"></a>Più array
Può essere necessario distribuire più array virtuali per tenere conto di un working set di dati crescente che può comportare la distribuzione nel cloud, influendo quindi sulle prestazioni del dispositivo. In questi casi, è consigliabile ridimensionare i dispositivi man mano che il working set cresce. Ciò richiede l'aggiunta di uno o più dispositivi nel data center locale. Quando si aggiungono i dispositivi, è possibile:

* Suddividere il set di dati corrente.
* Distribuire nuovi carichi in nuovi dispositivi.
* Se si distribuiscono più array virtuali, dal punto di vista del bilanciamento del carico è consigliabile distribuire l'array in host hypervisor diversi.
* Più array virtuali, se configurati come file server o server iSCSI, possono essere distribuiti in uno spazio dei nomi del file system distribuito. Per i passaggi dettagliati, vedere [Distributed File System Namespace Solution with Hybrid Cloud Storage Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507)(Guida alla distribuzione di una soluzione per lo spazio dei nomi del file system distribuito con archiviazione cloud ibrida). L'uso di Replica DFS con l'array virtuale non è attualmente consigliato. 

## <a name="see-also"></a>Vedere anche
Informazioni su come [amministrare l'array virtuale StorSimple](storsimple-virtual-array-manager-service-administration.md) usando il servizio StorSimple Manager.


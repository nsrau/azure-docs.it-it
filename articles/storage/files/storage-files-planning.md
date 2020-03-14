---
title: Pianificazione per la distribuzione dei File di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b5bf5cc5c44226236f39a6e32c33ebe346e36eeb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269016"
---
# <a name="planning-for-an-azure-files-deployment"></a>Pianificazione per la distribuzione dei file di Azure
[File di Azure](storage-files-introduction.md) può essere distribuito in due modi principali: montando direttamente le condivisioni file di Azure senza server o memorizzando nella cache le condivisioni file di Azure in locale usando sincronizzazione file di Azure. L'opzione di distribuzione scelta cambia gli elementi che è necessario prendere in considerazione durante la pianificazione della distribuzione. 

- **Montaggio diretto di una condivisione file di Azure**: poiché file di Azure fornisce l'accesso SMB, è possibile montare condivisioni file di Azure in locale o nel cloud usando il client SMB standard disponibile in Windows, MacOS e Linux. Poiché le condivisioni file di Azure sono senza server, la distribuzione per gli scenari di produzione non richiede la gestione di un dispositivo file server o NAS. Ciò significa che non è necessario applicare patch software o scambiare dischi fisici. 

- **Memorizzare nella cache la condivisione file di Azure in locale con sincronizzazione file di Azure**: sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in file di Azure, garantendo al tempo stesso la flessibilità, le prestazioni e la compatibilità di un file server locale. Sincronizzazione file di Azure trasforma un server Windows locale (o cloud) in una cache rapida della condivisione file di Azure. 

Questo articolo illustra principalmente le considerazioni sulla distribuzione per la distribuzione di una condivisione file di Azure da montare direttamente da un client locale o cloud. Per pianificare una distribuzione di Sincronizzazione file di Azure, vedere [pianificazione di una distribuzione di sincronizzazione file di Azure](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Concetti relativi alla gestione
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Quando si distribuiscono condivisioni file di Azure in account di archiviazione, è consigliabile:

- Distribuire solo le condivisioni file di Azure in account di archiviazione con altre condivisioni file di Azure. Sebbene gli account di archiviazione GPv2 consentano di disporre di account di archiviazione per utilizzo misto, poiché le risorse di archiviazione come le condivisioni file di Azure e i contenitori BLOB condividono i limiti dell'account di archiviazione, la combinazione di risorse può rendere più difficile la risoluzione dei problemi problemi di prestazioni in un secondo momento. 

- Prestare attenzione alle limitazioni IOPS di un account di archiviazione durante la distribuzione di condivisioni file di Azure. Idealmente, è consigliabile eseguire il mapping delle condivisioni file 1:1 con gli account di archiviazione, ma ciò potrebbe non essere sempre possibile a causa di diversi limiti e restrizioni, sia dall'organizzazione che da Azure. Quando non è possibile avere una sola condivisione file distribuita in un account di archiviazione, valutare quali condivisioni saranno molto attive e quali condivisioni saranno meno attive per assicurarsi che le condivisioni file più calde non vengano inserite nello stesso account di archiviazione.

- Distribuire solo gli account GPv2 e filestorage e aggiornare utilizzo generico V1 e gli account di archiviazione classici quando vengono individuati nell'ambiente in uso. 

## <a name="identity"></a>Identità
Per accedere a una condivisione file di Azure, l'utente della condivisione file deve essere autenticato e avere l'autorizzazione per accedere alla condivisione. Questa operazione viene eseguita in base all'identità dell'utente che accede alla condivisione file. File di Azure si integra con tre provider di identità principali:
- **Active Directory di proprietà del cliente** (anteprima): gli account di archiviazione di Azure possono essere aggiunti a un dominio a un Active Directory di Windows Server di proprietà del cliente, proprio come un dispositivo windows server file server o NAS. Il controller di Dominio di Active Directory può essere distribuito in locale, in una macchina virtuale di Azure o anche come macchina virtuale in un altro provider di servizi cloud. File di Azure è indipendente dalla posizione in cui è ospitato il controller di dominio. Una volta che un account di archiviazione è stato aggiunto a un dominio, l'utente finale può montare una condivisione file con l'account utente che ha eseguito l'accesso al proprio PC con. L'autenticazione basata su Active Directory usa il protocollo di autenticazione Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS)** : Azure AD DS fornisce un Controller di dominio di Active Directory gestito da Microsoft che può essere usato per le risorse di Azure. Il dominio che aggiunge l'account di archiviazione a Azure AD DS offre vantaggi simili al dominio che lo aggiunge a una Active Directory di proprietà del cliente. Questa opzione di distribuzione è particolarmente utile per gli scenari di Lift-and-Shift per le applicazioni che richiedono autorizzazioni basate su AD. Poiché Azure AD DS fornisce l'autenticazione basata su Active Directory, questa opzione Usa anche il protocollo di autenticazione Kerberos.
- **Chiave account di archiviazione**di Azure: le condivisioni file di Azure possono essere montate anche con una chiave dell'account di archiviazione di Azure. Per montare una condivisione file in questo modo, viene usato il nome dell'account di archiviazione come nome utente e la chiave dell'account di archiviazione viene usata come password. L'uso della chiave dell'account di archiviazione per montare la condivisione file di Azure è in realtà un'operazione di amministratore, poiché la condivisione file montata disporrà di autorizzazioni complete per tutti i file e le cartelle della condivisione, anche se contengono ACL. Quando si usa la chiave dell'account di archiviazione per il montaggio tramite SMB, viene usato il protocollo di autenticazione NTLMv2.

Per i clienti che eseguono la migrazione da file server locali o per la creazione di nuove condivisioni file in File di Azure progettati per comportarsi come i file server Windows o i dispositivi NAS, il dominio che aggiunge l'account di archiviazione al **Active Directory di proprietà del cliente** è l'opzione consigliata. Per ulteriori informazioni sul dominio che aggiunge il proprio account di archiviazione a un Active Directory di proprietà del cliente, vedere [File di Azure Active Directory Overview](storage-files-active-directory-overview.md).

Se si intende usare la chiave dell'account di archiviazione per accedere alle condivisioni file di Azure, è consigliabile usare gli endpoint di servizio come descritto nella sezione [rete](#networking) .

## <a name="networking"></a>Rete
Le condivisioni file di Azure sono accessibili da qualsiasi posizione tramite l'endpoint pubblico dell'account di archiviazione. Ciò significa che le richieste autenticate, ad esempio le richieste autorizzate dall'identità di accesso di un utente, possono avere origine in modo sicuro all'interno o all'esterno di Azure. In molti ambienti del cliente, un montaggio iniziale della condivisione file di Azure nella workstation locale avrà esito negativo, anche se i montaggi dalle macchine virtuali di Azure hanno esito positivo. Il motivo è che molte organizzazioni e provider di servizi Internet (ISP) bloccano la porta utilizzata da SMB per comunicare, ovvero la porta 445. Passare a [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare un riepilogo degli ISP in grado di consentire o proibire l'accesso dalla porta 445.

Per sbloccare l'accesso alla condivisione file di Azure, sono disponibili due opzioni principali:

- Sbloccare la porta 445 per la rete locale dell'organizzazione. È possibile accedere alle condivisioni file di Azure solo tramite l'endpoint pubblico usando protocolli Internet sicuri, ad esempio SMB 3,0 e l'API filerest. Questo è il modo più semplice per accedere alla condivisione file di Azure dall'ambiente locale perché non richiede una configurazione di rete avanzata oltre a modificare le regole della porta in uscita dell'organizzazione, ma è consigliabile rimuovere le versioni legacy e deprecate di SMB protocollo, ovvero SMB 1,0. Per informazioni su come eseguire questa operazione, vedere [protezione di Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e [protezione di Linux](storage-how-to-use-files-linux.md#securing-linux).

- Accedere alle condivisioni file di Azure tramite una connessione VPN o ExpressRoute. Quando si accede alla condivisione file di Azure tramite un tunnel di rete, è possibile montare la condivisione file di Azure come una condivisione file locale perché il traffico SMB non attraversa i limiti dell'organizzazione.   

Sebbene dal punto di vista tecnico, è molto più semplice montare le condivisioni file di Azure tramite l'endpoint pubblico, si prevede che la maggior parte dei clienti scelga di montare le condivisioni file di Azure tramite una connessione VPN o ExpressRoute. A tale scopo, sarà necessario configurare quanto segue per l'ambiente:  

- **Tunneling di rete con ExpressRoute, da sito a sito o VPN da punto a sito**: il tunneling in una rete virtuale consente l'accesso alle condivisioni file di Azure dall'ambiente locale, anche se la porta 445 è bloccata.
- **Endpoint privati**: gli endpoint privati assegnano all'account di archiviazione un indirizzo IP dedicato dallo spazio degli indirizzi della rete virtuale. Questo consente il tunneling di rete senza la necessità di aprire reti locali fino a tutti gli intervalli di indirizzi IP di proprietà dei cluster di archiviazione di Azure. 
- **Inoltri DNS**: configurare il DNS locale per risolvere il nome dell'account di archiviazione (ad esempio `storageaccount.file.core.windows.net` per le aree del cloud pubblico) per la risoluzione nell'indirizzo IP degli endpoint privati.

Per pianificare la rete associata alla distribuzione di una condivisione file di Azure, vedere [file di Azure considerazioni sulla rete](storage-files-networking-overview.md).

## <a name="encryption"></a>Crittografia
File di Azure supporta due tipi diversi di crittografia, ovvero la crittografia in transito, che è correlata alla crittografia usata per il montaggio o l'accesso alla condivisione file di Azure e la crittografia dei dati inattivi, che fa riferimento alla modalità di crittografia dei dati quando vengono archiviati su disco. 

### <a name="encryption-in-transit"></a>Crittografia in transito
Per impostazione predefinita, la crittografia in transito è abilitata per tutti gli account di archiviazione di Azure. Quindi, quando si monta una condivisione file su SMB o vi si accede tramite il protocollo FileREST (ad esempio tramite il portale di Azure, PowerShell/interfaccia della riga di comando o Azure SDK), File di Azure consentirà la connessione solo se viene eseguita con SMB 3.0 o versione successiva con crittografia oppure HTTPS. I client che non supportano SMB 3.0 oppure che supportano SMB 3.0 ma non la crittografia SMB non potranno montare la condivisione file di Azure se è abilitata la crittografia in transito. Per altre informazioni sui sistemi operativi che supportano SMB 3.0 con crittografia, vedere la documentazione dettagliata per [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md). Tutte le versioni correnti di PowerShell, interfaccia della riga di comando e SDK supportano HTTPS.  

È possibile disabilitare la crittografia in transito per un account di archiviazione di Azure. Quando la crittografia è disabilitata, File di Azure consentirà anche SMB 2,1, SMB 3,0 senza crittografia e chiamate API filerest non crittografate su HTTP. La crittografia in transito viene in genere disabilitata principalmente per supportare un'applicazione legacy che deve essere eseguita in un sistema operativo meno recente, ad esempio Windows Server 2008 R2 o una distribuzione Linux precedente. File di Azure consente connessioni SMB 2.1 solo all'interno della stessa area di Azure della condivisione file di Azure. Un client SMB 2.1 situato all'esterno dell'area di Azure in cui si trova la condivisione file di Azure, ad esempio in locale o in un'area di Azure differente, non potrà accedere alla condivisione file.

Si consiglia vivamente di garantire che la crittografia dei dati in transito sia abilitata.

Per altre informazioni sulla crittografia in transito, vedere [Richiedere il trasferimento sicuro in Archiviazione di Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Livelli di archiviazione
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

In generale, le funzionalità di File di Azure e l'interoperabilità con altri servizi sono le stesse tra le condivisioni file Premium e le condivisioni file standard, ma esistono alcune importanti differenze:
- **Modello di fatturazione**
    - Le condivisioni file Premium vengono fatturate usando un modello di fatturazione con provisioning, ovvero si paga la quantità di spazio di archiviazione di cui si esegue il provisioning invece della quantità di spazio di archiviazione effettivamente richiesta. 
    - Le condivisioni file standard vengono fatturate usando un modello con pagamento in base al consumo, che include un costo di archiviazione di base per la quantità di spazio di archiviazione effettivamente utilizzata e quindi un costo aggiuntivo per le transazioni in base alla modalità di utilizzo della condivisione. Con le condivisioni file standard, la fattura aumenterà se si usa (lettura/scrittura/montaggio) la condivisione file di Azure.
- **Opzioni di ridondanza**
    - Le condivisioni file Premium sono disponibili solo per l'archiviazione con ridondanza locale (con ridondanza locale) e con ridondanza della zona (ZRS). 
    - Le condivisioni file standard sono disponibili per l'archiviazione con ridondanza locale, con ridondanza della zona, con ridondanza geografica (GRS) e con ridondanza geografica (GZRS).
- **Dimensioni massime della condivisione file**
    - È possibile eseguire il provisioning delle condivisioni file Premium per un massimo di 100 TiB senza ulteriori operazioni.
    - Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, sebbene il limite di condivisione possa essere aumentato a 100 TiB scegliendo il flag di funzionalità dell'account di archiviazione per la *condivisione file di grandi dimensioni* . Le condivisioni file standard possono includere fino a 100 TiB solo per gli account di archiviazione con ridondanza locale o con ridondanza della zona. Per ulteriori informazioni sull'aumento  
- **Disponibilità internazionale**
    - Le condivisioni file Premium non sono disponibili in ogni area e il supporto con ridondanza della zona è disponibile in un subset più piccolo di aree. Per scoprire se le condivisioni file Premium sono attualmente disponibili nella propria area geografica, vedere la pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=storage) per Azure. Per informazioni sulle aree che supportano ZRS, vedere [supporto per la zona di disponibilità di Azure in base all'area](../../availability-zones/az-overview.md#services-support-by-region). Per aiutarci a classificare in ordine di priorità le nuove aree e le funzionalità del livello Premium, compila questo [sondaggio](https://aka.ms/pfsfeedback).
    - Le condivisioni file standard sono disponibili in ogni area di Azure.
- Azure Kubernetes Service (AKS) supporta le condivisioni file Premium nella versione 1,13 e successive.

Una volta creata una condivisione file come una condivisione file Premium o standard, non è possibile convertirla automaticamente nell'altro livello. Se si desidera passare all'altro livello, è necessario creare una nuova condivisione file in tale livello e copiare manualmente i dati dalla condivisione originale alla nuova condivisione creata. Per eseguire tale copia, è consigliabile usare `robocopy` per Windows o `rsync` per macOS e Linux.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Informazioni sul provisioning per le condivisioni file Premium
Il provisioning delle condivisioni file premium viene effettuato in base a un rapporto fisso tra GiB, operazioni di I/O al secondo e velocità effettiva. Per ogni GiB di cui viene effettuato il provisioning, alla condivisione viene assegnata un'operazione di I/O al secondo con 0,1 MiB/s di velocità effettiva fino ai limiti massimi per singola condivisione. Il valore minimo di provisioning consentito è 100 GiB con il minimo di operazioni di I/O al secondo e velocità effettiva.

In base ad approssimazioni ottimali, tutte le condivisioni possono essere potenziate fino a tre operazioni di I/O al secondo per ogni GiB di archiviazione di cui viene effettuato il provisioning per 60 minuti o più, a seconda della dimensione della condivisione. Le nuove condivisioni iniziano con il credito di burst totale in base alla capacità sottoposta a provisioning.

È necessario eseguire il provisioning delle condivisioni con incrementi di 1 GiB. La dimensione minima è 100 GiB, la dimensione successiva è 101 GiB e così via.

> [!TIP]
> IOPS Baseline = 1 * GiB con provisioning. (Fino a un massimo di 100.000 IOPS).
>
> Limite di espansione = 3 * IOPS Baseline. (Fino a un massimo di 100.000 IOPS).
>
> velocità in uscita = 60 MiB/s + 0,06 * GiB con provisioning
>
> velocità in ingresso = 40 MiB/s + 0,04 * GiB con provisioning

Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di La quota di condivisione può essere aumentata in qualsiasi momento, ma può essere ridotta solo dopo 24 ore dall'ultimo aumento. Dopo aver atteso 24 ore senza un aumento della quota, è possibile ridurre la quota di condivisione tutte le volte che si desidera, fino a quando non si aumenta nuovamente. Le modifiche alla scalabilità IOPS/velocità effettiva saranno effettive entro pochi minuti dopo la modifica delle dimensioni.

È possibile ridurre le dimensioni della condivisione di cui è stato effettuato il provisioning sotto il GiB usato. In tal caso, non si perderanno i dati, ma verranno comunque addebitate le dimensioni usate e si riceveranno le prestazioni (IOPS di base, velocità effettiva e IOPS di aumento) della condivisione di cui è stato effettuato il provisioning, non le dimensioni usate.

La tabella seguente illustra alcuni esempi di queste formule per le dimensioni della condivisione di cui è stato effettuato il provisioning:

|Capacità (GiB) | Operazioni di I/O al secondo di base | IOPS a impulsi | In uscita (MiB/s) | Ingresso (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Fino a 300     | 66   | 44   |
|500         | 500     | Fino a 1.500   | 90   | 60   |
|1\.024       | 1\.024   | Fino a 3.072   | 122   | 81   |
|5120       | 5120   | Fino a 15.360  | 368   | 245   |
|10.240      | 10.240  | Fino a 30.720  | 675 | 450   |
|33.792      | 33.792  | Fino a 100.000 | 2\.088 | 1\.392   |
|51.200      | 51.200  | Fino a 100.000 | 3\.132 | 2\.088   |
|102.400     | 100,000 | Fino a 100.000 | 6\.204 | 4\.136   |

> [!NOTE]
> Le prestazioni delle condivisioni file sono soggette a limiti di rete del computer, larghezza di banda di rete disponibile, dimensioni i/o, parallelismo, tra molti altri fattori. Ad esempio, in base a test interni con 8 dimensioni di i/o in lettura/scrittura, una singola macchina virtuale Windows, una *F16s_v2 standard*, connessa alla condivisione file Premium tramite SMB, potrebbe raggiungere 20.000 IOPS di lettura e 15.000 di operazioni di i/o in scrittura. Con le dimensioni di i/o in lettura/scrittura di 512 MiB, la stessa VM potrebbe ottenere 1,1 GiB/s in uscita e 370 MiB/s di velocità effettiva in ingresso. Per ottenere la massima scalabilità delle prestazioni, suddividere il carico tra più macchine virtuali. Consultare la [Guida alla risoluzione dei](storage-troubleshooting-files-performance.md) problemi relativi a problemi di prestazioni e soluzioni alternative comuni.

#### <a name="bursting"></a>Espansione nel
Le condivisioni file Premium possono aumentare il valore di IOPS fino a un fattore pari a tre. L'espansione è automatizzata e funziona in base a un sistema di credito. La funzione di espansione è basata sul massimo sforzo e il limite di espansione non è una garanzia, le condivisioni file possono *aumentare fino al* limite.

I crediti si accumulano in un bucket di espansione ogni volta che il traffico per la condivisione file è inferiore al valore di IOPS Baseline. Una condivisione GiB 100, ad esempio, ha 100 IOPS Baseline. Se il traffico effettivo nella condivisione è 40 IOPS per un intervallo di 1 secondo specifico, le operazioni di 60 i/o al secondo non usate vengono accreditate a un bucket di espansione. Questi crediti verranno quindi usati in seguito quando le operazioni superano il IOPs di base.

> [!TIP]
> Dimensioni del bucket di espansione = Baseline IOPS * 2 * 3600.

Ogni volta che una condivisione supera le operazioni di i/o al secondo per la baseline e ha crediti in un bucket di espansione, il produrrà Le condivisioni possono continuare a funzionare fino a quando i crediti restano, anche se le condivisioni inferiori a 50 TiB rimarranno al limite di un massimo di un'ora. Le condivisioni superiori a 50 TiB possono tecnicamente superare questo limite di un'ora, fino a due ore, ma si basa sul numero di crediti di espansione accumulati. Ogni i/o oltre il IOPS di base utilizza un credito e una volta che tutti i crediti vengono utilizzati, la condivisione tornerà a IOPS Baseline.

I crediti di condivisione hanno tre stati:

- Accumulo, quando la condivisione file usa un numero di IOPS inferiore a quello di base.
- In calo, quando la condivisione file è in fase di espansione.
- Costante rimanente, in cui non sono in uso crediti o IOPS di base.

Le nuove condivisioni file iniziano con il numero completo di crediti nel bucket di espansione. Se i valori di IOPS della condivisione scendono al di sotto della linea di base IOPS a causa della limitazione del server, i crediti di espansione non verranno acquisiti.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Consente di estendere le condivisioni file standard fino a 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilità internazionale
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Ridondanza
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrazione
In molti casi, non verrà stabilita una nuova condivisione file di rete per l'organizzazione, ma la migrazione di una condivisione file esistente da un dispositivo file server o NAS locale a File di Azure. Sono disponibili molti strumenti, forniti da Microsoft e da terze parti, per eseguire una migrazione a una condivisione file, ma possono essere suddivisi approssimativamente in due categorie:

- **Strumenti che gestiscono attributi file System come ACL e timestamp**:
    - **[Sincronizzazione file di Azure](storage-sync-files-planning.md)** : è possibile usare sincronizzazione file di Azure come metodo per inserire i dati in una condivisione file di Azure, anche quando la distribuzione finale desiderata non mantiene una presenza locale. Sincronizzazione file di Azure può essere installato sul posto in distribuzioni esistenti di Windows Server 2012 R2, Windows Server 2016 e Windows Server 2019. Un vantaggio dell'uso di Sincronizzazione file di Azure come meccanismo di inserimento è che gli utenti finali possono continuare a usare la condivisione file esistente. il taglio alla condivisione file di Azure può verificarsi dopo il completamento del caricamento di tutti i dati in background.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy è uno strumento di copia noto fornito con Windows e Windows Server. Robocopy può essere usato per trasferire i dati in File di Azure montando la condivisione file in locale e quindi usando il percorso montato come destinazione del comando Robocopy.

- **Strumenti che non gestiscono gli attributi file System**:
    - **Data Box**: data box fornisce un meccanismo di trasferimento dati offline per la spedizione fisica dei dati in Azure. Questo metodo è progettato per aumentare la velocità effettiva e ridurre la larghezza di banda, ma attualmente non supporta file system attributi come timestamp e ACL.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy è un'utilità della riga di comando progettata per copiare i dati da e verso File di Azure, oltre ad Archiviazione BLOB di Azure usando semplici comandi con prestazioni ottimali.

## <a name="next-steps"></a>Passaggi successivi
* [Planning for an Azure File Sync Deployment](storage-sync-files-planning.md) (Pianificazione della distribuzione di Sincronizzazione file di Azure)
* [Distribuzione di File di Azure](storage-files-deployment-guide.md)
* [Distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)

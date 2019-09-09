---
title: Pianificazione per la distribuzione dei File di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc532ed33fca5120736dfb9503d012b2877e675e
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806627"
---
# <a name="planning-for-an-azure-files-deployment"></a>Pianificazione per la distribuzione dei file di Azure

[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB standard di settore. Poiché File di Azure è completamente gestito, la sua distribuzione negli scenari di produzione è molto più semplice rispetto alla distribuzione e alla gestione di un file server o un dispositivo NAS. Questo articolo illustra gli argomenti da considerare quando si distribuisce una condivisione file di Azure per l'uso in produzione all'interno dell'organizzazione.

## <a name="management-concepts"></a>Concetti relativi alla gestione

 Il diagramma seguente illustra i costrutti di gestione di File di Azure:

![Struttura di Archiviazione file](./media/storage-files-introduction/files-concepts.png)

* **Account di archiviazione**: l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni dettagliate sulla capacità degli account di archiviazione, vedere gli [obiettivi di scalabilità e prestazioni](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Condivisione**: una condivisione di archiviazione file è una condivisione file SMB in Azure. Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino alla capacità totale della condivisione file. Per le condivisioni file standard, la capacità totale è fino a 5 TiB (GA) o 100 TiB (anteprima), per le condivisioni file Premium, la capacità totale è fino a 100 TiB.

* **Directory**: una gerarchia di directory facoltativa.

* **File**: un file nella condivisione. Le dimensioni massime di un file possono estendersi fino a 1 TiB.

* **Formato URL**: per le richieste a una condivisione file di Azure con il protocollo REST File, i file sono indirizzabili usando il formato di URL seguente:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Metodo di accesso ai dati

File di Azure offre due metodi di accesso ai dati utili e incorporati che è possibile usare separatamente o in combinazione, per accedere ai dati:

1. **Accesso diretto al cloud**: qualsiasi condivisione file di Azure può essere installata da [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) e/o [Linux](storage-how-to-use-files-linux.md) con il protocollo SMB (Server Message Block) standard di settore o tramite l'API REST File. Con SMB, le letture e scritture di file nella condivisione vengono eseguite direttamente in una condivisione file in Azure. Per montare una macchina virtuale in Azure, il client SMB nel sistema operativo deve supportare almeno la versione 2.1 di SMB. Per montare in locale, ad esempio in una workstation dell'utente, il client SMB supportato dalla workstation deve supportare almeno la versione 3.0 di SMB con crittografia. Oltre a SMB, nuove applicazioni o servizi possono accedere direttamente alla condivisione file tramite REST di File, che offre un'Application Programming Interface semplice e scalabile per lo sviluppo di software.
2. **Sincronizzazione file di Azure**: Con Sincronizzazione file di Azure, è possibile replicare le condivisioni in Windows Server in locale o in Azure. Gli utenti possono accedere alla condivisione file da Windows Server, ad esempio tramite una condivisione SMB o NFS. Questo è utile per gli scenari in cui si accede e si modificano i dati lontano da un datacenter di Azure, ad esempio in una succursale. I dati possono essere replicati tra più endpoint di Windows Server, ad esempio tra più succursali. Infine, i dati potrebbero essere disposti su livelli in File di Azure, in modo che siano tutti ancora accessibili tramite il Server, ma il Server non dispone di una copia completa dei dati. Invece i dati vengono semplicemente richiamati quando vengono aperti dall'utente.

La tabella seguente illustra come gli utenti e le applicazioni possono accedere alla condivisione file di Azure:

| | Accesso al cloud diretto | Sincronizzazione file di Azure |
|------------------------|------------|-----------------|
| Quali protocolli è necessario usare? | File di Azure supporta SMB 2.1, SMB 3.0 e API REST di File. | Accedere alla condivisione file di Azure con un qualsiasi protocollo supportato in Windows Server, ad esempio SMB, NFS, FTPS e così via. |  
| Dove si esegue il carico di lavoro? | **In Azure**: File di Azure offre accesso diretto ai dati. | **In locale con una rete lenta**: i client Windows, Linux e macOS possono montare una condivisione file di Windows locale come una cache veloce della condivisione file di Azure. |
| Quale livello di ACL è necessario? | Livello di condivisione e file. | Livello di condivisione, file e utente. |

## <a name="data-security"></a>Sicurezza dei dati

File di Azure offre diverse opzioni predefinite per garantire la sicurezza dei dati:

* Supporto per la crittografia in entrambi i protocolli attraverso la rete: La crittografia SMB 3.0 e REST di File tramite HTTPS. Per impostazione predefinita: 
    * I client che supportano la crittografia SMB 3,0 inviano e ricevono dati su un canale crittografato.
    * I client che non supportano SMB 3,0 con crittografia possono comunicare intra-datacenter su SMB 2,1 o SMB 3,0 senza crittografia. Ai client SMB non è consentita la comunicazione tra più data center su SMB 2.1 o SMB 3.0 senza crittografia.
    * I client possono comunicare su REST di File con HTTP o HTTPS.
* Crittografia dei dati inattivi ([Crittografia del servizio Archiviazione di Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione. Crittografia dei dati inattivi con chiavi completamente gestite. La crittografia dei dati inattivi non aumenta i costi di archiviazione, né riduce le prestazioni. 
* Requisito facoltativo di dati crittografati in transito: quando è selezionato, File di Azure rifiuta l'accesso ai dati tramite canali non crittografati. In particolare, vengono consentiti solo HTTPS e SMB 3.0 con connessioni di crittografia.

    > [!Important]  
    > La richiesta di trasferimento protetto dei dati non consentirà la comunicazione tra le vecchie versioni dei client SMB e SMB 3.0 con crittografia. Per altre informazioni, vedere [Montare su Windows](storage-how-to-use-files-windows.md), [Montare su Linux](storage-how-to-use-files-linux.md) e [Montare su macOS](storage-how-to-use-files-mac.md).

Per garantire la massima sicurezza, ogni volta che si usano client moderni per accedere ai dati è consigliabile abilitare sempre sia la crittografia dei dati a riposo che la crittografia dei dati in transito. Ad esempio, se è necessario montare una condivisione su una macchina virtuale di Windows Server 2008 R2, che supporta solo SMB 2.1, è necessario consentire il traffico non crittografato all'account di archiviazione poiché SMB 2.1 non supporta la crittografia.

Se si usa Sincronizzazione file di Azure per accedere alla condivisione file di Azure, si userà sempre HTTPS e SMB 3.0 con crittografia per sincronizzare i dati nei server Windows, indipendentemente dal fatto che sia necessaria la crittografia dei dati inattivi.

## <a name="file-share-performance-tiers"></a>Livelli di prestazioni delle condivisioni file

File di Azure offre due livelli di prestazioni: standard e Premium.

### <a name="standard-file-shares"></a>Condivisioni file Standard

Le condivisioni file standard sono supportate da unità disco rigido (HDD). Le condivisioni file standard offrono prestazioni affidabili per i carichi di lavoro di i/o meno sensibili alla variabilità delle prestazioni, ad esempio le condivisioni file per utilizzo generico e gli ambienti di sviluppo/test. Le condivisioni file standard sono disponibili solo in un modello di fatturazione con pagamento in base al consumo.

Le condivisioni file standard con dimensioni fino a 5 TiB sono disponibili come offerta GA. Mentre le condivisioni file di dimensioni maggiori, ovvero qualsiasi condivisione con dimensioni maggiori di 5 TiB, fino a un massimo di 100 TiB, sono attualmente disponibili come offerta di anteprima.

> [!IMPORTANT]
> Vedere la sezione [onboarding to large file Shares (livello standard)](#onboard-to-larger-file-shares-standard-tier) per i passaggi da eseguire per l'onboarding, nonché l'ambito e le restrizioni dell'anteprima.

### <a name="premium-file-shares"></a>Condivisioni file Premium

Le condivisioni file Premium sono supportate da unità SSD (Solid-State Drive). Le condivisioni file Premium offrono prestazioni elevate e bassa latenza coerenti, all'interno di millisecondi a una sola cifra per la maggior parte delle operazioni di i/o, per i carichi di lavoro con In questo modo sono adatti per un'ampia gamma di carichi di lavoro, ad esempio database, hosting di siti Web e ambienti di sviluppo. Le condivisioni file premium sono disponibili solo in un modello di fatturazione con provisioning. Le condivisioni file Premium usano un modello di distribuzione separato dalle condivisioni file standard.

Backup di Azure è disponibile per le condivisioni file Premium e il servizio Azure Kubernetes supporta le condivisioni file Premium nella versione 1,13 e successive.

Per informazioni su come creare una condivisione file Premium, vedere l'articolo sull'argomento: [Come creare un account di archiviazione file Premium di Azure](storage-how-to-create-premium-fileshare.md).

Attualmente, non è possibile eseguire la conversione diretta tra una condivisione file standard e una condivisione file Premium. Se si desidera passare a uno dei livelli, è necessario creare una nuova condivisione file in tale livello e copiare manualmente i dati dalla condivisione originale alla nuova condivisione creata. A tale scopo, è possibile utilizzare uno degli strumenti di copia File di Azure supportati, ad esempio Robocopy o AzCopy.

> [!IMPORTANT]
> Le condivisioni file Premium sono disponibili solo con con ridondanza locale e sono disponibili nella maggior parte delle aree che offrono account di archiviazione. Per scoprire se le condivisioni file Premium sono attualmente disponibili nella propria area geografica, vedere la pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=storage) per Azure.

#### <a name="provisioned-shares"></a>Condivisioni con provisioning

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
> Le prestazioni delle condivisioni file sono soggette a limiti di rete del computer, larghezza di banda di rete disponibile, dimensioni i/o, parallelismo, tra molti altri fattori. Per ottenere la massima scalabilità delle prestazioni, suddividere il carico tra più macchine virtuali. Consultare la [Guida alla risoluzione dei](storage-troubleshooting-files-performance.md) problemi relativi a problemi di prestazioni e soluzioni alternative comuni.

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

## <a name="file-share-redundancy"></a>Ridondanza delle condivisioni file

File di Azure condivisioni standard supporta quattro opzioni di ridondanza dei dati: archiviazione con ridondanza locale (con ridondanza locale), archiviazione con ridondanza della zona (ZRS), archiviazione con ridondanza geografica (GRS) e archiviazione con ridondanza della zona geografica (GZRS) (anteprima).

File di Azure le condivisioni Premium supportano solo archiviazione con ridondanza locale (con ridondanza locale).

Le sezioni seguenti descrivono le differenze tra le diverse opzioni di ridondanza:

### <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

> [!Warning]  
> Se si usa la condivisione file di Azure come endpoint cloud in un account di archiviazione con ridondanza geografica, è consigliabile non avviare il failover dell'account di archiviazione. Il failover causerebbe l'arresto della sincronizzazione e potrebbe causare inoltre una perdita di dati imprevista nel caso di file appena disposti su livelli. Nel caso di perdita di un'area di Azure, Microsoft attiverà il failover dell'account di archiviazione in modo compatibile con Sincronizzazione file di Azure.

L'archiviazione con ridondanza geografica (GRS) è progettata per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti nell'arco di un anno eseguendo la replica dei dati in un'area secondaria distante centinaia di chilometri dall'area primaria. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

Se si sceglie l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è necessario tenere presente che il file di Azure non supporta attualmente l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) in qualsiasi area. Le condivisioni file nell'account di archiviazione RA-GRS funzionano come per gli account GRS e vengono addebitati i prezzi GRS.

L'archiviazione con ridondanza geografica replica i dati in un altro data center in un'area secondaria, ma i dati sono disponibili per la lettura solo se Microsoft avvia un failover dall'area primaria a quella secondaria.

Per un account di archiviazione con la funzionalità GRS abilitata, tutti i dati vengono prima replicati con archiviazione con ridondanza locale (con ridondanza locale). Prima di tutto, viene eseguito il commit di un aggiornamento nella località primaria e viene eseguita la replica con l'archiviazione con ridondanza locale. L'aggiornamento viene quindi replicato in modo asincrono nell'area secondaria tramite l'archiviazione con ridondanza geografica. Quando i dati vengono scritti nella località secondaria, vengono anche replicati all'interno di tale località usando l'archiviazione con ridondanza locale.

Entrambe le aree primaria e secondaria gestiscono le repliche tra domini di errore e domini di aggiornamento separati all'interno di un'unità di scala di archiviazione. L'unità di scala di archiviazione è l'unità di replica di base nel data center. La replica a questo livello viene fornita da con ridondanza locale; Per ulteriori informazioni, vedere [archiviazione con ridondanza locale (con ridondanza locale): ridondanza dei dati a basso costo per Archiviazione di Azure](../common/storage-redundancy-lrs.md).

Nella scelta dell'opzione di replica da usare, tenere presenti queste considerazioni:

* Archiviazione con ridondanza geografica (GZRS) (anteprima) offre disponibilità elevata insieme alla durabilità massima tramite la replica in modo sincrono dei dati in tre zone di disponibilità di Azure e la replica dei dati in modo asincrono nell'area secondaria. È anche possibile abilitare l'accesso in lettura all'area secondaria. GZRS è progettato per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti in un determinato anno. Per altre informazioni su GZRS, vedere [archiviazione con ridondanza della zona geografica per disponibilità elevata e durabilità massima (anteprima)](../common/storage-redundancy-gzrs.md).
* L'archiviazione con ridondanza della zona (ZRS) fornisce disponibilità elevata con la replica sincrona e può essere la scelta migliore per alcuni scenari rispetto a GRS. Per altre informazioni sull'archiviazione con ridondanza della zona, vedere [Archiviazione con ridondanza della zona](../common/storage-redundancy-zrs.md).
* La replica asincrona implica un ritardo dal momento in cui i dati vengono scritti nell'area primaria a quello in cui vengono replicati nell'area secondaria. Nel caso in cui si verifichi un'emergenza a livello di area, è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria.
* Con l'archiviazione con ridondanza geografica, la replica non è disponibile per la lettura o la scrittura a meno che Microsoft non avvii un failover nell'area secondaria. In caso di failover, si avrà accesso in lettura e scrittura a tali dati al termine del failover. Per altre informazioni, vedere le [indicazioni sul ripristino di emergenza](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Onboarding in condivisioni file di dimensioni maggiori (livello standard)

Questa sezione si applica solo alle condivisioni file standard. Tutte le condivisioni file Premium sono disponibili con 100 TiB come offerta GA.

### <a name="restrictions"></a>Restrizioni

- Le [condizioni](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per l'anteprima di Azure si applicano a condivisioni file di grandi dimensioni in anteprima, incluso quando vengono usate con distribuzioni sincronizzazione file di Azure
- Richiede la creazione di un nuovo account di archiviazione per utilizzo generico (non è possibile espandere gli account di archiviazione esistenti).
- CON ridondanza locale/ZRS alla conversione dell'account GRS/GZRS non sarà possibile in alcun nuovo account di archiviazione creato dopo che la sottoscrizione è stata accettata per l'anteprima delle condivisioni file più grandi.


### <a name="regional-availability"></a>Disponibilità a livello di area

Le condivisioni file standard sono disponibili in tutte le aree fino a 5 TiB. In determinate aree, è disponibile con un limite di 100 TiB, le aree sono elencate nella tabella seguente:

|Region |Ridondanza supportata |Supporta gli account di archiviazione esistenti |Supporto del portale * |
|-------|---------|---------|---------|
|Australia orientale |Archiviazione con ridondanza locale     |No    |Sì|
|Australia sud-orientale|Archiviazione con ridondanza locale     |No    |Non ancora|
|India centrale  |Archiviazione con ridondanza locale     |No    |Non ancora|
|Francia centrale  |CON RIDONDANZA LOCALE, ZRS|No    |CON ridondanza locale-Sì, ZRS-non ancora|
|India meridionale    |Archiviazione con ridondanza locale     |No    |Non ancora|
|Asia sud-orientale |CON RIDONDANZA LOCALE, ZRS|No    |Sì|
|Stati Uniti centro-occidentali|Archiviazione con ridondanza locale     |No    |Non ancora|
|Europa occidentale    |CON RIDONDANZA LOCALE, ZRS|No    |Sì|
|Stati Uniti occidentali 2      |CON RIDONDANZA LOCALE, ZRS|No    |Yes|

\* Per le aree senza supporto per il portale, è comunque possibile usare PowerShell o l'interfaccia della riga di comando di Azure per creare più di 5 condivisioni TiB. In alternativa, creare una nuova condivisione tramite il portale senza specificare la quota. Verrà creata una condivisione con le dimensioni predefinite 100 TiB, che può essere aggiornata in un secondo momento tramite PowerShell o l'interfaccia della riga di comando di Azure.

Per aiutarci a classificare in ordine di priorità nuove aree e funzionalità, compila questo [sondaggio](https://aka.ms/azurefilesatscalesurvey).

### <a name="steps-to-onboard"></a>Passaggi da eseguire per l'onboarding

Per registrare la sottoscrizione nell'anteprima delle condivisioni file più grandi, è necessario usare Azure PowerShell. È possibile usare [Azure cloud Shell](https://shell.azure.com/) o installare il [modulo di Azure PowerShell localmente](https://docs.microsoft.com/powershell/azure/install-Az-ps?view=azps-2.4.0) per eseguire i comandi di PowerShell seguenti:

Assicurarsi prima di tutto che sia selezionata la sottoscrizione che si vuole registrare nell'anteprima:

```powershell
$context = Get-AzSubscription -SubscriptionId ...
Set-AzContext $context
```

Eseguire quindi la registrazione nell'anteprima usando i comandi seguenti:

```powershell
Register-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
La sottoscrizione viene approvata automaticamente dopo l'esecuzione di entrambi i comandi.

Per verificare lo stato della registrazione, è possibile eseguire il comando seguente:

```powershell
Get-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
```

Potrebbero essere necessari fino a 15 minuti prima che lo stato venga aggiornato in **registrato**. Una volta **registrato**lo stato, si dovrebbe essere in grado di utilizzare la funzionalità.

### <a name="use-larger-file-shares"></a>Usare condivisioni file di dimensioni maggiori

Per iniziare a usare condivisioni file di dimensioni maggiori, creare un nuovo account di archiviazione per utilizzo generico V2 e una nuova condivisione file.

## <a name="data-growth-pattern"></a>Modello di crescita dei dati

Attualmente, le dimensioni massime per una condivisione file di Azure sono pari a 5 TiB (100 TiB in anteprima). A causa di questa limitazione attuale, durante la distribuzione di una condivisione file di Azure è necessario tenere conto della crescita dei dati stimata.

È possibile sincronizzare più condivisioni file di Azure in un singolo file server Windows con Sincronizzazione file di Azure. In questo modo, è possibile assicurarsi che le condivisioni file meno recenti e di grandi dimensioni presenti in locale possano essere inserite in Sincronizzazione file di Azure. Per altre informazioni, vedere [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md).

## <a name="data-transfer-method"></a>Metodo di trasferimento dati

Esistono diverse semplici opzioni per trasferire i dati in blocco da una condivisione file esistente, ad esempio una condivisione file locale, in File di Azure. Quelle più diffuse includono (elenco non completo):

* **Sincronizzazione file di Azure**: come parte di una prima sincronizzazione tra una condivisione file di Azure, ovvero un "Endpoint cloud", e uno spazio dei nomi della directory di Windows, ovvero un "Endpoint server", Sincronizzazione file di Azure replicherà tutti i dati dalla condivisione file esistente a File di Azure.
* **[Importazione/Esportazione di Microsoft Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : Il servizio Importazione/Esportazione di Azure permette di trasferire in tutta sicurezza grandi quantità di dati in una condivisione file di Azure tramite la spedizione di dischi rigidi a un data center di Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy è un noto strumento di copia incluso in Windows e Windows Server. Robocopy può essere usato per trasferire i dati in File di Azure montando la condivisione file in locale e quindi usando il percorso montato come destinazione del comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy è un'utilità della riga di comando progettata per copiare i dati da e verso File di Azure, oltre ad Archiviazione BLOB di Azure usando semplici comandi con prestazioni ottimali.

## <a name="next-steps"></a>Passaggi successivi
* [Planning for an Azure File Sync Deployment](storage-sync-files-planning.md) (Pianificazione della distribuzione di Sincronizzazione file di Azure)
* [Distribuzione di File di Azure](storage-files-deployment-guide.md)
* [Distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)

---
title: Pianificazione per la distribuzione dei File di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d720f60bff1aa4510ac26ac092c42eb98871c851
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540345"
---
# <a name="planning-for-an-azure-files-deployment"></a>Pianificazione per la distribuzione dei file di Azure

[File di Azure](storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB standard di settore. Poiché File di Azure è completamente gestito, la sua distribuzione negli scenari di produzione è molto più semplice rispetto alla distribuzione e alla gestione di un file server o un dispositivo NAS. Questo articolo illustra gli argomenti da considerare quando si distribuisce una condivisione file di Azure per l'uso in produzione all'interno dell'organizzazione.

## <a name="management-concepts"></a>Concetti relativi alla gestione

 Il diagramma seguente illustra i costrutti di gestione di File di Azure:

![Struttura di Archiviazione file](./media/storage-files-introduction/files-concepts.png)

* **Account di archiviazione**: l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni dettagliate sulla capacità degli account di archiviazione, vedere gli [obiettivi di scalabilità e prestazioni](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **Condivisione**: una condivisione di archiviazione file è una condivisione file SMB in Azure. Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino a una capacità totale di 5 TiB di condivisione del file.

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
    * I client che supportano la crittografia SMB 3.0 inviare e ricevano dati tramite un canale crittografato.
    * I client che non supportano SMB 3.0 con crittografia possono comunicare tra più datacenter su SMB 2.1 o SMB 3.0 senza crittografia. Ai client SMB non è consentita la comunicazione tra più data center su SMB 2.1 o SMB 3.0 senza crittografia.
    * I client possono comunicare su REST di File con HTTP o HTTPS.
* Crittografia dei dati inattivi ([Crittografia del servizio Archiviazione di Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione. Crittografia dei dati inattivi con chiavi completamente gestite. La crittografia dei dati inattivi non aumenta i costi di archiviazione, né riduce le prestazioni. 
* Requisito facoltativo di dati crittografati in transito: quando è selezionato, File di Azure non consente l'accesso ai dati tramite canali non crittografati. In particolare, vengono consentiti solo HTTPS e SMB 3.0 con connessioni di crittografia.

    > [!Important]  
    > La richiesta di trasferimento protetto dei dati non consentirà la comunicazione tra le vecchie versioni dei client SMB e SMB 3.0 con crittografia. Per altre informazioni, vedere [Montare su Windows](storage-how-to-use-files-windows.md), [Montare su Linux](storage-how-to-use-files-linux.md) e [Montare su macOS](storage-how-to-use-files-mac.md).

Per garantire la massima sicurezza, ogni volta che si usano client moderni per accedere ai dati è consigliabile abilitare sempre sia la crittografia dei dati a riposo che la crittografia dei dati in transito. Ad esempio, se è necessario montare una condivisione su una macchina virtuale di Windows Server 2008 R2, che supporta solo SMB 2.1, è necessario consentire il traffico non crittografato all'account di archiviazione poiché SMB 2.1 non supporta la crittografia.

Se si usa Sincronizzazione file di Azure per accedere alla condivisione file di Azure, si userà sempre HTTPS e SMB 3.0 con crittografia per sincronizzare i dati nei server Windows, indipendentemente dal fatto che sia necessaria la crittografia dei dati inattivi.

## <a name="file-share-performance-tiers"></a>Livelli di prestazioni delle condivisioni file

File di Azure offre due livelli di prestazioni: standard e premium.

### <a name="standard-file-shares"></a>Condivisioni file Standard

Le condivisioni di file standard sono supportate da unità disco rigido (HDD). Condivisioni di file standard offrono prestazioni affidabili per i carichi di lavoro dei / o che sono meno sensibili alla variabilità delle prestazioni, ad esempio condivisioni di file per utilizzo generico e ambienti di sviluppo/test. Le condivisioni file standard sono disponibili solo in un modello di fatturazione con pagamento in base al consumo.

Le condivisioni di file standard fino a 5 TiB di dimensioni sono disponibili come un'offerta disponibile a livello generale. Mentre le condivisioni di file più grandi, sono presenti condivisioni dimensioni superiori a 5 TiB e un massimo di 100 TiB, sono attualmente disponibili come un'offerta di anteprima.

> [!IMPORTANT]
> Vedere le [l'Onboarding in condivisioni di file più grandi (livello standard)](#onboard-to-larger-file-shares-standard-tier) sezione per i passaggi per eseguire l'onboarding, nonché l'ambito e restrizioni della fase di anteprima.

### <a name="premium-file-shares"></a>Condivisioni file Premium

Le condivisioni file Premium sono supportate da unità SSD (Solid State Drive). Condivisioni di file Premium offrono prestazioni elevate omogenee e bassa latenza, in millisecondi a cifra singola per la maggior parte delle operazioni dei / o, per carichi di lavoro a elevato utilizzo dei / o. Ciò li rende idonei per una vasta gamma di carichi di lavoro, ad esempio database, l'hosting di siti web e gli ambienti di sviluppo. Le condivisioni file premium sono disponibili solo in un modello di fatturazione con provisioning. Le condivisioni file Premium usano un modello di distribuzione separato dalle condivisioni file standard.

Backup di Azure è disponibile per le condivisioni file premium e Azure Kubernetes Service supporta le condivisioni file premium in versione 1.13 e versioni successive.

Se si desidera imparare a creare una condivisione di file premium, vedere l'articolo sull'argomento: [Come creare un account di archiviazione file di Azure premium](storage-how-to-create-premium-fileshare.md).

Attualmente, è possibile convertire direttamente tra una condivisione file standard e una condivisione di file premium. Se si desidera passare a un livello, è necessario creare una nuova condivisione file in tale livello e copiare manualmente i dati dalla condivisione originale alla nuova condivisione creata. È possibile farlo usando uno degli strumenti di copia i file di Azure supportati, ad esempio Robocopy o AzCopy.

> [!IMPORTANT]
> Le condivisioni file Premium sono disponibili solo con archiviazione con ridondanza locale e sono disponibili nella maggior parte delle aree che offrono gli account di archiviazione. Per scoprire se le condivisioni file premium sono attualmente disponibili nella propria area, vedere la [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pagina per Azure.

### <a name="provisioned-shares"></a>Condivisioni con provisioning

Il provisioning delle condivisioni file premium viene effettuato in base a un rapporto fisso tra GiB, operazioni di I/O al secondo e velocità effettiva. Per ogni GiB di cui viene effettuato il provisioning, alla condivisione viene assegnata un'operazione di I/O al secondo con 0,1 MiB/s di velocità effettiva fino ai limiti massimi per singola condivisione. Il valore minimo di provisioning consentito è 100 GiB con il minimo di operazioni di I/O al secondo e velocità effettiva.

In base ad approssimazioni ottimali, tutte le condivisioni possono essere potenziate fino a tre operazioni di I/O al secondo per ogni GiB di archiviazione di cui viene effettuato il provisioning per 60 minuti o più, a seconda della dimensione della condivisione. Le nuove condivisioni iniziano con il credito di burst totale in base alla capacità sottoposta a provisioning.

È necessario eseguirne il provisioning di condivisioni in incrementi di 1 GiB. Dimensione minima è 100 GiB, dimensione successiva è 101 GiB e così via.

> [!TIP]
> Linea di base di IOPS = 1 * provisioning GiB. (Fino a un massimo di 100.000 IOPS).
>
> Potenziare limite = 3 * linea di base di IOPS. (Fino a un massimo di 100.000 IOPS).
>
> velocità di uscita = 60 MiB/s + 0,06 * provisioning GiB
>
> velocità in ingresso = 40 MiB/s + 0,04 * provisioning GiB

Dimensioni della condivisione possono essere aumentata in qualsiasi momento, ma possono essere ridotto solo dopo 24 ore dopo l'ultimo incremento. Dopo aver atteso per 24 ore senza un aumento delle dimensioni, è possibile ridurre le dimensioni della condivisione come tutte le volte che si desidera, fino a quando non si aumenta. IOPS/velocità effettiva scalabilità modifiche saranno applicati entro pochi minuti dopo la modifica delle dimensioni.

È possibile ridurre le dimensioni della condivisione con provisioning di sotto di GiB usati. In questo caso, non si perderanno i dati ma, si verrà comunque addebitato per la dimensione utilizzata e si ricevono le prestazioni (linea di base di IOPS, velocità effettiva e IOPS burst) della condivisione con provisioning, non le dimensioni utilizzate.

Nella tabella seguente illustra alcuni esempi di queste formule per le dimensioni di condivisione con provisioning:

|Capacità (GiB) | Operazioni di I/O al secondo di base | Burst di IOPS | Traffico in uscita (MiB/s) | Traffico in ingresso (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Fino a 300     | 66   | 44   |
|500         | 500     | Massimo di 1.500   | 90   | 60   |
|1\.024       | 1\.024   | Fino a 3.072   | 122   | 81   |
|5120       | 5120   | Fino a 15,360  | 368   | 245   |
|10,240      | 10,240  | Fino a 30.720  | 675 | 450   |
|33,792      | 33,792  | Fino a 100.000 | 2,088 | 1,392   |
|51,200      | 51,200  | Fino a 100.000 | 3\.132 | 2,088   |
|102,400     | 100,000 | Fino a 100.000 | 6,204 | 4,136   |

> [!NOTE]
> Le prestazioni delle condivisioni file sono soggette a limiti di rete di macchina, larghezza di banda disponibile, le dimensioni dei / o, il parallelismo, tra molti altri fattori. Per ottenere una scalabilità le massime prestazioni, dividere il carico tra più macchine virtuali. Consultare [Guida alla risoluzione dei](storage-troubleshooting-files-performance.md) per alcuni problemi di prestazioni comuni e soluzioni alternative.

### <a name="bursting"></a>Burst

Le condivisioni file Premium possono eseguire il burst fino a un fattore pari a tre loro IOPS. La suddivisione viene automatizzata e opera in base a un sistema di carta di credito. Espansione funziona in base ad approssimazioni ottimali e il limite di burst non garantisce, le condivisioni di file possono eseguire il burst *fino a* il limite.

I crediti si accumulano in un bucket di picco ogni volta che il traffico per la condivisione file è di sotto della linea di base di IOPS. Ad esempio, una 100 GiB condivisione presenta una linea di base di 100 operazioni IOPS. Se effettivo del traffico per la condivisione è 40 IOPS per un intervallo specifico di 1 secondo, il numero di IOPS inutilizzati 60 è accreditato in un bucket di burst. Questi crediti verranno quindi utilizzati in un secondo momento quando operazioni supererebbe il numero di IOPs della linea di base.

> [!TIP]
> Dimensione del bucket burst = IOPS di linea di base * 2 * 3600.

Ogni volta che una condivisione supera la linea di base di IOPS e dispone di crediti in un bucket di burst, verrà burst. Condivisioni possono continuare a eseguire il burst, purché i crediti rimanenti, anche se inferiori a 50 TiB condivisioni rimarranno solo raggiunge il limite di burst al massimo un'ora. Condivisioni di dimensioni superiori a 50 TiB tecnicamente possibile superare questo limite di un'ora, backup a due ore ma, ciò si basa sul numero di crediti di burst accumulati. Ogni i/o di là della linea di base IOPS utilizza una carta di credito e una volta che vengono utilizzati tutti i crediti della condivisione ritornerà a linea di base di IOPS.

I crediti di condivisione presentano tre stati:

- Accumulo, quando la condivisione file di utilizzo è inferiore alla linea di base di IOPS.
- Rifiuto, quando la condivisione file è burst.
- IOPS rimanenti costante, quando non sono presenti alcun crediti o linea di base sono in uso.

Inizio nuovo condivisioni di file con il numero completo di crediti in relativi bucket di burst. Se la condivisione di IOPS scende di sotto della linea di base di IOPS dovuto alla limitazione dal server, i crediti di burst non essere accumulati.

## <a name="file-share-redundancy"></a>Ridondanza delle condivisioni file

Condivisioni standard di file di Azure supportano tre opzioni di ridondanza dei dati: archiviazione con ridondanza locale (LRS), archiviazione con ridondanza della zona (ZRS) e archiviazione con ridondanza geografica (GRS).

Le condivisioni premium di Azure i file supportano solo l'archiviazione con ridondanza locale (LRS).

Le sezioni seguenti descrivono le differenze tra le diverse opzioni di ridondanza:

### <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

> [!Warning]  
> Se si usa la condivisione file di Azure come endpoint cloud in un account di archiviazione con ridondanza geografica, è consigliabile non avviare il failover dell'account di archiviazione. Il failover causerebbe l'arresto della sincronizzazione e potrebbe causare inoltre una perdita di dati imprevista nel caso di file appena disposti su livelli. Nel caso di perdita di un'area di Azure, Microsoft attiverà il failover dell'account di archiviazione in modo compatibile con Sincronizzazione file di Azure.

L'archiviazione con ridondanza geografica (GRS) è progettata per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti nell'arco di un anno eseguendo la replica dei dati in un'area secondaria distante centinaia di chilometri dall'area primaria. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

Se si opta per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS), è necessario sapere che i File di Azure non supporta l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) in qualsiasi area in questo momento. Le condivisioni file nell'account di archiviazione RA-GRS funzionano come fossero in account di archiviazione con ridondanza geografica e vengono addebitati i prezzi di archiviazione con ridondanza geografica.

L'archiviazione con ridondanza geografica replica i dati in un altro data center in un'area secondaria, ma i dati sono disponibili per la lettura solo se Microsoft avvia un failover dall'area primaria a quella secondaria.

Per un account di archiviazione con archiviazione con ridondanza geografica abilitata, tutti i dati vengono prima replicati con archiviazione con ridondanza locale (LRS). Prima di tutto, viene eseguito il commit di un aggiornamento nella località primaria e viene eseguita la replica con l'archiviazione con ridondanza locale. L'aggiornamento viene quindi replicato in modo asincrono nell'area secondaria tramite l'archiviazione con ridondanza geografica. Quando i dati vengono scritti nella località secondaria, vengono anche replicati all'interno di tale località usando l'archiviazione con ridondanza locale.

Entrambe le aree primaria e secondaria gestiscono le repliche tra domini di errore e domini di aggiornamento separati all'interno di un'unità di scala di archiviazione. L'unità di scala di archiviazione è l'unità di replica di base nel data center. La replica a questo livello viene assicurata dall'archiviazione con ridondanza locale; per altre informazioni, vedere [archiviazione con ridondanza locale (LRS): ridondanza dei dati a basso costo per Archiviazione di Azure](../common/storage-redundancy-lrs.md).

Nella scelta dell'opzione di replica da usare, tenere presenti queste considerazioni:

* Archiviazione con ridondanza della zona (ZRS) fornisce elevata disponibilità con replica sincrona e può essere una scelta migliore per alcuni scenari di archiviazione con ridondanza geografica. Per altre informazioni sull'archiviazione con ridondanza della zona, vedere [Archiviazione con ridondanza della zona](../common/storage-redundancy-zrs.md).
* La replica asincrona implica un ritardo dal momento in cui i dati vengono scritti nell'area primaria a quello in cui vengono replicati nell'area secondaria. Nel caso in cui si verifichi un'emergenza a livello di area, è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria.
* Con l'archiviazione con ridondanza geografica, la replica non è disponibile per la lettura o la scrittura a meno che Microsoft non avvii un failover nell'area secondaria. In caso di failover, si avrà accesso in lettura e scrittura a tali dati al termine del failover. Per altre informazioni, vedere le [indicazioni sul ripristino di emergenza](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Condivisioni file eseguire l'onboarding di dimensioni maggiori (livello standard)

In questa sezione si applica solo alle condivisioni di file standard. Tutte le condivisioni di file premium sono disponibili con 100 TiB come un'offerta disponibile a livello generale.

### <a name="restrictions"></a>Restrizioni

- È necessario creare un nuovo account di archiviazione di uso generale (non è possibile espandere gli account di archiviazione esistente).
- Archiviazione con ridondanza locale per la conversione di account di archiviazione con ridondanza geografica non sarà possibile in qualsiasi nuovo account di archiviazione creati dopo la sottoscrizione viene accettata per l'anteprima di condivisioni di file più grande.

### <a name="regional-availability"></a>Disponibilità internazionale

Le condivisioni di file standard sono disponibili in tutte le aree fino a 5 TiB. In determinate aree geografiche, è disponibile con un limite di 100 TiB, tali aree sono elencate nella tabella seguente:

|Region  |Ridondanza supportata  |Supporta gli account di archiviazione esistenti  |
|---------|---------|---------|
|Asia sudorientale     |Archiviazione con ridondanza locale|No         |
|Europa occidentale     |Archiviazione con ridondanza locale|No         |
|Stati Uniti occidentali 2     |LRS, ZRS|No         |


### <a name="steps-to-onboard"></a>Passaggi per eseguire l'onboarding

Per registrare la sottoscrizione all'anteprima di condivisioni file di dimensioni maggiori, eseguire i comandi di PowerShell seguenti:

```powershell
Register-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
La sottoscrizione viene approvata automaticamente dopo che entrambi i comandi vengono eseguiti.

Per verificare lo stato della registrazione, è possibile eseguire il comando seguente:

```powershell
Get-AzProviderFeature -FeatureName AllowLargeFileShares -ProviderNamespace Microsoft.Storage
```

Potrebbero occorrere fino a 15 minuti per il tuo stato di eseguire l'aggiornamento a "registrato", tuttavia, dovrebbe essere possibile usare la funzionalità nonostante che.

### <a name="use-larger-file-shares"></a>Usare le condivisioni di file più grandi

Per iniziare a usare le condivisioni di file più grandi, creare un nuovo account di archiviazione v2 generico e una nuova condivisione file.

## <a name="data-growth-pattern"></a>Modello di crescita dei dati

Oggi, la dimensione massima per una condivisione file di Azure è 5 TiB (100 TiB disponibile in anteprima). A causa di questa limitazione attuale, durante la distribuzione di una condivisione file di Azure è necessario tenere conto della crescita dei dati stimata.

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

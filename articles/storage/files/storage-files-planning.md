---
title: Pianificazione per la distribuzione dei File di Azure | Microsoft Docs
description: Informazioni sugli aspetti da considerare quando si pianifica una distribuzione di File di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: a9c37258d7c9631c6e5fe13007b78c4205a1c249
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473885"
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
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
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
    * I client che supportano la crittografia SMB 3.0 inviano e ricevono dati tramite un canale crittografato.
    * I client che non supportano SMB 3.0 con crittografia possono comunicare tra più data center su SMB 2.1 o SMB 3.0 senza crittografia. Ai client SMB non è consentita la comunicazione tra più data center su SMB 2.1 o SMB 3.0 senza crittografia.
    * I client possono comunicare su REST di File con HTTP o HTTPS.
* Crittografia dei dati inattivi ([Crittografia del servizio Archiviazione di Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione. Crittografia dei dati inattivi con chiavi completamente gestite. La crittografia dei dati inattivi non aumenta i costi di archiviazione, né riduce le prestazioni. 
* Requisito facoltativo di dati crittografati in transito: quando è selezionato, File di Azure non consente l'accesso ai dati tramite canali non crittografati. In particolare, vengono consentiti solo HTTPS e SMB 3.0 con connessioni di crittografia. 

    > [!Important]  
    > La richiesta di trasferimento protetto dei dati non consentirà la comunicazione tra le vecchie versioni dei client SMB e SMB 3.0 con crittografia. Per altre informazioni, vedere [Montare su Windows](storage-how-to-use-files-windows.md), [Montare su Linux](storage-how-to-use-files-linux.md) e [Montare su macOS](storage-how-to-use-files-mac.md).

Per garantire la massima sicurezza, ogni volta che si usano client moderni per accedere ai dati è consigliabile abilitare sempre sia la crittografia dei dati a riposo che la crittografia dei dati in transito. Ad esempio, se è necessario montare una condivisione su una macchina virtuale di Windows Server 2008 R2, che supporta solo SMB 2.1, è necessario consentire il traffico non crittografato all'account di archiviazione poiché SMB 2.1 non supporta la crittografia.

Se si usa Sincronizzazione file di Azure per accedere alla condivisione file di Azure, si userà sempre HTTPS e SMB 3.0 con crittografia per sincronizzare i dati nei server Windows, indipendentemente dal fatto che sia necessaria la crittografia dei dati inattivi.

## <a name="file-share-performance-tiers"></a>Livelli di prestazioni delle condivisioni file
File di Azure supporta due livelli di prestazioni: standard e premium.

* Le **condivisioni file standard** sono supportate da unità disco rigido rotazionali (HDD) che forniscono prestazioni affidabili per i carichi di lavoro di I/O che sono meno sensibili alla variabilità delle prestazioni, ad esempio condivisioni file per utilizzo generico e ambienti di sviluppo/test. Le condivisioni file standard sono disponibili solo in un modello di fatturazione con pagamento in base al consumo.
* Le **condivisioni file premium (anteprima)** sono supportate da unità a stato solido (SSD) che forniscono prestazioni elevate omogenee e a bassa latenza, in millisecondi a cifra singola per la maggior parte delle operazioni di I/O, per la maggior parte dei carichi di lavoro a elevato utilizzo di I/O. Queste condivisioni sono quindi idonee per una vasta gamma di carichi di lavoro, ad esempio database, hosting di siti Web, ambienti di sviluppo e così via. Le condivisioni file premium sono disponibili solo in un modello di fatturazione con provisioning.

### <a name="provisioned-shares"></a>Condivisioni con provisioning
Il provisioning delle condivisioni file premium viene effettuato in base a un rapporto fisso tra GiB, operazioni di I/O al secondo e velocità effettiva. Per ogni GiB di cui viene effettuato il provisioning, alla condivisione viene assegnata un'operazione di I/O al secondo con 0,1 MiB/s di velocità effettiva fino ai limiti massimi per singola condivisione. Il valore minimo di provisioning consentito è 100 GiB con il minimo di operazioni di I/O al secondo e velocità effettiva. La dimensione della condivisione può essere aumentata e ridotta in qualsiasi momento, ma la riduzione può essere eseguita una volta ogni 24 ore dopo l'ultimo incremento.

In base ad approssimazioni ottimali, tutte le condivisioni possono essere potenziate fino a tre operazioni di I/O al secondo per ogni GiB di archiviazione di cui viene effettuato il provisioning per 60 minuti o più, a seconda della dimensione della condivisione. Le nuove condivisioni iniziano con il credito di burst totale in base alla capacità sottoposta a provisioning.

| Capacità con provisioning | 100 GiB | 500 GiB | 1 TiB | 5 TiB | 
|----------------------|---------|---------|-------|-------|
| Operazioni di I/O al secondo di base | 100 | 500 | 1.024 | 5120 | 
| Limite di burst | 300 | 1.500 | 3.072 | 15.360 | 
| Velocità effettiva | 110 MiB/sec | 150 MiB/sec | 202 MiB/sec | 612 MiB/sec |

## <a name="file-share-redundancy"></a>Ridondanza delle condivisioni file
File di Azure supporta tre opzioni di ridondanza dei dati: archiviazione con ridondanza locale, archiviazione con ridondanza della zona e archiviazione con ridondanza geografica. Le sezioni seguenti descrivono le differenze tra le diverse opzioni di ridondanza:

### <a name="locally-redundant-storage"></a>Archiviazione con ridondanza locale
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Archiviazione con ridondanza della zona
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>Modello di crescita dei dati
Oggi, la dimensione massima di una condivisione di File di Azure è 5 TiB. A causa di questa limitazione attuale, durante la distribuzione di una condivisione file di Azure è necessario tenere conto della crescita dei dati stimata. 

È possibile sincronizzare più condivisioni file di Azure in un singolo file server Windows con Sincronizzazione file di Azure. In questo modo, è possibile assicurarsi che le condivisioni file meno recenti e di grandi dimensioni presenti in locale possano essere inserite in Sincronizzazione file di Azure. Per altre informazioni, vedere [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md).

## <a name="data-transfer-method"></a>Metodo di trasferimento dati
Esistono diverse semplici opzioni per trasferire i dati in blocco da una condivisione file esistente, ad esempio una condivisione file locale, in File di Azure. Quelle più diffuse includono (elenco non completo):

* **Sincronizzazione file di Azure**: come parte di una prima sincronizzazione tra una condivisione file di Azure, ovvero un "Endpoint cloud", e uno spazio dei nomi della directory di Windows, ovvero un "Endpoint server", Sincronizzazione file di Azure replicherà tutti i dati dalla condivisione file esistente a File di Azure.
* **[Importazione/Esportazione di Microsoft Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Il servizio Importazione/Esportazione di Azure permette di trasferire in tutta sicurezza grandi quantità di dati in una condivisione file di Azure tramite la spedizione di dischi rigidi a un data center di Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy è un noto strumento di copia incluso in Windows e Windows Server. Robocopy può essere usato per trasferire i dati in File di Azure montando la condivisione file in locale e quindi usando il percorso montato come destinazione del comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy è un'utilità della riga di comando progettata per copiare i dati da e verso File di Azure, oltre ad Archiviazione BLOB di Azure usando semplici comandi con prestazioni ottimali. AzCopy è disponibile per Windows e Linux.

## <a name="next-steps"></a>Passaggi successivi
* [Planning for an Azure File Sync Deployment](storage-sync-files-planning.md) (Pianificazione della distribuzione di Sincronizzazione file di Azure)
* [Distribuzione di File di Azure](storage-files-deployment-guide.md)
* [Distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)

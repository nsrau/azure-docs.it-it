---
title: Opzioni di trasferimento dei dati di Azure per set di dati di grandi dimensioni con larghezza di banda di rete moderata o elevata | Microsoft Docs
description: Informazioni su come scegliere una soluzione di Azure per il trasferimento dei dati quando la larghezza di banda di rete è moderata o elevata nell'ambiente e si pianifica il trasferimento di set di dati di grandi dimensioni.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 8dd55032c933cdc31b848addfdac991550376dcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729232"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Trasferimento dei dati per set di dati di grandi dimensioni con larghezza di banda da rete da moderata a elevata
 
Questo articolo offre una panoramica delle soluzioni per il trasferimento dei dati quando la larghezza di banda di rete è moderata o elevata nell'ambiente e si pianifica il trasferimento di set di dati di grandi dimensioni. L'articolo descrive anche le opzioni di trasferimento dei dati consigliate e la rispettiva matrice di funzionalità chiave per questo scenario.

Per una panoramica di tutte le opzioni di trasferimento dei dati disponibili, vedere [Scegliere una soluzione di trasferimento dei dati di Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Descrizione dello scenario

Con set di dati di grandi dimensioni si intendono dati di dimensioni nell'ordine di TB o PB. Con larghezza di banda di rete da moderata a elevata si intende da 100 Mbps a 10 Gbps.

## <a name="recommended-options"></a>Opzioni consigliate

Le opzioni consigliate in questo scenario variano a seconda che larghezza di banda di rete sia moderata o elevata.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Larghezza di banda di rete moderata (100 Mbps - 1 Gbps)

Con la larghezza di banda di rete moderata, è necessario prevedere il tempo richiesto per il trasferimento dei dati in rete.

Usare la tabella seguente per stimare il tempo e, in base al risultato, scegliere tra un trasferimento offline e uno in rete. La tabella indica il tempo previsto per il trasferimento dei dati di rete, per diverse larghezze di banda di rete disponibili (presupponendo un utilizzo del 90%).  

![Trasferimento di rete o trasferimento offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Se risulta che il trasferimento di rete sarà troppo lento, è consigliabile usare un dispositivo fisico. Le opzioni consigliate in questo caso sono i dispositivi di trasferimento offline della famiglia di prodotti Azure Data Box oppure Importazione/Esportazione di Azure con i propri dischi.

    - **Famiglia di prodotti Azure Data Box per i trasferimenti offline**: usare i dispositivi Data Box forniti da Microsoft per spostare grandi quantità di dati in Azure quando sono presenti limiti di tempo, disponibilità di rete o costi. Copiare i dati locali usando strumenti come Robocopy. A seconda delle dimensioni dei dati da trasferire, è possibile scegliere tra Data Box Disk, Data Box o Data Box Heavy.
    - **Importazione/Esportazione di Azure**: usare il servizio Importazione/Esportazione di Azure tramite la spedizione delle unità disco per importare in modo sicuro grandi quantità di dati nell'archivio BLOB di Azure e in File di Azure. È anche possibile usare questo servizio per trasferire i dati dall'archivio BLOB di Azure a unità disco per la spedizione al sito locale.

- Se risulta che il trasferimento di rete sarà accettabile, è possibile usare uno degli strumenti seguenti, illustrati in dettaglio in [Larghezza di banda di rete elevata](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Larghezza di banda di rete elevata (1 Gbps - 100 Gbps)

Se la larghezza di banda di rete disponibile è elevata, usare uno degli strumenti seguenti.

- **AzCopy**: usare questo strumento da riga di comando per copiare facilmente i dati da e verso l'archivio BLOB, file e tabelle di Azure con prestazioni ottimali. AzCopy supporta la concorrenza e il parallelismo e consente di riprendere le operazioni di copia in caso di interruzione.
- **API REST/SDK di Archiviazione di Azure**: quando si compila un'applicazione, è possibile sviluppare l'applicazione tramite le API REST di Archiviazione di Azure e usare gli SDK di Azure disponibili in più linguaggi.
- **Famiglia di prodotti Azure Data Box per i trasferimenti online**: Data Box Edge e Data Box Gateway sono dispositivi di rete online che possono spostare i dati all'interno e all'esterno di Azure. Usare il dispositivo fisico Data Box Edge quando è necessario eseguire simultaneamente l'inserimento continuo e la pre-elaborazione dei dati prima del caricamento. Data Box Gateway è una versione virtuale del dispositivo con le stesse capacità di trasferimento dati. In ogni caso, il trasferimento dei dati viene gestito dal dispositivo.
- **Azure Data Factory**: Data Factory deve essere usato per ampliare un'operazione di trasferimento e se sono necessarie funzionalità di orchestrazione e monitoraggio a livello aziendale. Usare Data Factory per trasferire regolarmente i file tra diversi servizi di Azure, l'ambiente locale o una combinazione dei due. Con Data Factory è possibile creare e pianificare flussi di lavoro basati sui dati, detti pipeline, che inseriscono dati provenienti da archivi diversi e automatizzano lo spostamento dati e la trasformazione dei dati.

## <a name="comparison-of-key-capabilities"></a>Confronto delle funzionalità chiave

Le tabelle seguenti riepilogano le differenze nelle funzionalità chiave per le opzioni consigliate.

### <a name="moderate-network-bandwidth"></a>Larghezza di banda di rete moderata

Se si usa il trasferimento dei dati offline, usare la tabella seguente per conoscere le differenze nelle funzionalità chiave.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Importazione/Esportazione                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Dimensioni dei dati                        |    Fino a 35 TB                 |    Fino a 80 TB per dispositivo                       |    Fino a 800 TB per dispositivo               |    Variabile                            |
|    Tipo di dati                        |    BLOB di Azure                  |    BLOB di Azure<br>File di Azure                    |    BLOB di Azure<br>File di Azure            |    BLOB di Azure<br>File di Azure          |
|    Fattore di forma                      |    5 unità SSD per ordine             |    1 dispositivo desktop da 23 kg per ordine    |    1 dispositivo di grandi dimensioni da circa 230 kg per ordine    |    Fino a 10 HDD/unità SSD per ordine        |
|    Tempo di configurazione iniziale               |    Basso <br>(15 min)            |    Da basso a moderato <br> (<30 min)               |    Moderata<br>(1-2 ore)               |    Da moderato a elevato<br>(variabile) |
|    Inviare dati ad Azure               |    Sì                          |    Sì                                           |    Sì                                   |    Sì                                 |
|    Esportare dati da Azure           |    No                            |    No                                             |    No                                     |    Sì                                 |
|    Crittografia                       |    AES a 128 bit                  |    AES a 256 bit                                   |    AES a 256 bit                           |    AES a 128 bit                         |
|    Hardware                         |     Fornito da Microsoft          |    Fornito da Microsoft                            |    Fornito da Microsoft                    |    Fornito dal cliente                   |
|    interfaccia di rete                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integrazione dei partner              |    Alcuni                         |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alcuni                                |
|    Spedizione                         |    Gestita da Microsoft            |    Gestita da Microsoft                             |    Gestita da Microsoft                     |    Gestita dal cliente                    |
| Uso quando i dati vengono spostati         |Entro un confine commerciale|Entro un confine commerciale|Entro un confine commerciale|Oltre i confini geografici, ad esempio dagli USA all'UE|
|    Prezzi                          |    [Prezzi](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prezzi](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Se si usa il trasferimento dei dati online, usare la tabella nella sezione seguente per la larghezza di banda di rete elevata.

### <a name="high-network-bandwidth"></a>Larghezza di banda di rete elevata

|                                     |    Strumenti AzCopy, <br>Azure PowerShell, <br>Interfaccia della riga di comando di Azure             |    API REST, SDK di Archiviazione di Azure                   |    Gateway di dati finestra o bordo casella dei dati          |    Data factory di Azure                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Tipo di dati                  |    BLOB di Azure, file di Azure, tabelle di Azure    |    BLOB di Azure, file di Azure, tabelle di Azure    |    BLOB di Azure, file di Azure                           |   Supporta più di 70 connettori dati per archivi e formati dati    |
|    Fattore di forma                |    Strumenti da riga di comando                        |    Interfaccia programmatica                    |    Microsoft fornisce un dispositivo <br>fisico o virtuale     |    Servizio nel portale di Azure                                            |
|    Configurazione singola iniziale     |    Semplice               |    Moderata                       |    Da semplice (<30 minuti) a moderata (1-2 ore)            |    Estesa                                                          |
|    Pre-elaborazione dei dati              |    No                                         |    No                                         |    Sì (con calcolo Edge)                               |    Sì                                                                |
|    Trasferimento da altri cloud       |    No                                         |    No                                         |    No                                                     |    Sì                                                                |
|    Tipo di utente                        |    Professionisti IT o sviluppatori                                       |    Sviluppo                                       |    Professionisti IT                                                |    Professionisti IT                                                             |
|    Prezzi                          |    Gratuiti, si applicano le tariffe per dati in uscita         |    Gratuiti, si applicano le tariffe per dati in uscita         |    [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Prezzi](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come trasferire dati con Importazione/Esportazione](/azure/storage/common/storage-import-export-data-to-blobs).
- Informazioni su come

    - [Trasferire dati con Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Trasferire dati con Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Trasferire dati con AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Informazioni su come:
    - [Trasferire dati con Data Box Gateway](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Trasformare i dati con Data Box Edge prima dell'invio ad Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Informazioni su come trasferire dati con Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Usare le API REST per trasferire i dati

    - [In .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)

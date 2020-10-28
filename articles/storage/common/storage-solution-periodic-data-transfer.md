---
title: Scegliere una soluzione di Azure per il trasferimento dei dati periodico | Microsoft Docs
description: Informazioni su come scegliere una soluzione di Azure per il trasferimento dei dati quando si trasferiscono dati periodicamente.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a0efc6f6f6d4ae6355fbb42fbc7e13ad7c078cf3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792889"
---
# <a name="solutions-for-periodic-data-transfer"></a>Soluzioni per il trasferimento dei dati periodico
 
Questo articolo offre una panoramica delle soluzioni per il trasferimento dei dati quando si trasferiscono dati periodicamente. Il trasferimento dei dati periodico tramite la rete può essere classificato come ricorrente a intervalli regolari o come spostamento dati continuo. L'articolo descrive anche le opzioni di trasferimento dei dati consigliate e la rispettiva matrice di funzionalità chiave per questo scenario.

Per una panoramica di tutte le opzioni di trasferimento dei dati disponibili, vedere [Scegliere una soluzione di trasferimento dei dati di Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Opzioni consigliate

Le opzioni consigliate per il trasferimento dei dati periodico rientrano in due categorie a seconda che il trasferimento dei dati sia ricorrente o continuo.

- **Strumenti con script/programmatici** : per il trasferimento dei dati che si verifica a intervalli regolari, usare gli strumenti con script e programmatici, ad esempio AzCopy e le API REST di Archiviazione di Azure. Questi strumenti sono destinati a professionisti IT e sviluppatori.

    - **AzCopy** : usare questo strumento da riga di comando per copiare facilmente i dati da e verso l'archivio BLOB, file e tabelle di Azure con prestazioni ottimali. AzCopy supporta la concorrenza e il parallelismo e consente di riprendere le operazioni di copia in caso di interruzione.
    - **API REST/SDK di Archiviazione di Azure** : quando si compila un'applicazione, è possibile sviluppare l'applicazione tramite le API REST di Archiviazione di Azure e usare gli SDK di Azure disponibili in più linguaggi. Le API REST possono anche sfruttare la libreria per lo spostamento dei dati di Archiviazione di Azure progettata appositamente per la copia a prestazioni elevate di dati da e verso Azure.

- **Strumenti di inserimento dati continuo** : per l'inserimento dati continuo, è possibile scegliere tra il dispositivo di trasferimento online Data Box e Azure Data Factory. Questi strumenti vengono configurati dai professionisti IT e possono automatizzare in modo trasparente il trasferimento dei dati.

    - **Azure Data Factory** : Data Factory deve essere usato per ampliare un'operazione di trasferimento e se sono necessarie funzionalità di orchestrazione e monitoraggio a livello aziendale. Usare Azure Data Factory per configurare una pipeline cloud che trasferisce regolarmente i file tra diversi servizi di Azure, l'ambiente locale o una combinazione dei due. Azure Data Factory consente di orchestrare flussi di lavoro basati sui dati che inseriscono dati provenienti da archivi diversi e automatizzano lo spostamento dati e la trasformazione dei dati.
    - **Azure Data Box famiglia per i trasferimenti online** : Data Box Edge e data box gateway sono dispositivi di rete online che possono spostare dati in e da Azure. Data Box Edge usa il calcolo Edge abilitato per l'intelligenza artificiale per pre-elaborare i dati prima del caricamento. Data Box Gateway è una versione virtuale del dispositivo con le stesse capacità di trasferimento dati.


## <a name="comparison-of-key-capabilities"></a>Confronto delle funzionalità chiave

La tabella seguente contiene un riepilogo delle differenze principali in termini di funzionalità.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Trasferimento dei dati di rete con script/programmatico

| Funzionalità                  | AzCopy                                 | API REST di Archiviazione di Azure       |
|-----------------------------|----------------------------------------|-------------------------------|
| Fattore di forma                 | Strumento da riga di comando di Microsoft       | I clienti sviluppano nelle API REST di <br> Archiviazione usando le librerie client di Azure |
| Configurazione singola iniziale     | Minime                                | Moderata, lavoro richiesto per lo sviluppo variabile    |
| Formato dati                 | BLOB di Azure, file di Azure, tabelle di Azure | BLOB di Azure, file di Azure, tabelle di Azure   |
| Prestazioni                 | Già ottimizzate                      | Ottimizzazione durante lo sviluppo                  |
| Prezzi                     | Gratuiti, si applicano le tariffe per dati in uscita      | Gratuiti, si applicano le tariffe per dati in uscita        |

### <a name="continuous-data-ingestion-over-network"></a>Inserimento dati continuo in rete

| Funzionalità                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Fattore di forma                                   | Dispositivo virtuale             | Dispositivo fisico          | Servizio nel portale di Azure, agente in locale                                                            |
| Hardware                                      | Hypervisor dell'utente            | Fornito da Microsoft    | ND                                                            |
| Lavoro richiesto per la configurazione iniziale                          | Basso (<30 min.)            | Moderato (~due ore) | Elevato (alcuni giorni)                                                 |
| Formato dati                                   | BLOB di Azure, file di Azure   | BLOB di Azure, file di Azure | [Supporta più di 70 connettori dati per archivi e formati dati](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Pre-elaborazione dei dati                           | No                         | Sì, tramite il calcolo Edge    | Sì                                                           |
| Cache locale<br>(per archiviare i dati locali)    | Sì                        | Sì                      | No                                                            |
| Trasferimento da altri cloud                    | No                         | No                       | Sì                                                           |
| Prezzi                                       | [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prezzi](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Passaggi successivi

- [Trasferire i dati con AzCopy](./storage-use-azcopy-v10.md?toc=%252fazure%252fstorage%252ftables%252ftoc.json).
- [Altre informazioni sul trasferimento dei dati con le API REST di Archiviazione](/dotnet/api/overview/azure/storage).
- Informazioni su come:
    - [Trasferire dati con Data Box Gateway](../../databox-online/data-box-gateway-deploy-add-shares.md).
    - [Trasformare i dati con Data Box Edge prima dell'invio ad Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Informazioni su come trasferire dati con Azure Data Factory](../../data-factory/tutorial-bulk-copy-portal.md).
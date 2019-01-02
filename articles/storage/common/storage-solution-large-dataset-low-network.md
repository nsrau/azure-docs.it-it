---
title: Opzioni di trasferimento dei dati di Azure per set di dati di grandi dimensioni con larghezza di banda di rete bassa o assente | Microsoft Docs
description: Informazioni su come scegliere una soluzione di Azure per il trasferimento dei dati quando la larghezza di banda di rete è ridotta o assente nell'ambiente e si pianifica il trasferimento di set di dati di grandi dimensioni.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: 54a51f5f124857419727ed7ca574f717e17b125a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263216"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Trasferimento dei dati per set di dati di grandi dimensioni con larghezza di banda di rete bassa o assente
 
Questo articolo offre una panoramica delle soluzioni per il trasferimento dei dati quando la larghezza di banda di rete è ridotta o assente nell'ambiente e si pianifica il trasferimento di set di dati di grandi dimensioni. L'articolo descrive anche le opzioni di trasferimento dei dati consigliate e la rispettiva matrice di funzionalità chiave per questo scenario.

Per una panoramica di tutte le opzioni di trasferimento dei dati disponibili, vedere [Scegliere una soluzione di trasferimento dei dati di Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Trasferimento offline o trasferimento di rete

I set di dati di grandi dimensioni implicano la presenza di alcuni TB o PB di dati. La larghezza di banda di rete è ridotta o assente, la rete è lenta o non affidabile. Tenere presente anche quanto segue:

- Esistono limiti di costo del trasferimento di rete previsti dai provider di servizi Internet (ISP).
- I criteri di sicurezza o dell'organizzazione non consentono connessioni in uscita quando si gestiscono dati sensibili.

In tutti gli esempi precedenti usare un dispositivo fisico per eseguire un unico trasferimento dei dati in blocco. Scegliere tra i dispositivi Data Box Disk, Data Box, Data Box Heavy forniti da Microsoft oppure Importazione/Esportazione con i propri dischi.

Per verificare se un dispositivo fisico è l'opzione appropriata, usare la tabella seguente, che indica il tempo previsto per il trasferimento dei dati di rete, per diverse larghezze di banda disponibili (presupponendo un utilizzo del 90%). Se risulta che il trasferimento di rete sarà troppo lento, è consigliabile usare un dispositivo fisico.  

![Trasferimento di rete o trasferimento offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Opzioni consigliate

Le opzioni disponibili in questo scenario sono i dispositivi per il trasferimento offline con Azure Data Box o Importazione/Esportazione di Azure.

- **Famiglia di prodotti Azure Data Box per i trasferimenti offline**: usare i dispositivi Data Box forniti da Microsoft per spostare grandi quantità di dati in Azure quando sono presenti limiti di tempo, disponibilità di rete o costi. Copiare i dati locali usando strumenti come Robocopy. A seconda delle dimensioni dei dati da trasferire, è possibile scegliere tra Data Box Disk, Data Box o Data Box Heavy.
- **Importazione/Esportazione di Azure**: usare il servizio Importazione/Esportazione di Azure tramite la spedizione delle unità disco per importare in modo sicuro grandi quantità di dati nell'archivio BLOB di Azure e in File di Azure. È anche possibile usare questo servizio per trasferire i dati dall'archivio BLOB di Azure a unità disco per la spedizione al sito locale.

## <a name="comparison-of-key-capabilities"></a>Confronto delle funzionalità chiave

La tabella seguente contiene un riepilogo delle differenze principali in termini di funzionalità.

|                                     |    Data Box Disk (anteprima)    |    Data Box                                      |    Data Box Heavy (anteprima)              |    Importazione/Esportazione                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Dimensioni dei dati                        |    Fino a 35 TB                 |    Fino a 80 TB per dispositivo                       |    Fino a 800 TB per dispositivo               |    Variabile                            |
|    Tipo di dati                        |    BLOB di Azure                  |    BLOB di Azure<br>File di Azure                    |    BLOB di Azure<br>File di Azure            |    BLOB di Azure<br>File di Azure          |
|    Fattore di forma                      |    5 unità SSD per ordine             |    1 dispositivo desktop da 23 kg per ordine    |    1 dispositivo di grandi dimensioni da circa 230 kg per ordine    |    Fino a 10 HDD/unità SSD per ordine        |
|    Tempo di configurazione iniziale               |    Basso <br>(15 min)            |    Da basso a moderato <br> (<30 min)               |    Moderata<br>(1-2 ore)               |    Da moderato a elevato<br>(variabile) |
|    Inviare dati ad Azure               |    Yes                          |    Sì                                           |    Sì                                   |    Yes                                 |
|    Esportare dati da Azure           |    No                            |    No                                             |    No                                     |    Yes                                 |
|    Crittografia                       |    AES a 128 bit                  |    AES a 256 bit                                   |    AES a 256 bit                           |    AES a 128 bit                         |
|    Hardware                         |     Fornito da Microsoft          |    Fornito da Microsoft                            |    Fornito da Microsoft                    |    Fornito dal cliente                   |
|    interfaccia di rete                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integrazione dei partner              |    Alcuni                         |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alcuni                                |
|    Spedizione                         |    Gestita da Microsoft            |    Gestita da Microsoft                             |    Gestita da Microsoft                     |    Gestita dal cliente                    |
| Uso quando i dati vengono spostati         |Entro un confine commerciale|Entro un confine commerciale|Entro un confine commerciale|Oltre i confini geografici, ad esempio dagli USA all'UE|
|    Prezzi                          |    [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prezzi](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prezzi](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come

    - [Trasferire dati con Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Trasferire dati con Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Trasferire dati con Importazione/Esportazione](/azure/storage/common/storage-import-export-data-to-blobs).
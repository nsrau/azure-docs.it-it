---
title: Requisiti di sistema di Microsoft Azure Data Box | Microsoft Docs
description: Informazioni sui requisiti relativi al software e alla rete per Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206748"
---
# <a name="azure-data-box-system-requirements"></a>Requisiti di sistema di Azure Data Box

Questo articolo descrive i requisiti di sistema importanti per Microsoft Azure Data Box e per i client che si connettono a Data Box. Prima di distribuire Data Box è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema includono:

* **Requisiti software per gli host che si connettono a Data Box:** descrizione delle piattaforme supportate, dei browser per l'interfaccia utente Web locale, dei client SMB e degli eventuali requisiti aggiuntivi per gli host che possono connettersi a Data Box.
* **Requisiti di rete per Data Box:** informazioni sui requisiti di rete per il funzionamento ottimale di Data Box.


## <a name="software-requirements"></a>Requisiti software

I requisiti software includono le informazioni sui sistemi operativi supportati, sui browser supportati per l'interfaccia utente Web locale e sui client SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

Di seguito è riportato l'elenco dei sistemi operativi supportati per l'operazione di copia dei dati eseguiti tramite i client connessi al dispositivo Data Box.

| **Sistema operativo** | **Versioni** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
|  Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>File system supportati per client Linux

| **Protocolli** | **Versioni** | 
| --- | --- | 
| SMB |2.X e versioni successive |
| NFS | Tutte le versioni fino alla 4.1 compresa|

### <a name="supported-storage-accounts"></a>Account di archiviazione supportati

Di seguito è riportato un elenco degli account di archiviazione e dei tipi di archiviazione supportati per il dispositivo Data Box. Per l'intero elenco dei diversi tipi di account di archiviazione e delle funzionalità complete, vedere [Tipi di account di archiviazione](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine*** |**File di Azure** |**Note**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S |
| Utilizzo generico v1 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Sono supportati BLOB sia ad accesso frequente che sporadico. |

\*  *I dati caricati nei BLOB di pagine devono essere pari a 512 byte allineati, ad esempio vhd.*

>[!NOTE]
> Gli account di Azure Data Lake Storage Gen 2 non sono supportati.


### <a name="supported-storage-types"></a>Tipi di archiviazione supportati

Di seguito è riportato un elenco dei tipi di archiviazione supportati per il dispositivo Data Box.

| **Formato file** | **Note** |
| --- | --- |
| BLOB in blocchi di Azure | |
| BLOB di pagine di Azure  | I dati devono essere allineati a 512 byte.|
| File di Azure | |


### <a name="supported-web-browsers"></a>Web browser supportati

Di seguito è riportato un elenco dei Web browser supportati per l'interfaccia utente Web locale.

| **Browser** | **Versioni** | **Requisiti aggiuntivi/note** |
| --- | --- | --- |
| Google Chrome |Versione più recente |Testato con Chrome|
| Microsoft Edge |Versione più recente | |
| FireFox | Versione più recente | Testato con FireFox|
| Internet Explorer |Versione più recente |Se non è possibile accedere, verificare se cookie e Javascript sono abilitati. Per abilitare l'accesso all'interfaccia utente, aggiungere l'IP del dispositivo in **Gestione della privacy** in modo che il dispositivo possa accedere ai cookie. |


## <a name="networking-requirements"></a>Requisiti di rete

Il data center disponga di una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE per copiare i dati, ma la velocità dell'operazione ne risentirà.

## <a name="next-step"></a>Passaggio successivo

* [Distribuire Azure Data Box](data-box-deploy-ordered.md)


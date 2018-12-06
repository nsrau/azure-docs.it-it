---
title: Architettura di archiviazione di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Architettura di archiviazione della distribuzione di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e692cc1fd8670cc14b42e4714d84356d4d4c53a2
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275993"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architettura di archiviazione di SAP HANA (istanze Large)

Il layout di archiviazione per SAP HANA in Azure (istanze Large) è configurato da SAP HANA nel modello di distribuzione classico in base alle linee guida consigliate da SAP, come illustrato nel white paper [SAP HANA storage requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisiti per le risorse di archiviazione di SAP HANA).

Nelle unità di istanze Large di HANA della classe di tipo I è disponibile un volume di archiviazione quattro volte superiore al volume di memoria. Per le unità di istanze Large di HANA della classe di tipo II, il volume di archiviazione non è di altre quattro volte superiore. Le unità sono dotate di un volume destinato all'archiviazione dei backup del log delle transazioni di HANA. Per altre informazioni, vedere [Come installare e configurare SAP HANA (istanze Large) in Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per l'allocazione dello spazio di archiviazione, vedere la tabella seguente, che elenca la capacità approssimativa dei diversi volumi disponibili nelle diverse unità di istanze Large di HANA.

| SKU delle istanze Large di HANA | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S96 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S192xm |  11.520 GB |  1536 GB |  1792 GB |  1536 GB |
| S384 | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xxm |  20.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576m | 20.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576xm | 31.744 GB | 4.096 GB | 2.048 GB | 4.096 GB |
| S768m | 28.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768xm | 40.960 GB | 6.144 GB | 4.096 GB | 6.144 GB |
| S960m | 36.000 GB | 4100 GB | 2050 GB | 4100 GB |


I volumi distribuiti effettivi possono variare in base alla distribuzione e allo strumento usato per visualizzare le dimensioni dei volumi.

In caso di suddivisione di uno SKU delle istanze Large di HANA, ecco alcuni esempi delle possibili dimensioni delle partizioni:

| Partizione di memoria in GB | hana/data | hana/log | hana/shared | HANA/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1.024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1.536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


Queste dimensioni sono approssimative e possono variare leggermente in base alla distribuzione e agli strumenti usati per esaminare i volumi. Sono ipotizzabili anche altre dimensioni per le partizioni, ad esempio 2,5 TB. Queste dimensioni di archiviazione vengono calcolate con una formula simile a quella usata per le partizioni riportate sopra. Il termine "partizione" non significa che le risorse del sistema operativo, della memoria o della CPU sono in qualche modo partizionate. Indica le partizioni di archiviazione per le diverse istanze di HANA che si vorranno eventualmente distribuire in una singola unità di istanze Large di HANA. 

Se è necessario altro spazio di archiviazione, il cliente può acquistarne altro in unità da 1 TB. Questo spazio di archiviazione può essere aggiunto come un nuovo volume oppure essere usato per estendere uno o più dei volumi esistenti. Non è possibile ridurre le dimensioni dei volumi originariamente distribuiti, in gran parte documentati nelle tabelle precedenti, né modificare i nomi dei volumi o i nomi di montaggio. I volumi di archiviazione descritti in precedenza vengono collegati alle unità di istanze Large di HANA come volumi NFS4.

È possibile usare gli snapshot di archiviazione a scopo di backup/ripristino e ripristino di emergenza. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per informazioni sui dettagli relativi al layout di archiviazione per lo scenario, vedere [Scenari HLI supportati](hana-supported-scenario.md).

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Eseguire più istanze di SAP HANA in un'unità di istanze Large di HANA

Nelle unità di istanze Large di HANA è possibile ospitare più istanze attive di SAP HANA. Per offrire le funzionalità degli snapshot di archiviazione e del ripristino di emergenza, una configurazione di questo tipo richiede un set di volumi per istanza. Attualmente, le unità di istanze Large di HANA possono essere suddivise come segue:

- **S72, S72m, S96, S144, S192**: con incrementi di 256 GB e 256 GB come unità iniziale più piccola. È possibile combinare incrementi diversi, ad esempio 256 GB e 512 GB, fino al massimo della memoria dell'unità.
- **S144m e S192m**: con incrementi di 256 GB e 512 GB come unità più piccola. È possibile combinare incrementi diversi, ad esempio 512 GB e 768 GB, fino al massimo della memoria dell'unità.
- **Classe di tipo II**: con incrementi di 512 GB e 2 TB come unità iniziale più piccola. È possibile combinare incrementi diversi, ad esempio 512 GB, 1 TB e 1,5 TB, fino al massimo della memoria dell'unità.

Di seguito sono riportati alcuni esempi di esecuzione di più istanze di SAP HANA.

| SKU | Dimensioni memoria | Dimensioni della risorsa di archiviazione | Dimensioni con più database |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 istanza di HANA da 768 GB<br /> o 1 istanza da 512 GB + 1 istanza da 256 GB<br /> o 3 istanze da 256 GB | 
| S72m | 1,5 TB | 6 TB | 3 istanze di HANA da 512 GB<br />o 1 istanza da 512 GB + 1 istanza da 1 TB<br />o 6 istanze da 256 GB<br />o 1 istanza da 1,5 TB | 
| S192m | 4 TB | 16 TB | 8 istanze da 512 GB<br />o 4 istanze da 1 TB<br />o 4 istanze da 512 GB + 2 istanze da 1 TB<br />o 4 istanze da 768 GB + 2 istanze da 512 GB<br />o 1 istanza da 4 TB |
| S384xm | 8 TB | 22 TB | 4 istanze da 2 TB<br />o 2 istanze da 4 TB<br />o 2 istanze da 3 TB + 1 istanza da 2 TB<br />o 2 istanze da 2,5 TB + 1 istanza da 3 TB<br />o 1 istanza da 8 TB |


Esistono anche altre varianti. 

## <a name="encryption-of-data-at-rest"></a>Crittografia dei dati inattivi
Le risorse di archiviazione usate per le istanze Large di HANA consentono una crittografia trasparente dei dati al momento dell'archiviazione sui dischi. Quando un'unità di istanze Large di HANA viene distribuita, è possibile abilitare questo tipo di crittografia. È anche possibile passare ai volumi crittografati in seguito alla distribuzione. Il passaggio dai volumi non crittografati a quelli crittografati è trasparente e non comporta tempi di inattività. 

Con gli SKU della classe di tipo I, il volume in cui è archiviato il LUN di avvio è crittografato. Nel caso degli SKU della classe di tipo II delle istanze Large di HANA, è necessario crittografare il LUN di avvio con i metodi del sistema operativo. Per altre informazioni, contattare il team di gestione dei servizi Microsoft.

**Passaggi successivi**
- Vedere [Scenari supportati nelle istanze Large di HANA](hana-supported-scenario.md)
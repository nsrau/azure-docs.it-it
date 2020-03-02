---
title: Eseguire il mapping di una struttura di cartelle a una topologia di Sincronizzazione file di Azure
description: Mapping di una struttura di file e cartelle esistente alle condivisioni file di Azure per l'uso con Sincronizzazione file di Azure. Un blocco di testo comune, condiviso tra i documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209558"
---
In questo passaggio si sta valutando il numero di condivisioni file di Azure necessarie. Un singolo server di Windows (o cluster) può sincronizzare fino a 30 condivisioni file di Azure.

È possibile che nel StorSimple siano presenti più cartelle da condividere localmente come condivisioni SMB per utenti e app. Il modo più semplice è quello di prevedere una condivisione locale per eseguire il mapping di 1:1 a una condivisione file di Azure. Se questo numero è troppo piccolo, ovvero inferiore a 30 per un singolo server Windows o si prevede di avere due server Windows (60) e così via, è consigliabile usare un mapping 1:1.

Se sono presenti più condivisioni di 30, spesso non è necessario eseguire il mapping di una condivisione locale 1:1 a una condivisione file di Azure.
Valutare le opzioni seguenti:

#### <a name="share-grouping"></a>Raggruppamento di condivisione

Ad esempio, se il reparto risorse umane dispone di un totale di 15 condivisioni, è possibile considerare l'archiviazione di tutti i dati HR in una singola condivisione file di Azure. L'archiviazione di più condivisioni locali in una condivisione file di Azure non impedisce di creare le 15 condivisioni SMB usuali sul server Windows locale. Significa solo che si organizzano le cartelle radice di queste 15 condivisioni come sottocartelle in una cartella comune. Si sincronizza quindi questa cartella comune con una condivisione file di Azure. In questo modo, per questo gruppo di condivisioni locali è necessaria solo una singola condivisione file di Azure nel cloud.

#### <a name="volume-sync"></a>Sincronizzazione del volume

Sincronizzazione file di Azure supporta la sincronizzazione della radice di un volume in una condivisione file di Azure.
Se si sincronizza la cartella radice, tutte le sottocartelle e i file finiranno nella stessa condivisione file di Azure.

#### <a name="other-best-practices-to-consider"></a>Altre procedure consigliate da considerare

Oltre ai 30 limiti di sincronizzazione della condivisione file di Azure per server, la considerazione principale è l'efficienza della sincronizzazione.

Quando nel server sono presenti più condivisioni sincronizzate con la propria condivisione file di Azure, la sincronizzazione può funzionare in parallelo per tutte le condivisioni. Il vettore di scala non è la dimensione di tutti i file in un ambito di sincronizzazione. È il numero di elementi (file e cartelle) che necessitano di elaborazione.

Una singola condivisione file di Azure può avere fino a 100 TiB.
Sincronizzazione file di Azure supporta la sincronizzazione di un massimo di 100 milioni elementi per ogni condivisione file di Azure.

La sincronizzazione del volume radice non sarà sempre la risposta migliore. La sincronizzazione di più posizioni comporta vantaggi, in modo da ridurre il numero di elementi per ogni ambito di sincronizzazione. La configurazione di sincronizzazione file di Azure con un numero inferiore di elementi non è solo importante per la sincronizzazione, ma anche i vantaggi del ripristino sul lato cloud dai backup, oltre ad aiutare la velocità del ripristino di emergenza nel caso in cui si perda il server e ne effettui il provisioning di un nuovo che si connette alla stessa Azure fil condivisioni e.

Usare una combinazione dei concetti precedenti per determinare il numero di condivisioni file di Azure necessarie e le parti dei dati StorSimple esistenti che finiranno con la condivisione file di Azure.

Creare un elenco che registra le considerazioni, in modo che sia possibile farvi riferimento nel passaggio successivo. Organizzare qui è importante perché può essere facile perdere i dettagli del piano di mapping quando si effettua il provisioning di molte risorse contemporaneamente.

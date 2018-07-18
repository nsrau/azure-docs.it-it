---
title: Crittografare i dati delle tabelle di archiviazione di Azure | Microsoft Docs
description: Informazioni sulla crittografia dei dati delle tabelle nell'archiviazione di Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/11/2018
ms.author: sngun
ms.openlocfilehash: 082e8a54cc8625a4bbdea2157f73874dbc86fde2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659725"
---
# <a name="encrypt-table-data"></a>Crittografare i dati delle tabelle
La libreria client di Archiviazione di Azure per .NET supporta la crittografia di proprietà di entità stringa per le operazioni di inserimento e sostituzione. Le stringhe crittografate vengono archiviate nel servizio come proprietà binarie e vengono convertite nuovamente in stringhe dopo la decrittografia.    

Per le tabelle, oltre al criterio di crittografia, gli utenti devono specificare le proprietà da crittografare. Questa operazione può essere eseguita specificando un attributo [EncryptProperty] \(per le entità POCO che derivano da TableEntity) o un resolver di crittografia nelle opzioni di richiesta. Un resolver di crittografia è un delegato che accetta una chiave di partizione, una chiave di riga e un nome di proprietà e restituisce un valore booleano che indica se tale proprietà deve essere crittografata. Durante la crittografia, la libreria client usa queste informazioni per decidere se una proprietà deve essere crittografata durante la scrittura in rete. Il delegato fornisce inoltre la possibilità di logica per la modalità di crittografia delle proprietà. (Ad esempio, se X, quindi crittografa la proprietà A; in caso contrario crittografa le proprietà A e B). Non è necessario fornire queste informazioni durante la lettura o la query su entità.

## <a name="merge-support"></a>Supporto per l'unione

L’unione non è attualmente supportata. Poiché un subset di proprietà potrebbe essere stato crittografato in precedenza usando una chiave diversa, la semplice unione delle nuove proprietà e l'aggiornamento dei metadati comportano la perdita di dati. L'unione richiede chiamate a servizi aggiuntivi per la lettura dell’entità preesistente dal servizio o l’utilizzo di una nuova chiave per ogni proprietà, entrambe operazioni non idonee per motivi di prestazioni.     

Per informazioni sulla crittografia dei dati di tabella, vedere [Crittografia lato client e insieme di credenziali delle chiavi di Azure per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Passaggi successivi

- [Modelli di progettazione tabella](table-storage-design-patterns.md)
- [Modellazione di relazioni](table-storage-design-modeling.md)
- [Modellazione di relazioni](table-storage-design-modeling.md)
- [Progettazione per la modifica dei dati](table-storage-design-for-modification.md)
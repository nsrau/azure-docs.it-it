---
title: Crittografia di dati inattivi del database NoSQL - Azure DocumentDB | Documentazione Microsoft
description: Informazioni su come Azure DocumentDB fornisce la crittografia predefinita di tutti i dati NoSQL.
services: documentdb
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/31/2017
ms.author: voellm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: 9c97f76d581935cb85f33b0016c192ded3b6f880
ms.contentlocale: it-it
ms.lasthandoff: 04/28/2017


---

# <a name="documentdb-nosql-database-encryption-at-rest"></a>Crittografia di dati inattivi del database NoSQL in DocumentDB

Il termine "crittografia di dati inattivi" denota in genere la crittografia dei dati su dispositivi di archiviazione non volatili, come ad esempio unità a stato solido (SSD) e dischi rigidi (HDD).  DocumentDB archivia i propri database primari su unità SSD e gli allegati multimediali e i backup nei blob di Azure, il cui backup in genere è eseguito su unità HDD.  Con il rilascio della crittografia di dati inattivi per DocumentDB, tutti i database, gli allegati multimediali e i backup sono ora crittografati.  Ciò significa che vengono crittografati sia i dati in transito (attraverso la rete) sia quelli inattivi (archiviazione non volatile) fornendo una crittografia end-to-end.

Il servizio PaaS ha cercato di semplificare molto l'utilizzo di DocumentDB.  A tal fine viene eseguita la crittografia di tutti i dati utente inattivi e in transito archiviati in DocumentDB senza che sia necessaria alcuna azione da parte dell'utente.  Un'altra possibilità è abilitare la crittografia dei dati per impostazione predefinita.  Non sono presenti controlli da abilitare o disabilitare e questa funzionalità viene fornita continuando a soddisfare i [contratti di servizio relativi alla disponibilità e alle prestazioni](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/).

## <a name="how-does-encryption-at-rest-work"></a>Come funziona la crittografia dei dati inattivi?

La crittografia dei dati inattivi viene implementata attraverso una serie di tecnologie di protezione, inclusi i sistemi di archiviazione protetta delle chiavi, le reti crittografate e le API di crittografia.  Il diagramma seguente mostra come viene separata l'archiviazione dei dati crittografati e la gestione delle chiavi.  I sistemi che decrittografano ed elaborano i dati devono comunicare con i sistemi di gestione delle chiavi.

![Diagramma di progettazione](./media/documentdb-nosql-database-encryption-at-rest/design-diagram.png)

Il flusso di base di una richiesta dell'utente è il seguente:
- L'account del database utente viene preparato e le chiavi di archiviazione vengono recuperate tramite una richiesta al provider di risorse (RP) del servizio di gestione.
- Un utente crea una connessione a DocumentDB tramite il trasporto HTTPS/protetto; i componenti SDK consentono di estrapolare i dettagli.
- L'utente invia un documento JSON da archiviare tramite la connessione protetta creata in precedenza.
- Il documento JSON è indicizzato, a meno che l'utente non abbia disabilitato l'indicizzazione.
- Sia il documento JSON sia i dati dell'indice vengono scritti in un archivio protetto.
- Periodicamente i dati vengono letti dalla risorsa di archiviazione protetta e viene eseguito il backup nell'archivio BLOB crittografato di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="q-how-much-more-does-azure-storage-cost-if-sse-is-enabled"></a>D: A quanto ammonta il costo aggiuntivo dell'Archiviazione di Azure se si abilita Crittografia del servizio di archiviazione?
R: Non sono previsti costi aggiuntivi.

### <a name="q-who-manages-the-encryption-keys"></a>D: Chi gestisce le chiavi di crittografia?
R: Le chiavi vengono gestite da Microsoft.

### <a name="q-can-i-use-my-own-encryption-keys"></a>D: È possibile usare le proprie chiavi di crittografia?
R: DocumentDB è un servizio PaaS e l'intento è quello di garantire la semplicità d'uso.  Spesso questa domanda viene usata come domanda indiretta per soddisfare una conformità come PCI-DSS.  Nell'ambito di creazione di questa funzionalità, il lavoro di collaborazione con i garanti della conformità ha permesso ai clienti che usano DocumentDB di soddisfare i propri requisiti senza dover gestire personalmente le chiavi.
Ecco perché attualmente viene offerta agli utenti l'opzione di farsi carico dell'onere di gestione delle chiavi.

### <a name="q-what-regions-have-encryption-turned-on"></a>D: In quali aree è attiva la crittografia?
R: La crittografia è attiva in tutte le aree di DocumentDB per tutti i dati utente.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della sicurezza di DocumentDB e dei miglioramenti più recenti, vedere [Sicurezza del database NoSQL in DocumentDB](documentdb-nosql-database-security.md).

Per altre informazioni sulle certificazioni Microsoft, vedere il [Centro protezione di Azure](https://azure.microsoft.com/en-us/support/trust-center/).


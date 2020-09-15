---
title: Domande frequenti su Azure Resource Mover?
description: Risposte alle domande comuni su Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: raynew
ms.openlocfilehash: 18de210d817ee309ac4970ecea6b62f6ef03ef02
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530404"
---
# <a name="common-questions"></a>Domande frequenti

Questo articolo risponde a domande comuni su [Azure Resource Mover](overview.md).

## <a name="general"></a>Generale

### <a name="is-resource-mover-generally-available"></a>Il motore di risorse è disponibile a livello generale?

Il motore di risorse è attualmente disponibile in anteprima pubblica. Sono supportati i carichi di lavoro di produzione.



## <a name="moving-across-regions"></a>Trasferimento tra aree

### <a name="can-i-move-resources-across-any-regions"></a>È possibile spostare le risorse in tutte le aree?

Attualmente, è possibile spostare le risorse da qualsiasi area pubblica di origine a qualsiasi area pubblica di destinazione, a seconda dei [tipi di risorse disponibili in tale area](https://azure.microsoft.com/global-infrastructure/services/). Il trasferimento delle risorse nelle aree di Azure per enti pubblici non è attualmente supportato.

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>Quali risorse è possibile spostare tra le aree usando il motore di risorse?

Tramite il motore di risorse è possibile spostare le risorse seguenti tra le aree:

- Macchine virtuali di Azure e dischi associati
- Schede di interfaccia di rete
- Set di disponibilità 
- Reti virtuali di Azure 
- Indirizzi IP pubblici
- Gruppi di sicurezza di rete (NSG)
- Bilanciamento del carico interno e pubblico 
- Database SQL di Azure e pool elastici


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>È possibile spostare le risorse tra le sottoscrizioni quando si spostano in aree diverse?

È possibile modificare la sottoscrizione dopo aver spostato le risorse nell'area di destinazione. [Altre](../azure-resource-manager/management/move-resource-group-and-subscription.md) informazioni sullo stato di trasferimento delle risorse in una sottoscrizione diversa. 

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>Dove vengono archiviati i metadati per lo stato di trasferimento tra aree?

Viene archiviato in un database di [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e nell' [archiviazione BLOB di Azure](../storage/common/storage-service-encryption.md)in una sottoscrizione Microsoft. Attualmente i metadati vengono archiviati nell'area Stati Uniti orientali 2 ed Europa settentrionale. Questa copertura verrà espansa in altre aree. Questo non impedisce di trasferire le risorse in tutte le aree pubbliche.

### <a name="is-the-collected-metadata-encrypted"></a>I metadati raccolti sono crittografati?

Sì, sia in transito che inattivi.
- Durante il transito, i metadati vengono inviati in modo sicuro al servizio Resource Mover tramite Internet tramite HTTPS.
- Nell'archiviazione i metadati sono crittografati.

### <a name="how-is-managed-identity-used-in-resource-mover"></a>In che modo viene usata l'identità gestita nel motore di risorse?

[Identità gestita](../active-directory/managed-identities-azure-resources/overview.md) (precedentemente nota come identità del servizio gestita (MIS)) fornisce servizi di Azure con un'identità gestita automaticamente in Azure ad.
- Il motore di risorse usa l'identità gestita in modo che possa accedere alle sottoscrizioni di Azure per spostare le risorse tra le aree.
- Una raccolta di spostamento richiede un'identità assegnata dal sistema, con accesso alla sottoscrizione che contiene le risorse che si stanno spostando.

- Se si spostano risorse tra aree nel portale, questo processo viene eseguito automaticamente.
- Se si spostano le risorse usando PowerShell, si eseguono i cmdlet per assegnare un'identità assegnata dal sistema alla raccolta e quindi si assegna un ruolo con le autorizzazioni appropriate per la sottoscrizione all'entità Identity. 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>Quali autorizzazioni di identità gestite sono necessarie per il motore risorse?

Per l'identità gestita di Azure Resource Mover sono necessarie almeno le autorizzazioni seguenti: 

- Autorizzazione per la scrittura e la creazione di risorse nella sottoscrizione utente, disponibile con il ruolo *collaboratore* . 
- Autorizzazione per la creazione di assegnazioni di ruolo. Generalmente disponibile con i ruoli *proprietario* o *amministratore accesso utenti* o con un ruolo personalizzato con l' *autorizzazione Microsoft. Authorization/Role/Write* assegnata. Questa autorizzazione non è necessaria se all'identità gestita della risorsa di condivisione dati è già stato concesso l'accesso all'archivio dati di Azure. 
 
Quando si aggiungono risorse nell'hub del motore di risorse nel portale, le autorizzazioni vengono gestite automaticamente, purché l'utente disponga delle autorizzazioni descritte in precedenza. Se si aggiungono risorse con PowerShell, si assegnano le autorizzazioni manualmente.

> [!IMPORTANT]
> Si consiglia vivamente di non modificare o rimuovere le assegnazioni di ruolo Identity. 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>Cosa fare se non si hanno le autorizzazioni per assegnare l'identità del ruolo?

**Possibile causa** | **Consiglio**
--- | ---
Quando si aggiunge una risorsa per la prima volta, non si è un *collaboratore* e un *amministratore di accesso utente* (o *proprietario*). | Utilizzare un account con autorizzazioni *collaboratore* e *amministratore accesso utenti* (o *proprietario*) per la sottoscrizione.
L'identità gestita del motore di risorse non ha il ruolo richiesto. | Aggiungere i ruoli "collaboratore" e "amministratore accesso utenti".
L'identità gestita del motore di risorse è stata reimpostata su *None*. | Riabilitare un'identità assegnata dal sistema nell' **identità**di spostamento della raccolta >. In alternativa, aggiungere di nuovo la risorsa in **Aggiungi risorse**, che esegue la stessa operazione.  
La sottoscrizione è stata spostata in un tenant diverso. | Disabilitare e quindi abilitare l'identità gestita per la raccolta di spostamento.


## <a name="next-steps"></a>Passaggi successivi

[Altre](about-move-process.md) informazioni sui componenti del motore di risorse e il processo di spostamento.

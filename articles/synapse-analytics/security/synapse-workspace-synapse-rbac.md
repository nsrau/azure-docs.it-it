---
title: Controllo degli accessi in base al ruolo sinapsi
description: Articolo che illustra il controllo degli accessi in base al ruolo in Azure sinapsi Analytics
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7972f34bf0d2b93828899903e013c2e35bc997c0
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523528"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Che cos'è il controllo degli accessi in base al ruolo di sinapsi (RBAC)?

Il controllo degli accessi in base al ruolo [(RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) estende le funzionalità di controllo degli accessi in base al ruolo 

Il controllo degli accessi in base al ruolo di Azure viene usato per gestire gli utenti che possono creare, aggiornare o eliminare l'area di lavoro sinapsi e i relativi pool SQL, pool di Apache Spark e runtime di integrazione.

Il controllo degli accessi in base al ruolo viene usato per gestire chi può:
- Pubblicare elementi di codice ed elencare o accedere a elementi di codice pubblicati, 
- Eseguire codice nei pool Spark Apache e nei runtime di integrazione
- Accedere ai servizi collegati (dati) protetti da credenziali 
- Monitorare o annullare l'esecuzione del processo, rivedere l'output del processo e i log di esecuzione.  

>[!Note]
>Anche se il controllo degli accessi in base al ruolo viene usato per gestire l'accesso agli script SQL pubblicati, fornisce solo il controllo di accesso limitato ai pool SQL senza server e _non_ viene usato per controllare l'accesso ai pool SQL dedicati.  L'accesso ai pool SQL è controllato principalmente mediante la sicurezza di SQL.

## <a name="what-can-i-do-with-synapse-rbac"></a>Cosa è possibile fare con le sinapsi RBAC?

Ecco alcuni esempi di ciò che è possibile fare con il controllo degli accessi in base al ruolo:
  - Consentire a un utente di pubblicare le modifiche apportate ai Apache Spark notebook e ai processi al servizio Live.
  - Consentire a un utente di eseguire e annullare i notebook e i processi Spark in un pool di Apache Spark specifico.
  - Consentire a un utente di utilizzare credenziali specifiche in modo da poter eseguire pipeline protette dall'identità del sistema dell'area di lavoro e accedere ai dati nei servizi collegati protetti con le credenziali. 
  - Consentire a un amministratore di gestire, monitorare e annullare l'esecuzione del processo in pool Spark specifici.    

## <a name="how-synapse-rbac-works"></a>Funzionamento di sinapsi RBAC
Come per il controllo degli accessi in base al ruolo di Azure, la sinapsi RBAC funziona creando assegnazioni Un'assegnazione di ruolo è costituita da tre elementi: un'entità di sicurezza, una definizione del ruolo e un ambito.  

### <a name="security-principals"></a>Entità di sicurezza

Un' _entità di sicurezza_ è un utente, un gruppo, un'entità servizio o un'identità gestita.

### <a name="roles"></a>Ruoli
 
Un _ruolo_ è una raccolta di autorizzazioni o azioni che possono essere eseguite su tipi di risorse o tipi di artefatti specifici.

Sinapsi fornisce ruoli predefiniti che definiscono raccolte di azioni che soddisfano le esigenze di utenti diversi:
- Gli amministratori possono ottenere l'accesso completo per creare e configurare un'area di lavoro 
- Gli sviluppatori possono creare, aggiornare ed eseguire il debug di script SQL, notebook, pipeline e flussi di dati, ma non essere in grado di pubblicare o eseguire questo codice per le risorse di calcolo o i dati di produzione
- Gli operatori possono monitorare e gestire lo stato del sistema, l'esecuzione dell'applicazione e i log di revisione, senza accedere al codice o agli output dell'esecuzione.
- Il personale di sicurezza può gestire e configurare gli endpoint senza avere accesso al codice, a risorse di calcolo o a dati.

[Altre](./synapse-workspace-synapse-rbac-roles.md) informazioni sui ruoli sinapsi predefiniti. 

### <a name="scopes"></a>Ambiti

Un _ambito_ definisce le risorse o gli elementi a cui viene applicato l'accesso.  La sinapsi supporta gli ambiti gerarchici.  Le autorizzazioni concesse a un ambito di livello superiore vengono ereditate dagli oggetti a un livello inferiore.  Nelle sinapsi RBAC l'ambito di primo livello è un'area di lavoro.  L'assegnazione di un ruolo con ambito area di lavoro concede le autorizzazioni a tutti gli oggetti applicabili nell'area di lavoro.  

Gli ambiti supportati correnti in un'area di lavoro sono: pool di Apache Spark, Runtime di integrazione, servizio collegato e credenziali. 

L'accesso agli elementi del codice viene concesso con l'ambito dell'area di lavoro.  La concessione dell'accesso alle raccolte di artefatti all'interno di un'area di lavoro sarà supportata in una versione successiva.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Risoluzione delle assegnazioni di ruolo per determinare le autorizzazioni

Un'assegnazione di ruolo concede all'entità le autorizzazioni definite dal ruolo nell'ambito specificato.

Il controllo degli accessi in base al ruolo è un modello aggiuntivo, ad esempio RBAC È possibile assegnare più ruoli a una singola entità e a ambiti diversi. Quando si calcolano le autorizzazioni di un'entità di sicurezza, il sistema considera tutti i ruoli assegnati al principale e ai gruppi che includono direttamente o indirettamente l'entità.  Considera inoltre l'ambito di ogni assegnazione nella determinazione delle autorizzazioni valide.  

## <a name="enforcing-assigned-permissions"></a>Applicazione delle autorizzazioni assegnate

In sinapsi studio è possibile che pulsanti o opzioni specifiche risultino disattivate o che venga restituito un errore di autorizzazione quando si tenta di eseguire un'azione se non si hanno le autorizzazioni necessarie. 

Se un pulsante o un'opzione è disabilitata, il puntatore del mouse sul pulsante o sull'opzione Mostra una descrizione comando con l'autorizzazione richiesta.  Contattare un amministratore di sinapsi per assegnare un ruolo che conceda l'autorizzazione necessaria. È possibile visualizzare i ruoli che [forniscono azioni specifiche](./synapse-workspace-synapse-rbac-roles.md).

## <a name="who-can-assign-synapse-rbac-roles"></a>Chi può assegnare i ruoli RBAC controllo

Solo un amministratore di sinapsi può assegnare i ruoli RBAC.  Un amministratore di sinapsi a livello di area di lavoro può concedere l'accesso a qualsiasi ambito.  Un amministratore di sinapsi a un ambito di livello inferiore può concedere l'accesso solo a tale ambito. 

Quando viene creata una nuova area di lavoro, al creatore viene assegnato automaticamente il ruolo di amministratore di sinapsi nell'ambito dell'area di lavoro.   

## <a name="where-do-i-manage-synapse-rbac"></a>Dove si gestisce la sinapsi RBAC?

Il controllo degli accessi in base al ruolo è gestito da sinapsi Studio mediante gli strumenti di controllo di accesso nell'hub Gestisci. 

## <a name="next-steps"></a>Passaggi successivi

Comprendere i ruoli di controllo degli accessi in base al [ruolo di sinapsi](./synapse-workspace-synapse-rbac-roles.md)predefiniti.

Informazioni [su come esaminare le assegnazioni di ruolo RBAC](./how-to-review-synapse-rbac-role-assignments.md) per un'area di lavoro.

Informazioni [su come assegnare i ruoli di](./how-to-manage-synapse-rbac-role-assignments.md) controllo delle sinapsi
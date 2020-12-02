---
title: Comprendere i ruoli necessari per eseguire attività comuni in sinapsi
description: Questo articolo descrive i ruoli predefiniti di sinapsi RBAC necessari per eseguire attività specifiche
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: aadc8e817eb2b5de856ac73cfd010b48d0531bfc
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523498"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Comprendere i ruoli necessari per eseguire attività comuni in sinapsi

Questo articolo consente di comprendere quali sinapsi RBAC (controllo degli accessi in base al ruolo) o ruoli RBAC di Azure è necessario svolgere in sinapsi Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Riepilogo del flusso di lavoro e controllo di accesso di sinapsi Studio 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Accesso a sinapsi studio e visualizzazione del contenuto

- È possibile aprire sinapsi studio e visualizzare i dettagli dell'area di lavoro ed elencare le risorse di Azure (pool SQL, pool Spark o runtime di integrazione) se è stato assegnato un ruolo di controllo degli accessi in base al ruolo di controllo degli accessi in base al ruolo o al ruolo di lettore o proprietario di Azure nell'area di lavoro.

### <a name="resource-management"></a>Resource management

- Se si è un proprietario o un collaboratore di Azure nell'area di lavoro, è possibile creare pool SQL, pool di Apache Spark e runtime di integrazione.
- È possibile sospendere o ridimensionare un pool SQL dedicato, configurare un pool Spark o un runtime di integrazione se si è un proprietario o un collaboratore di Azure nell'area di lavoro o nella risorsa.

### <a name="viewing-and-editing-code-artifacts"></a>Visualizzazione e modifica di elementi di codice

- Con l'accesso a sinapsi studio è possibile creare nuovi elementi di codice, ad esempio script SQL, notebook, processi Spark, servizi collegati, pipeline, flussi di lavoro, trigger e credenziali.  Questi elementi possono essere pubblicati o salvati con autorizzazioni aggiuntive.  
- Se si è un utente dell'artefatto sinapsi, un autore di elementi sinapsi, un collaboratore sinapsi o un amministratore di sinapsi è possibile elencare, aprire e modificare gli elementi di codice già pubblicati.

### <a name="executing-your-code"></a>Esecuzione del codice

- È possibile eseguire script SQL in pool SQL se si dispone delle autorizzazioni SQL necessarie definite nei pool SQL.  
- È possibile eseguire i notebook e i processi Spark se si dispone di autorizzazioni operatore di calcolo sinapsi per l'area di lavoro o per pool di Apache Spark specifici.  
- Con le autorizzazioni dell'operatore di calcolo per l'area di lavoro o specifici runtime di integrazione e le autorizzazioni appropriate per le credenziali è possibile eseguire le pipeline.

### <a name="monitoring-and-managing-execution"></a>Monitoraggio e gestione dell'esecuzione
- È possibile esaminare lo stato dei notebook e dei processi in esecuzione nei pool di Apache Spark se si è un utente sinapsi.
- È possibile esaminare i log e annullare i processi e le pipeline in esecuzione se si è un operatore di calcolo sinapsi nell'area di lavoro o per una pipeline o un pool Spark specifico.  

### <a name="publishing-and-saving-your-code"></a>Pubblicazione e salvataggio del codice

- È possibile pubblicare artefatti di codice nuovi o aggiornati nel servizio se si è un editore di elementi sinapsi, un collaboratore sinapsi o un amministratore di sinapsi. 
- È possibile eseguire il commit degli elementi di codice in un ramo di lavoro di un repository git se l'area di lavoro è abilitata per git e si dispone delle autorizzazioni git. Con git abilitato, la pubblicazione è consentita solo dal ramo collaborazione.
- Se si chiude sinapsi Studio senza pubblicare o eseguire il commit delle modifiche agli artefatti di codice, le modifiche andranno perse.


## <a name="tasks-and-required-roles"></a>Attività e ruoli necessari

La tabella seguente elenca le attività comuni e per ogni attività, i ruoli sinapsi RBAC o controllo degli accessi in base al ruolo di Azure necessari.  

>[!Note]
>- L'amministratore di sinapsi non è elencato per ogni attività a meno che non sia l'unico ruolo che fornisce l'autorizzazione necessaria.  Un amministratore sinapsi può eseguire tutte le attività abilitate da altri ruoli di controllo degli accessi in base al ruolo</br>
>- Tutti i ruoli di controllo degli accessi in base al ruolo in qualsiasi ambito forniscono autorizzazioni utente sinapsi nell'area di lavoro
>- Tutte le autorizzazioni/azioni RBAC RBAC visualizzate nella tabella sono precedute da Microsoft/sinapsi/workspaces/... </br>


Attività (desidero...) |Ruolo (devo essere...)|Autorizzazione/azione RBAC RBAC
--|--|--
|Aprire sinapsi studio in un'area di lavoro|Utente sinapsi o|leggere
| |Proprietario, collaboratore o lettore di Azure nell'area di lavoro|none
|Elencare pool SQL, pool di Apache Spark, Runtime di integrazione e accedere ai dettagli di configurazione|Utente sinapsi o|leggere|
||Proprietario, collaboratore o lettore di Azure nell'area di lavoro|none
|Elencare i servizi collegati, le credenziali, gli endpoint privati gestiti|Utente sinapsi|leggere
**Pool SQL**||
Creare un pool SQL dedicato o un pool SQL senza server|Proprietario o collaboratore di Azure nell'area di lavoro|none
Gestire (sospendere, ridimensionare o eliminare) un pool SQL dedicato|Proprietario o collaboratore di Azure nel pool SQL o nell'area di lavoro|none
Creazione di uno script SQL</br>|Utente sinapsi o </br>Proprietario o collaboratore di Azure nell'area di lavoro </br>*Per eseguire uno script SQL sono necessarie autorizzazioni SQL aggiuntive*.|
Elencare e aprire qualsiasi script SQL pubblicato| Utente artefatto sinapsi, autore artefatto, collaboratore sinapsi|elementi/lettura
Eseguire uno script SQL in un pool SQL senza server|Autorizzazioni SQL nel pool (concesse automaticamente a un amministratore di sinapsi)|none
Eseguire uno script SQL in un pool SQL dedicato|Sono necessarie autorizzazioni SQL per il pool|none
Pubblicare uno script SQL nuovo, aggiornato o eliminato|Server di pubblicazione artefatto sinapsi, collaboratore sinapsi|SQLScripts/Write, Delete
Eseguire il commit delle modifiche apportate a uno script SQL in un repository git|Richiede autorizzazioni git per il repository|
Assegnare Active Directory amministratore nell'area di lavoro (tramite le proprietà dell'area di lavoro nel portale di Azure)|Proprietario o collaboratore di Azure nell'area di lavoro |
**Pool di Apache Spark**||
Creare un pool di Apache Spark|Proprietario o collaboratore di Azure nell'area di lavoro|
Monitorare applicazioni Apache Spark| Utente sinapsi|leggere
Visualizzare i log per l'esecuzione del notebook e del processo |Operatore di calcolo sinapsi|
Annulla qualsiasi notebook o processo Spark in esecuzione in un pool di Apache Spark|Operatore di calcolo sinapsi nel pool di Apache Spark.|bigDataPools/useCompute
Creare un notebook o una definizione di processo|Utente sinapsi o proprietario, collaboratore o lettore di Azure nell'area di lavoro</br> *Per eseguire, pubblicare o salvare sono necessarie autorizzazioni aggiuntive*|leggere
Elencare e aprire un notebook o una definizione di processo pubblicati, inclusa la revisione degli output salvati|Utente artefatto sinapsi, server di pubblicazione artefatto sinapsi, collaboratore sinapsi nell'area di lavoro|elementi/lettura
Eseguire un notebook ed esaminare l'output|Sinapsi Apache Spark Administrator, operatore di calcolo sinapsi nel pool di Apache Spark selezionato|bigDataPools/useCompute 
Pubblicare o eliminare un notebook o una definizione di processo (incluso l'output) nel servizio|Autore dell'artefatto nell'area di lavoro, sinapsi Apache Spark amministratore|Notebook/scrittura, eliminazione
Eseguire il commit delle modifiche apportate a un notebook o alla definizione del processo nel ramo di lavoro git|Autorizzazioni git|none
**Pipeline, Runtime di integrazione, flussi di data, DataSet e trigger**||
Creare, aggiornare o eliminare un runtime di integrazione|Proprietario o collaboratore di Azure nell'area di lavoro|
Monitorare lo stato di runtime di integrazione|Utente sinapsi|lettura, pipeline/viewOutputs
Esaminare le esecuzioni di pipeline|Collaboratore per autore/sinapsi artefatto sinapsi|lettura, pipeline/viewOutputs 
Creare una pipeline |Utente sinapsi </br>[**_in considerazione + utente credenziale sinapsi in WorkspaceSystemIdentity_* _]</br>Sono necessarie autorizzazioni di _Additional per pubblicare o salvare *|Read, credentials/UseSecret/Action
Creare un flusso di dati, un set di dati o un trigger |Utente sinapsi</br>*Sono necessarie autorizzazioni aggiuntive per pubblicare o salvare*|leggere
Elencare e aprire una pipeline pubblicata |Utente artefatto sinapsi | elementi/lettura
Anteprima dati del set di dati|Utente sinapsi + credenziali sinapsi utente in WorkspaceSystemIdentity| 
Eseguire il debug di una pipeline usando il runtime di integrazione predefinito|Utente sinapsi e credenziali sinapsi utente sulla credenziale WorkspaceSystemIdentity|lettura </br>credenziali/useSecret
Creare un trigger, incluso trigger Now|Utente sinapsi + credenziali sinapsi utente in WorkspaceSystemIdentity|Read, credentials/useSecret/Action
Copiare dati utilizzando lo strumento Copia dati|Utente sinapsi e credenziali sinapsi utente nell'identità del sistema dell'area di lavoro|Read, credentials/useSecret/Action
Inserire dati (usando una pianificazione)|Utente di sinapsi Author + sinapsi Credential nell'identità del sistema dell'area di lavoro|Read, credentials/useSecret/Action
Pubblicare una pipeline nuova, aggiornata o eliminata, un flusso di data o un trigger nel servizio|Server di pubblicazione dell'artefatto sinapsi nell'area di lavoro|pipeline/scrittura, eliminazione</br>flussi di flussi di scrittura, eliminazione</br>trigger/scrittura, eliminazione
Pubblicare un nuovo flusso di dati, un set di dati o un trigger aggiornato o eliminato nel servizio|Autore artefatto nell'area di lavoro|trigger/scrittura, eliminazione
Salvare (eseguire il commit) le modifiche apportate a pipeline, flussi di data, set di DataSet e trigger nel repository git |Autorizzazioni git|none 
**Servizi collegati**||
Creare un servizio collegato (incluso l'assegnazione di una credenziale)|Utente sinapsi</br>*Per eseguire, pubblicare o salvare sono necessarie autorizzazioni aggiuntive*|leggere
Elencare e aprire un servizio collegato pubblicato|Utente artefatto sinapsi|linkedServices/scrittura, eliminazione  
Testare la connessione in un servizio collegato protetto da una credenziale|Utente sinapsi e credenziali sinapsi|credenziali/useSecret/azione|
Pubblicare un servizio collegato|Server di pubblicazione artefatto sinapsi|linkedServices/scrittura, eliminazione
Salvare (eseguire il commit) le definizioni dei servizi collegati nel repository git|Autorizzazioni git|none
**Gestione degli accessi**||
Esaminare le assegnazioni di ruolo RBAC RBAC in qualsiasi ambito|Utente sinapsi|leggere
Assegnare e rimuovere le assegnazioni di ruolo RBAC RBAC per utenti, gruppi ed entità servizio| Amministratore sinapsi nell'area di lavoro o in un ambito specifico dell'elemento dell'area di lavoro|roleAssignments/scrittura, eliminazione
Creare o rimuovere l'accesso RBAC RBAC agli elementi del codice|Amministratore della sinapsi nell'ambito dell'area di lavoro|roleAssignments/scrittura, eliminazione   

>[!Note]
>Gli utenti guest di un altro tenant non sono in grado di esaminare, aggiungere o modificare le assegnazioni di ruolo indipendentemente dal ruolo assegnato. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su come esaminare le assegnazioni di ruolo RBAC per le sinapsi](./how-to-review-synapse-rbac-role-assignments.md)

Informazioni [su come gestire le assegnazioni di ruolo RBAC per le sinapsi](./how-to-manage-synapse-rbac-role-assignments.md). 
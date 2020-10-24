---
title: Impostare i ruoli di Azure per l'accesso amministrativo di Azure
titleSuffix: Azure Cognitive Search
description: Controllo degli accessi in base al ruolo di Azure (RBAC di Azure) nella portale di Azure per il controllo e la delega delle attività amministrative per la gestione ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519500"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Impostare i ruoli di Azure per l'accesso amministrativo ad Azure ricerca cognitiva

Azure offre un [modello di autorizzazione basata sui ruoli globali](../role-based-access-control/role-assignments-portal.md) per tutti i servizi gestiti tramite il portale o le API di Resource Manager. I ruoli proprietario, collaboratore e lettore determinano il livello di *amministrazione del servizio* per Active Directory utenti, gruppi e entità di sicurezza assegnati a ogni ruolo. 

> [!Note]
> Non è disponibile alcun controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per la protezione del contenuto nel servizio. Si userà una chiave API di amministrazione o una chiave API di query per le richieste autenticate al servizio stesso. Per gli accessi in base al ruolo sui risultati della ricerca è possibile creare filtri di sicurezza per vagliare i risultati in base all'identità, rimuovendo eventuali documenti a cui il richiedente non dovrebbe avere accesso. Per altre informazioni, vedere [filtri di sicurezza](search-security-trimming-for-azure-search.md).

## <a name="management-tasks-by-role"></a>Attività di gestione in base al ruolo

Per ricerca cognitiva di Azure, i ruoli sono associati ai livelli di autorizzazione che supportano le seguenti attività di gestione:

| Ruolo | Attività |
| --- | --- |
| Proprietario |Creare o eliminare il servizio o qualsiasi oggetto nel servizio, inclusi chiavi API, indici, indicizzatori, origini dati di un indicizzatore e pianificazioni di indicizzatore.<p>Visualizzare lo stato del servizio, inclusi conteggi e dimensioni.<p>Aggiunta o eliminazione dell'appartenenza al ruolo, che può essere gestita solo da un Proprietario.<p>Gli amministratori delle sottoscrizioni e i proprietari del servizio vengono aggiunti automaticamente al ruolo proprietario. |
| Autore di contributi | Lo stesso livello di accesso del proprietario, meno la gestione dei ruoli di Azure. Ad esempio, un Collaboratore può creare o eliminare un oggetto o visualizzare e rigenerare [chiavi API](search-security-api-keys.md), ma non può modificare le appartenenze ai ruoli.<br><br>[Servizio di ricerca collaboratore](../role-based-access-control/built-in-roles.md#search-service-contributor) è equivalente al ruolo predefinito collaboratore generico. |
| Reader |Visualizzare gli elementi di base del servizio, ad esempio endpoint servizio, sottoscrizione, gruppo di risorse, area, livello e capacità. È anche possibile visualizzare le metriche del servizio, ad esempio query medie al secondo, nella scheda monitoraggio. I membri di questo ruolo non possono visualizzare le informazioni relative a indici, indicizzatori, origini dati o competenze. Sono inclusi i dati di utilizzo per tali oggetti, ad esempio il numero di indici presenti nel servizio. |

I ruoli non concedono diritti di accesso all'endpoint di servizio. Le operazioni del servizio di ricerca, ad esempio la gestione e il popolamento degli indici e le query sui dati di ricerca, sono controllate tramite le chiavi API, non tramite i ruoli. Per altre informazioni, vedere [Gestire le chiavi API](search-security-api-keys.md).

## <a name="permissions-table"></a>Tabella delle autorizzazioni

Nella tabella seguente sono riepilogate le operazioni consentite in Azure ricerca cognitiva e la chiave che sblocca l'accesso a una determinata operazione.

Le autorizzazioni RBAC di Azure si applicano alle operazioni del portale e alla gestione dei servizi (creare, eliminare o modificare un servizio o le relative chiavi API). Le chiavi API vengono create dopo l'esistenza di un servizio e si applicano alle operazioni sul contenuto nel servizio. Inoltre, per le operazioni correlate al contenuto nel portale, ad esempio la creazione o l'eliminazione di oggetti, un proprietario o un collaboratore RBAC di Azure interagisce con il servizio con una chiave API di amministrazione implicita.

| Operazione | Controllato da |
|-----------|-------------------------|
| Creare un servizio | Autorizzazioni RBAC di Azure: proprietario o collaboratore |
| Ridimensionare un servizio | Autorizzazioni RBAC di Azure: proprietario o collaboratore|
| Eliminare un servizio | Autorizzazioni RBAC di Azure: proprietario o collaboratore |
| Gestisci chiavi di amministratore o di query | Autorizzazioni RBAC di Azure: proprietario o collaboratore|
| Visualizzare le informazioni sul servizio nel portale o in un'API di gestione | Autorizzazioni RBAC di Azure: proprietario, collaboratore o lettore  |
| Visualizzare le informazioni sugli oggetti e le metriche nel portale o in un'API di gestione | Autorizzazioni RBAC di Azure: proprietario o collaboratore |
| Creare, modificare, eliminare oggetti nel servizio: <br>Indici e parti componenti (incluse definizioni analizzatore, profili di punteggio, opzioni CORS), indicizzatori, origini dati, sinonimi, suggerimenti | Chiave amministratore se si usa un'API, un proprietario o un collaboratore RBAC di Azure se si usa il portale |
| Eseguire una query su un indice | Amministratore o chiave di query se si usa un'API, un proprietario o un collaboratore RBAC di Azure se si usa il portale |
| Eseguire query sulle informazioni di sistema relative a oggetti, ad esempio la restituzione di statistiche, conteggi ed elenchi di oggetti | Chiave amministratore se si usa un'API, un proprietario o un collaboratore RBAC di Azure se si usa il portale |

## <a name="next-steps"></a>Passaggi successivi

+ [Gestire usando PowerShell](search-manage-powershell.md) 
+ [Prestazioni e ottimizzazione in Azure ricerca cognitiva](search-performance-optimization.md)
+ [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).

---
title: Impostare i ruoli RBAC per l'accesso amministrativo di Azure nel portale - Ricerca di Azure
description: Controllo amministrativo basato sui ruoli nel portale di Azure per il controllo e la delega di attività amministrative per la gestione di Ricerca di Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 34def35eba1e5c1645e6e1f9a505704d153ac716
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277784"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Impostare i ruoli RBAC per l'accesso amministrativo

Azure offre un [modello di autorizzazione basata sui ruoli globali](../role-based-access-control/role-assignments-portal.md) per tutti i servizi gestiti tramite il portale o le API di Resource Manager. I ruoli Proprietario, Collaboratore e Lettore determinano il livello di *amministrazione del servizio* per gli utenti, i gruppi e le entità di sicurezza di Active Directory assegnati a ogni ruolo. 

> [!Note]
> Non vi è alcun controllo di accesso basato sul ruolo per la protezione di parti di un indice o un subset dei documenti. Per gli accessi in base al ruolo sui risultati della ricerca è possibile creare filtri di sicurezza per vagliare i risultati in base all'identità, rimuovendo eventuali documenti a cui il richiedente non dovrebbe avere accesso. Per altre informazioni, vedere [Filtri di sicurezza](search-security-trimming-for-azure-search.md) e [Secure with Active Directory](search-security-trimming-for-azure-search-with-aad.md) (Protezione mediante Active Directory).

## <a name="management-tasks-by-role"></a>Attività di gestione in base al ruolo

Per la Ricerca di Azure, i ruoli sono associati ai livelli di autorizzazione che supportano le attività di gestione seguenti:

| Ruolo | Attività |
| --- | --- |
| Proprietario |Creare o eliminare il servizio o qualsiasi oggetto nel servizio, inclusi chiavi API, indici, indicizzatori, origini dati di un indicizzatore e pianificazioni di indicizzatore.<p>Visualizzare lo stato del servizio, inclusi conteggi e dimensioni.<p>Aggiunta o eliminazione dell'appartenenza al ruolo, che può essere gestita solo da un Proprietario.<p>Gli amministratori delle sottoscrizioni e i proprietari del servizio vengono aggiunti automaticamente al ruolo proprietario. |
| Collaboratore |Stesso livello di accesso del Proprietario, tranne la gestione dei ruoli Controllo degli accessi in base al ruolo. Ad esempio, un Collaboratore può creare o eliminare un oggetto o visualizzare e rigenerare [chiavi API](search-security-api-keys.md), ma non può modificare le appartenenze ai ruoli. |
| [Ruolo predefinito collaboratore servizio di ricerca](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Equivalente al ruolo di Collaboratore. |
| Reader |Visualizza le informazioni di base e le metriche del servizio. I membri con questo ruolo non possono visualizzare l’indice, l'indicizzatore, l’origine dati o informazioni sulla chiave.  |

I ruoli non concedono diritti di accesso all'endpoint di servizio. Le operazioni del servizio di ricerca, ad esempio la gestione e il popolamento degli indici e le query sui dati di ricerca, sono controllate tramite le chiavi API, non tramite i ruoli. Per altre informazioni, vedere [Gestire le chiavi API](search-security-api-keys.md).

## <a name="see-also"></a>Vedere anche 

+ [Gestire usando PowerShell](search-manage-powershell.md) 
+ [Prestazioni e ottimizzazione in ricerca di Azure](search-performance-optimization.md)
+ [Introduzione al Controllo degli accessi in base al ruolo di Azure nel portale di Azure](../role-based-access-control/overview.md).
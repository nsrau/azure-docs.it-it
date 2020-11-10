---
title: Unità amministrative in Azure Active Directory | Microsoft Docs
description: Usare le unità amministrative per la delega più granulare delle autorizzazioni in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fb24d0cd2714969b5a888b1036f524c4c062d76
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027619"
---
# <a name="administrative-units-in-azure-active-directory"></a>Unità amministrative in Azure Active Directory

Questo articolo descrive le unità amministrative in Azure Active Directory (Azure AD). Un'unità amministrativa è una risorsa di Azure AD che può essere un contenitore per altre risorse di Azure AD. Un'unità amministrativa può contenere solo utenti e gruppi.

Le unità amministrative consentono di concedere autorizzazioni di amministratore limitate a un reparto, un'area o un altro segmento definito dell'organizzazione. È possibile usare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello dettagliato. Ad esempio, un amministratore account utente potrebbe aggiornare le informazioni sul profilo, reimpostare le password e assegnare le licenze agli utenti solo nella propria unità amministrativa.

O ancora, delegare il ruolo [Amministratore di supporto tecnico](permissions-reference.md#helpdesk-administrator) agli specialisti del supporto tecnico locale, che limita la gestione solo agli utenti dell'area che supportano.

## <a name="deployment-scenario"></a>Scenario di distribuzione

La limitazione dell'ambito amministrativo con le unità amministrative può essere utile nelle organizzazioni che sono costituite da divisioni indipendenti di qualsiasi tipologia. Si consideri l'esempio di un'università di grandi dimensioni costituita da numerosi dipartimenti autonomi (Economia e commercio, Ingegneria e così via). Ogni dipartimento ha un team di amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri per la propria facoltà. 

Un amministratore centrale potrebbe:

- Creare un ruolo con autorizzazioni amministrative solo per gli utenti di Azure AD nell'unità amministrativa del dipartimento di Economia e commercio.
- Creare un'unità amministrativa per il dipartimento di Economia e commercio.
- Popolare l'unità amministrativa solo con gli studenti e il personale del dipartimento di Economia e commercio.
- Aggiungere il team IT del dipartimento di Economia e commercio al ruolo con il relativo ambito.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

Per usare le unità amministrative, è necessario che ogni amministratore di unità amministrativa abbia una licenza di Azure Active Directory Premium e che i membri dell'unità amministrativa abbiano una licenza di Azure Active Directory Free. Per altre informazioni, vedere [Introduzione a Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gestire le unità amministrative

È possibile gestire le unità amministrative tramite il portale di Azure, i cmdlet e gli script di PowerShell o Microsoft Graph. Per altre informazioni, vedere:

- [Creare, rimuovere, popolare e aggiungere ruoli alle unità amministrative](admin-units-manage.md): include le procedure dettagliate complete.
- [Usare le unità amministrative](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): illustra come usare le unità amministrative con PowerShell.
- [Supporto delle unità amministrative in Microsoft Graph](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): fornisce la documentazione dettagliata su Microsoft Graph per le unità amministrative.

### <a name="plan-your-administrative-units"></a>Pianificare le unità amministrative

È possibile usare le unità amministrative per raggruppare in modo logico le risorse di Azure AD. Ad esempio, per un'organizzazione il cui reparto IT è dislocato in varie parti del mondo, potrebbe essere utile creare unità amministrative che definiscono tali limiti geografici. Nel caso di un'organizzazione multinazionale con diverse *sotto-organizzazioni* semi-autonome nelle operazioni, ogni sotto-organizzazione può essere rappresentata da un'unità amministrativa.

I criteri in base ai quali creare le unità amministrative dipenderanno dai requisiti univoci di un'organizzazione. Le unità amministrative sono un modo comune per definire la struttura nei vari servizi di Microsoft 365. È consigliabile preparare le unità amministrative tenendo in considerazione il loro utilizzo nei servizi di Microsoft 365. Il valore massimo delle unità amministrative si ottiene quando è possibile associare risorse comuni dei servizi di Microsoft 365 in un'unità amministrativa.

La creazione di unità amministrative in un'organizzazione è in genere costituita dalle fasi seguenti:

1. **Adozione iniziale** : l'organizzazione inizia a creare unità amministrative in base a criteri iniziali e il numero di unità amministrative aumenta man mano che i criteri vengono affinati.
1. **Eliminazione** : una volta che i criteri sono ben definiti, le unità amministrative che non sono più necessarie vengono eliminate.
1. **Stabilizzazione** : la struttura organizzativa è ben definita e il numero di unità amministrative non verrà modificato in modo significativo nel breve termine.

## <a name="currently-supported-scenarios"></a>Scenari attualmente supportati

Gli amministratori globali o gli amministratori ruolo con privilegi possono usare il portale di Azure AD per creare unità amministrative, aggiungere utenti come membri delle unità amministrative e quindi assegnare il personale IT ai ruoli di amministratore con ambito unità amministrativa. Gli amministratori con ambito unità amministrativa possono quindi usare l'interfaccia di amministrazione di Microsoft 365 per la gestione di base degli utenti nelle rispettive unità amministrative.

È inoltre possibile aggiungere gruppi come membri di un'unità amministrativa. Un amministratore di gruppo con ambito unità amministrativa può gestirli usando PowerShell, Microsoft Graph e il portale di Azure AD.

La tabella seguente descrive il supporto corrente per gli scenari delle unità amministrative:

### <a name="administrative-unit-management"></a>Gestione delle unità amministrative

| Autorizzazioni |   Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365 | 
| -- | -- | -- | -- |
| Creazione ed eliminazione di unità amministrative   |    Supportato    |   Supportato   |    Non supportate | 
| Aggiunta e rimozione di membri dell'unità amministrativa singolarmente    |   Supportato    |   Supportato   |    Non supportato | 
| Aggiunta e rimozione in blocco di membri di unità amministrative mediante file CSV   |    Non supportato     |  Supportato   |    Nessun piano di supporto | 
| Assegnazione di amministratori con ambito unità amministrativa  |     Supportato    |   Supportato    |   Non supportato | 
| Aggiunta e rimozione di membri dell'unità amministrativa in modo dinamico in base ad attributi | Non supportato | Non supportato | Non supportate 

### <a name="user-management"></a>Gestione utente

| Autorizzazioni |   Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365 |
| -- | -- | -- | -- |
| Gestione con ambito unità amministrativa di proprietà utente, password e licenze   |    Supportato     |  Supportato   |   Supportato |
| Blocco e sblocco con ambito unità amministrativa degli accessi degli utenti    |   Supportato   |    Supportato   |    Supportato |
| Gestione con ambito unità amministrativa delle credenziali di autenticazione a più fattori degli utenti   |    Supportato   |   Supportato   |   Non supportate |

### <a name="group-management"></a>Gestione di gruppi

| Autorizzazioni |   Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365 |
| -- | -- | -- | -- |
| Gestione con ambito unità amministrativa di proprietà e membri del gruppo     |  Supportato   |    Supportato    |  Non supportato |
| Gestione con ambito unità amministrativa delle licenze del gruppo   |    Supportato  |    Supportato   |   Non supportate |


Le unità amministrative applicano l'ambito solo alle autorizzazioni di gestione. Non impediscono a membri o agli amministratori di usare le proprie [autorizzazioni utente predefinite](../fundamentals/users-default-permissions.md) per esplorare altri utenti, gruppi o risorse all'esterno dell'unità amministrativa. Nell'interfaccia di amministrazione di Microsoft 365 gli utenti esterni alle unità amministrative dell'amministratore con ambito vengono esclusi. È tuttavia possibile esplorare altri utenti nel portale di Azure AD, in PowerShell e in altri servizi Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le unità amministrative](admin-units-manage.md)
- [Gestire gli utenti nelle unità amministrative](admin-units-add-manage-users.md)
- [Gestire i gruppi nelle unità amministrative](admin-units-add-manage-groups.md)
- [Assegnare ruoli con ambito a un'unità amministrativa](admin-units-assign-roles.md)

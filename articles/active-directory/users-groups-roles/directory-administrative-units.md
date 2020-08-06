---
title: Gestione delle unità amministrative (anteprima) - Azure AD | Microsoft Docs
description: Utilizzo delle unità amministrative per la delega più granulare delle autorizzazioni in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 093f4f3cd54f1e4638a0fbcd161a2af4fecba5da
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475788"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestione delle unità amministrative in Azure Active Directory (anteprima)

Questo articolo descrive le unità amministrative in Azure Active Directory (Azure AD). Un'unità amministrativa è una risorsa di Azure AD che può essere un contenitore per altre risorse di Azure AD. In questa versione di anteprima un'unità amministrativa può contenere solo utenti e gruppi.

Le unità amministrative consentono di concedere autorizzazioni di amministratore limitate a un reparto, un'area o un altro segmento definito dell'organizzazione. È possibile usare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello dettagliato. Ad esempio, un amministratore di account utente potrebbe aggiornare le informazioni sul profilo, reimpostare le password e assegnare le licenze agli utenti solo nella propria unità amministrativa.

 Ad esempio, la delega del ruolo [Amministratore supporto tecnico](directory-assign-admin-roles.md#helpdesk-administrator) agli specialisti del supporto locale limita la gestione ai soli utenti dell'area che supportano.

## <a name="deployment-scenario"></a>Scenario di distribuzione

La limitazione dell'ambito amministrativo con le unità amministrative può essere utile nelle organizzazioni che sono costituite da divisioni indipendenti di qualsiasi tipo. Si consideri l'esempio di una grande Università costituita da molti dipartimenti autonomi (Economia e commercio, Ingegneria e così via), ognuno dei quali dispone di un team di amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri per il proprio dipartimento. Un amministratore centrale potrebbe:

- Creare un ruolo con autorizzazioni amministrative solo sugli utenti di Azure AD nell'unità amministrativa del dipartimento di Economia e commercio
- Creare un'unità amministrativa per il dipartimento di Economia e commercio
- Popolare l'unità amministrativa solo con gli studenti e il personale del dipartimento di Economia e commercio
- Aggiungere il team IT del dipartimento di Economia e commercio al ruolo con il proprio ambito

## <a name="license-requirements"></a>Requisiti relativi alle licenze

Per usare le unità amministrative, è necessario che ogni amministratore di unità amministrativa abbia una licenza di Azure Active Directory Premium e che i membri dell'unità amministrativa abbiano una licenza di Azure Active Directory Free. Per altre informazioni, vedere [Introduzione a Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gestire le unità amministrative

In questa versione di anteprima è possibile gestire le unità amministrative usando il portale di Azure, i cmdlet e gli script di PowerShell o Microsoft Graph. Per altre informazioni, è possibile fare riferimento alla documentazione seguente:

- [Creare, rimuovere, popolare e aggiungere ruoli alle unità amministrative](roles-admin-units-manage.md): completare le procedure dettagliate
- [Uso delle unità amministrative](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): come usare le unità amministrative con PowerShell
- [Supporto delle unità amministrative in Microsoft Graph](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): documentazione dettagliata su Microsoft Graph per le unità amministrative.

### <a name="planning-your-administrative-units"></a>Pianificazione delle unità amministrative

Le unità amministrative possono essere usate per raggruppare in modo logico le risorse di Azure AD. Ad esempio, per un'organizzazione il cui reparto IT è dislocato in varie parti del mondo, potrebbe essere utile creare unità amministrative che definiscono tali limiti geografici. Nel caso di un'organizzazione multinazionale con diverse "sotto-organizzazioni" semi-autonome nelle operazioni, ogni sotto-organizzazione può essere rappresentata da un'unità amministrativa.

I criteri in base ai quali creare le unità amministrative dipenderanno dai requisiti univoci di un'organizzazione. Le unità amministrative sono un modo comune per definire la struttura nei vari servizi di Microsoft 365. È consigliabile preparare le unità amministrative tenendo in considerazione il loro utilizzo nei servizi di Microsoft 365. Il valore massimo delle unità amministrative si ottiene quando è possibile associare risorse comuni dei servizi di Microsoft 365 in un'unità amministrativa.

La creazione di unità amministrative in un'organizzazione è in genere costituita dalle fasi seguenti:

1. Adozione iniziale: l'organizzazione inizia a creare unità amministrative in base a criteri iniziali e il numero di unità amministrative aumenta man mano che i criteri vengono affinati.
1. Eliminazione: una volta che i criteri sono ben definiti, le unità amministrative che non sono più necessarie vengono eliminate.
1. Stabilizzazione: la struttura organizzativa è ben definita e il numero di unità amministrative non verrà modificato in modo significativo per brevi periodi di tempo.

## <a name="currently-supported-scenarios"></a>Scenari attualmente supportati

Gli amministratori globali o gli amministratori ruolo con privilegi possono usare il portale di Azure AD per creare unità amministrative, aggiungere utenti come membri delle unità amministrative e quindi assegnare il personale IT ai ruoli di amministratore con ambito unità amministrativa. Gli amministratori con ambito unità amministrativa possono quindi usare il portale di Microsoft 365 per la gestione di base degli utenti nelle rispettive unità amministrative.

Inoltre, è possibile aggiungere gruppi come membri dell'unità amministrativa e un amministratore di gruppo con ambito unità amministrativa può gestirli usando PowerShell, Microsoft Graph e il portale di Azure AD.

La tabella seguente descrive il supporto attuale per gli scenari di unità amministrative.

### <a name="administrative-unit-management"></a>Gestione delle unità amministrative

Autorizzazioni |   Microsoft Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Creazione ed eliminazione di unità amministrative   |    Supportato    |   Supportato   |    Non supportate
Aggiunta e rimozione di membri dell'unità amministrativa singolarmente    |   Supportato    |   Supportato   |    Non supportate
Aggiunta e rimozione in blocco di membri di unità amministrative mediante il file CSV   |    Non supportate     |  Supportato   |    Nessun piano di supporto
Assegnazione di amministratori con ambito unità amministrativa  |     Supportato    |   Supportato    |   Non supportate
Aggiunta e rimozione di membri dell'unità amministrativa in modo dinamico in base ad attributi | Non supportate | Non supportate | Non supportate

### <a name="user-management"></a>Gestione utente

Autorizzazioni |   Microsoft Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Gestione con ambito unità amministrativa di proprietà utente, password e licenze   |    Supportato     |  Supportato   |   Supportato
Blocco e sblocco con ambito unità amministrativa degli accessi degli utenti    |   Supportato   |    Supportato   |    Supportato
Gestione con ambito unità amministrativa delle credenziali MFA degli utenti   |    Supportato   |   Supportato   |   Non supportate

### <a name="group-management"></a>Gestione di gruppi

Autorizzazioni |   Microsoft Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Gestione con ambito unità amministrativa di proprietà e membri del gruppo     |  Supportato   |    Supportato    |  Non supportate
Gestione con ambito unità amministrativa delle licenze del gruppo   |    Supportato  |    Supportato   |   Non supportate

> [!NOTE]
>
> Gli amministratori con ambito unità amministrativa non possono gestire le regole di appartenenza a gruppi dinamica.

Le unità amministrative applicano l'ambito solo alle autorizzazioni di gestione. Non impediscono a membri o amministratori di usare le proprie [autorizzazioni utente predefinite](../fundamentals/users-default-permissions.md) per esplorare altri utenti, gruppi o risorse al di fuori dell'unità amministrativa. Nel portale di Microsoft 365 gli utenti esterni alle unità amministrative di un amministratore con ambito sono esclusi, ma è possibile esplorare altri utenti nel portale di Azure AD, in PowerShell e in altri servizi Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione delle unità amministrative](roles-admin-units-manage.md)
- [Gestire gli utenti nelle unità amministrative](roles-admin-units-add-manage-users.md)
- [Gestire i gruppi nelle unità amministrative](roles-admin-units-add-manage-groups.md)
- [Assegnare ruoli con ambito a un'unità amministrativa](roles-admin-units-assign-roles.md)
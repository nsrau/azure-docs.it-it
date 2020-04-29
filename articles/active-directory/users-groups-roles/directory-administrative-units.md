---
title: Gestione delle unità amministrative (anteprima)-Azure AD | Microsoft Docs
description: Utilizzo delle unità amministrative per la delega più granulare delle autorizzazioni in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406453"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestione delle unità amministrative in Azure Active Directory (anteprima)

Questo articolo descrive le unità amministrative in Azure Active Directory (Azure AD). Un'unità amministrativa è una risorsa Azure AD che può essere un contenitore per altre risorse Azure AD. In questa versione di anteprima, un'unità amministrativa può contenere solo utenti e gruppi.

Le unità amministrative consentono di concedere autorizzazioni di amministratore limitate a un reparto, un'area o un altro segmento dell'organizzazione definito dall'utente. È possibile utilizzare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello granulare. Ad esempio, un amministratore dell'account utente potrebbe aggiornare le informazioni sul profilo, reimpostare le password e assegnare le licenze per gli utenti solo nell'unità amministrativa.

 Ad esempio, delegando gli specialisti del supporto tecnico regionale, il ruolo di [amministratore helpdesk](directory-assign-admin-roles.md#helpdesk-administrator) è limitato alla gestione dei soli utenti nell'area in cui sono supportati.

## <a name="deployment-scenario"></a>Scenario di distribuzione

La limitazione dell'ambito amministrativo tramite unità amministrative può essere utile nelle organizzazioni che sono costituite da divisioni indipendenti di qualsiasi tipo. Si consideri l'esempio di un'Università di grandi dimensioni costituita da molte scuole autonome (School of business, School of Engineering e così via), ognuna delle quali dispone di un team di amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri per la propria scuola. Un amministratore centrale può:

- Creazione di un ruolo con autorizzazioni amministrative solo per Azure AD utenti nell'unità amministrativa dell'Istituto di istruzione
- Creare un'unità amministrativa per la School of business
- Popola l'unità amministrativa solo con gli studenti e il personale dell'Istituto di istruzione
- Aggiungere il team IT di Business School al ruolo con ambito

## <a name="license-requirements"></a>Requisiti relativi alle licenze

L'uso di unità amministrative richiede una licenza di Azure Active Directory Premium per ogni amministratore di unità amministrative e Azure Active Directory Free licenze per i membri dell'unità amministrativa. Per ulteriori informazioni, vedere [Introduzione a Azure ad Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Gestire le unità amministrative

In questa versione di anteprima è possibile gestire le unità amministrative usando il portale di Azure, i cmdlet e gli script di PowerShell o l'Microsoft Graph. Per informazioni dettagliate, vedere la documentazione seguente:

- [Creare, rimuovere, popolare e aggiungere ruoli a unità amministrative](roles-admin-units-manage.md): procedure complete
- Uso [delle unità di amministrazione](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): come usare le unità amministrative con PowerShell
- [Supporto per Graph di unità amministrative](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): documentazione dettagliata su Microsoft Graph per le unità amministrative.

### <a name="planning-your-administrative-units"></a>Pianificazione delle unità amministrative

Le unità amministrative possono essere usate per raggruppare in modo logico le risorse Azure AD. Ad esempio, per un'organizzazione il cui reparto IT è suddiviso a livello globale, potrebbe essere utile creare unità amministrative che definiscono tali limiti geografici. In un altro scenario in cui un'organizzazione multinazionale ha diverse "sottoorganizzazioni", che sono semi-autonome nelle operazioni, ogni organizzazione secondaria può essere rappresentata da un'unità amministrativa.

I criteri in base ai quali vengono create le unità amministrative verranno guidati dai requisiti univoci di un'organizzazione. Le unità amministrative sono un modo comune per definire la struttura tra i servizi M365. Si consiglia di preparare le unità amministrative con il loro uso nei servizi di M365. È possibile ottenere il valore massimo da unità amministrative quando è possibile associare risorse comuni tra M365 in un'unità amministrativa.

È possibile prevedere la creazione di unità amministrative nell'organizzazione per eseguire le fasi seguenti:

1. Adozione iniziale: l'organizzazione inizierà a creare unità amministrative in base ai criteri iniziali e il numero di unità amministrative aumenterà quando i criteri vengono affinati.
1. Eliminazione: quando i criteri sono ben definiti, le unità amministrative che non sono più necessarie verranno eliminate.
1. Stabilizzazione: la struttura organizzativa è ben definita e il numero di unità amministrative non verrà modificato in modo significativo per brevi periodi di tempo.

## <a name="currently-supported-scenarios"></a>Scenari attualmente supportati

Gli amministratori globali o gli amministratori dei ruoli con privilegi possono usare il portale di Azure AD per creare unità amministrative, aggiungere utenti come membri di unità amministrative e quindi assegnare il personale IT ai ruoli amministrativi con ambito unità amministrativa. Gli amministratori con ambito unità amministrativa possono quindi usare il portale di Office 365 per la gestione di base degli utenti nelle rispettive unità amministrative.

Inoltre, è possibile aggiungere gruppi come membri dell'unità amministrativa e un amministratore del gruppo con ambito unità di amministrazione può gestirli usando PowerShell, il Microsoft Graph e il portale di Azure AD.

La tabella seguente descrive il supporto corrente per gli scenari di unità amministrative.

### <a name="administrative-unit-management"></a>Gestione delle unità amministrative

Autorizzazioni |   MS Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Creazione ed eliminazione di unità amministrative   |    Supportato    |   Supportato   |    Non supportato
Aggiunta e rimozione di membri di unità amministrative singolarmente    |   Supportato    |   Supportato   |    Non supportato
Aggiunta e rimozione bulk di membri di unità amministrative mediante il file CSV   |    Non supportate     |  Supportato   |    Nessun piano da supportare
Assegnazione di amministratori con ambito unità amministrativa  |     Supportato    |   Supportato    |   Non supportato
Aggiunta e rimozione di membri AU in modo dinamico in base agli attributi | Non supportato | Non supportato | Non supportato

### <a name="user-management"></a>Gestione utente

Autorizzazioni |   MS Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gestione con ambito di unità amministrative di proprietà utente, password e licenze   |    Supportato     |  Supportato   |   Supportato
blocco e sblocco con ambito di unità amministrative per gli accessi degli utenti    |   Supportato   |    Supportato   |    Supportato
gestione con ambito di unità amministrative per le credenziali dell'autenticazione a più fattori   |    Supportato   |   Supportato   |   Non supportato

### <a name="group-management"></a>Gestione dei gruppi

Autorizzazioni |   MS Graph/PowerShell   | Portale di Azure AD | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gestione con ambito unità amministrativa di membri e proprietà del gruppo     |  Supportato   |    Supportato    |  Non supportato
gestione delle licenze di gruppo con ambito di unità amministrative   |    Supportato  |    Supportato   |   Non supportato

> [!NOTE]
>
> Gli amministratori con ambito di unità amministrativa non possono gestire le regole di appartenenza dinamica ai gruppi.

Le unità amministrative applicano l'ambito solo alle autorizzazioni di gestione. Non impediscono a membri o amministratori di usare le [autorizzazioni utente predefinite](../fundamentals/users-default-permissions.md) per esplorare altri utenti, gruppi o risorse al di fuori dell'unità amministrativa. Nel portale di Office 365, gli utenti esterni alle unità amministrative dell'amministratore con ambito sono esclusi, ma è possibile esplorare altri utenti nel portale Azure AD, in PowerShell e in altri servizi Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione di AUs](roles-admin-units-manage.md)
- [Gestire gli utenti in AUs](roles-admin-units-add-manage-users.md)
- [Gestire i gruppi in AUs](roles-admin-units-add-manage-groups.md)
- [Assegnare ruoli con ambito a un AU](roles-admin-units-assign-roles.md)
---
title: Gestione delle unità amministrative (anteprima) - Azure AD Documenti Microsoft
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406453"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestione delle unità amministrative in Azure Active Directory (anteprima)Administrative units management in Azure Active Directory (preview)

Questo articolo descrive le unità amministrative in Azure Active Directory (Azure AD). Un'unità amministrativa è una risorsa di Azure AD che può essere un contenitore per altre risorse di Azure AD. In questa versione di anteprima, un'unità amministrativa può contenere solo utenti e gruppi.

Le unità amministrative consentono di concedere autorizzazioni di amministratore limitate a un reparto, un'area o un altro segmento dell'organizzazione definito dall'utente. È possibile utilizzare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello granulare. Ad esempio, un amministratore dell'account utente potrebbe aggiornare le informazioni del profilo, reimpostare le password e assegnare le licenze solo per gli utenti nell'unità amministrativa.

 Ad esempio, delegando agli specialisti del supporto regionale il ruolo [di amministratore del supporto](directory-assign-admin-roles.md#helpdesk-administrator) tecnico limitato alla gestione solo degli utenti nell'area che supportano.

## <a name="deployment-scenario"></a>Scenario di distribuzione

Limitare l'ambito amministrativo utilizzando le unità amministrative può essere utile nelle organizzazioni costituite da divisioni indipendenti di qualsiasi tipo. Si consideri l'esempio di una grande università costituita da molte scuole autonome (School of Business, School of Engineering e così via) che hanno un team di amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri per la propria scuola. Un amministratore centrale può:

- Creare un ruolo con autorizzazioni amministrative solo per gli utenti di Azure AD nell'unità amministrativa della scuola aziendaleCreate a role with administrative permissions over only Azure AD users in the business school administrative unit
- Creare un'unità amministrativa per la School of Business
- Popolare l'unità di amministrazione solo con gli studenti e il personale della scuola di business
- Aggiungere il team IT della scuola aziendale al ruolo con il relativo ambito

## <a name="license-requirements"></a>Requisiti relativi alle licenze

L'uso di unità amministrative richiede una licenza di Azure Active Directory Premium per ogni amministratore dell'unità amministrativa e le licenze gratuite di Azure Active Directory per i membri dell'unità amministrativa. Per altre informazioni, vedere [Introduzione ad Azure AD Premium.](../fundamentals/active-directory-get-started-premium.md)

## <a name="manage-administrative-units"></a>Gestire le unità amministrative

In this preview release, you can manage administrative units using the Azure portal, PowerShell cmdlets and scripts, or the Microsoft Graph. Puoi consultare la nostra documentazione per i dettagli:

- [Creare, rimuovere, popolare e aggiungere ruoli alle unità amministrative:](roles-admin-units-manage.md)procedure complete
- [Utilizzo delle unità di amministrazione:](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)come usare le unità amministrative tramite PowerShellWorking with Admin Units : How to work with administrative units using PowerShell
- Supporto per [Administrative Unit Graph:](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)documentazione dettagliata su Microsoft Graph per le unità amministrative.

### <a name="planning-your-administrative-units"></a>Pianificazione delle unità amministrative

Le unità amministrative possono essere usate per raggruppare logicamente le risorse di Azure AD. Ad esempio, per un'organizzazione il cui reparto IT è distribuito a livello globale, potrebbe essere opportuno creare unità amministrative che definiscono tali confini geografici. In un altro scenario in cui un'organizzazione multinazionale ha "organizzazioni secondarie" diverse, che sono semi-autonome nelle operazioni, ogni sottoorganizzazione può essere rappresentata da un'unità amministrativa.

I criteri in base ai quali vengono create le unità amministrative saranno guidati dai requisiti univoci di un'organizzazione. Le unità amministrative sono un modo comune per definire la struttura tra i servizi M365. Si consiglia di preparare le unità amministrative con il loro utilizzo tra i servizi M365 in mente. È possibile ottenere il valore massimo dalle unità amministrative quando è possibile associare risorse comuni tra M365 in un'unità amministrativa.

È possibile prevedere la creazione di unità amministrative nell'organizzazione per passare attraverso le seguenti fasi:

1. Adozione iniziale: l'organizzazione inizierà a creare unità amministrative in base acriteri iniziali e il numero di unità amministrative aumenterà man mano che i criteri vengono perfezionati.
1. Pruning: una volta che i criteri sono ben definiti, le unità amministrative che non sono più necessarie verranno eliminate.
1. Stabilizzazione: la struttura organizzativa è ben definita e il numero di unità amministrative non cambierà in modo significativo per brevi durate.

## <a name="currently-supported-scenarios"></a>Scenari attualmente supportati

Gli amministratori globali o gli amministratori dei ruoli con privilegi possono usare il portale di Azure AD per creare unità amministrative, aggiungere utenti come membri di unità amministrative e quindi assegnare il personale IT ai ruoli di amministratore con ambito di unità amministrativa. Gli amministratori con ambito di unità amministrativa possono quindi utilizzare il portale di Office 365 per la gestione di base degli utenti nelle proprie unità amministrative.

Inoltre, i gruppi possono essere aggiunti come membri dell'unità amministrativa e un amministratore del gruppo con ambito di amministratore può gestirli usando PowerShell, Microsoft Graph e il portale di Azure AD.

Nella tabella seguente viene descritto il supporto corrente per gli scenari di unità amministrative.

### <a name="administrative-unit-management"></a>Gestione delle unità amministrative

Autorizzazioni |   MS Graph/PowerShell   | Portale di Azure ADAzure AD portal | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Creazione ed eliminazione di unità amministrative   |    Supportato    |   Supportato   |    Non supportate
Aggiunta e rimozione di membri dell'unità amministrativa singolarmente    |   Supportato    |   Supportato   |    Non supportate
Aggiunta e rimozione in blocco dei membri dell'unità amministrativa tramite file CSV   |    Non supportate     |  Supportato   |    Nessun piano di supporto
Assegnazione di amministratori amministrativi con ambito unitarioAssigning administrative unit-scoped administrators  |     Supportato    |   Supportato    |   Non supportate
Aggiunta e rimozione dinamica di membri AU in base agli attributi | Non supportate | Non supportate | Non supportate

### <a name="user-management"></a>Gestione utente

Autorizzazioni |   MS Graph/PowerShell   | Portale di Azure ADAzure AD portal | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gestione amministrativa con ambito di unità di proprietà utente, password, licenze   |    Supportato     |  Supportato   |   Supportato
blocco e sblocco degli indirizzi utente con ambito amministrativo    |   Supportato   |    Supportato   |    Supportato
gestione con ambito unità amministrativa delle credenziali utente MFA   |    Supportato   |   Supportato   |   Non supportate

### <a name="group-management"></a>Gestione dei gruppi

Autorizzazioni |   MS Graph/PowerShell   | Portale di Azure ADAzure AD portal | Interfaccia di amministrazione di Microsoft 365
----------- | ----------------------- | --------------- | -----------------
gestione amministrativa con ambito di unità delle proprietà e dei membri del gruppo     |  Supportato   |    Supportato    |  Non supportate
gestione amministrativa delle licenze di gruppo con ambito di unità amministrativa   |    Supportato  |    Supportato   |   Non supportate

> [!NOTE]
>
> Gli amministratori con un ambito di unità amministrativa non possono gestire le regole di appartenenza ai gruppi dinamici.

Le unità amministrative si applicano all'ambito solo alle autorizzazioni di gestione. Non impediscono ai membri o agli amministratori di utilizzare [le autorizzazioni utente predefinite](../fundamentals/users-default-permissions.md) per esplorare altri utenti, gruppi o risorse all'esterno dell'unità amministrativa. Nel portale di Office 365 gli utenti esterni alle unità amministrative di un amministratore con ambito vengono filtrati, ma è possibile esplorare altri utenti nel portale di Azure AD, in PowerShell e in altri servizi Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione delle cuve](roles-admin-units-manage.md)
- [Gestire gli utenti in un'ora di accesso](roles-admin-units-add-manage-users.md)
- [Gestire i gruppi in un'ora di un'ora](roles-admin-units-add-manage-groups.md)
- [Assegnare ruoli con ambito a un'Unione guidataAssign scoped roles to an AU](roles-admin-units-assign-roles.md)
---
title: Gestione delle unità amministrative (anteprima) - Azure Active Directory | Microsoft Docs
description: Utilizzo delle unità amministrative per la delega più granulare delle autorizzazioni in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f1a6e5b1e8191c1497e437cc26e1caf1255ba7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472368"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Gestione delle unità amministrative in Azure Active Directory (anteprima pubblica)

Questo articolo descrive le unità amministrative, un nuovo contenitore di risorse di Azure Active Directory (Azure AD) che può essere usato per la delega di autorizzazioni amministrative su subset di utenti e per l'applicazione di criteri a un subset di utenti. In Azure Active Directory, le unità amministrative consentono agli amministratori centrali di delegare le autorizzazioni agli amministratori regionali o di impostare criteri a livello dettagliato.

Ciò risulta utile nelle organizzazioni con divisioni indipendenti, ad esempio, una grande università è costituita da molte scuole autonome (facoltà di gestione aziendale, facoltà di ingegneria, ecc.) indipendenti tra loro. Tali divisioni dispongono dei propri amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri in modo specifico per la propria facoltà. Gli amministratori centrali desiderano essere in grado di concedere a questi amministratori di divisione autorizzazioni sugli utenti delle proprie divisioni. In particolare, utilizzando questo esempio, un amministratore centrale può, ad esempio, creare un'unità amministrativa per una facoltà particolare (facoltà di gestione aziendale) e popolarla con solo gli utenti della facoltà. Quindi un amministratore centrale può aggiungere il personale IT della facoltà di gestione aziendale a un ruolo con ambito, ovvero, concedere al personale IT della facoltà di gestione aziendale autorizzazioni amministrative solo per l'unità amministrativa della facoltà.

> [!IMPORTANT]
> Per usare unità amministrative, è necessario che l'amministratore con ambito unità amministrativa abbia una licenza Azure Active Directory Premium e che siano disponibili licenze Azure Active Directory Basic per tutti gli utenti nell'unità amministrativa. Per altre informazioni, vedere [Introduzione a Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>


Dal punto di vista dell'amministratore centrale, un'unità amministrativa è un oggetto directory che può essere creato e popolato con le risorse. **In questa versione di anteprima, queste risorse possono essere solo gli utenti.**  Dopo averla creata e popolata, l'unità amministrativa può essere utilizzata come ambito per limitare le autorizzazioni concesse solo alle risorse contenute nell’unità amministrativa.

## <a name="managing-administrative-units"></a>Gestione delle unità amministrative
In questa versione di anteprima, è possibile creare e gestire le unità amministrative utilizzando i cmdlet del modulo di Microsoft Azure Active Directory per Windows PowerShell. Per ulteriori informazioni su come eseguire queste operazioni, vedere [Uso di unità amministrative](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Per ulteriori informazioni sui requisiti software e l'installazione del modulo di Azure AD e per informazioni sui cmdlet di modulo di Azure AD per la gestione di unità amministrative, tra cui sintassi, descrizioni dei parametri ed esempi, vedere [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Passaggi successivi
[Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md)

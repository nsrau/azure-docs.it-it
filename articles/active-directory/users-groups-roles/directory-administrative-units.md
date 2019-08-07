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
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b4bdced50f806367a53881d5ef0abd0a3710496
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736773"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Gestione delle unità amministrative in Azure Active Directory (anteprima pubblica)

Questo articolo descrive le unità amministrative in Azure Active Directory (Azure AD), ovvero un contenitore di risorse che possono essere usate per delegare le autorizzazioni amministrative su subset di utenti e per applicare criteri a un subset di utenti. In Azure AD le unità amministrative consentono agli amministratori centrali di delegare le autorizzazioni agli amministratori regionali o di impostare criteri a livello granulare.

Ciò risulta utile nelle organizzazioni con divisioni indipendenti, ad esempio, una grande università è costituita da molte scuole autonome (facoltà di gestione aziendale, facoltà di ingegneria, ecc.) indipendenti tra loro. Tali divisioni dispongono dei propri amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri in modo specifico per la propria facoltà. Gli amministratori centrali desiderano essere in grado di concedere a questi amministratori di divisione autorizzazioni sugli utenti delle proprie divisioni. In particolare, utilizzando questo esempio, un amministratore centrale può, ad esempio, creare un'unità amministrativa per una facoltà particolare (facoltà di gestione aziendale) e popolarla con solo gli utenti della facoltà. Quindi un amministratore centrale può aggiungere il personale IT della facoltà di gestione aziendale a un ruolo con ambito, ovvero, concedere al personale IT della facoltà di gestione aziendale autorizzazioni amministrative solo per l'unità amministrativa della facoltà.

> [!IMPORTANT]
> Per usare le unità amministrative, è necessario che l'amministratore dell'unità amministrativa disponga di una licenza Azure Active Directory Premium. Per altre informazioni, vedere [Introduzione a Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>

Dal punto di vista dell'amministratore centrale, un'unità amministrativa è un oggetto directory che può essere creato e popolato con le risorse. **In questa versione di anteprima, queste risorse possono essere solo gli utenti.** Dopo averla creata e popolata, l'unità amministrativa può essere utilizzata come ambito per limitare le autorizzazioni concesse solo alle risorse contenute nell’unità amministrativa.

## <a name="managing-administrative-units"></a>Gestione delle unità amministrative

In questa versione di anteprima, è possibile creare e gestire le unità amministrative utilizzando i cmdlet del modulo di Microsoft Azure Active Directory per Windows PowerShell. Per ulteriori informazioni su come eseguire queste operazioni, vedere [Uso di unità amministrative](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Per ulteriori informazioni sui requisiti software e l'installazione del modulo di Azure AD e per informazioni sui cmdlet di modulo di Azure AD per la gestione di unità amministrative, tra cui sintassi, descrizioni dei parametri ed esempi, vedere [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Passaggi successivi

[Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md)

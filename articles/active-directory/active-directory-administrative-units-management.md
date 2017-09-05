---
title: "Anteprima della gestione delle unità amministrative in Azure Active Directory"
description: "Utilizzo delle unità amministrative per la delega più granulare delle autorizzazioni in Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8464cd6b-1d1a-470d-a4fb-ee29b8eab4c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: e12a0aea8264b1ea67c26294ec5bbe9c404a171e
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gestione delle unità amministrative in Azure AD - Anteprima pubblica
Questo articolo descrive le unità amministrative, un nuovo contenitore di risorse di Azure Active Directory che può essere usato per la delega di autorizzazioni amministrative su subset di utenti e per l'applicazione di criteri a un subset di utenti. In Azure Active Directory, le unità amministrative consentono agli amministratori centrali di delegare le autorizzazioni agli amministratori regionali o di impostare criteri a livello dettagliato.

Ciò risulta utile nelle organizzazioni con divisioni indipendenti, ad esempio, una grande università è costituita da molte scuole autonome (facoltà di gestione aziendale, facoltà di ingegneria, ecc.) indipendenti tra loro. Tali divisioni dispongono dei propri amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri in modo specifico per la propria facoltà. Gli amministratori centrali desiderano essere in grado di concedere a questi amministratori di divisione autorizzazioni sugli utenti delle proprie divisioni. In particolare, utilizzando questo esempio, un amministratore centrale può, ad esempio, creare un'unità amministrativa per una facoltà particolare (facoltà di gestione aziendale) e popolarla con solo gli utenti della facoltà. Quindi un amministratore centrale può aggiungere il personale IT della facoltà di gestione aziendale a un ruolo con ambito, ovvero, concedere al personale IT della facoltà di gestione aziendale autorizzazioni amministrative solo per l'unità amministrativa della facoltà.

> [!IMPORTANT]
> È possibile assegnare ruoli di amministratore con ambito unità amministrativa solo se si abilita Azure Active Directory Premium. Per altre informazioni, vedere [Introduzione a Azure AD Premium](active-directory-get-started-premium.md).
>


Dal punto di vista dell'amministratore centrale, un'unità amministrativa è un oggetto directory che può essere creato e popolato con le risorse. **In questa versione di anteprima, queste risorse possono essere solo gli utenti.** Dopo averla creata e popolata, l'unità amministrativa può essere utilizzata come ambito per limitare le autorizzazioni concesse solo alle risorse contenute nell’unità amministrativa.

## <a name="managing-administrative-units"></a>Gestione delle unità amministrative
In questa versione di anteprima, è possibile creare e gestire le unità amministrative utilizzando i cmdlet del modulo di Microsoft Azure Active Directory per Windows PowerShell. Per ulteriori informazioni su come eseguire queste operazioni, vedere [Uso di unità amministrative](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Per ulteriori informazioni sui requisiti software e l'installazione del modulo di Azure AD e per informazioni sui cmdlet di modulo di Azure AD per la gestione di unità amministrative, tra cui sintassi, descrizioni dei parametri ed esempi, vedere [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Passaggi successivi
[Edizioni di Azure Active Directory](active-directory-editions.md)


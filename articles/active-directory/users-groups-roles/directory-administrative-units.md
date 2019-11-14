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
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028424"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestione delle unità amministrative in Azure Active Directory (anteprima)

Questo articolo descrive le unità amministrative in Azure Active Directory (Azure AD). Un'unità amministrativa è una risorsa Azure AD che può essere un contenitore per altre risorse Azure AD. In questa versione di anteprima, queste risorse possono essere solo gli utenti. Ad esempio, un amministratore dell'account utente con ambito unità amministrativa può aggiornare le informazioni del profilo, reimpostare le password e assegnare le licenze per gli utenti solo nell'unità amministrativa.

È possibile utilizzare unità amministrative per delegare le autorizzazioni amministrative su subset di utenti e applicare criteri a un subset di utenti. È possibile utilizzare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello granulare.

## <a name="deployment-scenario"></a>Scenario di distribuzione

Le unità amministrative possono essere utili nelle organizzazioni con divisioni indipendenti. Si consideri l'esempio di un'Università di grandi dimensioni costituita da molte scuole autonome (School of business, School of Engineering e così via) che ognuno ha i propri amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri per la propria scuola. Un amministratore centrale può creare un'unità amministrativa per la School of business e popolarla solo con gli studenti e il personale dell'Istituto di istruzione. Quindi, l'amministratore centrale può aggiungere il personale IT dell'Istituto di istruzione a un ruolo con ambito che concede autorizzazioni amministrative solo ai Azure AD utenti dell'unità amministrativa dell'Istituto di istruzione.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

Per usare le unità amministrative è necessario disporre di una licenza di Azure Active Directory Premium per ogni amministratore di unità amministrative. Per ulteriori informazioni, vedere [Introduzione a Azure ad Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>Gestione delle unità amministrative

In questa versione di anteprima, l'unico modo per creare e gestire le unità amministrative consiste nell'usare il modulo Azure Active Directory per i cmdlet di Windows PowerShell, come descritto in [utilizzo di unità amministrative](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Per ulteriori informazioni sui requisiti software e sull'installazione del modulo Azure AD e per informazioni di riferimento sui cmdlet del modulo Azure AD per la gestione delle unità amministrative, incluse sintassi, descrizioni dei parametri ed esempi, vedere [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Passaggi successivi

[Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md)

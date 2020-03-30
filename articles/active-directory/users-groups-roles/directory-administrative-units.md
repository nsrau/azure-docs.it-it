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
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028424"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Gestione delle unità amministrative in Azure Active Directory (anteprima)Administrative units management in Azure Active Directory (preview)

Questo articolo descrive le unità amministrative in Azure Active Directory (Azure AD). Un'unità amministrativa è una risorsa di Azure AD che può essere un contenitore per altre risorse di Azure AD. In questa versione di anteprima, queste risorse possono essere solo gli utenti. Ad esempio, un amministratore dell'account utente con ambito di unità amministrativa può aggiornare le informazioni del profilo, reimpostare le password e assegnare licenze per gli utenti solo nell'unità amministrativa.

È possibile utilizzare le unità amministrative per delegare le autorizzazioni amministrative su sottoinsiemi di utenti e applicare criteri a un sottoinsieme di utenti. È possibile utilizzare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello granulare.

## <a name="deployment-scenario"></a>Scenario di distribuzione

Le unità amministrative possono essere utili nelle organizzazioni con divisioni indipendenti. Si consideri l'esempio di una grande università costituita da molte scuole autonome (Scuola di Business, Scuola di Ingegneria e così via) che ognuna ha i propri amministratori IT che controllano l'accesso, gestiscono gli utenti e impostano i criteri per la propria scuola. Un amministratore centrale potrebbe creare un'unità amministrativa per la School of Business e popolarla solo con gli studenti e il personale delle scuole di business. L'amministratore centrale può quindi aggiungere il personale IT della scuola aziendale a un ruolo con ambito che concede autorizzazioni amministrative solo per gli utenti di Azure AD nell'unità amministrativa della scuola aziendale.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

Per usare le unità amministrative, è necessaria una licenza di Azure Active Directory Premium per ogni amministratore dell'unità amministrativa. Per altre informazioni, vedere [Introduzione ad Azure AD Premium.](../fundamentals/active-directory-get-started-premium.md)

## <a name="managing-administrative-units"></a>Gestione delle unità amministrative

In questa versione di anteprima, l'unico modo per creare e gestire le unità amministrative consiste nell'utilizzare i cmdlet di Azure Active Directory Module per Windows PowerShell, come descritto in [Utilizzo delle unità amministrative](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Per altre informazioni sui requisiti software e sull'installazione del modulo di Azure AD e per informazioni di riferimento sui cmdlet del modulo di Azure AD per la gestione delle unità amministrative, tra cui sintassi, descrizioni di parametri ed esempi, vedere [Azure Active Directory PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

## <a name="next-steps"></a>Passaggi successivi

[Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md)

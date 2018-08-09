---
title: Gestire i programmi e i controlli per le verifiche di accesso di Azure AD | Microsoft Docs
description: È possibile creare programmi aggiuntivi per ogni governance, iniziativa di conformità e gestione dei rischi nell'organizzazione per raccogliere e organizzare le verifiche di accesso di Azure Active Directory come controlli.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: a1a08ac29605e66dc14ac5e32d21e00dad8cb655
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622287"
---
# <a name="manage-programs-and-their-controls"></a>Gestire i programmi e i relativi controlli 

Azure Active Directory (Azure AD) include le verifiche di accesso dell'accesso delle applicazioni e di membri del gruppo. Questi esempi di controlli assicurano la supervisione per chi ha accesso alle applicazioni e alle appartenenze ai gruppi dell'organizzazione. Le organizzazioni possono usare questi controlli per gestire in modo efficiente la governance, i requisiti di conformità e la gestione dei rischi.

## <a name="create-and-manage-programs-and-their-controls"></a>Creare e gestire i programmi e i relativi controlli
È possibile semplificare il rilevamento e la raccolta delle verifiche di accesso per scopi diversi organizzandole nei programmi. Ogni verifica di accesso può essere collegata a un programma. Quando si preparano i report per un revisore, è possibile concentrarsi sulle verifiche di accesso nell'ambito per un'iniziativa specifica.  I programmi e i risultati delle verifiche di accesso sono visibili agli utenti con il ruolo Amministratore globale, Amministratore account utente, Amministratore della sicurezza o Ruolo con autorizzazioni di lettura per la sicurezza.

Per visualizzare un elenco di programmi, accedere alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selezionare **Programmi**.

**Programma predefinito** è sempre presente. Gli utenti con un ruolo di amministratore globale o amministratore account utente possono creare programmi aggiuntivi. Ad esempio, è possibile scegliere che sia disponibile un programma per ogni obiettivo di business o iniziativa di conformità.

Se un programma non è più necessario e non dispone di controlli collegati, è possibile eliminarlo.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)
- [Retrieve the results of an access review (Recuperare i risultati di una verifica di accesso)](active-directory-azure-ad-controls-retrieve-access-review.md)

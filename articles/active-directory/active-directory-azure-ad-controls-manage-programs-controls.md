---
title: Gestire i programmi e i controlli per le verifiche di accesso di Azure AD | Microsoft Docs
description: "È possibile creare programmi aggiuntivi per ogni governance, iniziativa di conformità e gestione dei rischi nell'organizzazione per raccogliere e organizzare le verifiche di accesso di Azure Active Directory come controlli."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f6d7c2a1a3e3bf78b568f2f6b079476e403b9911
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-programs-and-their-controls"></a>Gestire i programmi e i relativi controlli 

Azure AD include verifiche di accesso dei membri del gruppo e dell'accesso alle applicazioni, che sono esempi di controlli che assicurano la supervisione degli utenti con accesso. Questi controlli consentono alle organizzazioni di gestire in modo efficiente la governance, i requisiti di conformità e la gestione dei rischi.  

## <a name="how-to-manage-programs-and-their-controls"></a>Come gestire i programmi e i relativi controlli
È possibile semplificare il rilevamento e la raccolta delle verifiche di accesso per scopi diversi organizzandole nei programmi.  Ogni verifica di accesso può essere collegata a un programma, in modo che quando si preparano i report per un revisore siano visibili solo le verifiche di accesso nell'ambito per un'iniziativa particolare.

Per visualizzare un elenco di programmi, accedere alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e passare alla scheda **Programmi**.

Esiste un programma sempre presente, il "programma predefinito".  Gli utenti con un ruolo di amministratore globale possono creare programmi aggiuntivi.  Ad esempio, è possibile scegliere che sia disponibile un programma per ogni obiettivo di business o iniziativa di conformità.

Se un programma non è più necessario e non dispone di controlli collegati, è possibile eliminarlo.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)


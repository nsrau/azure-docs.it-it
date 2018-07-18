---
title: La pagina dell'applicazione non viene visualizzata correttamente per un'applicazione proxy di applicazione | Microsoft Docs
description: Linee guida per i casi in cui la pagina non viene visualizzata correttamente in un'applicazione proxy di applicazione integrata con Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ee06018cd500937c69824f796c137e3972c55f6c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334701"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>La pagina dell'applicazione non viene visualizzata correttamente per un'applicazione proxy di applicazione

Questo articolo semplifica la risoluzione dei problemi relativi ad applicazioni Azure Active Directory Application Proxy quando è possibile passare alla pagina, ma questa non viene visualizzata correttamente.

## <a name="overview"></a>Panoramica
Quando si pubblica un'app proxy di applicazione, solo le pagine all'interno della radice sono accessibili quando si accede all'applicazione. Se la pagina non viene visualizzata correttamente, l'URL interno radice usato per l'applicazione potrebbe non includere alcune risorse della pagina. Per risolvere questo problema, assicurarsi di aver pubblicato *tutte* le risorse per la pagina come parte dell'applicazione.

Per verificare che il problema siano le risorse mancanti, aprire lo strumento di individuazione di rete, ad esempio Fiddler o altri strumenti F12 in Internet Explorer/Microsoft Edge, caricare la pagina e cercare eventuali errori 404. Questi errori indicano che le pagine non possono essere trovate e che è necessario pubblicarle.

Come esempio di questo caso, si supponga di avere pubblicato un'applicazione per il calcolo delle spese usando l'URL interno http://myapps/expenses, mentre l'app usa il foglio di stile http://myapps/style.css. In questo caso, il foglio di stile non viene pubblicato nell'applicazione e di conseguenza il caricamento dell'app genera un errore 404 mentre tenta di caricare style.css. In questo esempio il problema viene risolto pubblicando l'applicazione con l'URL interno http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Problemi relativi alla pubblicazione come singola applicazione

Se non è possibile pubblicare tutte le risorse all'interno della stessa applicazione, è necessario pubblicare più applicazioni e abilitare i collegamenti tra loro.

A questo scopo, è consigliabile usare la soluzione con [domini personalizzati](manage-apps/application-proxy-configure-custom-domain.md). Con questa soluzione, tuttavia, è necessario essere il proprietario del certificato per il dominio e che le applicazioni usino nomi di domino completi (FQDN). Per altre opzioni, vedere la [documentazione per la risoluzione dei problemi relativi a collegamenti interrotti](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Passaggi successivi
[Pubblicare applicazioni mediante il proxy di applicazione AD Azure](manage-apps/application-proxy-publish-azure-portal.md)

---
title: La pagina dell'applicazione non viene visualizzata correttamente per un'applicazione proxy di applicazione | Microsoft Docs
description: Linee guida per i casi in cui la pagina non viene visualizzata correttamente in un'applicazione proxy di applicazione integrata con Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cac4c333e59ef9a0f28a2f93a7afee22eeafd54e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>La pagina dell'applicazione non viene visualizzata correttamente per un'applicazione proxy di applicazione

Questo articolo semplifica la risoluzione dei problemi relativi ad applicazioni proxy di applicazione di Azure Active Directory quando è possibile passare alla pagina, ma questa non viene visualizzata correttamente.

## <a name="overview"></a>Panoramica
Quando si pubblica un'app proxy di applicazione, solo le pagine all'interno della radice sono accessibili quando si accede all'applicazione. Se la pagina non viene visualizzata correttamente, l'URL interno radice usato per l'applicazione potrebbe non includere alcune risorse della pagina. Per risolvere questo problema, assicurarsi di aver pubblicato *tutte* le risorse per la pagina come parte dell'applicazione.

Per verificare che il problema sia questo, aprire lo strumento di individuazione di rete, ad esempio Fiddler o altri strumenti F12 in Internet Explorer/Edge, caricare la pagina e cercare eventuali errori 404. Questi errori indicano che le pagine non possono essere trovate e potrebbero dover essere pubblicate.

Come esempio di questo caso, si supponga di aver pubblicato un'applicazione per il calcolo delle spese usando l'URL interno <http://myapps/expenses>, mentre l'app usa il foglio di stile <http://myapps/style.css>. In questo caso, il foglio di stile non viene pubblicato nell'applicazione e di conseguenza il caricamento dell'app genera un errore 404 mentre tenta di caricare style.css. In questo esempio il problema può essere risolto pubblicando l'applicazione con l'URL interno <http://myapp/>.

## <a name="problems-with-publishing-as-one-application"></a>Problemi relativi alla pubblicazione come singola applicazione

Se non è possibile pubblicare tutte le risorse all'interno della stessa applicazione, è necessario pubblicare più applicazioni e abilitare i collegamenti tra loro.

A questo scopo, è consigliabile usare la soluzione con [domini personalizzati](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains). Con questa soluzione, tuttavia, è necessario essere il proprietario del certificato per il dominio e che le applicazioni usino nomi di domino completi (FQDN). Per altre opzioni, vedere la [documentazione per la risoluzione dei problemi relativi a collegamenti interrotti](https://microsoft-my.sharepoint.com/personal/harshja_microsoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=IxuG3mFVbnPWI3Yn4Qi7wCNi8VIfHS5mwPt5quh8DMw%3d&docid=2_14558cd6ddea34c1c9887dc640feb5831&rev=1).

## <a name="next-steps"></a>Passaggi successivi
[Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-publish-azure-portal.md)

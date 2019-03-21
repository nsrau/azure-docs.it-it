---
title: La pagina dell'applicazione non viene visualizzata correttamente per un'applicazione proxy di applicazione | Microsoft Docs
description: Linee guida per i casi in cui la pagina non viene visualizzata correttamente in un'applicazione proxy di applicazione integrata con Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 950a349506f32405261d90c05312d24aa3deadc5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089948"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>La pagina dell'applicazione non viene visualizzata correttamente per un'applicazione proxy di applicazione

Questo articolo semplifica la risoluzione dei problemi relativi ad applicazioni Azure Active Directory Application Proxy quando è possibile passare alla pagina, ma questa non viene visualizzata correttamente.

## <a name="overview"></a>Panoramica
Quando si pubblica un'app proxy di applicazione, solo le pagine all'interno della radice sono accessibili quando si accede all'applicazione. Se la pagina non viene visualizzata correttamente, l'URL interno radice usato per l'applicazione potrebbe non includere alcune risorse della pagina. Per risolvere questo problema, assicurarsi di aver pubblicato *tutte* le risorse per la pagina come parte dell'applicazione.

Per verificare se il problema è dovuto a risorse mancanti, aprire lo strumento di individuazione di rete, ad esempio Fiddler o altri strumenti F12 in Internet Explorer o Microsoft Edge, caricare la pagina e cercare eventuali errori 404. Questi errori indicano che le pagine non possono essere trovate e che è necessario pubblicarle.

Come esempio di questo caso, si supponga di avere pubblicato un'applicazione per il calcolo delle spese usando l'URL interno `http://myapps/expenses`, mentre l'app usa il foglio di stile `http://myapps/style.css`. In questo caso, il foglio di stile non viene pubblicato nell'applicazione e di conseguenza il caricamento dell'app genera un errore 404 mentre tenta di caricare style.css. In questo esempio il problema viene risolto pubblicando l'applicazione con l'URL interno `http://myapp/`.

## <a name="problems-with-publishing-as-one-application"></a>Problemi relativi alla pubblicazione come singola applicazione

Se non è possibile pubblicare tutte le risorse all'interno della stessa applicazione, è necessario pubblicare più applicazioni e abilitare i collegamenti tra loro.

A questo scopo, è consigliabile usare la soluzione con [domini personalizzati](application-proxy-configure-custom-domain.md). Con questa soluzione, tuttavia, è necessario essere il proprietario del certificato per il dominio e che le applicazioni usino nomi di domino completi (FQDN). Per altre opzioni, vedere la [documentazione per la risoluzione dei problemi relativi a collegamenti interrotti](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Passaggi successivi
[Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-add-on-premises-application.md)

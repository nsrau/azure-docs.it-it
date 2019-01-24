---
title: I collegamenti nella pagina non funzionano per un'applicazione proxy di applicazione | Microsoft Docs
description: Come risolvere i problemi relativi a collegamenti interrotti in applicazioni proxy di applicazione integrate con Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 65e903ee1ee8111d3d3b064d6018f49b2e96af47
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478009"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>I collegamenti nella pagina non funzionano per un'applicazione proxy di applicazione

Questo articolo semplifica la risoluzione dei problemi di funzionamento dei collegamenti nell'applicazione proxy di applicazione di Azure Active Directory.

## <a name="overview"></a>Panoramica 
Dopo aver pubblicato un'app proxy di applicazione, gli unici collegamenti che per impostazione predefinita funzionano nell'applicazione sono i collegamenti a destinazioni contenute all'interno dell'URL radice pubblicato. I collegamenti all'interno delle applicazioni non funzionano probabilmente perché l'URL interno per l'applicazione non include tutte le destinazioni dei collegamenti nell'applicazione.

**Perché si verifica questo problema?** Quando si fa clic su un collegamento in un'applicazione, il proxy di applicazione prova a risolvere l'URL come URL interno nella stessa applicazione oppure come URL disponibile esternamente. Se il collegamento punta a un URL interno che non è incluso nella stessa applicazione, non appartiene ad alcuno di questi bucket e restituisce un errore di URL non trovato.

## <a name="ways-you-can-resolve-broken-links"></a>Come correggere i collegamenti interrotti

È possibile risolvere questo problema in tre modi. Le scelte seguenti sono elencate in ordine di complessità crescente.

1.  Assicurarsi che l'URL interno sia una radice che contiene tutti i collegamenti rilevanti per l'applicazione. In questo modo, tutti i collegamenti possono essere risolti come contenuto pubblicato all'interno della stessa applicazione.

    Se si modifica l'URL interno, ma non si vuole modificare la pagina di destinazione per gli utenti, modificare l'URL di pagina iniziale nell'URL interno precedentemente pubblicato. A questo scopo, è possibile passare a "Azure Active Directory" -&gt; Registrazioni per l'app -&gt; selezionare l'applicazione -&gt; Proprietà. In questa scheda delle proprietà è visualizzato il campo "URL pagina iniziale", che può essere impostato come pagina di destinazione desiderata.

2.  Se le applicazioni usano nomi di dominio completi (FQDN), usare [domini personalizzati](application-proxy-configure-custom-domain.md) per pubblicare le applicazioni. Questa funzionalità permette l'uso dello stesso URL internamente ed esternamente.

    Questa opzione garantisce che i collegamenti nell'applicazione siano accessibili esternamente tramite il proxy di applicazione, perché i collegamenti a URL interni nell'applicazione vengono riconosciuti anche esternamente. Tutti i collegamenti devono appartenere a un'applicazione pubblicata. Con questa opzione, tuttavia, i collegamenti non devono appartenere alla stessa applicazione, ma possono appartenere a più applicazioni.

3.  Se nessuna di queste opzioni è fattibile, sono disponibili più opzioni per l'abilitazione della traduzione dei collegamenti inline. Tali opzioni includono l'utilizzo di Intune Managed Browser, l'estensione App personali, o l'impostazione di traduzione del collegamento nella propria applicazione. Per altre informazioni su ciascuna opzione e la relativa modalità di attivazione, vedere [Reindirizzare i collegamenti hardcoded per le app pubblicate con il proxy di applicazione Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Passaggi successivi
[Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)


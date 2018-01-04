---
title: I collegamenti nella pagina non funzionano per un'applicazione proxy di applicazione | Microsoft Docs
description: Come risolvere i problemi relativi a collegamenti interrotti in applicazioni proxy di applicazione integrate con Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 17f2afb0aaf3b899784a504b77f33a1284f0a232
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
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

2.  Se le applicazioni usano nomi di dominio completi (FQDN), usare [domini personalizzati](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) per pubblicare le applicazioni. Questa funzionalità permette l'uso dello stesso URL internamente ed esternamente.

    Questa opzione garantisce che i collegamenti nell'applicazione siano accessibili esternamente tramite il proxy di applicazione, perché i collegamenti a URL interni nell'applicazione vengono riconosciuti anche esternamente. Tutti i collegamenti devono appartenere a un'applicazione pubblicata. Con questa opzione, tuttavia, i collegamenti non devono appartenere alla stessa applicazione, ma possono appartenere a più applicazioni.

3.  Se nessuna di queste opzioni è applicabile, è possibile iscriversi all'anteprima di una nuova funzionalità che esegue la conversione e la riscrittura degli URL. Con questa opzione, gli URL o i collegamenti interni presenti nel corpo HTML delle applicazioni possono essere convertiti, o "mappati", negli URL del proxy di applicazione esterni pubblicati. Questa opzione funziona solo per collegamenti nel codice HTML o CSS e non è utile se il collegamento viene generato tramite JS. 

Di conseguenza, è consigliabile usare la soluzione con [domini personalizzati](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains), se possibile. Per iscriversi all'anteprima della funzionalità, inviare un messaggio di posta elettronica all'indirizzo <aadapfeedback@microsoft.com>, indicando gli ID applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Usare server proxy locali esistenti](application-proxy-working-with-proxy-servers.md)


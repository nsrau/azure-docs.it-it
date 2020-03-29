---
title: I collegamenti nella pagina non funzionano per un'applicazione proxy di applicazione
description: Come risolvere i problemi relativi a collegamenti interrotti in applicazioni proxy di applicazione integrate con Azure AD
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430239"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>I collegamenti nella pagina non funzionano per un'applicazione proxy di applicazione

Questo articolo semplifica la risoluzione dei problemi di funzionamento dei collegamenti nell'applicazione proxy di applicazione di Azure Active Directory.

## <a name="overview"></a>Panoramica 
Dopo aver pubblicato un'app proxy di applicazione, gli unici collegamenti che per impostazione predefinita funzionano nell'applicazione sono i collegamenti a destinazioni contenute all'interno dell'URL radice pubblicato. I collegamenti all'interno delle applicazioni non funzionano probabilmente perché l'URL interno per l'applicazione non include tutte le destinazioni dei collegamenti nell'applicazione.

**Perché si verifica questa situazione?** Quando si fa clic su un collegamento in un'applicazione, il proxy di applicazione prova a risolvere l'URL come URL interno nella stessa applicazione oppure come URL disponibile esternamente. Se il collegamento punta a un URL interno che non è incluso nella stessa applicazione, non appartiene ad alcuno di questi bucket e restituisce un errore di URL non trovato.

## <a name="ways-you-can-resolve-broken-links"></a>Come correggere i collegamenti interrotti

È possibile risolvere questo problema in tre modi. Le scelte seguenti sono elencate in ordine di complessità crescente.

1.  Assicurarsi che l'URL interno sia una radice che contiene tutti i collegamenti rilevanti per l'applicazione. In questo modo, tutti i collegamenti possono essere risolti come contenuto pubblicato all'interno della stessa applicazione.

    Se si modifica l'URL interno, ma non si vuole modificare la pagina di destinazione per gli utenti, modificare l'URL di pagina iniziale nell'URL interno precedentemente pubblicato. Questa operazione può essere eseguita andando&gt; a "Azure&gt; Active Directory" - Registrazioni app - selezionare l'applicazione -&gt; branding. Nella sezione di personalizzazione viene visualizzato il campo "URL pagina iniziale", che è possibile modificare in modo che sia la pagina di destinazione desiderata. Se stai ancora utilizzando l'esperienza legacy di registrazione app, la scheda delle proprietà mostrerà i dettagli "URL pagina iniziale". 
    
    > [!IMPORTANT]
    > Per apportare le modifiche precedenti sono necessari i diritti per modificare gli oggetti applicazione in Azure AD. All'utente deve essere assegnato il ruolo [di amministratore dell'applicazione](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) che concede all'utente i diritti di modifica dell'applicazione in Azure AD.
    >

2.  Se le applicazioni usano nomi di dominio completi (FQDN), usare [domini personalizzati](application-proxy-configure-custom-domain.md) per pubblicare le applicazioni. Questa funzionalità permette l'uso dello stesso URL internamente ed esternamente.

    Questa opzione garantisce che i collegamenti nell'applicazione siano accessibili esternamente tramite il proxy di applicazione, perché i collegamenti a URL interni nell'applicazione vengono riconosciuti anche esternamente. Tutti i collegamenti devono appartenere a un'applicazione pubblicata. Con questa opzione, tuttavia, i collegamenti non devono appartenere alla stessa applicazione, ma possono appartenere a più applicazioni.

3.  Se nessuna di queste opzioni è fattibile, sono disponibili più opzioni per l'abilitazione della traduzione dei collegamenti inline. Tali opzioni includono l'utilizzo di Intune Managed Browser, l'estensione App personali, o l'impostazione di traduzione del collegamento nella propria applicazione. Per altre informazioni su ciascuna opzione e la relativa modalità di attivazione, vedere [Reindirizzare i collegamenti hardcoded per le app pubblicate con il proxy di applicazione Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Passaggi successivi
[Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)


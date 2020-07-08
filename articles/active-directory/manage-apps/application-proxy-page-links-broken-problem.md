---
title: I collegamenti nella pagina non funzionano per un'applicazione proxy di applicazione
description: Come risolvere i problemi relativi a collegamenti interrotti in applicazioni proxy di applicazione integrate con Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050874b301628348b513b8b5b0c2b878a1dcecaa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760202"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>I collegamenti nella pagina non funzionano per un'applicazione proxy di applicazione

Questo articolo semplifica la risoluzione dei problemi di funzionamento dei collegamenti nell'applicazione proxy di applicazione di Azure Active Directory.

## <a name="overview"></a>Panoramica 
Dopo aver pubblicato un'app proxy di applicazione, gli unici collegamenti che per impostazione predefinita funzionano nell'applicazione sono i collegamenti a destinazioni contenute all'interno dell'URL radice pubblicato. I collegamenti all'interno delle applicazioni non funzionano probabilmente perché l'URL interno per l'applicazione non include tutte le destinazioni dei collegamenti nell'applicazione.

**Perché si verifica questa situazione?** Quando si fa clic su un collegamento in un'applicazione, il proxy di applicazione prova a risolvere l'URL come URL interno nella stessa applicazione oppure come URL disponibile esternamente. Se il collegamento punta a un URL interno che non è incluso nella stessa applicazione, non appartiene ad alcuno di questi bucket e restituisce un errore di URL non trovato.

## <a name="ways-you-can-resolve-broken-links"></a>Come correggere i collegamenti interrotti

È possibile risolvere questo problema in tre modi. Le scelte seguenti sono elencate in ordine di complessità crescente.

1.  Assicurarsi che l'URL interno sia una radice che contiene tutti i collegamenti rilevanti per l'applicazione. In questo modo, tutti i collegamenti possono essere risolti come contenuto pubblicato all'interno della stessa applicazione.

    Se si modifica l'URL interno, ma non si vuole modificare la pagina di destinazione per gli utenti, modificare l'URL di pagina iniziale nell'URL interno precedentemente pubblicato. A tale scopo, passare a "Azure Active Directory"- &gt; registrazioni per l'app- &gt; selezionare la personalizzazione dell'applicazione &gt; . Nella sezione personalizzazione viene visualizzato il campo "Home page URL", che può essere modificato in modo da essere la pagina di destinazione desiderata. Se si sta ancora usando l'esperienza legacy Registrazioni app la scheda Proprietà Visualizza i dettagli dell'URL della Home page. 
    
    > [!IMPORTANT]
    > Per apportare le modifiche sopra riportate, è necessario disporre dei diritti per modificare gli oggetti applicazione in Azure AD. L'utente deve essere assegnato al ruolo di [amministratore dell'applicazione](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) , che concede i diritti modificaion dell'applicazione in Azure ad all'utente.
    >

2.  Se le applicazioni usano nomi di dominio completi (FQDN), usare [domini personalizzati](application-proxy-configure-custom-domain.md) per pubblicare le applicazioni. Questa funzionalità permette l'uso dello stesso URL internamente ed esternamente.

    Questa opzione garantisce che i collegamenti nell'applicazione siano accessibili esternamente tramite il proxy di applicazione, perché i collegamenti a URL interni nell'applicazione vengono riconosciuti anche esternamente. Tutti i collegamenti devono appartenere a un'applicazione pubblicata. Con questa opzione, tuttavia, i collegamenti non devono appartenere alla stessa applicazione, ma possono appartenere a più applicazioni.

3.  Se nessuna di queste opzioni è fattibile, sono disponibili più opzioni per l'abilitazione della traduzione dei collegamenti inline. Tali opzioni includono l'utilizzo di Intune Managed Browser, l'estensione App personali, o l'impostazione di traduzione del collegamento nella propria applicazione. Per altre informazioni su ciascuna opzione e la relativa modalità di attivazione, vedere [Reindirizzare i collegamenti hardcoded per le app pubblicate con il proxy di applicazione Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Passaggi successivi
[Usare server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md)


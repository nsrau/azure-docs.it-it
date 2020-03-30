---
title: Sviluppo e integrazione dell'API CrossConnnections di Azure ExpressRouteAzure ExpressRoute CrossConnnections API development and integration
description: Questo articolo fornisce una panoramica dettagliata per i partner ExpressRoute sul tipo di risorsa expressRouteCrossConnections.This article provides a detailed overview for ExpressRoute partners about the expressRouteCrossConnections resource type.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187020"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Sviluppo e integrazione dell'API CrossRoute CrossConnnections

L'API ExpressRoute Partner Resource Manager consente ai partner ExpressRoute di gestire la configurazione layer-2 e layer-3 dei circuiti ExpressRoute dei clienti. L'API ExpressRoute Partner Resource Manager introduce un nuovo tipo di risorsa, **expressRouteCrossConnections**. I partner usano questa risorsa per gestire i circuiti ExpressRoute dei clienti.

## <a name="workflow"></a>Flusso di lavoro

La risorsa expressRouteCrossConnections è una risorsa shadow per il circuito ExpressRoute.The expressRouteCrossConnections resource is a shadow resource to the ExpressRoute circuit. Quando un cliente di Azure crea un circuito ExpressRoute e seleziona un partner ExpressRoute specifico, Microsoft crea una risorsa ExpressRouteCrossConnections nella sottoscrizione di gestione di Azure ExpressRoute del partner. In questo modo, Microsoft definisce un gruppo di risorse in cui creare la risorsa expressRouteCrossConnections.In so so, Microsoft defines a resource group to create the expressRouteCrossConnections resource in. Lo standard di denominazione per il gruppo di risorse è *"CrossConnection-* PeeringLocation"; dove PeeringLocation: percorso ExpressRoute.where PeeringLocation - the ExpressRoute Location. Ad esempio, se un cliente crea un circuito ExpressRoute a Denver, CrossConnection verrà creato nella sottoscrizione di Azure del partner nel gruppo di risorse seguente: **CrossConnnection-Denver**.

I partner ExpressRoute gestiscono la configurazione layer-2 e layer-3 eseguendo operazioni REST sulla risorsa expressRouteCrossConnections.ExpressRoute partners manage layer-2 and layer-3 configuration by issuing REST operations against the expressRouteCrossConnections resource.

## <a name="benefits"></a>Vantaggi

Vantaggi dello spostamento alla risorsa expressRouteCrossConnections:

* Eventuali miglioramenti futuri per i partner ExpressRoute saranno resi disponibili nella risorsa ExpressRouteCrossConnection.Any future enhancements for ExpressRoute partners will be made available on the ExpressRouteCrossConnection resource.

* I partner possono applicare il controllo degli accessi in base al ruolo alla risorsa expressRouteCrossConnection.Partners can apply [Role-Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/overview) to the expressRouteCrossConnection resource. Questi controlli possono definire le autorizzazioni per le quali gli account utente possono modificare la risorsa expressRouteCrossConnection e aggiungere/aggiornare/eliminare le configurazioni di peering.

* La risorsa expressRouteCrossConnection espone le API che possono essere utili per la risoluzione dei problemi relativi alle connessioni ExpressRoute.The expressRouteCrossConnection resource exposes APIs that can be helpful in troubleshooting ExpressRoute connections. Sono inclusi i dettagli della tabella ARP, del riepilogo della tabella route BGP e della tabella route BGP. Questa funzionalità non è supportata dalle API di distribuzione classiche.

* I partner possono anche cercare le community annunciate nel peering Microsoft usando la risorsa *RouteFilter.*

## <a name="api-development-and-integration-steps"></a>Passaggi di sviluppo e integrazione delle API

Per sviluppare in base all'API partner, i partner ExpressRoute sfruttano la configurazione di un cliente di test e di un partner di test. La configurazione del cliente di test verrà usata per creare circuiti ExpressRoute in percorsi di peering di test mappati a dispositivi e porte fittizi. La configurazione del partner di test viene usata per gestire i circuiti ExpressRoute creati nel percorso di peering di test.

### <a name="1-enlist-subscriptions"></a>1. Arruolare gli abbonamenti

Per richiedere il partner di test e la configurazione del cliente di prova, inserire due sottoscrizioni di Azure con pagamento in base al client di progettazione ExpressRoute:To request the test partner and test customer setup, enlist two Pay-as-You-Go Azure subscriptions to your ExpressRoute engineering contact:
* **ExpressRoute_API_Dev_Provider_Sub:** Questa sottoscrizione verrà usata per gestire i circuiti ExpressRoute creati in percorsi di peering di test su dispositivi e porte fittizie.

* **ExpressRoute_API_Dev_Customer_Sub:** Questa sottoscrizione verrà usata per creare circuiti ExpressRoute in percorsi di peering di test mappati a dispositivi e porte fittizie.

Le posizioni di peering di test: i dispositivi fittizi e le porte non sono esposti ai clienti di produzione per impostazione predefinita. Per creare circuiti ExpressRoute mappati all'impostazione di test, è necessario abilitare un flag di funzionalità di sottoscrizione.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registrare la sottoscrizione di Dev_Provider per accedere all'API expressRouteCrossConnections

Per accedere all'API expressRouteCrossConnections, la sottoscrizione partner deve essere registrata nel **Provider di risorse Microsoft.Network**. Seguire i passaggi nell'articolo Tipi di [risorse di Azure](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) per completare il processo di registrazione.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Configurare l'autenticazione per le chiamate all'API REST di Azure Resource Manager

La maggior parte dei servizi di Azure richiede il codice client per l'autenticazione con Resource Manager, usando credenziali valide, prima di chiamare le API del servizio. L'autenticazione è coordinata tra i vari attori da Azure AD e fornisce al client un token di accesso come prova di autenticazione.

Il processo di autenticazione prevede due passaggi principali:The authentication process involves two main steps:

1. [Registrare il client](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Creare la richiesta](https://docs.microsoft.com/rest/api/azure/#create-the-request)di accesso .

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Fornire l'autorizzazione Collaboratore di rete per l'applicazione client

Una volta configurata correttamente l'autenticazione, è necessario concedere l'accesso Collaboratore di rete all'applicazione client, nella Dev_Provider_Sub. Per concedere l'autorizzazione, accedere al portale di Azure e completare i passaggi seguenti:To grant permission, sign in to the [Azure portal](https://ms.portal.azure.com/#home) and complete the following steps:

1. Passare a Sottoscrizioni e selezionare il Dev_Provider_Sub
2. Passare al controllo di accesso (IAM)Navigate to Access Control (IAM)
3. Aggiungi assegnazione ruolo
4. Selezionare il ruolo Collaboratore rete
5. Assegnare l'accesso a un utente, un gruppo o un'entità servizio di Azure ADAssign Access to Azure AD User, Group, or Service Principal
6. Selezionare l'applicazione client
7. Salvare le modifiche

### <a name="5-develop"></a>5. Sviluppare

Sviluppare in base [all'API expressRouteCrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>API REST

Vedere [ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) per la documentazione dell'API REST.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su tutte le API REST ExpressRoute, vedere API REST ExpressRoute.For more information on all [ExpressRoute REST APIs, see ExpressRoute REST APIs](https://docs.microsoft.com/rest/api/expressroute/).
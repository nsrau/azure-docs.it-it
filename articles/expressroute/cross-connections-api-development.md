---
title: Sviluppo e integrazione dell'API CrossConnnections di Azure ExpressRoute
description: Questo articolo fornisce una panoramica dettagliata dei partner ExpressRoute sul tipo di risorsa expressRouteCrossConnections.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187020"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Sviluppo e integrazione dell'API CrossConnnections di ExpressRoute

L'API Gestione risorse partner ExpressRoute consente ai partner ExpressRoute di gestire la configurazione di livello 2 e livello 3 dei circuiti ExpressRoute del cliente. L'API Gestione risorse partner ExpressRoute introduce un nuovo tipo di risorsa, **expressRouteCrossConnections**. I partner usano questa risorsa per gestire i circuiti ExpressRoute del cliente.

## <a name="workflow"></a>Flusso di lavoro

La risorsa expressRouteCrossConnections è una risorsa shadow per il circuito ExpressRoute. Quando un cliente di Azure crea un circuito ExpressRoute e seleziona un partner ExpressRoute specifico, Microsoft crea una risorsa expressRouteCrossConnections nella sottoscrizione di gestione di Azure ExpressRoute del partner. In questo modo, Microsoft definisce un gruppo di risorse in cui creare la risorsa expressRouteCrossConnections. Lo standard di denominazione per il gruppo di risorse è **CrossConnection-* PeeringLocation * * *; dove PeeringLocation = il percorso di ExpressRoute. Se, ad esempio, un cliente crea un circuito ExpressRoute a Denver, il CrossConnection verrà creato nella sottoscrizione di Azure del partner nel seguente gruppo di risorse: **CrossConnnection-Denver**.

I partner ExpressRoute gestiscono la configurazione di livello 2 e di livello 3 mediante l'emissione di operazioni REST sulla risorsa expressRouteCrossConnections.

## <a name="benefits"></a>Vantaggi

Vantaggi del passaggio alla risorsa expressRouteCrossConnections:

* Eventuali miglioramenti futuri per i partner ExpressRoute verranno resi disponibili sulla risorsa ExpressRouteCrossConnection.

* I partner possono applicare il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview) alla risorsa expressRouteCrossConnection. Questi controlli possono definire le autorizzazioni per le quali gli account utente possono modificare la risorsa expressRouteCrossConnection e aggiungere/aggiornare/eliminare le configurazioni del peering.

* La risorsa expressRouteCrossConnection espone le API che possono essere utili per la risoluzione dei problemi di connessione ExpressRoute. Sono inclusi la tabella ARP, il riepilogo della tabella di route BGP e i dettagli della tabella di route BGP. Questa funzionalità non è supportata dalle API di distribuzione classica.

* I partner possono anche cercare le community annunciate nel peering Microsoft usando la risorsa *Route* .

## <a name="api-development-and-integration-steps"></a>Procedure per lo sviluppo e l'integrazione di API

Per lo sviluppo con l'API partner, i partner ExpressRoute sfruttano una configurazione del cliente e del partner di test. La configurazione del cliente di test verrà usata per creare circuiti ExpressRoute in percorsi di peering di test che vengono mappati a dispositivi e porte fittizi. La configurazione del partner di test viene usata per gestire i circuiti ExpressRoute creati nel percorso di peering dei test.

### <a name="1-enlist-subscriptions"></a>1. integrare le sottoscrizioni

Per richiedere al partner di test e testare la configurazione del cliente, integrare due sottoscrizioni di Azure con pagamento in base al consumo per il contatto ExpressRoute Engineering:
* **ExpressRoute_API_Dev_Provider_Sub:** Questa sottoscrizione verrà usata per gestire i circuiti ExpressRoute creati in percorsi di peering di test su dispositivi e porte fittizi.

* **ExpressRoute_API_Dev_Customer_Sub:** Questa sottoscrizione verrà usata per creare circuiti ExpressRoute in percorsi di peering di test con mapping a dispositivi e porte fittizi.

I percorsi di peering di test: i dispositivi fittizi e le porte non vengono esposti ai clienti di produzione per impostazione predefinita. Per creare circuiti ExpressRoute mappati alla configurazione di test, è necessario abilitare un flag della funzionalità di sottoscrizione.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. registrare la sottoscrizione di Dev_Provider per accedere all'API expressRouteCrossConnections

Per accedere all'API expressRouteCrossConnections, è necessario registrare la sottoscrizione partner nel **provider di risorse Microsoft. Network**. Per completare il processo di registrazione, seguire la procedura descritta nell'articolo relativo ai [tipi e ai provider di risorse di Azure](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) .

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. configurare l'autenticazione per le chiamate API REST di Azure Resource Manager

La maggior parte dei servizi di Azure richiede che il codice client esegua l'autenticazione con Gestione risorse, usando credenziali valide, prima di chiamare le API del servizio. L'autenticazione è coordinata tra i vari attori da Azure AD e fornisce al client un token di accesso come prova di autenticazione.

Il processo di autenticazione prevede due passaggi principali:

1. [Registrare il client](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Creare la richiesta di accesso](https://docs.microsoft.com/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. fornire l'autorizzazione di collaboratore alla rete per l'applicazione client

Dopo che l'autenticazione è stata configurata correttamente, è necessario concedere l'accesso di collaboratore alla rete all'applicazione client, nel Dev_Provider_Sub. Per concedere l'autorizzazione, accedere al [portale di Azure](https://ms.portal.azure.com/#home) e completare i passaggi seguenti:

1. Passare a sottoscrizioni e selezionare la Dev_Provider_Sub
2. Passa a controllo di accesso (IAM)
3. Aggiungi assegnazione di ruolo
4. Selezionare il ruolo Collaboratore rete
5. Assegnare l'accesso a Azure AD utente, gruppo o entità servizio
6. Selezionare l'applicazione client
7. Salvare le modifiche

### <a name="5-develop"></a>5. sviluppare

Sviluppare con l' [API expressRouteCrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>API REST

Vedere la documentazione dell'API [Rest ExpressRoute CrossConnections](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) per l'API REST.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su tutte le API REST di ExpressRoute, vedere [API REST di ExpressRoute](https://docs.microsoft.com/rest/api/expressroute/).
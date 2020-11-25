---
title: Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione
description: Guida per architetti e amministratori IT sull'uso del proxy di applicazione per l'accesso resiliente alle applicazioni locali
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8bfc3fb239f30911eddf0aa27496a465e36c486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919565"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Resilienza della compilazione nell'accesso alle applicazioni con il proxy di applicazione

Application Proxy è una funzionalità di Azure AD che consente agli utenti di accedere alle applicazioni Web locali da un client remoto. Il proxy di applicazione include il servizio proxy di applicazione nel cloud e i connettori del proxy di applicazione, che vengono eseguiti in un server locale. 

Gli utenti accedono alle risorse locali tramite un URL pubblicato tramite il proxy di applicazione. Vengono reindirizzati alla pagina di accesso Azure AD. Il servizio proxy di applicazione in Azure AD quindi Invia un token al connettore del proxy di applicazione nella rete aziendale, che passa il token al Active Directory locale. l'utente autenticato può quindi accedere alla risorsa locale. Nel diagramma seguente i [connettori](../manage-apps/application-proxy-connectors.md) vengono visualizzati in un [gruppo di connettori](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> Quando si pubblicano le applicazioni tramite il proxy di applicazione, è necessario implementare [la pianificazione della capacità e la ridondanza appropriata per i connettori del proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning).

![Diagramma dell'architettura dell'applicazione y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Ricerca per categorie implementare il proxy di applicazione?

Per implementare l'accesso remoto con Azure AD proxy di applicazione, vedere le risorse seguenti.

* [Pianificazione di una distribuzione del proxy dell'applicazione](../manage-apps/application-proxy-deployment-plan.md)

* [Procedure consigliate per la disponibilità elevata e il bilanciamento del carico](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Configurare i server proxy](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Progettare una strategia di controllo degli accessi resiliente](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Passaggi successivi
Risorse di resilienza per amministratori e architetti
 
* [Resilienza della compilazione con la gestione delle credenziali](resilience-in-credentials.md)

* [Resilienza di compilazione con gli Stati dei dispositivi](resilience-with-device-states.md)

* [Resilienza della compilazione usando la valutazione di accesso continuo (CAE)](resilience-with-continuous-access-evaluation.md)

* [Resilienza di compilazione nell'autenticazione utente esterna](resilience-b2b-authentication.md)

* [Resilienza di compilazione nell'autenticazione ibrida](resilience-in-hybrid.md)

Risorse di resilienza per gli sviluppatori

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza delle build nei sistemi CIAM](resilience-b2c.md)

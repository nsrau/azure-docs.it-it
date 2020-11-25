---
title: Interfacce resilienti con processi esterni che usano Azure AD B2C | Microsoft Docs
description: Metodi per creare interfacce resilienti con processi esterni
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96856c988cae891e64ddf460d61851102e4666c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919534"
---
# <a name="resilient-interfaces-with-external-processes"></a>Interfacce resilienti con processi esterni

Questo articolo fornisce indicazioni su come pianificare e implementare le API RESTful nel percorso utente e rendere l'applicazione più resiliente agli errori dell'API.

![Image Mostra le interfacce con i componenti del processo esterno](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Verificare la posizione corretta delle API

I criteri di Framework dell'esperienza di identità (Framework dell'esperienza) consentono di chiamare un sistema esterno usando un [profilo tecnico API RESTful](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile). I sistemi esterni non sono controllati dall'ambiente di runtime di Framework dell'esperienza e rappresentano un potenziale punto di errore.

### <a name="how-to-manage-external-systems-using-apis"></a>Come gestire i sistemi esterni usando le API

- Quando si chiama un'interfaccia per accedere a determinati dati, verificare se i dati dovranno guidare la decisione di autenticazione. Valutare se le informazioni sono essenziali per le funzionalità di base dell'applicazione. Ad esempio, un e-commerce rispetto a una funzionalità secondaria, ad esempio l'amministrazione. Se le informazioni non sono necessarie per l'autenticazione e sono necessarie solo per gli scenari secondari, provare a trasferire la chiamata alla logica dell'applicazione.

- Se i dati necessari per l'autenticazione sono relativamente statici e di piccole dimensioni e non esistono altri motivi aziendali da esternalizzare dalla directory, provare a utilizzarli nella directory.

- Quando possibile, rimuovere le chiamate API dal percorso con autenticazione preliminare. Se non è possibile, è necessario inserire protezioni rigorose per attacchi Denial of Service (DoS) e attacchi di tipo Denial of Service (DDoS, Distributed Denial of Service) davanti alle API. Gli utenti malintenzionati possono caricare la pagina di accesso e provare a inondare l'API con attacchi DoS e paralizzare l'applicazione. Ad esempio, usando CAPTCHA nell'accesso, il flusso di iscrizione può essere di aiuto.

- Usare i [connettori API del flusso utente di iscrizione predefinito](https://docs.microsoft.com/azure/active-directory-b2c/api-connectors-overview) laddove possibile per l'integrazione con le API Web dopo aver eseguito l'accesso con un provider di identità o prima di creare l'utente. Poiché i flussi utente sono già stati testati in modo esteso, è probabile che non sia necessario eseguire test funzionali, delle prestazioni o della scalabilità a livello di flusso utente. È comunque necessario testare le applicazioni per le funzionalità, le prestazioni e la scalabilità.

- Azure AD i [profili tecnici](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) delle API RESTful non forniscono alcun comportamento di Caching. Al contrario, il profilo API RESTful implementa una logica di ripetizione dei tentativi e un timeout incorporato nei criteri.

- Per le API che richiedono la scrittura di dati, accodare un'attività per eseguire tali attività da un thread di lavoro in background. È possibile usare servizi come le [code di Azure](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction) . In questo modo l'API tornerà a migliorare in modo efficiente le prestazioni di esecuzione dei criteri.  

## <a name="api-error-handling"></a>Gestione degli errori delle API

Poiché le API risiedono all'esterno del sistema di Azure AD B2C, è necessario disporre di una corretta gestione degli errori all'interno del profilo tecnico. Assicurarsi che l'utente finale sia informato in modo appropriato e che l'applicazione possa gestire correttamente gli errori.

### <a name="how-to-gracefully-handle-api-errors"></a>Come gestire correttamente gli errori dell'API

- Un'API potrebbe non riuscire per vari motivi, in modo da garantire la resilienza dell'applicazione a tali errori. [Restituisce un messaggio di errore HTTP 4xx](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#returning-validation-error-message) se l'API non è in grado di completare la richiesta. Nei criteri di Azure AD B2C, provare a gestire normalmente l'indisponibilità dell'API ed eventualmente eseguire il rendering di un'esperienza ridotta.

- [Gestire correttamente gli errori temporanei](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#error-handling). Il profilo API RESTful consente di configurare i messaggi di errore per diversi [interruttori](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker).

- Monitorare in modo proattivo e usare l'integrazione continua/recapito continuo (CICD), ruotare le credenziali di accesso dell'API, ad esempio le password e i certificati usati dal [motore di profilo tecnico](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile).

## <a name="api-management---best-practices"></a>Gestione API-procedure consigliate

Quando si distribuiscono le API REST e si configura il profilo tecnico RESTful, le procedure consigliate seguenti consentono di evitare errori comuni e di trascurare gli elementi.

### <a name="how-to-manage-apis"></a>Come gestire le API

- Gestione API (gestione API) pubblica, gestisce e analizza le API. GESTIONE API gestisce anche l'autenticazione per fornire un accesso sicuro ai servizi e ai microservizi back-end. Usare un gateway API per scalare le distribuzioni API, la memorizzazione nella cache e il bilanciamento del carico.

- Si consiglia di ottenere il token corretto all'inizio del percorso utente anziché chiamare più volte per ogni API e [proteggere un'API Gestione API di Azure](https://docs.microsoft.com/azure/active-directory-b2c/secure-api-management?tabs=app-reg-ga).

## <a name="next-steps"></a>Passaggi successivi

- [Risorse di resilienza per sviluppatori Azure AD B2C](resilience-b2c.md)
  - [Esperienza utente finale resiliente](resilient-end-user-experience.md)
  - [Resilienza tramite le procedure consigliate per gli sviluppatori](resilience-b2c-developer-best-practices.md)
  - [Resilienza tramite monitoraggio e analisi](resilience-with-monitoring-alerting.md)
- [Resilienza di compilazione nell'infrastruttura di autenticazione](resilience-in-infrastructure.md)
- [Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni](resilience-app-development-overview.md)

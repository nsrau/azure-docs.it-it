---
title: Procedure consigliate per Azure AD B2C
titleSuffix: Azure AD B2C
description: Raccomandazioni e procedure consigliate da considerare quando si lavora con Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: b16c60130836cf0e3b38092b894129f503ee6e83
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141676"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Suggerimenti e procedure consigliate per Azure Active Directory B2C

Le procedure consigliate e le raccomandazioni seguenti illustrano alcuni aspetti principali dell'integrazione di Azure Active Directory (Azure AD) B2C in ambienti applicativi nuovi o esistenti.

## <a name="fundamentals"></a>Concetti fondamentali

|  |  |
|--|--|
| Scegliere i flussi utente per la maggior parte degli scenari | Il Framework dell'esperienza di identità di Azure AD B2C rappresenta il livello di attendibilità principale del servizio. I criteri descrivono in modo completo le esperienze per le identità, ad esempio iscrizione, accesso o modifica del profilo. Per poter configurare le attività di gestione delle identità più comuni, il portale di Azure AD B2C include criteri predefiniti configurabili chiamati flussi utente. Con i flussi utente è possibile creare esperienze utente eccezionali in pochi minuti, con pochi clic. [Informazioni sul momento in cui usare i flussi utente rispetto ai criteri personalizzati](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Registrazioni per l'app | Ogni applicazione (Web, nativa) e API da proteggere deve essere registrata in Azure AD B2C. Se un'app include sia una versione Web che una versione nativa di iOS e Android, è possibile registrarle come un'unica applicazione in Azure AD B2C con lo stesso ID client. Informazioni su come [registrare OIDC, SAML, Web e app native](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications). Altre informazioni sui [tipi di applicazioni che è possibile usare in Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/application-types). |
| Passa a fatturazione utenti attivi mensili | Azure AD B2C è stato spostato da autenticazioni attive mensili a una fatturazione mensile di utenti attivi (MAU). La maggior parte dei clienti troverà questo modello economicamente conveniente. [Altre informazioni sulla fatturazione mensile degli utenti attivi](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>pianificazione e progettazione

Definire l'architettura del servizio e dell'applicazione, inventariare i sistemi correnti e pianificare la migrazione a Azure AD B2C.

|  |  |
|--|--|
| Progettare una soluzione end-to-end | Includere tutte le dipendenze delle applicazioni quando si pianifica un'integrazione Azure AD B2C. Prendere in considerazione tutti i servizi e i prodotti attualmente presenti nell'ambiente o che potrebbero essere necessari per l'aggiunta alla soluzione, ad esempio funzioni di Azure, sistemi CRM (Customer Relationship Management), gateway di gestione API di Azure e servizi di archiviazione. Prendere in considerazione la sicurezza e la scalabilità per tutti i servizi. |
| Documentare le esperienze degli utenti | Informazioni dettagliate su tutti i percorsi utente che i clienti possono sperimentare nell'applicazione. Includere tutte le schermate e i flussi di diramazione che possono verificarsi durante l'interazione con gli aspetti relativi all'identità e al profilo dell'applicazione. Includere usabilità, accessibilità e localizzazione nella pianificazione. |
| Scegliere il protocollo di autenticazione corretto |  Per una suddivisione dei diversi scenari di applicazione e dei flussi di autenticazione consigliati, vedere [scenari e flussi di autenticazione supportati](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilota un'esperienza utente end-to-end per il modello di prova (POC) | Inizia a usare gli esempi di [codice Microsoft](code-samples.md) e gli [esempi della community](https://github.com/azure-ad-b2c/samples). |
| Creare un piano di migrazione |La pianificazione in anticipo può rendere la migrazione più agevole. Altre informazioni sulla [migrazione degli utenti](user-migration.md).|
| Usabilità rispetto alla sicurezza | La soluzione deve raggiungere il giusto equilibrio tra l'usabilità dell'applicazione e il livello di rischio accettabile per l'organizzazione. |
| Spostare le dipendenze locali nel cloud | Per garantire una soluzione resiliente, provare a trasferire le dipendenze delle applicazioni esistenti nel cloud. |
| Eseguire la migrazione di app esistenti a b2clogin.com | La deprecazione di login.microsoftonline.com diviene valida per tutti i tenant Azure AD B2C il 04 dicembre 2020. [Altre informazioni](b2clogin.md). |

## <a name="implementation"></a>Implementazione

Durante la fase di implementazione, prendere in considerazione i consigli seguenti.

|  |  |
|--|--|
| Modificare i criteri personalizzati con l'estensione Azure AD B2C per Visual Studio Code | Scarica Visual Studio Code e questa estensione compilata [dalla community da Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Sebbene non sia un prodotto Microsoft ufficiale, l'estensione Azure AD B2C per Visual Studio Code include diverse funzionalità che consentono di semplificare l'utilizzo di criteri personalizzati. |
| Informazioni su come risolvere i problemi Azure AD B2C | Informazioni su come [risolvere i problemi relativi ai criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) durante lo sviluppo. Informazioni sul flusso di autenticazione normale e su come usare gli strumenti per individuare anomalie ed errori. Ad esempio, usare [Application Insights](troubleshoot-with-application-insights.md) per esaminare i log di output dei percorsi utente. |
| Sfrutta la nostra libreria di modelli di criteri personalizzati collaudati | [Esempi](https://github.com/azure-ad-b2c/samples) per diversi percorsi utente avanzati per la gestione delle identità e degli accessi Azure ad B2C clienti (CIAM). |


## <a name="testing"></a>Test

Testare e automatizzare l'implementazione del Azure AD B2C.

|  |  |
|--|--|
| Account per il traffico globale | Usare le origini del traffico da un indirizzo globale diverso per verificare i requisiti di prestazioni e localizzazione. Assicurarsi che tutti i HTML, i CSS e le dipendenze possano soddisfare le esigenze di prestazioni. |
| Test funzionali e dell'interfaccia utente | Testare i flussi utente end-to-end. Aggiungi test sintetici a intervalli di pochi minuti usando Selenium, test Web di Visual Studio e così via. |
| Test di penna | Prima di procedere con la soluzione, eseguire esercizi di test di penetrazione per verificare che tutti i componenti siano protetti, incluse eventuali dipendenze di terze parti. Verificare di avere protetto le API con i token di accesso e di aver usato il protocollo di autenticazione appropriato per lo scenario dell'applicazione. Scopri di più sul [test di penetrazione](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) e sulle [regole di test di penetrazione unificata Microsoft Cloud di engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| Test A/B | Esegui il volo delle nuove funzionalità con un piccolo set casuale di utenti prima di implementare l'intera popolazione. Con JavaScript abilitato in Azure AD B2C, è possibile eseguire l'integrazione con strumenti di test A/B come ottimizzazione, chiarezza e altro ancora. |
| Test di carico | Azure AD B2C possono essere ridimensionate, ma l'applicazione può essere ridimensionata solo se tutte le relative dipendenze possono essere ridimensionate. Test di carico delle API e della rete CDN. |
| Limitazione |  Azure AD B2C limita il traffico se un numero eccessivo di richieste viene inviato dalla stessa origine in un breve periodo di tempo. Usare diverse origini di traffico durante i test di carico e `AADB2C90229` gestire correttamente il codice di errore nelle applicazioni. |
| Automazione | USA pipeline di integrazione continua e recapito continuo per automatizzare i test e le distribuzioni, ad esempio [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operazioni

Gestire l'ambiente di Azure AD B2C.

|  |  |
|--|--|
| Creazione di più ambienti | Per semplificare le operazioni e implementare la distribuzione, creare ambienti distinti per lo sviluppo, il test, la pre-produzione e la produzione. Creare Azure AD B2C tenant per ognuno di essi. |
| Usare il controllo della versione per i criteri personalizzati | Si consiglia di usare GitHub, Azure Repos o un altro sistema di controllo della versione basato sul cloud per i criteri personalizzati Azure AD B2C. |
| Usare l'API Microsoft Graph per automatizzare la gestione dei tenant B2C | API Microsoft Graph:<br/>Gestire il [Framework dell'esperienza](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) di gestione delle identità (criteri personalizzati)<br/>[Chiavi](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Flussi degli utenti](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Eseguire l'integrazione con Azure DevOps | Una [pipeline](deploy-custom-policies-devops.md) di integrazione continua/recapito continuo rende più semplice lo trasferimento di codice tra ambienti diversi e garantisce sempre la preparazione alla produzione.   |
| Integrare con Monitoraggio di Azure | [Gli eventi del log di controllo](view-audit-logs.md) vengono conservati per sette giorni. Eseguire l' [integrazione con monitoraggio di Azure](azure-monitor.md) per conservare i log per l'uso a lungo termine o per l'integrazione con strumenti di gestione di informazioni ed eventi di sicurezza di terze parti per ottenere informazioni approfondite sull'ambiente. |
| Configurare gli avvisi attivi e il monitoraggio | [Tenere traccia del comportamento degli utenti](active-directory-b2c-custom-guide-eventlogger-appins.md) in Azure AD B2C usando Application Insights. |


## <a name="support-and-status-updates"></a>Supporto e aggiornamenti di stato

È possibile rimanere sempre aggiornati sullo stato del servizio e trovare le opzioni di supporto.

|  |  |
|--|--|
| [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  È possibile rimanere sempre aggiornati sugli annunci e gli aggiornamenti del prodotto Azure AD B2C. |
| [supporto tecnico Microsoft](support-options.md) | Archiviare una richiesta di supporto per Azure AD B2C problemi tecnici. Il supporto per fatturazione e gestione delle sottoscrizioni viene fornito gratuitamente. |
| [Stato di Azure](https://status.azure.com/status) | Visualizza lo stato di integrità corrente di tutti i servizi di Azure. |

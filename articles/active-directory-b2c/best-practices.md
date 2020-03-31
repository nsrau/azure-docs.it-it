---
title: Procedure consigliate per Azure AD B2CBest practices for Azure AD B2C
titleSuffix: Azure AD B2C
description: Consigli e procedure consigliate da considerare quando si lavora con Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136160"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Consigli e procedure consigliate per Azure Active Directory B2C

Le procedure consigliate e i suggerimenti seguenti riguardano alcuni degli aspetti principali dell'integrazione di Azure Active Directory (Azure AD) B2C in ambienti applicativi nuovi o esistenti.

## <a name="fundamentals"></a>Nozioni fondamentali

|  |  |
|--|--|
| Scegliere i flussi utente per la maggior parte degli scenariChoose user flows for most scenarios | Il Framework di esperienza di identità di Azure AD B2C è il punto di forza del servizio. I criteri descrivono in modo completo le esperienze per le identità, ad esempio iscrizione, accesso o modifica del profilo. Per poter configurare le attività di gestione delle identità più comuni, il portale di Azure AD B2C include criteri predefiniti configurabili chiamati flussi utente. Con i flussi utente, puoi creare esperienze utente eccezionali in pochi minuti, con pochi clic. [Informazioni su quando usare i flussi utente e](custom-policy-overview.md#comparing-user-flows-and-custom-policies)i criteri personalizzati.|
| Registrazioni per l'app | Ogni applicazione (web, nativa) e API protetta deve essere registrata in Azure AD B2C. Se un'app ha sia una versione Web che una versione nativa di iOS e Android, è possibile registrarle come un'applicazione in Azure AD B2C con lo stesso ID client. Scopri come [registrare app OIDC, SAML, Web e native.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications) Altre informazioni sui [tipi di applicazione che possono essere usati in Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/application-types) |
| Passare alla fatturazione mensile degli utenti attivi | Azure AD B2C è passato dalle autenticazioni attive mensili alla fatturazione mensile degli utenti attivi (MAU). La maggior parte dei clienti troverà questo modello conveniente. [Ulteriori informazioni sulla fatturazione mensile degli utenti attivi](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>pianificazione e progettazione

Definire l'architettura dell'applicazione e del servizio, creare l'inventario dei sistemi correnti e pianificare la migrazione ad Azure AD B2C.

|  |  |
|--|--|
| Progettare una soluzione end-to-end | Includere tutte le dipendenze delle applicazioni quando si pianifica un'integrazione B2C di Azure AD. Considerare tutti i servizi e i prodotti attualmente presenti nell'ambiente o che potrebbero essere necessari per essere aggiunti alla soluzione, ad esempio Funzioni di Azure, sistemi di gestione delle relazioni con i clienti, gateway di gestione API di Azure e servizi di archiviazione. Prendere in considerazione la sicurezza e la scalabilità per tutti i servizi. |
| Documentare le esperienze degli utenti | Dettagliare tutti i percorsi utente che i clienti possono sperimentare nell'applicazione. Includi ogni schermata e tutti i flussi di diramazione che potrebbero incontrare quando interagisci con gli aspetti di identità e profilo dell'applicazione. Includere usabilità, accessibilità e localizzazione nella pianificazione. |
| Scegliere il protocollo di autenticazione corretto |  Per una suddivisione dei diversi scenari dell'applicazione e dei relativi flussi di autenticazione consigliati, vedere Scenari e flussi di [autenticazione supportati.](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) |
| Pilotare un'esperienza utente end-to-end proof-of-concept (POC) | Iniziare con gli esempi di [codice Microsoft](code-samples.md) ed esempi [della community](https://github.com/azure-ad-b2c/samples). |
| Creare un piano di migrazioneCreate a migration plan |Pianificare in anticipo può rendere la migrazione più agevole. Ulteriori informazioni sulla [migrazione degli utenti](user-migration.md).|
| Usabilità e sicurezza | La soluzione deve trovare il giusto equilibrio tra l'usabilità delle applicazioni e il livello di rischio accettabile dell'organizzazione. |
| Spostare le dipendenze locali nel cloudMove on-premises dependencies to the cloud | Per garantire una soluzione resiliente, è consigliabile spostare le dipendenze delle applicazioni esistenti nel cloud. |
| Eseguire la migrazione delle app esistenti a b2clogin.com | La deprecazione di login.microsoftonline.com è stata attivata per tutti i tenant B2C di Azure AD il 04 dicembre 2020. [Scopri di più](b2clogin.md). |

## <a name="implementation"></a>Implementazione

Durante la fase di implementazione, considerare le seguenti raccomandazioni.

|  |  |
|--|--|
| Modificare i criteri personalizzati con l'estensione B2C di Azure AD per il codice di Visual StudioEdit custom policies with the Azure AD B2C extension for Visual Studio Code | Scaricare Visual Studio Code e questa estensione basata sulla comunità [da Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Anche se non è un prodotto Microsoft ufficiale, l'estensione B2C di Azure AD per Visual Studio Code include diverse funzionalità che semplificano l'utilizzo dei criteri personalizzati. |
| Informazioni su come risolvere i problemi relativi ad Azure AD B2C | Scopri come [risolvere i problemi relativi ai criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) durante lo sviluppo. Informazioni sull'aspetto di un normale flusso di autenticazione e utilizzare gli strumenti per individuare anomalie ed errori. Ad esempio, usare [Application Insights](troubleshoot-with-application-insights.md) per esaminare i log di output dei percorsi utente. |
| Sfrutta la nostra libreria di modelli di criteri personalizzati comprovati | Trova [esempi](https://github.com/azure-ad-b2c/samples) per diversi percorsi utente avanzati di Gestione delle identità e accessi (CIAM) di Azure AD B2C. |


## <a name="testing"></a>Test

Testare e automatizzare l'implementazione B2C di Azure AD.

|  |  |
|--|--|
| Conto del traffico globale | Usare le origini del traffico da un indirizzo globale diverso per testare i requisiti di prestazioni e localizzazione. Assicurati che tutti i FILE HTML, CSS e dipendenze siano in grado di soddisfare le esigenze di prestazioni. |
| Test funzionali e dell'interfaccia utente | Testare i flussi utente end-to-end. Aggiungere test sintetici ogni pochi minuti utilizzando Selenium, VS Web Test, ecc. |
| Test della penna | Prima di passare in tempo reale con la soluzione, eseguire esercizi di test di penetrazione per verificare che tutti i componenti siano sicuri, incluse eventuali dipendenze di terze parti. Verificare di aver protetto le API con token di accesso e di aver usato il protocollo di autenticazione corretto per lo scenario dell'applicazione. Ulteriori informazioni sui test di [penetrazione](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) e sulle [regole di coinvolgimento dei test di penetrazione unificati di Microsoft Cloud](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| Test A/B | Vola le tue nuove funzionalità con un piccolo set casuale di utenti prima di distribuirlo a tutta la tua popolazione. Con JavaScript abilitato in Azure AD B2C, è possibile integrarsi con strumenti di test A/B come Optimizely, Clarity e altri. |
| Test di carico | Azure AD B2C può essere ridimensionato, ma l'applicazione può essere ridimensionata solo se tutte le relative dipendenze possono essere ridimensionate. Test del carico delle API e della rete CDN. |
| Limitazione |  Azure AD B2C limita il traffico se troppe richieste vengono inviate dalla stessa origine in un breve periodo di tempo. Usare diverse origini del traffico durante `AADB2C90229` il test di carico e gestire correttamente il codice di errore nelle applicazioni. |
| Automazione | Usare pipeline di integrazione e recapito continue (CI/CD) per automatizzare test e distribuzioni, ad esempio [DevOps di Azure.Use](deploy-custom-policies-devops.md)continuous integration and delivery (CI/CD) pipelines to automate testing and deployments, for example, Azure DevOps . |

## <a name="operations"></a>Operazioni

Gestire l'ambiente B2C di Azure AD.

|  |  |
|--|--|
| Creare più ambienti | Per semplificare le operazioni e l'implementazione della distribuzione, creare ambienti separati per sviluppo, test, pre-produzione e produzione. Creare tenant di Azure AD B2C per ognuno. |
| Usare il controllo della versione per i criteri personalizzatiUse version control for your custom policies | È consigliabile usare GitHub, Azure Repos o un altro sistema di controllo della versione basato su cloud per i criteri personalizzati di Azure AD B2C.Consider using GitHub, Azure Repos, or another cloud-based version control system for your Azure AD B2C custom policies. |
| Usare l'API Microsoft Graph per automatizzare la gestione dei tenant B2CUse the Microsoft Graph API to automate the management of your B2C tenants | API di Microsoft Graph:<br/>Gestire [Identity Experience Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) (criteri personalizzati)<br/>[Chiavi](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Flussi degli utenti](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integrazione con DevOps di AzureIntegrate with Azure DevOps | Una [pipeline CI/CD](deploy-custom-policies-devops.md) semplifica lo spostamento del codice tra ambienti diversi e garantisce la preparazione alla produzione in ogni momento.   |
| Integrare con Monitoraggio di Azure | [Gli eventi del registro](view-audit-logs.md) di controllo vengono mantenuti solo per sette giorni. [Integrazione con Monitoraggio di Azure](azure-monitor.md) per conservare i log per l'uso a lungo termine o integrarsi con strumenti SIEM (Security Information and Event Management) di terze parti per ottenere informazioni dettagliate sull'ambiente. |
| Configurare avvisi e monitoraggio attivi | [Tenere traccia del comportamento degli utenti](active-directory-b2c-custom-guide-eventlogger-appins.md) in Azure AD B2C usando Application Insights.Track user behavior in Azure AD B2C using Application Insights. |


## <a name="support-and-status-updates"></a>Aggiornamenti di supporto e stato

Rimani aggiornato con lo stato del servizio e trova le opzioni di supporto.

|  |  |
|--|--|
| [Aggiornamenti del servizio](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Rimani aggiornato con gli aggiornamenti e gli annunci dei prodotti B2C di Azure AD. |
| [Supporto tecnico Microsoft](support-options.md) | Presentare una richiesta di supporto per problemi tecnici di Azure AD B2C. Il supporto per fatturazione e gestione delle sottoscrizioni viene fornito gratuitamente. |
| [Stato di Azure](https://status.azure.com/status) | Visualizzare lo stato di integrità corrente di tutti i servizi di Azure.View the current health status of all Azure services. |
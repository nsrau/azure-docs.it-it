---
title: Motivi e modalità di aggiunta delle applicazioni ad Azure Active Directory
titleSuffix: Microsoft identity platform
description: Che cosa significa per un'applicazione essere aggiunta ad Azure AD e come si esegue questa operazione?
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/04/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebf6b9a07e775c76188dcebece011b01e90fbcf5
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803447"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Come vengono aggiunte le applicazioni in Azure AD e perché

In Azure AD ci sono due rappresentazioni delle applicazioni: 
* [Oggetti applicazione](app-objects-and-service-principals.md#application-object): anche se ci sono alcune [eccezioni](#notes-and-exceptions), questi oggetti possono essere considerati la definizione di un'applicazione.
* [Entità servizio](app-objects-and-service-principals.md#service-principal-object): questi oggetti possono essere considerati un'istanza di un'applicazione. Le entità servizio in genere fanno riferimento a un oggetto applicazione e a ogni oggetto applicazione possono fare riferimento più entità servizio in diverse directory.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Cosa sono gli oggetti applicazione e da dove provengono?
È possibile gestire gli [oggetti applicazione](app-objects-and-service-principals.md#application-object) nel portale di Azure tramite l'esperienza [Registrazioni per l'app](https://aka.ms/appregistrations). Gli oggetti applicazione descrivono l'applicazione ad Azure AD e possono essere considerati la definizione dell'applicazione, che consente al servizio di sapere come rilasciare token per l'applicazione in base alle relative impostazioni. L'oggetto applicazione è disponibile solo nella relativa home directory, anche se si tratta di un'applicazione multi-tenant che supporta entità servizio in altre directory. L'oggetto applicazione può includere gli elementi seguenti (oltre che informazioni aggiuntive non elencate):
* Nome, logo ed editore
* URI di reindirizzamento
* Segreti (chiavi simmetriche e/o asimmetriche usate per autenticare l'applicazione)
* Dipendenze API (OAuth)
* API/risorse/ambiti pubblicati (OAuth)
* Ruoli app (Controllo degli accessi in base al ruolo)
* Configurazione e metadati SSO
* Metadati e configurazione del provisioning utenti
* Metadati e configurazione proxy

È possibile creare oggetti applicazione in più modi, tra cui:
* Registrazione delle applicazioni nel portale di Azure
* Creazione di una nuova applicazione con Visual Studio e configurazione dell'applicazione per l'uso dell'autenticazione di Azure AD
* Quando un amministratore aggiunge un'applicazione dalla raccolta di app (in questo caso viene creata anche un'entità servizio)
* Usando l'API Microsoft Graph, l'API Graph di Azure AD o PowerShell per creare una nuova applicazione
* Molti altri modi, tra cui diverse esperienze di sviluppo in Azure ed esperienze di esplorazione API nei centri per sviluppatori

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Cosa sono le entità servizio e da dove provengono?
È possibile gestire le [entità servizio](app-objects-and-service-principals.md#service-principal-object) nel portale di Azure tramite l'esperienza [Applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/). Le entità servizio sono oggetti che governano un'applicazione che si connette ad Azure AD e possono essere considerate come l'istanza dell'applicazione nella directory. Per ogni applicazione specifica possono esserci più di un oggetto applicazione (registrato in una "home" directory) e uno o più oggetti entità servizio che rappresentano le istanze dell'applicazione in ogni directory in cui è in funzione. 

L'entità servizio può includere:

* Un riferimento a un oggetto applicazione tramite la proprietà ID applicazione
* Record delle assegnazioni di ruolo dell'applicazione a gruppi e utenti locali
* Record delle autorizzazioni amministratore e utente locali concesse all'applicazione
  * Ad esempio, l'autorizzazione dell'applicazione ad accedere a un indirizzo di posta elettronica di un utente specifico
* Record dei criteri locali, inclusi i criteri di accesso condizionale
* Record delle impostazioni locali alternative per un'applicazione
  * Regole di trasformazione delle attestazioni
  * Mapping degli attributi (provisioning utenti)
  * Ruoli dell'app specifici della directory (se l'applicazione supporta i ruoli personalizzati)
  * Nome o logo specifico della directory

Come gli oggetti applicazione, anche gli oggetti entità servizio possono essere creati in più modi, tra cui:

* Quando gli utenti accedono a un'applicazione di terze parti integrata con Azure AD
  * Durante la procedura di accesso, agli utenti viene chiesto di concedere all'applicazione l'autorizzazione ad accedere al proprio profilo e altri tipi di autorizzazioni. Quando viene dato il primo consenso da un utente, l'entità servizio che rappresenta l'applicazione viene aggiunta alla directory.
* Quando un utente accede ai Microsoft Online Services, ad esempio [Office 365](https://products.office.com/)
  * Quando si effettua l'abbonamento a Office 365 o si avvia una versione di valutazione, vengono create una o più entità servizio nella directory che rappresentano i vari servizi usati per fornire tutte le funzionalità associate a Office 365.
  * Alcuni servizi di Office 365 come SharePoint creano entità servizio in modo continuativo, per consentire la comunicazione sicura tra i componenti, inclusi i flussi di lavoro.
* Quando un amministratore aggiunge un'applicazione dalla raccolta di app (in questo caso viene creato anche un oggetto app sottostante)
* Aggiungere un'applicazione per l'uso di [Azure AD Application Proxy](/azure/active-directory/manage-apps/application-proxy)
* Connettere un'applicazione per l'accesso Single Sign-On tramite SAML o l'accesso SSO basato su password
* A livello di codice tramite l'API Graph di Azure AD o PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Qual è la relazione tra oggetti applicazione ed entità servizio?

Un'applicazione ha un oggetto applicazione nella relativa home directory a cui fanno riferimento una o più entità di servizio in ognuna delle directory in cui è in funzione (inclusa la home directory dell'applicazione).

![Mostra la relazione tra oggetti app ed entità servizio][apps_service_principals_directory]

Nel diagramma precedente Microsoft mantiene internamente due directory (visualizzate a sinistra) usate per pubblicare le applicazioni:

* Una per le app Microsoft (directory di servizi Microsoft)
* Una per le applicazioni di terze parti preintegrate (directory della raccolta di app)

I fornitori/editori di applicazioni che procedono all'integrazione con Azure AD devono avere una directory di pubblicazione (illustrata a destra come directory SaaS).

Le applicazioni aggiunte dall'utente (indicate come **app dell'utente** al centro del diagramma) includono:

* App sviluppate dall'utente (integrate con Azure AD)
* App connesse per l'accesso Single Sign-On
* App pubblicate tramite Azure AD Application Proxy

### <a name="notes-and-exceptions"></a>Note ed eccezioni

* Non tutte le entità servizio fanno riferimento a un oggetto applicazione. Al momento dello sviluppo inziale di Azure AD, i servizi forniti alle applicazioni erano più limitati e l'entità servizio era sufficiente per stabilire l'identità di un'applicazione. L'entità servizio originale assomigliava più all'account del servizio di Windows Server Active Directory. Per questo motivo, è ancora possibile creare entità servizio in modi diversi, ad esempio usando Azure AD PowerShell, senza dover prima creare un oggetto applicazione. L'API Graph di Azure AD richiede che sia disponibile un oggetto applicazione prima di creare un'entità servizio.
* Non tutte le informazioni riportate sopra sono al momento esposte a livello di codice. Le funzionalità seguenti sono disponibili solo nell'interfaccia utente:
  * Regole di trasformazione delle attestazioni
  * Mapping degli attributi (provisioning utenti)
* Per informazioni dettagliate su oggetti applicazione ed entità servizio, vedere la documentazione di riferimento dell'API REST di Azure AD Graph:
  * [Applicazione](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Entità servizio](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Perché le applicazioni vengono integrate con Azure AD?

Le applicazioni vengono aggiunte ad Azure AD per sfruttare uno o più dei servizi offerti, tra cui:

* Autenticazione e autorizzazione delle applicazioni.
* Autenticazione e autorizzazione degli utenti.
* Accesso SSO tramite federazione o password.
* Provisioning e sincronizzazione degli utenti.
* Controllo degli accessi in base al ruolo. È possibile usare la directory per definire i ruoli dell'applicazione per eseguire i controlli di autorizzazione in base al ruolo in un'applicazione.
* Servizi di autorizzazione OAuth (usati da Office 365 e altre applicazioni Microsoft per autorizzare l'accesso ad API e risorse).
* Proxy e pubblicazione dell'applicazione. È possibile pubblicare un'applicazione in Internet da una rete privata.

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Chi è autorizzato ad aggiungere applicazioni alla mia istanza di Azure AD?

Mentre ci sono alcune attività che solo gli amministratori globali possono svolgere (ad esempio l'aggiunta di applicazioni dalla raccolta di app e la configurazione di un'applicazione per usare il proxy di applicazione), per impostazione predefinita tutti gli utenti nella directory hanno i diritti per registrare gli oggetti applicazione che stanno sviluppando, oltre che la possibilità di scegliere quali applicazioni condividere e a quali consentire l'accesso ai dati aziendali tramite consenso. Se l'utente della directory che accede per la prima volta a un'applicazione concede il consenso, verrà creata un'entità servizio nel tenant. In caso contrario, le informazioni di concessione del consenso verranno archiviate nell'entità servizio esistente.

Anche se il fatto di concedere agli utenti di registrarsi e dare il consenso alle applicazioni può inizialmente destare qualche preoccupazione, tenere presente quanto segue:


* Le applicazioni hanno usato Windows Server Active Directory per autenticare gli utenti per anni senza richiedere la registrazione dell'applicazione nella directory. Ora le organizzazioni hanno maggiore visibilità sul numero esatto di applicazioni che usano la directory e sul relativo scopo.
* Delegando queste responsabilità agli utenti si evita la necessità di un processo di pubblicazione e di registrazione dell'applicazione basato su amministratore. Con Active Directory Federation Services (ADFS) un amministratore doveva in genere aggiungere un'applicazione come relying party per conto degli sviluppatori. Ora gli sviluppatori possono eseguire queste operazioni in modo indipendente.
* L'accesso alle applicazioni da parte degli utenti per scopi lavorativi tramite un account aziendale è un fatto positivo. Se successivamente gli utenti lasciano l'azienda, perdono automaticamente l'accesso al proprio account nelle applicazioni in uso.
* È positivo anche il fatto di poter tenere sotto controllo quali dati sono stati condivisi e con quali applicazioni. I dati sono più trasportabili che mai ed è quindi utile sapere chi ha condiviso determinati dati e con quali applicazioni.
* I proprietari di API che usano Azure AD per OAuth stabiliscono esattamente quali sono le autorizzazioni che gli utenti possono concedere alle applicazioni e quali autorizzazioni richiedono il consenso da parte dell'amministratore. Solo gli amministratori possono dare il consenso per ambiti più ampi e autorizzazioni più significative, mentre il consenso degli utenti è limitato alle funzionalità e ai dati degli utenti stessi.
* Quando un utente aggiunge un'applicazione o consente all'applicazione di accedere ai propri dati, l'evento può essere controllato, quindi è possibile visualizzare i report di controllo nel portale di Azure per determinare il modo in cui un'applicazione è stata aggiunta alla directory.

Se si vuole comunque impedire agli utenti nella directory di registrare le applicazioni e di accedere alle applicazioni senza l'approvazione dell'amministratore, ci sono due impostazioni che è possibile modificare per disattivare queste funzionalità:

* Per impedire agli utenti di dare il consenso alle applicazioni per proprio conto:
  1. Nel portale di Azure passare alla sezione [Impostazioni utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) in Applicazioni aziendali.
  2. Impostare l'opzione **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** su **No**.
     
     > [!NOTE]
     > Se si decide di disattivare il consenso dell'utente, un amministratore dovrà fornire il consenso per qualsiasi nuova applicazione che un utente deve usare.

* Per impedire agli utenti di registrare le proprie applicazioni:
  1. Nel portale di Azure passare alla sezione [Impostazioni utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) in Azure Active Directory
  2. Impostare l'opzione **Gli utenti possono registrare applicazioni** su **No**.

> [!NOTE]
> Microsoft usa la configurazione predefinita che consente agli utenti di registrare le applicazioni e fornire il consenso alle applicazioni per proprio conto.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

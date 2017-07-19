---
title: Accesso all'interfaccia API di Servizi multimediali di Azure con l'autenticazione di Azure Active Directory | Microsoft Docs
description: Informazioni sui concetti e i passaggi da eseguire per usare Azure Active Directory (Azure AD) per autenticare l'accesso all'API di Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: bdeae5d1440cf8930a9cbc1ddea38ea7754ff7f6
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Accesso all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD
 
L'API di Servizi multimediali di Azure è di tipo RESTful. È possibile usarla per eseguire operazioni sulle risorse multimediali tramite un'API REST o tramite gli SDK client disponibili. Servizi multimediali di Azure offre un SDK client di Servizi multimediali per Microsoft .NET. Per essere autorizzati ad accedere alle risorse e all'API di Servizi multimediali, è innanzitutto necessario essere autenticati. 

Servizi multimediali supporta l'[autenticazione basata su Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md). Il servizio REST di Servizi multimediali di Azure richiede che l'utente o l'applicazione che invia le richieste all'API REST abbia almeno il ruolo di **Collaboratore** o **Proprietario** per poter accedere alle risorse. Per altre informazioni, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-what-is.md).  

> [!IMPORTANT]
> Attualmente, Servizi multimediali supporta il modello di autenticazione del Servizio di controllo di accesso Azure. L'autorizzazione di Controllo di accesso, tuttavia, verrà dichiarata deprecata il 1° giugno 2018. È consigliabile eseguire la migrazione all'autenticazione di Azure AD il più presto possibile.

Questo documento offre una panoramica su come accedere all'API di Servizi multimediali tramite l'API REST o .NET.

## <a name="access-control"></a>Controllo di accesso

Perché la richiesta REST di Servizi multimediali di Azure abbia esito positivo, l'utente chiamante deve avere il ruolo di Collaboratore o Proprietario per l'account di Servizi multimediali al quale sta tentando di accedere.  
Solo un utente con il ruolo di Proprietario può concedere a nuovi utenti o a nuove app l'accesso (account) alle risorse multimediali. Il ruolo di Collaboratore consente di accedere solo alla risorsa multimediale.
Le richieste non autorizzate hanno esito negativo e restituiscono il codice di stato 401. Se viene visualizzato questo codice di errore, verificare se all'utente è stato assegnato il ruolo di Collaboratore o Proprietario per l'account di Servizi multimediali. Questa operazione può essere eseguita nel portale di Azure. Cercare l'account dei servizi multimediali e quindi fare clic sulla scheda **Controllo di accesso**. 

![Scheda Controllo di accesso](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Tipi di autenticazione 
 
Quando si utilizza l'autenticazione di Azure AD con Servizi multimediali di Azure, sono disponibili due opzioni di autenticazione:

- **Autenticazione utente**. Consente di eseguire l'autenticazione di una persona che usa l'app per interagire con le risorse di Servizi multimediali. L'applicazione interattiva deve prima richiedere all'utente le credenziali. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live. 
- **Autenticazione basata su entità servizio**. Consente di eseguire l'autenticazione di un servizio. Le applicazioni che in genere usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati. Esempi sono le app Web, le app per le funzioni, le app per la logica, l'interfaccia API e i microservizi.

### <a name="user-authentication"></a>Autenticazione utente 

Le applicazioni che devono usare il metodo di autenticazione utente sono app native di gestione o monitoraggio: app per dispositivi mobili, app di Windows e app console. Questo tipo di soluzione è utile quando si desidera un'interazione umana con il servizio in uno degli scenari seguenti:

- Dashboard di monitoraggio per i processi di codifica.
- Dashboard di monitoraggio per lo streaming live.
- Applicazione di gestione per gli utenti di computer desktop o di dispositivi mobili per amministrare le risorse in un account di Servizi multimediali.

> [!NOTE]
> Questo metodo di autenticazione non deve essere usato con le applicazioni per consumatori. 

Un'applicazione nativa deve innanzitutto acquisire un token di accesso da Azure AD e quindi usarlo quando si inviano richieste HTTP all'API REST di Servizi multimediali. Aggiungere il token di accesso all'intestazione della richiesta. 

Nel diagramma seguente viene illustrato un tipico flusso interattivo di autenticazione di un'applicazione: 

![Diagramma di app native](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

Nel diagramma precedente i numeri rappresentano il flusso delle richieste in ordine cronologico.

> [!NOTE]
> Quando si usa il metodo di autenticazione utente, tutte le app condividono gli stessi ID client (predefinito) e URI di reindirizzamento dell'applicazione nativa. 

1. Richiedere le credenziali all'utente.
2. Richiedere un token di accesso di Azure AD con i parametri seguenti:  

    * Endpoint tenant di Azure AD.

        Le informazioni sul tenant possono essere recuperate dal portale di Azure. Posizionare il cursore sul nome dell'utente connesso nell'angolo in alto a destra.
    * URI di risorsa per Servizi multimediali. 

        Questo URI è identico a quello degli account di Servizi multimediali inclusi nello stesso ambiente Azure, ad esempio https://rest.media.azure.net.

    * ID client dell'applicazione Servizi multimediali (nativa).
    * URI di reindirizzamento dell'applicazione Servizi multimediali (nativa).
    * URI di risorsa per Servizi multimediali REST.
        
        L'URI rappresenta l'endpoint API REST, ad esempio https://test03.restv2.westus.media.azure.net/api/.

    Per ottenere i valori di questi parametri, vedere [Uso del portale di Azure per accedere alle impostazioni di autenticazione di Azure AD](media-services-portal-get-started-with-aad.md) usando l'opzione di autenticazione utente.

3. Il token di accesso di Azure AD viene inviato al client.
4. Il client invia una richiesta all'API REST di Servizi multimediali di Azure con il token di accesso di Azure AD.
5. Il client ottiene nuovamente i dati da Servizi multimediali.

Per informazioni su come usare l'autenticazione di Azure AD per comunicare con le richieste REST tramite l'SDK del client .NET di Servizi multimediali, vedere [Uso dell'autenticazione Azure AD per accedere all'API di Servizi multimediali con .NET](media-services-dotnet-get-started-with-aad.md). 

Se non si usa l'SDK del client .NET di Servizi multimediali , è necessario creare manualmente una richiesta di token di accesso di Azure AD tramite i parametri descritti nel passaggio 2. Per altre informazioni, vedere [Come usare Azure AD Authentication Library per ottenere il token di Azure AD](../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Le applicazioni che usano in genere questo metodo di autenticazione sono app che eseguono servizi di livello intermedio e processi pianificati: app Web, app per le funzioni, app per la logica, API e microservizi. Questo metodo di autenticazione è anche adatto per le applicazioni interattive in cui è possibile usare un account del servizio per gestire le risorse.

Quando si usa il metodo di autenticazione basato sull'entità servizio per compilare scenari di consumer, l'autenticazione è gestita in genere nel livello intermedio (tramite alcune API) e non direttamente in un'applicazione desktop o per dispositivi mobili. 

Per usare questo metodo, creare un'applicazione Azure AD e l'entità servizio nel relativo tenant. Dopo aver creato l'applicazione, è possibile assegnare all'app l'accesso associato al ruolo di Collaboratore o Proprietario all'account di Servizi multimediali. È possibile eseguire questa operazione dal portale di Azure mediante l'interfaccia della riga di comando di Azure o uno script di PowerShell. È anche possibile usare un'applicazione Azure AD esistente. Per registrare e gestire l'app e l'entità servizio di Azure AD accedere [al portale di Azure](media-services-portal-get-started-with-aad.md) oppure usare l'[interfaccia della riga di comando di Azure 2.0](media-services-use-aad-auth-to-access-ams-api.md) o [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![App di livello intermedio](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Dopo aver creato l'applicazione Azure AD, si otterranno i valori per le impostazioni riportate di seguito. Per l'autenticazione sono necessari i valori seguenti:

- ID Client 
- Segreto client 

Nel diagramma precedente i numeri rappresentano il flusso delle richieste in ordine cronologico:
    
1. Un'app di livello intermedio (API Web o applicazione Web) richiede un token di accesso di Azure AD con i parametri seguenti:  

    * Endpoint tenant di Azure AD.

        Le informazioni sul tenant possono essere recuperate dal portale di Azure. Posizionare il cursore sul nome dell'utente connesso nell'angolo in alto a destra.
    * URI di risorsa per Servizi multimediali. 

        Questo URI è identico a quello degli account di Servizi multimediali inclusi nello stesso ambiente Azure, ad esempio https://rest.media.azure.net.

    * URI di risorsa per Servizi multimediali REST.

        L'URI rappresenta l'endpoint API REST, ad esempio https://test03.restv2.westus.media.azure.net/api/.

    * Valori dell'applicazione Azure AD: l'ID client e il segreto client.
    
    Per ottenere i valori di questi parametri, vedere [Uso del portale di Azure per accedere alle impostazioni di autenticazione di Azure AD](media-services-portal-get-started-with-aad.md) usando l'opzione di autenticazione basata su entità servizio.

2. Il token di accesso di Azure AD viene inviato al livello intermedio.
4. Il livello intermedio invia una richiesta all'API REST di Servizi multimediali di Azure con il token di Azure AD.
5. Il livello intermedio ottiene nuovamente i dati da Servizi multimediali.

Per altre informazioni su come usare l'autenticazione di Azure AD per comunicare con le richieste REST tramite l'SDK del client .NET di Servizi multimediali, vedere [Uso dell'autenticazione Azure AD per accedere all'API di Servizi multimediali di Azure con .NET](media-services-dotnet-get-started-with-aad.md). 

Se non si usa l'SDK del client .NET di Servizi multimediali , è necessario creare manualmente una richiesta di token di Azure AD tramite i parametri descritti nel passaggio 1. Per altre informazioni, vedere [Come usare Azure AD Authentication Library per ottenere il token di Azure AD](../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Eccezione: "Il server remoto ha restituito un errore: (401) Non autorizzato."

Soluzione: perché la richiesta REST di Servizi multimediali abbia esito positivo, l'utente chiamante deve avere il ruolo di Collaboratore o Proprietario per l'account di Servizi multimediali al quale sta tentando di accedere. Per altre informazioni, vedere la sezione [Controllo dell'accesso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

## <a name="resources"></a>Risorse

Gli articoli seguenti contengono alcune panoramiche dei concetti di autenticazione di Azure AD: 

- [Scenari di autenticazione per Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [Aggiungere, aggiornare o rimuovere un'applicazione in Azure AD](../active-directory/develop/active-directory-integrating-applications.md)
- [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)

## <a name="next-steps"></a>Passaggi successivi

* Usare il portale di Azure per [accedere all'autenticazione di Azure AD e usare l'API di Servizi multimediali di Azure](media-services-portal-get-started-with-aad.md).
* Usare l'autenticazione di Azure AD per [accedere all'API di Servizi multimediali di Azure con .NET](media-services-dotnet-get-started-with-aad.md).



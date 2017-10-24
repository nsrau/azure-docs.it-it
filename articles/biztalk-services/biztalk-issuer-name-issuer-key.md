---
title: "Nome e chiave dell'autorità emittente in Servizi BizTalk | Microsoft Docs"
description: "Informazioni su come recuperare il nome dell'autorità emittente e la chiave dell'autorità emittente per il bus di servizio o il Servizio di controllo di accesso (ACS) in Servizi BizTalk. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: b9fd985c23558596408e78eadae00dd0f95c4214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Servizi BizTalk: nome e chiave dell'autorità emittente

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Servizi BizTalk di Azure utilizza il nome e la chiave dell'autorità emittente del bus di servizio e il nome e la chiave dell'autorità emittente del Controllo di accesso. In particolare:

| Attività | Nome e chiave dell'autorità emittente da utilizzare |
| --- | --- |
| Distribuzione dell'applicazione da Visual Studio |Nome e chiave dell'autorità emittente del Controllo di accesso |
| Configurazione del Portale Servizi BizTalk di Azure |Nome e chiave dell'autorità emittente del Controllo di accesso |
| Creazione di inoltri LOB con i servizi dell'adapter di BizTalk in Visual Studio |Nome e chiave dell'autorità emittente del bus di servizio |

Questo argomento elenca i passaggi necessari per recuperare il nome e la chiave dell'autorità emittente. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nome e chiave dell'autorità emittente del Controllo di accesso
Il nome e la chiave dell'autorità emittente del Controllo di accesso vengono utilizzati dagli elementi seguenti:

* Applicazione del servizio BizTalk di Azure creata in Visual Studio: per distribuire correttamente l'applicazione del servizio BizTalk in Visual Studio in Azure, immettere il nome e la chiave dell'autorità emittente del Controllo di accesso. 
* Portale dei servizi BizTalk di Azure: quando si crea un servizio BizTalk e si apre il portale dei servizi BizTalk, il nome e la chiave dell'autorità emittente del Controllo di accesso vengono registrati automaticamente per le distribuzioni con gli stessi valori del Controllo di accesso.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Ottenere il nome e la chiave dell'autorità emittente del Controllo di accesso

Per usare il servizio contenitore di Azure per l'autenticazione e ottenere i valori di Nome e chiave dell'autorità emittente, la procedura generale include:

1. [Installare i cmdlet di Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Aggiungere il proprio Account Azure: `Add-AzureAccount`
3. Restituire il nome della sottoscrizione: `get-azuresubscription`
4. Selezionare la propria sottoscrizione: `select-azuresubscription <name of your subscription>` 
5. Creare un nuovo spazio dei nomi: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Esempio: `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Quando viene creato il nuovo spazio dei nomi del servizio contenitore di Azure (che può richiedere alcuni minuti), nella stringa di connessione sono elencati i valori di Nome e chiave dell'autorità emittente. 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Per riepilogare:  
Nome dell'autorità emittente = SharedSecretIssuer  
Chiave dell'autorità emittente = SharedSecretKey

Altre informazioni sul cmdlet [New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx). 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome e chiave dell'autorità emittente del bus di servizio
Il nome e la chiave dell'autorità emittente del bus di servizio vengono utilizzati dai servizi dell'adapter di BizTalk. Nel progetto di Servizi BizTalk in Visual Studio è possibile usare Servizi Adapter BizTalk per la connessione a un sistema LOB (Line-of-Business) locale. Per effettuare la connessione, creare l'inoltro LOB e immettere i dettagli relativi al sistema LOB. Quando si esegue questa operazione, è necessario immettere anche il nome e la chiave dell'autorità emittente del bus di servizio.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Per recuperare il nome e la chiave dell'autorità emittente del bus di servizio
1. Accedere al [portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel pannello di navigazione sinistro selezionare **Bus di servizio**.
3. Selezionare lo spazio dei nomi. Nella barra delle applicazioni selezionare **Informazioni di connessione**. Verranno visualizzati i valori relativi a **nome dell'autorità emittente** e **Chiave dell'autorità emittente**. Questi valori possono essere copiati.  

Per riepilogare:  
Nome dell'autorità di certificazione = Autorità di certificazione predefinita  
Chiave autorità di certificazione = Chiave predefinita

## <a name="next"></a>Avanti
Altri argomenti relativi a Servizi BizTalk di Azure:

* [Installare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Servizi BizTalk: Esercitazioni ed esempi](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Vedere anche
* [Procedura: Usare il servizio di gestione ACS per la configurazione delle identità del servizio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Servizi BizTalk: Grafico edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Servizi BizTalk: effettuare il provisioning di un servizio BizTalk mediante il portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Servizi BizTalk: Tabella degli stati del servizio](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Servizi BizTalk: Schede Dashboard, Monitoraggio, Scalabilità](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Servizi BizTalk: backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>


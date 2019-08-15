---
title: Connessioni ibride - Servizio app di Azure | Microsoft Docs
description: Informazioni su come creare e usare Connessioni ibride per accedere alle risorse in reti diverse
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4b125649dee51680625ac5a92b31bdc9f6830529
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67069598"
---
# <a name="azure-app-service-hybrid-connections"></a>Connessioni ibride del Servizio app di Azure #

Connessioni ibride è un servizio disponibile in Azure e una funzionalità del Servizio app di Azure. Come servizio, fornisce modalità d'uso e funzionalità superiori rispetto a quelle usate nel Servizio app. Per altre informazioni su Connessioni ibride e il relativo utilizzo al di fuori del servizio app, vedere [connessioni ibride di inoltro di Azure][HCService].

Nel Servizio app la funzionalità Connessioni ibride può essere usata per accedere alle risorse di applicazione presenti in altre reti. Fornisce l'accesso dalla propria app a un endpoint applicazione. Non abilita una funzionalità alternativa per accedere all'applicazione. Quando usata in Servizio app, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP. Ciò significa che l'endpoint della connessione ibrida può trovarsi in qualsiasi sistema operativo e in qualsiasi applicazione, a condizione che si acceda a una porta TCP in ascolto. La funzionalità Connessioni ibride non conosce né deve conoscere quale sia il protocollo dell'applicazione o a quale risorsa l'utente stia accedendo, in quanto si limita a fornire l'accesso alla rete.  


## <a name="how-it-works"></a>Funzionamento ##
La funzionalità Connessioni ibride è costituita da due chiamate in uscita a Inoltro del bus di servizio di Azure. Esistono una connessione da una libreria nell'host in cui l'app è in esecuzione nel Servizio app e una connessione da Gestione connessione ibrida a Inoltro del bus di servizio. Gestione connessione ibrida è un servizio di inoltro che viene distribuito nell'ambito della rete che ospita la risorsa a cui si prova ad accedere. 

Grazie alle due connessioni congiunte, l'app dispone di un tunnel TCP a una combinazione host:porta fissa sull'altro lato di Gestione connessione ibrida. La connessione usa TLS 1.2 per la sicurezza e le chiavi di firma di accesso condiviso per l'autenticazione e l'autorizzazione.    

![Diagramma del flusso di livello generale delle connessioni ibride][1]

Quando l'app esegue una richiesta DNS che corrisponde a un endpoint di Connessione ibrida configurato, il traffico TCP in uscita viene reindirizzato lungo la connessione ibrida.  

> [!NOTE]
> Ciò significa che è consigliabile usare sempre un nome DNS per la connessione ibrida. Alcuni software client non eseguono una ricerca DNS se l'endpoint usa un indirizzo IP al posto del nome DNS.
>

### <a name="app-service-hybrid-connection-benefits"></a>Vantaggi della funzionalità Connessioni ibride di Servizio app di Azure ###

La funzionalità Connessioni ibride offre numerosi vantaggi tra cui:

- Le app possono accedere in modo sicuro a servizi e sistemi locali.
- La funzionalità non richiede un endpoint accessibile tramite Internet.
- È facile e rapida da configurare. 
- Ogni connessione ibrida corrisponde a una singola combinazione host:porta, utile per la sicurezza.
- In genere non richiede varchi nei firewall, in quanto le connessioni sono tutte in uscita su porte Web standard.
- Dal momento che si tratta di una funzionalità a livello di rete, è indipendente dalla lingua usata dall'app e dalla tecnologia usata dall'endpoint.
- Può essere usata per fornire l'accesso a più reti da una singola app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Operazioni che non è possibile eseguire con la funzionalità Connessioni ibride ###

Le operazioni che non è possibile eseguire con connessioni ibride includono le seguenti:

- Montare un'unità.
- Usare UDP.
- Accedere a servizi basati su TCP che usano porte dinamiche, ad esempio la modalità FTP passiva o la modalità passiva estesa.
- Supportare LDAP, perché può richiedere UDP.
- Supportare Active Directory, perché non è possibile aggiungere a un dominio un ruolo di lavoro del servizio app.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Aggiungere e creare connessioni ibride nell'app ##

Per creare una connessione ibrida, passare alla [portale di Azure][portal] e selezionare l'app. Selezionare **Rete** > **Configurare gli endpoint della connessione ibrida**. Da qui è possibile visualizzare le connessioni ibride configurate per l'app.  

![Schermata dell'elenco delle connessioni ibride][2]

Per aggiungere una nuova connessione ibrida, selezionare **[+] Aggiungi connessione ibrida**.  Verrà visualizzato un elenco delle connessioni ibride che sono già state create. Per aggiungere una o più connessioni all'app, selezionare quelle desiderate e quindi selezionare **Aggiungi connessione ibrida selezionata**.  

![Schermata del portale delle connessioni ibride][3]

Se si intende creare una nuova connessione ibrida, selezionare **Crea nuova connessione ibrida**. Specificare le informazioni seguenti: 

- Nome connessione ibrida.
- Nome host dell'endpoint.
- Porta dell'endpoint.
- Spazio dei nomi del bus di servizio che si vuole usare.

![Schermata della finestra di dialogo Crea nuova connessione ibrida][4]

Ogni connessione ibrida è associata a uno spazio dei nomi del bus di servizio e ogni spazio dei nomi del bus di servizio si trova in un'area di Azure. È importante provare a usare uno spazio dei nomi del bus di servizio nella stessa area dell'app, in modo da evitare la latenza indotta dalla rete.

Se si vuole rimuovere la connessione ibrida dall'app, fare clic con il pulsante destro del mouse sulla connessione e selezionare **Disconnetti**.  

È possibile visualizzare i dettagli di una connessione ibrida aggiunta all'app semplicemente selezionandola. 

![Schermata dei dettagli delle connessioni ibride][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Creare una connessione ibrida nel portale del Servizio di inoltro di Azure ###

Oltre all'esperienza del portale offerta all'interno dell'app, è possibile creare connessioni ibride all'interno del portale del Servizio di inoltro di Azure. Affinché una connessione ibrida possa essere usata dal Servizio app, deve:

* Richiedere l'autorizzazione client.
* Includere un elemento di metadati, denominato endpoint, che contenga una combinazione host:porta come valore.

## <a name="hybrid-connections-and-app-service-plans"></a>Connessioni ibride e piani di servizio app ##

Le connessioni ibride del servizio app sono disponibili solo per gli SKU con piano tariffario Basic, Standard, Premium e Isolato. Esistono limiti legati al piano tariffario.  

| Piano tariffario | Numero di connessioni ibride a disposizione nel piano |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolato | 200 |

L'interfaccia utente del piano di servizio app mostra quante connessioni ibride sono in uso e da parte di quali app.  

![Schermata delle proprietà del piano di servizio app][6]

Selezionare la connessione ibrida per visualizzarne i dettagli. È possibile vedere tutte le informazioni disponibili nella visualizzazione dell'app, oltre al numero di app nello stesso piano che usano la connessione ibrida selezionata.

È previsto un limite al numero di endpoint della connessione ibrida che possono essere usati in un piano di servizio app. Tuttavia, ogni connessione ibrida usata può essere associata a un numero qualsiasi di app nel piano di servizio app specifico. Ad esempio, una singola connessione ibrida usata in cinque app distinte in un piano di servizio app viene conteggiata come una connessione ibrida.

### <a name="pricing"></a>Prezzi ###

Oltre a un requisito per lo SKU del piano di servizio app, sono previsti costi aggiuntivi per l'uso di connessioni ibride. Viene addebitato anche ogni listener usato da una connessione ibrida. Il listener è l'agente Gestione connessione ibrida. Cinque connessioni ibride supportate da due agenti Gestione connessione ibrida equivalgono a dieci listener. Per altre informazioni, vedere [Prezzi del bus di servizio][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestione connessione ibrida ##

La funzionalità Connessioni ibride richiede un agente di inoltro nella rete che ospita l'endpoint della connessione ibrida. L'agente di inoltro viene chiamato Gestione connessione ibrida. Per scaricare gestione connessione ibrida, dall'app nella [portale di Azure][portal]selezionare **rete** > **configurare gli endpoint della connessione ibrida**.  

Questo strumento viene eseguito in Windows Server 2012 e versioni successive. Gestione connessione ibrida viene eseguito come servizio e si connette in uscita a Inoltro di Azure sulla porta 443.  

Dopo avere installato Gestione connessione ibrida, è possibile eseguire HybridConnectionManagerUi.exe per usare l'interfaccia utente per lo strumento. Questo file è contenuto nella directory di installazione di Gestione connessione ibrida. In Windows 10 è sufficiente digitare *interfaccia utente Gestione connessione ibrida* nella casella di ricerca.  

![Schermata di Gestione connessione ibrida][7]

Quando si avvia l'interfaccia utente di Gestione connessione ibrida, la prima cosa che si vede è una tabella nella quale sono elencate tutte le connessioni ibride configurate con questa istanza di Gestione connessione ibrida. Per apportare eventuali modifiche, eseguire prima l'autenticazione con Azure. 

Per aggiungere una o più connessioni ibride a Gestione connessione ibrida:

1. Avviare l'interfaccia utente di Gestione connessione ibrida.
2. Selezionare **Configure another Hybrid Connection** (Configura un'altra connessione ibrida).
![Schermata Configure New Hybrid Connections (Configura nuove connessioni ibride)][8]

1. Accedere con l'account Azure per ottenere la Connessioni ibride disponibile con le sottoscrizioni. Gestione connessione ibrida non continua a usare l'account di Azure, oltre a questo. 
1. Scegliere una sottoscrizione.
1. Selezionare le connessioni ibride che si vuole vengano inoltrate tramite Gestione connessione ibrida.
![Schermata delle connessioni ibride][9]

1. Selezionare **Salva**.

Ora è possibile visualizzare le connessioni ibride che sono state aggiunte. È anche possibile selezionare la connessione ibrida configurata per visualizzarne i dettagli.

![Schermata Hybrid Connection Details (Dettagli connessioni ibride)][10]

Per supportare le connessioni ibride con cui è configurato, Gestione connessione ibrida richiede quanto segue:

- Accesso TCP ad Azure sulla porta 443.
- Accesso TCP all'endpoint della connessione ibrida.
- Possibilità di eseguire ricerche DNS nell'host endpoint e nello spazio dei nomi del bus di servizio.

> [!NOTE]
> Il servizio di inoltro di Azure si affida a WebSocket per la connettività. Questa funzionalità è disponibile solo in Windows Server 2012 e versioni successive. Per questo motivo, Gestione connessione ibrida non è supportato nelle versioni precedenti a Windows Server 2012.
>

### <a name="redundancy"></a>Ridondanza ###

Ogni istanza di Gestione connessione ibrida può supportare più connessioni ibride. Ogni connessione ibrida specificata può anche essere supportata da più istanze di Gestione connessione ibrida. Il comportamento predefinito consiste nell'indirizzare il traffico tra le istanze configurate di Gestione connessione ibrida per un endpoint specificato. Se si intende usufruire della disponibilità elevata nelle connessioni ibride dalla rete, eseguire più istanze di Gestione connessione ibrida in computer separati. L'algoritmo di distribuzione del carico usato dal servizio Inoltro per distribuire il traffico agli agenti Gestione connessione ibrida prevede un'assegnazione casuale. 

### <a name="manually-add-a-hybrid-connection"></a>Aggiungere manualmente una connessione ibrida ###

Per consentire a un'entità esterna alla propria sottoscrizione di ospitare un'istanza di Gestione connessione ibrida per una connessione ibrida specificata, condividere con tale entità la stringa di connessione gateway per la connessione ibrida. È possibile visualizzare la stringa di connessione del gateway nelle proprietà della connessione ibrida nel [portale di Azure][portal]. Per usare tale stringa, selezionare **Immetti manualmente** in Gestione connessione ibrida e incollare la stringa di connessione gateway.

![Aggiungere manualmente una connessione ibrida][11]

### <a name="upgrade"></a>Aggiorna ###

A Gestione connessione ibrida vengono apportati aggiornamenti periodici per correggere errori o aggiungere miglioramenti. Quando gli aggiornamenti vengono resi disponibili, viene visualizzata una finestra popup nell'interfaccia utente di Gestione connessione ibrida. L'aggiornamento applica le modifiche e riavvia Gestione connessione ibrida. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Aggiunta di una connessione ibrida all'app a livello di codice ##

Le API seguenti possono essere usate direttamente per gestire le connessioni ibride connesse alle app. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

L'oggetto JSON associato a una connessione ibrida ha un aspetto simile al seguente:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Un modo per usare queste informazioni è il armclient, che è possibile ottenere dal progetto GitHub [armclient][armclient] . Ecco un esempio di collegamento di una connessione ibrida preesistente all'app. Creare un file JSON in base allo schema sopra, simile al seguente:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Per usare questa API, sono necessari la chiave di invio e l'ID risorsa di inoltro. Se le informazioni sono state salvate con il nome di file hctest.json, immettere questo comando per collegare la connessione ibrida all'app: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>risoluzione dei problemi ##

Lo stato "Connesso" indica che almeno un'istanza di Gestione connessione ibrida è configurata con quella specifica connessione ed è in grado di raggiungere Azure. Se lo stato della connessione ibrida non è **Connesso**, la connessione ibrida non è configurata in alcuna istanza di Gestione connessione ibrida con accesso ad Azure.

Il motivo principale per cui i client non riescono a connettersi al relativo endpoint è perché l'endpoint è stato specificato usando un indirizzo IP anziché un nome DNS. Se l'app non riesce a raggiungere l'endpoint desiderato ed è stato specificato un indirizzo IP, usare un nome DNS valido nell'host in cui Gestione connessione ibrida è in esecuzione. È necessario controllare anche che il nome DNS venga risolto correttamente nell'host in cui Gestione connessione ibrida è in esecuzione e che vi sia connettività tra l'host in cui Gestione connessione ibrida è in esecuzione e l'endpoint della connessione ibrida.  

Nel servizio app, lo strumento da riga di comando **tcpping** può essere richiamato dalla console strumenti avanzati (kudu). Questo strumento indica se si dispone dell'accesso a un endpoint TCP, ma non se si dispone dell'accesso all'endpoint di una connessione ibrida. Quando lo strumento viene usato nella console per rilevare l'endpoint di una connessione ibrida, viene confermato solo che usa una combinazione host:porta.  

Se si dispone di un client della riga di comando per l'endpoint, è possibile testare la connettività dalla console app. Ad esempio, è possibile testare l'accesso agli endpoint server Web usando curl.

## <a name="biztalk-hybrid-connections"></a>Connessioni ibride BizTalk ##

La forma precedente di questa funzionalità è nota come Connessioni ibride BizTalk. Questa funzionalità ha raggiunto la fine del ciclo di vita il 31 maggio 2018 e ha smesso di funzionare. Le connessioni ibride BizTalk sono state rimosse da tutte le app e non sono più accessibili tramite il portale o l'API. Se queste connessioni obsolete sono ancora configurate in Gestione connessione ibrida, continuerà a essere indicato lo stato Sospeso e verrà visualizzata una nota sulla fine del ciclo di vita nella parte inferiore.

![Connessioni ibride BizTalk in Gestione connessione ibrida][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/

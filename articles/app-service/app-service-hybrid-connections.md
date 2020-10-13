---
title: Connessioni ibride
description: Informazioni su come creare e usare connessioni ibride nel servizio app Azure per accedere alle risorse in reti diversi.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/08/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1cb86f77a6ffcbb0fb45b3a57b57de531822f2b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742605"
---
# <a name="azure-app-service-hybrid-connections"></a>Connessioni ibride del Servizio app di Azure

Connessioni ibride è un servizio disponibile in Azure e una funzionalità del Servizio app di Azure. Come servizio, fornisce modalità d'uso e funzionalità superiori rispetto a quelle usate nel Servizio app. Per altre informazioni su Connessioni ibride e sul suo uso al di fuori del Servizio app, vedere [Protocollo per le connessioni ibride di inoltro di Azure][HCService].

All'interno del servizio app, è possibile usare Connessioni ibride per accedere alle risorse dell'applicazione in qualsiasi rete in grado di effettuare chiamate in uscita ad Azure tramite la porta 443. Connessioni ibride fornisce l'accesso dall'app a un endpoint TCP e non consente un nuovo modo di accedere all'app. Quando usata in Servizio app, ogni connessione ibrida è correlata a una singola combinazione di host e porta TCP. Ciò consente alle app di accedere alle risorse in qualsiasi sistema operativo, purché sia un endpoint TCP. La funzionalità Connessioni ibride non conosce né deve conoscere quale sia il protocollo dell'applicazione o a quale risorsa l'utente stia accedendo, Fornisce semplicemente l'accesso alla rete.  

## <a name="how-it-works"></a>Funzionamento ##
Connessioni ibride richiede la distribuzione di un agente di inoltro dove può raggiungere sia l'endpoint desiderato che Azure. L'agente di inoltro, Gestione connessione ibrida (HCM), effettua una chiamata al relay di Azure sulla porta 443. Dal sito dell'app Web, l'infrastruttura del servizio app si connette anche al servizio di inoltro di Azure per conto dell'applicazione. Tramite le connessioni Unite, l'app è in grado di accedere all'endpoint desiderato. La connessione usa TLS 1.2 per la sicurezza e le chiavi di firma di accesso condiviso per l'autenticazione e l'autorizzazione.    

![Diagramma del flusso di livello generale delle connessioni ibride][1]

Quando l'app esegue una richiesta DNS che corrisponde a un endpoint di Connessione ibrida configurato, il traffico TCP in uscita viene reindirizzato lungo la connessione ibrida.  

> [!NOTE]
> Ciò significa che è consigliabile usare sempre un nome DNS per la connessione ibrida. Alcuni software client non eseguono una ricerca DNS se l'endpoint usa un indirizzo IP al posto del nome DNS. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Vantaggi della funzionalità Connessioni ibride di Servizio app di Azure ###

La funzionalità Connessioni ibride offre numerosi vantaggi tra cui:

- Le app possono accedere in modo sicuro a servizi e sistemi locali.
- La funzionalità non richiede un endpoint accessibile tramite Internet.
- È facile e rapida da configurare. Non sono richiesti gateway
- Ogni connessione ibrida corrisponde a una singola combinazione host:porta, utile per la sicurezza.
- In genere non richiede varchi nei firewall, in quanto le connessioni sono tutte in uscita su porte Web standard.
- Dal momento che si tratta di una funzionalità a livello di rete, è indipendente dalla lingua usata dall'app e dalla tecnologia usata dall'endpoint.
- Può essere usata per fornire l'accesso a più reti da una singola app. 
- È supportato in GA per le app native di Windows ed è in anteprima per le app Linux. Non è supportata per le app contenitore di Windows.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Operazioni che non è possibile eseguire con la funzionalità Connessioni ibride ###

Le operazioni che non è possibile eseguire con connessioni ibride includono le seguenti:

- Montare un'unità.
- Usare UDP.
- Accedere a servizi basati su TCP che usano porte dinamiche, ad esempio la modalità FTP passiva o la modalità passiva estesa.
- Supportare LDAP, perché può richiedere UDP.
- Supportare Active Directory, perché non è possibile aggiungere a un dominio un ruolo di lavoro del servizio app. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Aggiungere e creare connessioni ibride nell'app ##

Per creare una connessione ibrida, accedere al [portale di Azure][portal] e selezionare l'app. Selezionare **rete**  >  **configurare gli endpoint della connessione ibrida**. Da qui è possibile visualizzare le connessioni ibride configurate per l'app.  

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
| Basic | 5 per piano |
| Standard | 25 per piano |
| PremiumV2 | 200 per app |
| Isolato | 200 per app |

L'interfaccia utente del piano di servizio app mostra quante connessioni ibride sono in uso e da parte di quali app.  

![Schermata delle proprietà del piano di servizio app][6]

Selezionare la connessione ibrida per visualizzarne i dettagli. È possibile vedere tutte le informazioni disponibili nella visualizzazione dell'app, oltre al numero di app nello stesso piano che usano la connessione ibrida selezionata.

È previsto un limite al numero di endpoint della connessione ibrida che possono essere usati in un piano di servizio app. Tuttavia, ogni connessione ibrida usata può essere associata a un numero qualsiasi di app nel piano di servizio app specifico. Ad esempio, una singola connessione ibrida usata in cinque app distinte in un piano di servizio app viene conteggiata come una connessione ibrida.

### <a name="pricing"></a>Prezzi ###

Oltre a un requisito per lo SKU del piano di servizio app, sono previsti costi aggiuntivi per l'uso di connessioni ibride. Viene addebitato anche ogni listener usato da una connessione ibrida. Il listener è l'agente Gestione connessione ibrida. Cinque connessioni ibride supportate da due agenti Gestione connessione ibrida equivalgono a dieci listener. Per altre informazioni, vedere [Prezzi del bus di servizio][sbpricing].

## <a name="hybrid-connection-manager"></a>Gestione connessione ibrida ##

La funzionalità Connessioni ibride richiede un agente di inoltro nella rete che ospita l'endpoint della connessione ibrida. L'agente di inoltro viene chiamato Gestione connessione ibrida. Per scaricare Gestione connessione ibrida, dall'app nel [portale di Azure][portal] selezionare **Rete** > **Configurare gli endpoint della connessione ibrida**.  

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

Per consentire a un'entità esterna alla propria sottoscrizione di ospitare un'istanza di Gestione connessione ibrida per una connessione ibrida specificata, condividere con tale entità la stringa di connessione gateway per la connessione ibrida. La stringa di connessione gateway è visibile nelle proprietà della connessione ibrida nel [portale di Azure][portal]. Per usare tale stringa, selezionare **Immetti manualmente** in Gestione connessione ibrida e incollare la stringa di connessione gateway.

![Aggiungere manualmente una connessione ibrida][11]

### <a name="upgrade"></a>Aggiornamento ###

A Gestione connessione ibrida vengono apportati aggiornamenti periodici per correggere errori o aggiungere miglioramenti. Quando gli aggiornamenti vengono resi disponibili, viene visualizzata una finestra popup nell'interfaccia utente di Gestione connessione ibrida. L'aggiornamento applica le modifiche e riavvia Gestione connessione ibrida. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Aggiunta di una connessione ibrida all'app a livello di codice ##

È disponibile supporto dell'interfaccia della riga di comando di Azure per Connessioni ibride. I comandi forniti operano a livello di app e del piano di servizio app.  I comandi a livello di app sono:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

I comandi del piano di servizio app consentono di impostare la chiave che una determinata connessione ibrida utilizzerà. Esistono due chiavi impostate in ogni connessione ibrida, una primaria e una secondaria. È possibile scegliere di usare la chiave primaria o secondaria con i comandi seguenti. In questo modo è possibile cambiare le chiavi quando si desidera rigenerare periodicamente le chiavi. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Proteggere il Connessioni ibride ##

È possibile aggiungere una connessione ibrida esistente ad altre app Web del servizio app da parte di qualsiasi utente che disponga di autorizzazioni sufficienti per l'inoltro del bus di servizio di Azure sottostante. Ciò significa che se è necessario impedire ad altri utenti di riusare la stessa connessione ibrida, ad esempio quando la risorsa di destinazione è un servizio che non ha misure di sicurezza aggiuntive per impedire l'accesso non autorizzato, è necessario bloccare l'accesso al relè del bus di servizio di Azure.

Chiunque abbia `Reader` accesso all'inoltro potrà _visualizzare_ la connessione ibrida quando tenterà di aggiungerla all'App Web nel portale di Azure, ma non sarà in grado di _aggiungerla_ poiché non dispone delle autorizzazioni necessarie per recuperare la stringa di connessione usata per stabilire la connessione di inoltro. Per aggiungere correttamente la connessione ibrida, è necessario che disponga dell' `listKeys` autorizzazione ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ). Il `Contributor` ruolo o qualsiasi altro ruolo che include questa autorizzazione per l'inoltro consentirà agli utenti di usare la connessione ibrida e di aggiungerla alle proprie app Web.

## <a name="troubleshooting"></a>Risoluzione dei problemi ##

Lo stato "Connesso" indica che almeno un'istanza di Gestione connessione ibrida è configurata con quella specifica connessione ed è in grado di raggiungere Azure. Se lo stato della connessione ibrida non è **Connesso**, la connessione ibrida non è configurata in alcuna istanza di Gestione connessione ibrida con accesso ad Azure.

Il motivo principale per cui i client non riescono a connettersi al relativo endpoint è perché l'endpoint è stato specificato usando un indirizzo IP anziché un nome DNS. Se l'app non riesce a raggiungere l'endpoint desiderato ed è stato specificato un indirizzo IP, usare un nome DNS valido nell'host in cui Gestione connessione ibrida è in esecuzione. È necessario controllare anche che il nome DNS venga risolto correttamente nell'host in cui Gestione connessione ibrida è in esecuzione e che vi sia connettività tra l'host in cui Gestione connessione ibrida è in esecuzione e l'endpoint della connessione ibrida.  

Nel servizio app, lo strumento da riga di comando **tcpping** può essere richiamato dalla console strumenti avanzati (kudu). Questo strumento indica se si dispone dell'accesso a un endpoint TCP, ma non se si dispone dell'accesso all'endpoint di una connessione ibrida. Quando lo strumento viene usato nella console per rilevare l'endpoint di una connessione ibrida, viene confermato solo che usa una combinazione host:porta.  

Se si dispone di un client della riga di comando per l'endpoint, è possibile testare la connettività dalla console app. Ad esempio, è possibile testare l'accesso agli endpoint server Web usando curl.


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
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
---
title: Note sulla versione per Servizi BizTalk di Azure | Documentazione Microsoft
description: Elenca i problemi noti per i servizi BizTalk di Azure
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 84ad965ed6ef1711fda983220cf004fdd48d290d


---
# <a name="release-notes-for-azure-biztalk-services"></a>Note sulla versione per Servizi BizTalk di Azure
Le note sulla versione per Servizi BizTalk di Microsoft Azure contengono i problemi noti in questa versione.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Novità nell'aggiornamento di novembre di Servizi BizTalk
* È possibile abilitare Crittografia dati inattivi nel portale di Servizi BizTalk. Vedere [Abilitare Crittografia dati inattivi nel portale di Servizi BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Cronologia aggiornamenti
### <a name="october-update"></a>Aggiornamento di ottobre
* Supporto per gli account aziendali:  
  * **Scenario**: si è registrata la distribuzione di un servizio BizTalk usando un account Microsoft (ad esempio, user@live.com). In questo scenario solo gli utenti con account Microsoft possono gestire il servizio BizTalk usando il portale di Servizi BizTalk. Non è possibile usare un account aziendale.  
  * **Scenario**: si è registrata la distribuzione di un servizio BizTalk usando un account aziendale in un'istanza di Azure Active Directory (ad esempio, user@fabrikam.com or user@contoso.com). In questo scenario solo gli utenti di Azure Active Directory nella stessa organizzazione possono gestire il servizio BizTalk usando il portale di Servizi BizTalk. Non è possibile usare un account Microsoft.  
* Quando si crea un servizio BizTalk nel portale di Azure classico, l'utente viene registrato automaticamente nel portale di Servizi BizTalk.
  * **Scenario**: si accede al portale di Azure classico, si crea un servizio BizTalk e quindi si seleziona **Gestisci** per la prima volta. Quando si apre il portale di Servizi BizTalk, il servizio BizTalk viene registrato automaticamente ed è pronto per le distribuzioni.  
    Vedere [Registrazione e aggiornamento di una distribuzione di servizio BizTalk nel portale di Servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Aggiornamento del 14 agosto
* Dissociazione di bridge e accordo: i bridge e gli accordi tra partner commerciale sono ora separati nel portale di Servizi BizTalk. È ora possibile creare accordi e bridge separatamente e in fase di esecuzione i bridge si risolvono in un accordo in base ai valori nel messaggio EDI. Vedere [Creare accordi in Servizi BizTalk di Azure](https://msdn.microsoft.com/library/azure/hh689908.aspx), [Creare un bridge EDI con il portale di Servizi BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [Creare un bridge AS2 con il portale di Servizi BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx) e [Come i bridge si risolvono in accordi in fase di esecuzione](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* L'opzione per la creazione di modelli per gli accordi non è più disponibile.  
* Per l'accordo sul lato trasmissione, è ora possibile specificare set di delimitatori diversi per ogni schema. Questa configurazione è specificata nelle impostazioni del protocollo per l'accordo sul lato trasmissione. Per altre informazioni, vedere [Creare un accordo X12 in Servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx) e [Creare un accordo EDIFACT in Servizi BizTalk](https://msdn.microsoft.com/library/azure/dn606267.aspx). Sono state aggiunte due nuove entità all'API del modello a oggetti TPM per lo stesso scopo. Vedere [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) ed [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* I costrutti XSD standard, inclusi i tipi derivati, sono ora supportati. Vedere [Usare costrutti XSD standard nelle mappe](https://msdn.microsoft.com/library/azure/dn793987.aspx) e [Usare tipi derivati in scenari ed esempi di mapping](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 supporta nuovi algoritmi MIC per la firma dei messaggi e nuovi algoritmi di crittografia. Vedere [Creare un contratto AS2 nei servizi BizTalk di Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Problemi noti

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemi di connettività dopo l'aggiornamento del portale di Servizi BizTalk
  Se il portale di Servizi BizTalk è aperto durante l'aggiornamento di Servizi BizTalk per distribuire le modifiche apportate al servizio, potrebbero verificarsi problemi di connettività con il portale di Servizi BizTalk.  
  Per risolvere il problema, provare a riavviare il browser, a eliminare la cache del browser o ad avviare il portale in modalità privata.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>L'ambiente di sviluppo integrato (IDE) di Visual Studio non può individuare l'elemento se si fa clic su un errore o un avviso in un progetto di Servizi BizTalk
Installare Visual Studio 2012 Update 3 RC 1 per correggere il problema.  

### <a name="custom-binding-project-reference"></a>Riferimento al progetto di associazione personalizzata
Considerare le situazioni seguenti con un progetto di Servizi BizTalk in una soluzione di Visual Studio:  

* Nella stessa soluzione di Visual Studio sono disponibili un progetto di Servizi BizTalk e un progetto di associazione personalizzata. Il progetto di Servizi BizTalk contiene un riferimento a questo file di progetto di associazione personalizzata.
* Il progetto di Servizi BizTalk contiene un riferimento a una DLL di comportamento/associazione personalizzata.

La soluzione viene 'compilata' correttamente in Visual Studio. Successivamente, la soluzione viene 'ricompilata' o 'pulita'. In seguito, quando si procede di nuovo alla ricompilazione o alla pulizia, viene restituito l'errore seguente:  
  Impossibile copiare il file <Path to DLL> in "bin\Debug\FileName.dll". Il processo non riesce ad accedere al file 'bin\Debug\FileName.dll' perché è usato da un altro processo.  

#### <a name="workaround"></a>Soluzione alternativa
* Se [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) è installato, sono disponibili le due opzioni seguenti:
  
  * Riavviare Visual Studio
  * Riavviare la soluzione. Quindi, eseguire solo un'operazione di compilazione sulla soluzione.  
* Se [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) non è installato, aprire Gestione attività, fare clic sulla scheda Processi, fare clic sul processo MSBuild.exe e quindi fare clic sul pulsante Termina processo.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Il routing a endpoint BasicHttpRelay non è supportato dal bridge e il portale servizi BizTalk se i caratteri non stampabili vengono alzati di livello come intestazioni HTTP
Se si usano caratteri non stampabili come parte delle proprietà alzate di livello relative ai messaggi, questi non possono essere indirizzati a destinazioni di inoltro che usano l'associazione BasicHttpRelay. Inoltre, le proprietà alzate di livello disponibili come parte del rilevamento includono la codifica URL per i BLOB e sono senza codifica per le destinazioni.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>Un MDN viene inviato in modo asincrono anche se l'opzione di invio di un MDN asincrono non è selezionata
Esaminare questo scenario: se si seleziona la casella di controllo **Invia notifica sulla ricezione del messaggio asincrona**, si specifica un URL a cui inviare la notifica sulla ricezione del messaggio asincrona e quindi si deseleziona nuovamente la casella di controllo **Invia notifica sulla ricezione del messaggio asincrona**, la notifica viene comunque inviata all'URL specificato.  
Come soluzione alternativa, è necessario cancellare l'URL specificato prima di deselezionare la casella di controllo relativa all' **invio di notifiche sulla ricezione del messaggio asincrone** e quindi distribuire l'accordo AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Gli spazi vuoti che superano un interscambio valido causano l'invio di un messaggio vuoto all'endpoint di sospensione
Se sono presenti spazi vuoti che superano un segmento IEA, il disassemblatore li interpreta come la fine dell'interscambio corrente e considera il set di spazi vuoti successivo come un messaggio successivo. Dal momento che non si tratta di un interscambio valido, alla destinazione della route viene inviato un messaggio valido, mentre all'endpoint di sospensione viene inviato un messaggio vuoto.  

### <a name="tracking-in-biztalk-services-portal"></a>Rilevamento nel portale di Servizi BizTalk
Gli eventi di rilevamento vengono acquisiti nell'ambito dell'elaborazione dei messaggi EDI e di qualsiasi correlazione. Se un errore di elaborazione del messaggio si verifica all'esterno della fase del protocollo, il rilevamento risulterà elaborato in modo corretto. Per i dettagli relativi all'errore, vedere la sezione LOG della colonna **Dettagli** in **Rilevamento**.
Le impostazioni di ricezione e invio per X12 ([Creare un accordo X12 in Servizi BizTalk di Azure](https://msdn.microsoft.com/library/azure/hh689847.aspx)) forniscono le informazioni sulla fase del protocollo.  

### <a name="update-agreement"></a>Accordo di aggiornamento
Quando viene configurato un accordo, il portale di Servizi BizTalk consente di modificare il qualificatore di un'identità. Per effetto di questa operazione è possibile che vengano create proprietà incoerenti. Ad esempio nel caso di un accordo in cui vengono usate ZZ:1234567 e ZZ:7654321 come proprietà del qualificatore. Nelle impostazioni del profilo del portale di Servizi BizTalk, modificare ZZ:1234567 specificando 01:ChangedValue. Se si apre l'accordo, viene visualizzato 01:ChangedValue in sostituzione di ZZ:1234567.
Per modificare il qualificatore di un'identità, eliminare l'accordo, aggiornare il campo **Identità** nel profilo del partner, quindi creare nuovamente l'accordo.  

> AZURE.WARNING Questo comportamento influisce su X12 e AS2.  
> 
> 

### <a name="as2-attachments"></a>Allegati dei messaggi AS2
Gli allegati dei messaggi AS2 non sono supportati né in invio né in ricezione. In particolare, gli allegati vengono automaticamente ignorati e il corpo del messaggio viene elaborato come un normale messaggio AS2.  

### <a name="resources-remembering-path"></a>Risorse: memorizzazione del percorso
Quando si aggiungono **risorse**, è possibile che nella finestra di dialogo non venga memorizzato il percorso precedentemente usato per aggiungere una risorsa. Per fare in modo che il percorso precedentemente usato venga memorizzato, in Internet Explorer provare ad aggiungere il sito Web del portale di Servizi BizTalk all'area **Siti attendibili** .  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Se si rinomina il nome dell'entità di un bridge e si chiude il progetto senza salvare le modifiche, quando si apre nuovamente l'entità si verifica un errore
Si consideri uno scenario costituito da azioni nell'ordine seguente:  

* Si aggiunge un bridge, ad esempio un bridge unidirezionale XML, a un progetto di Servizi BizTalk  
* Si rinomina il bridge specificando un valore per la proprietà Entity Name. In questo modo il file con estensione bridgeconfig associato viene rinominato con il nome specificato.  
* Si chiude il file con estensione bcs chiudendo la relativa scheda in Visual Studio senza salvare le modifiche.  
* Si apre nuovamente il file BCS in Esplora soluzioni.  
  Si noterà che mentre il nome del file BRIDGECONFIG associato corrisponde al nuovo nome, il nome dell'entità nell'area di Creazione progetti corrisponde ancora al nome precedente. Se si cerca di aprire la configurazione dei bridge facendo doppio clic sul componente bridge, viene visualizzato l'errore seguente:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Per evitare questo scenario, assicurarsi di salvare le modifiche dopo avere rinominato le entità in un progetto Servizio BizTalk.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>La compilazione di un progetto di Servizi BizTalk viene eseguita in modo corretto anche se un elemento è stato escluso da un progetto di Visual Studio
Si consideri uno scenario in cui si aggiunge un elemento, ad esempio un file XSD, a un progetto di Servizi BizTalk, si include tale elemento nella Configurazione bridge, ad esempio impostandolo come tipo di messaggio di richiesta e quindi lo si esclude dal progetto di Visual Studio. In un caso come questo, la compilazione del progetto non genererà errori fino a quando l'elemento eliminato resterà disponibile sul disco nello stesso percorso in cui si trovava quando è stato incluso nel progetto di Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Il progetto di Servizi BizTalk non verifica la disponibilità degli schemi durante la configurazione dei bridge
In un progetto di Servizi BizTalk, se uno schema aggiunto al progetto importa un altro schema, il progetto di Servizi BizTalk non verifica se lo schema importato viene aggiunto o meno al progetto. Se si prova a compilare un progetto di questo tipo, non verranno rilevati errori di compilazione.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Il set di caratteri del messaggio di risposta per un bridge XML di richiesta/risposta è sempre UTF-8
Per questa versione, il set di caratteri del messaggio di risposta proveniente da un bridge XML di richiesta/risposta è sempre impostato su UTF-8.
  
### <a name="user-defined-datatypes"></a>Tipi di dati definiti dall'utente
Gli adattatori di BizTalk Adapter Pack inclusi nella funzionalità BizTalk Adapter Service sono in grado di usare tipi di dati definiti dall'utente per le operazioni eseguite dagli adattatori.
Quando si usano tipi di dati definiti dall'utente, copiare i file (.dll) in nome unità:\Programmi\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ o nella Global Assembly Cache (GAC) del server che ospita il servizio BizTalk Adapter Service. In caso contrario, nel client può verificarsi l'errore seguente:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> Per installare un file nella Global Assembly Cache si consiglia di usare lo strumento GACUtil.exe. In GACUtil.exe è inclusa la documentazione che illustra come usare questo strumento e le opzioni della riga di comando di Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Riavvio del sito Web del servizio BizTalk Adapter Service
L'installazione del **runtime di Servizio Adapter BizTalk*** crea in IIS il sito Web del **Servizio Adapter BizTalk** che contiene l'applicazione **BAService**. L'applicazione **BAService** internamente usa l'associazione di inoltro per estendere l'endpoint di servizio locale al cloud. Per un servizio ospitato localmente, l'endpoint di inoltro corrispondente verrà registrato nel bus di servizio solo dopo l'avvio del servizio locale.  

Se si arresta e si riavvia un'applicazione, la configurazione dell'avvio automatico di un'applicazione non viene rispettata. Di conseguenza, quando l'applicazione **BAService** viene arrestata, è sempre necessario riavviare il sito Web del **Servizio Adapter BizTalk**. Non avviare o arrestare l'applicazione **BAService** .

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Non usare caratteri speciali per i nomi degli indirizzi e delle entità dei componenti LOB
Evitare l'uso dei caratteri speciali per i nomi degli indirizzi e delle entità dei componenti LOB. Se si usano i caratteri speciali, viene visualizzato un messaggio di errore durante la distribuzione del progetto di Servizi BizTalk. Per alcuni caratteri, ad esempio "%", il sito Web del servizio BizTalk Adapter Service può passare allo stato di arresto e potrebbe essere necessario avviarlo manualmente.

### <a name="test-map-with-get-context-property"></a>Mappa di test con Get Context Property
Se una trasformazione contiene un'operazione di mappa **Get Context Property (Ottieni proprietà di contesto)**, l'operazione **Test mappa** non riuscirà. Come soluzione alternativa temporanea, sostituire l'operazione di mappa **Get Context Property** con un'operazione di mappa String Concatenate contenente dati fittizi. In questo modo verranno inseriti automaticamente dati nello schema di destinazione e sarà possibile testare altre funzionalità di trasformazione.

### <a name="test-map-property-does-not-display"></a>La proprietà Mappa di test non viene visualizzata
Le proprietà **Mappa di test** non vengono visualizzate in Visual Studio. Questo errore può verificarsi quando le finestre **Proprietà** ed **Esplora soluzioni** non vengono ancorate contemporaneamente. Per risolvere il problema, ancorare le finestre **Proprietà** ed **Esplora soluzioni**.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>L'elenco a discesa dell'operazione DateTime Reformat non è disponibile
Quando un'operazione di mappa DateTime Reformat viene aggiunta all'area di Creazione progetti e viene configurata, l'elenco a discesa Formato può non essere disponibile. Questa condizione può verificarsi se la risoluzione dello schermo del computer è impostata su **Medio - 125%** o su **Grande - 150%**. Per risolvere il problema, impostare la risoluzione su **Piccolo – 100% (impostazione predefinita)** seguendo questa procedura.  

1. Aprire il **Pannello di controllo** e selezionare **Aspetto e personalizzazione**.
2. Fare clic su **Schermo**.
3. Fare clic su **Piccolo - 100% (impostazione predefinita)** e quindi su **Applica**.

A questo punto l'elenco a discesa **Formato** dovrebbe funzionare normalmente.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Contratti duplicati nel portale di Servizi BizTalk
Si consideri lo scenario seguente:

1. Si crea un accordo con l'API del modello a oggetti Gestione dei partner commerciali.
2. Si apre l'accordo nel portale di Servizi BizTalk in due schede diverse.
3. Si distribuisce l'accordo da entrambe le schede.
4. A questo punto vengono distribuiti entrambi i contratti, con la conseguente generazione di voci duplicate nel portale di Servizi BizTalk

**Soluzione alternativa**. Aprire uno dei contratti duplicati nel portale di Servizi BizTalk e annullarne la distribuzione.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>I bridge non usano i certificati aggiornati nemmeno dopo l'aggiornamento di un certificato nell'archivio elementi
Esaminare gli scenari seguenti:  

**Scenario 1: Uso di certificati basati su identificazione personale per proteggere il trasferimento di messaggi da un bridge a un endpoint di servizio**  
Provare a usare certificati basati su identificazione personale nel progetto di Servizi BizTalk. Si aggiorna il certificato nel portale di Servizi BizTalk usando lo stesso nome e un'identificazione personale diversa, ma non si aggiorna il progetto di Servizi BizTalk di conseguenza. In uno scenario di questo tipo è possibile che il bridge continui a elaborare i messaggi perché nella cache del canale possono ancora essere presenti i dati del certificato precedente. Quando questi dati non sono più presenti, l'elaborazione dei messaggi ha esito negativo.  

**Soluzione alternativa**: aggiornare il certificato nel progetto di Servizi BizTalk e ridistribuire il progetto.  

**Scenario 2: Uso di comportamenti basati su nomi per identificare i certificati allo scopo di proteggere il trasferimento di messaggi da un bridge a un endpoint di servizio**

Provare a usare comportamenti basati su nomi per identificare i certificati nel progetto di Servizi BizTalk. Si aggiorna il certificato nel portale di Servizi BizTalk, ma non si aggiorna il progetto di Servizi BizTalk di conseguenza. In uno scenario di questo tipo è possibile che il bridge continui a elaborare i messaggi perché nella cache del canale possono ancora essere presenti i dati del certificato precedente. Quando questi dati non sono più presenti, l'elaborazione dei messaggi ha esito negativo.  

**Soluzione alternativa**: aggiornare il certificato nel progetto di Servizi BizTalk e ridistribuire il progetto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>I bridge continuano a elaborare i messaggi anche quando il database SQL è offline
I bridge di Servizi BizTalk continuano a elaborare i messaggi per un determinato periodo di tempo anche se il database SQL di Microsoft Azure, che archivia le informazioni di esecuzione quali pipeline ed elementi distribuiti, è offline. Questa condizione si verifica perché Servizi BizTalk usa gli elementi memorizzati nella cache e la configurazione del bridge.
Per evitare che i bridge elaborino i messaggi quando il database SQL è offline, è possibile usare i cmdlet di PowerShell per Servizi BizTalk per arrestare o sospendere il servizio BizTalk. Per informazioni sulla gestione delle operazioni con i cmdlet di Windows PowerShell, vedere un [esempio di gestione del servizio BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=329019) .  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>La lettura di un messaggio XML in un componente del codice personalizzato di un bridge include un carattere BOM aggiuntivo
Si consideri uno scenario in cui si vuole leggere un messaggio XML all'interno del codice personalizzato di un bridge. Se si usa il metodo System.Text.Encoding.UTF8.GetString(bytes) dell'API .NET, all'inizio del messaggio di output viene incluso un carattere BOM aggiuntivo. Se quindi non si vuole che nell'output venga incluso il carattere BOM aggiuntivo, è necessario usare ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>L'invio di messaggi a un bridge con WCF non supporta il ridimensionamento
I messaggi inviati a un bridge con WCF non supportano il ridimensionamento. Se si vuole un client scalabile, è necessario usare HttpWebRequest.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>AGGIORNAMENTO. Errore del provider di token dopo l'aggiornamento dalla versione di anteprima dei servizi BizTalk alla versione di disponibilità generale.
Questa situazione si verifica quando è presente un accordo EDI o AS2 con batch attivi. Quando Servizi BizTalk viene aggiornato dalla versione di anteprima alla versione di disponibilità generale, possono verificarsi le seguenti condizioni:

* Errore: Il provider di token non è riuscito a fornire un token di sicurezza. Messaggio restituito dal provider di token: Impossibile risolvere il nome remoto.
* Le attività batch vengono annullate.

**Soluzione alternativa**: dopo che il servizio BizTalk è stato aggiornato alla versione di disponibilità generale, ridistribuire l'accordo.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>AGGIORNAMENTO. Dopo l'aggiornamento di BizTalk Services SDK, nella casella degli strumenti vengono visualizzate le icone dei bridge di versioni precedenti.
Dopo avere aggiornato una versione precedente di BizTalk Services SDK, in cui i bridge erano rappresentati da icone di vecchio tipo, la casella degli strumenti continua a visualizzare le icone dei bridge di versioni precedenti. Se tuttavia si aggiunge un bridge all'area di Creazione progetti del progetto di Servizi BizTalk, nell'area verrà visualizzata la nuova icona.  

**Soluzione alternativa**. Per risolvere il problema, è possibile eliminare i file con estensione tbd in <system drive>:\Utenti\<utente>\AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>AGGIORNAMENTO. Durante l'aggiornamento del portale BizTalk dalla versione di anteprima alla versione disponibile per tutti potrebbe essere visualizzato un messaggio di errore indicante che la funzionalità EDI non è disponibile
Se si è connessi al portale di Servizi BizTalk mentre si esegue l'aggiornamento di Servizi BizTalk dalla versione di anteprima alla versione disponibile per tutti, nel portale può essere visualizzato il messaggio di errore seguente:  

Questa funzionalità non è disponibile come parte di questa versione di Servizi BizTalk di Microsoft Azure. Per usare queste funzionalità, passare a una versione appropriata.  

**Risoluzione**: disconnettersi dal portale, chiudere e aprire nuovamente il browser e riconnettersi al portale.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>AGGIORNAMENTO. I nuovi dati di rilevamento non vengono visualizzati dopo l'aggiornamento dei servizi BizTalk alla versione disponibile per tutti
Si consideri uno scenario in cui un bridge XML è stato distribuito con la sottoscrizione della versione di anteprima di Servizi BizTalk. Si inviano messaggi al bridge e i dati di rilevamento corrispondenti risultano disponibili nel portale di Servizi BizTalk. Tenere presente che se i bit di runtime del portale di Servizi BizTalk e di Servizi BizTalk vengono aggiornati alla versione disponibile per tutti e si invia un messaggio allo stesso endpoint del bridge distribuito in precedenza, i dati di rilevamento relativi ai messaggi inviati dopo l'aggiornamento non vengono visualizzati.  

### <a name="pipelines-versus-bridges"></a>Pipeline e bridge
In tutto il documento i termini "pipeline" e "bridge" sono stati usati in modo intercambiabile. Il significato dei due termini è pressoché uguale ed esprime un'unità di elaborazione dei messaggi distribuita in Servizi BizTalk.  

### <a name="concepts"></a>Concetti
[Servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   




<!--HONumber=Nov16_HO3-->



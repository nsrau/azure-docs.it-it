---
title: Spostare le app da Servizi BizTalk ad App per la logica di Azure | Microsoft Docs
description: Spostare o eseguire la migrazione di Servizi BizTalk di Microsoft Azure ad App per la logica
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: e58c6950d1d9420f32fc98ca917216dc5fae4fc3
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="move-from-biztalk-services-to-logic-apps"></a>Spostare da Servizi BizTalk ad App per la logica

Servizi BizTalk di Microsoft Azure (MABS) verrà ritirato. Usare questo argomento per spostare le soluzioni di integrazione MABS in App per la logica di Azure. 

## <a name="overview"></a>Panoramica

Servizi BizTalk è costituito da due servizi secondari:

1.    Connessioni ibride di Servizi BizTalk di Microsoft
2.    Integrazione basata su bridge di EAI ed EDI

Se si intende spostare le connessioni ibride, [Connessioni ibride del Servizio app di Azure](../app-service/app-service-hybrid-connections.md) descrive le modifiche e le funzionalità di questo servizio. Connessioni ibride di Azure sostituisce Connessioni ibride di Servizi BizTalk. Connessioni ibride di Azure è disponibile con il Servizio app di Azure e viene offerto nel portale di Azure. Connessioni ibride di Azure offre anche una nuova gestione per la connessione ibrida che consente di gestire le connessioni ibride di Servizi BizTalk esistenti oltre alle nuove connessioni ibride create nel portale. Connessioni ibride di Servizio app di Azure è disponibile a livello generale.

App per la logica sostituisce invece l'integrazione basata su bridge di EAI ed EDI. App per la logica offre le stesse funzionalità di Servizi BizTalk e altro ancora. App per la logica offre funzionalità di orchestrazione e gestione del flusso di lavoro in base al consumo e scalabilità cloud che consentono di creare in modo rapido e facile soluzioni di integrazione complesse tramite un browser oppure usando gli strumenti di Visual Studio.

La tabella seguente contiene una mappatura delle funzionalità di Servizi BizTalk in App per la logica.

| Servizi BizTalk   | App per la logica            | Scopo                  |
| ------------------ | --------------------- | ---------------------------- |
| Connettore          | Connettore             | Invio e ricezione di dati   |
| Bridge             | App per la logica             | Processore di pipeline           |
| Fase di convalida     | Operazione di convalida XML      | Convalidare un documento XML rispetto a uno schema             |
| Fase di miglioramento       | Token dei dati      | Alzare di livello delle proprietà nei messaggi o per decisioni di routing             |
| Fase di trasformazione    | Operazione di trasformazione      | Convertire i messaggi XML da un formato a un altro             |
| Fase di decodifica       | Operazione di decodifica di file flat      | Conversione da file flat in XML             |
| Fase di codifica       |  Operazione di codifica di file flat      | Conversione da XML a file flat             |
| Controllo messaggi       |  Funzioni di Azure o App per le API      | Eseguire il codice personalizzato nelle integrazioni             |
| Azione di routing      |  Condizione o switch      | Indirizzare i messaggi a uno dei connettori specificati             |

Esistono diversi tipi di elementi in Servizi BizTalk.

## <a name="connectors"></a>Connettori
I connettori di Servizi BizTalk consentono ai bridge di inviare e ricevere dati, anche ai bridge bidirezionali che hanno abilitato le interazioni di richiesta/risposta basate su HTTP. In App per la logica, viene usata la stessa terminologia. I connettori delle app per la logica hanno lo stesso scopo e includono oltre 140 connettori capaci di connettersi a un'ampia gamma di tecnologie e servizi, sia locali tramite il gateway di dati locale che sostituisce il servizio Adapter BizTalk usato dai servizi BizTalk, sia servizi PaaS e SaaS cloud quali OneDrive, Office 365, Dynamics CRM e molti altri.

Le origini di Servizi BizTalk sono limitate a sottoscrizioni FTP, SFTP e coda o argomento del bus di servizio.

![](media/logic-apps-move-from-mabs/sources.png)

Ogni bridge dispone di un endpoint HTTP predefinito, che viene configurato con l'indirizzo di runtime e le proprietà dell'indirizzo relativo del bridge. Per ottenere lo stesso risultato con App per la logica, usare le azioni di [richiesta e risposta](../connectors/connectors-native-reqres.md).

## <a name="xml-processing-and-bridges"></a>Bridge ed elaborazione XML
Un bridge in Servizi BizTalk è analogo a una pipeline di elaborazione. Un bridge può elaborare i dati ricevuti da un connettore e inviarli a un altro sistema. App per la logica esegue la stessa operazione supportando gli stessi modelli di interazione basati su pipeline di Servizi BizTalk e offre anche diversi modelli di integrazione. Il [bridge di richiesta-risposta XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) in Servizi BizTalk è noto come pipeline VETER e si compone di varie fasi che possono:

* Convalidare
* Migliorare
* Trasformare
* Migliorare
* Indirizzare

Come illustrato nell'immagine seguente, l'elaborazione si suddivide in richiesta e risposta e consente di controllare i percorsi di richiesta e risposta singolarmente, ad esempio usando mappe diverse per ognuno di essi:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

In aggiunta, un bridge unidirezionale XML aggiunge le fasi di decodifica e codifica all'inizio e alla fine dell'elaborazione e il bridge pass-through contiene un'unica fase di miglioramento.

### <a name="message-processing-and-decodingencoding"></a>Elaborazione e codifica/decodifica del messaggio
In Servizi BizTalk, si ricevono diversi tipi di messaggi XML e si determina lo schema corrispondente al messaggio ricevuto. Questa operazione viene eseguita nella fase **Tipi di messaggio** della pipeline di elaborazione della ricezione. Pertanto, la fase di decodifica usa il tipo di messaggio rilevato per decodificarlo tramite lo schema indicato. Se lo schema è uno schema file flat, il file flat in ingresso viene convertito in XML. 

App per la logica offre funzionalità simili. Si riceve un file flat tramite diversi tipi di protocolli, usando diversi trigger del connettore, ad esempio File System, FTP, HTTP e così via, e si usa l'azione [Flat File Decode](../logic-apps/logic-apps-enterprise-integration-flatfile.md) (Decodifica file flat) per convertire i dati in ingresso in XML. È possibile spostare direttamente gli schemi file flat esistenti nelle app per la logica senza richiedere alcuna modifica e quindi caricarli nell'account di integrazione.

### <a name="validation"></a>Convalida
Dopo aver convertito i dati in ingresso in XML, o se il messaggio ricevuto era in formato XML, la convalida viene eseguita per determinare se il messaggio sia conforme allo schema XSD. A tale scopo in App per la logica usare l'azione [Convalida XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md). Anche in questo caso, è possibile usare gli stessi schemi di Servizi BizTalk senza apportare modifiche.

### <a name="transform-messages"></a>Trasformare i messaggi
In Servizi BizTalk, la fase di trasformazione converte un formato di messaggi basato su XML in un altro. Questa operazione viene eseguita applicando una mappa tramite il mapping basato su TRFM. In App per la logica il processo è simile. L'azione Trasforma esegue una mappa dall'account di integrazione. La differenza principale è che le mappe di App per la logica sono in formato XSLT. Il formato XSLT implica la possibilità di riutilizzare i file XSLT esistenti, incluse le mappe create per BizTalk Server che contengono i functoid. 

### <a name="routing-rules"></a>Regole di routing
Servizi BizTalk prende una decisione di routing relativamente a endpoint/connettori per inviare i messaggi/dati in ingresso. È possibile selezionare uno dei numerosi endpoint preconfigurati usando l'opzione di filtro del routing:

![](media/logic-apps-move-from-mabs/route-filter.png)

App per la logica offre funzionalità di logica più sofisticate grazie alla [condizione](../logic-apps/logic-apps-use-logic-app-features.md) e allo [switch](../logic-apps/logic-apps-switch-case.md), attivando il flusso di controllo e routing avanzato. La conversione dei filtri di routing in Servizi BizTalk si ottiene al meglio usando una **condizione** *se* ci sono solo due opzioni. Se ci sono più di due opzioni, usare uno **switch**.

### <a name="enrich"></a>Miglioramento
La fase di miglioramento nell'elaborazione di Servizi BizTalk aggiunge le proprietà al contesto del messaggio associato ai dati ricevuti. Ad esempio, alzando il livello di una proprietà da usare per il routing da una ricerca nel database, come illustrato di seguito, o estraendo un valore con un'espressione XPath. App per la logica consente di accedere a tutti gli output di dati contestuali dalle azioni precedenti, semplificando la replica dello stesso comportamento. Ad esempio, se si usa l'azione di connessione SQL `Get Row`, vengono restituiti i dati di un database di SQL Server e vengono usati in un'azione di decisione per il routing. Analogamente, è possibile trattare le proprietà nei messaggi in ingresso del bus di servizio messi in coda da un trigger, nonché XPath usando l'espressione del linguaggio di definizione del flusso di lavoro xpath.

### <a name="use-custom-code"></a>Usare il codice personalizzato
Servizi BizTalk offre la possibilità di [eseguire il codice personalizzato](https://msdn.microsoft.com/library/azure/dn232389.aspx) caricato in assembly personalizzati. Ciò viene implementato dall'interfaccia [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx). Ogni fase del bridge include due proprietà, On Enter Inspector e On Exit Inspector, che indicano il tipo .Net creato che implementa questa interfaccia. Il codice personalizzato consente di eseguire un'elaborazione più complessa sui dati, nonché di riutilizzare il codice esistente negli assembly che eseguono la logica di business comune. 

App per la logica offre due metodi principali per eseguire il codice personalizzato: Funzioni di Azure e App per le API. È possibile creare le funzioni di Azure e richiamarle dalle app per la logica. Vedere [Aggiungere ed eseguire un codice personalizzato per le app per la logica di Azure tramite Funzioni di Azure](../logic-apps/logic-apps-azure-functions.md). Usare App per le API, parte del Servizio app di Azure per creare i trigger e le azioni. Altre informazioni sulla [creazione di un'API personalizzata da usare con App per la logica](../logic-apps/logic-apps-create-api-app.md). 

Se si dispone di un codice personalizzato nelle assembly richiamate da Servizi BizTalk, è possibile spostarlo in Funzioni di Azure oppure creare API personalizzate con App per le API, a seconda di ciò che si sta implementando. Ad esempio, se si dispone di un codice che esegue il wrapping di un altro servizio per cui App per la logica non dispone di un connettore, creare un'app per le API e usare le azioni indicate dall'app per le API all'interno dell'app per la logica. Se si dispone di funzioni o raccolte di supporto, Funzioni di Azure è probabilmente la soluzione ottimale.

### <a name="edi-processing-and-trading-partner-management"></a>Gestione del partner commerciale e dell'elaborazione di EDI
Servizi BizTalk include l'elaborazione EDI e B2B con supporto per AS2 (Applicability Statement 2), X12 ed EDIFACT. Lo stesso avviene per App per la logica. In Servizi BizTalk è possibile creare i bridge EDI e creare o gestire i partner commerciali e i contratti nel portale di gestione e rilevamento dedicato.

In App per la logica questa funzionalità è inclusa in [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Si tratta di azioni dell'account di integrazione e B2B per l'elaborazione di EDI e B2B. L'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) viene usato per creare e gestire i [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) e i [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md). Dopo aver creato un account di integrazione, è possibile associare una o più app per la logica all'account. Dopo aver eseguito l'associazione, è possibile usare le azioni B2B per accedere alle informazioni sui partner commerciali all'interno dell'app per la logica. È possibile eseguire le azioni seguenti:

* Codifica AS2
* Decodifica AS2
* Codifica X12
* Decodifica X12
* Codifica EDIFACT
* Decodifica EDIFACT

A differenza di Servizi BizTalk, queste azioni sono separate dai protocolli di trasporto. Pertanto quando si creano le app per la logica la flessibilità di scelta sui connettori da usare per inviare e ricevere dati è maggiore. Ad esempio, è possibile ricevere i file X12 come allegati di posta elettronica ed elaborarli in un'app per la logica. 

## <a name="manage-and-monitor"></a>Gestire e monitorare
Oltre alla gestione di partner commerciali, il portale dedicato per i Servizi BizTalk offre funzionalità di rilevamento per monitorare e risolvere i problemi. 

App per la logica offre funzionalità di monitoraggio e rilevamento più avanzate nel [portale di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) insieme alla [soluzione B2B di Operations Management Suite](../logic-apps/logic-apps-monitor-b2b-message.md), che include un'app mobile per avere un controllo continuo quando si è in viaggio.

## <a name="high-availability"></a>Disponibilità elevata
Per ottenere una disponibilità elevata in Servizi BizTalk, è possibile usare più istanze di una determinata area per condividere il carico di elaborazione. Con le app per la logica, la disponibilità elevata in un'area è incorporata senza alcun costo aggiuntivo. Per il ripristino di emergenza al di fuori dell'area per l'elaborazione B2B in Servizi BizTalk, è necessario un processo di backup e ripristino. App per la logica offre una [capacità DR](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) attiva/passiva per diverse aree, che consente la sincronizzazione dei dati B2B tra account di integrazione in aree diverse al fine di facilitare la continuità aziendale.

## <a name="next"></a>Avanti
* [Informazioni sulle app per la logica](logic-apps-what-are-logic-apps.md)
* [Creare la prima app per la logica](logic-apps-create-a-logic-app.md), oppure iniziare rapidamente usando un [modello predefinito](logic-apps-use-logic-app-templates.md)  
* [Visualizzare tutti i connettori disponibili](../connectors/apis-list.md) che è possibile usare in un'app per la logica


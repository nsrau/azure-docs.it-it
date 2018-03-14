---
title: Spostare le app da Servizi BizTalk ad App per la logica di Azure | Microsoft Docs
description: Eseguire lo spostamento o la migrazione di app da Servizi BizTalk di Azure ad App per la logica di Azure
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
ms.author: jonfan; LADocs
ms.openlocfilehash: 6e00e62e60c059a16731a77e529b4b93f50802e9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="move-from-biztalk-services-to-azure-logic-apps"></a>Eseguire lo spostamento da Servizi BizTalk ad App per la logica di Azure

Servizi BizTalk di Microsoft Azure (MABS) verrà ritirato. Per spostare le soluzioni di integrazione di Servizi BizTalk di Microsoft Azure ad [App per la logica di Azure](../logic-apps/logic-apps-overview.md), seguire le linee guida contenute in questo articolo. 

## <a name="introduction"></a>Introduzione

Servizi BizTalk è costituito da due servizi secondari:

* Connessioni ibride di Servizi BizTalk di Microsoft
* Integrazione basata su bridge di EAI ed EDI

[Connessioni ibride di Servizio app di Azure](../app-service/app-service-hybrid-connections.md) sostituisce Connessioni ibride di Servizi BizTalk. Connessioni ibride di Azure è disponibile con Servizio app di Azure tramite il portale di Azure. Questo servizio offre una funzionalità denominata Gestione connessione ibrida per poter gestire le connessioni ibride di Servizi BizTalk esistenti e anche nuove connessioni ibride create nel portale. 

[App per la logica](../logic-apps/logic-apps-overview.md) sostituisce l'integrazione basata su bridge di EAI ed EDI con tutte le stesse funzionalità di Servizi BizTalk e altre ancora. Questo servizio offre funzionalità di orchestrazione e gestione del flusso di lavoro di livello cloud in base al consumo che permettono di creare in modo semplice e rapido soluzioni di integrazione complesse tramite un browser o Visual Studio.

Questa tabella associa le funzionalità di Servizi BizTalk a quelle di App per la logica.

| Servizi BizTalk   | App per la logica            | Scopo                      |
| ------------------ | --------------------- | ---------------------------- |
| Connettore          | Connettore             | Inviare e ricevere dati   |
| Bridge             | App per la logica             | Processore di pipeline           |
| Fase di convalida     | Operazione di convalida XML | Convalidare un documento XML rispetto a uno schema | 
| Fase di miglioramento       | Token dei dati           | Alzare di livello delle proprietà nei messaggi o per decisioni di routing |
| Fase di trasformazione    | Operazione di trasformazione      | Convertire i messaggi XML da un formato a un altro |
| Fase di decodifica       | Operazione di decodifica di file flat | Conversione da file flat in XML |
| Fase di codifica       | Operazione di codifica di file flat | Conversione da XML a file flat |
| Controllo messaggi  | Funzioni di Azure o App per le API | Eseguire il codice personalizzato nelle integrazioni |
| Azione di routing       | Condizione o switch | Indirizzare i messaggi a uno dei connettori specificati |
|||| 

## <a name="biztalk-services-artifacts"></a>Elementi di Servizi BizTalk

Servizi BizTalk include diversi tipi di elementi. 

## <a name="connectors"></a>Connettori

I connettori di Servizi BizTalk permettono ai bridge di inviare e ricevere dati, inclusi i bridge bidirezionali che consentono interazioni di richiesta/risposta basate su HTTP. App per la logica usa la stessa terminologia e include oltre 180 connettori che assolvono allo stesso scopo connettendosi a un'ampia gamma di tecnologie e servizi. Ad esempio, sono disponibili connettori per servizi SaaS e PaaS cloud, come OneDrive, Office 365, Dynamics CRM e altri ancora, nonché sistemi locali tramite il gateway dati locale, che sostituisce il servizio Adapter BizTalk per Servizi BizTalk. Le origini di Servizi BizTalk sono limitate a sottoscrizioni FTP, SFTP e coda o argomento del bus di servizio.

![](media/logic-apps-move-from-mabs/sources.png)

Per impostazione predefinita, ogni bridge ha un endpoint HTTP, configurato con le proprietà per l'indirizzo di runtime e per l'indirizzo relativo per il bridge. Per ottenere gli stessi risultati con App per la logica, usare le azioni di [richiesta e risposta](../connectors/connectors-native-reqres.md).

## <a name="xml-processing-and-bridges"></a>Bridge ed elaborazione XML

In Servizi BizTalk un bridge è analogo a una pipeline di elaborazione. Un bridge può elaborare i dati ricevuti da un connettore, modificarli e quindi inviare i risultati a un altro sistema. App per la logica esegue la stessa operazione supportando gli stessi modelli di interazione basati su pipeline di Servizi BizTalk e offrendo anche altri modelli di integrazione. Il [bridge di richiesta-risposta XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) in Servizi BizTalk è noto come pipeline VETER, costituita da varie fasi che eseguono queste attività:

* Convalidare
* Migliorare
* Trasformare
* Migliorare
* Indirizzare

Questa immagine mostra in che modo l'elaborazione è divisa tra richiesta e risposta, fornendo il controllo sui percorsi di richiesta e risposta separatamente, ad esempio, usando mappe diverse per ciascun percorso:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Inoltre, un bridge unidirezionale XML aggiunge fasi di decodifica e codifica all'inizio e alla fine dell'elaborazione. Il bridge pass-through include una singola fase di miglioramento.

### <a name="message-processing-decoding-and-encoding"></a>Elaborazione, decodifica e codifica di messaggi

In Servizi BizTalk è possibile ricevere diversi tipi di messaggi XML e determinare lo schema corrispondente per il messaggio ricevuto. Questa operazione viene eseguita nella fase *Tipi di messaggio* della pipeline di elaborazione della ricezione. La fase di decodifica usa quindi il tipo di messaggio rilevato per decodificare il messaggio tramite lo schema specificato. Se lo schema è uno schema file flat, questa fase converte il file flat in ingresso in XML. 

App per la logica offre funzionalità simili. Un file flat viene ricevuto tramite diversi protocolli, usando trigger di connettori diversi (File System, FTP, HTTP e così via) ed è possibile usare l'azione di [decodifica dei file flat](../logic-apps/logic-apps-enterprise-integration-flatfile.md) per convertire i dati in ingresso in XML. È possibile spostare gli schemi file flat esistenti direttamente in App per la logica senza alcuna modifica e quindi caricarli nell'account di integrazione.

### <a name="validation"></a>Convalida

Dopo aver convertito i dati in ingresso in XML, o se il messaggio ricevuto era in formato XML, la convalida viene eseguita per determinare se il messaggio sia conforme allo schema XSD. Per eseguire questa attività in App per la logica, usare l'azione [Convalida XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md). È possibile usare gli stessi schemi di Servizi BizTalk senza alcuna modifica.

### <a name="transform-messages"></a>Trasformare i messaggi

In Servizi BizTalk, la fase di trasformazione converte un formato di messaggi basato su XML in un altro. Questa operazione viene eseguita applicando una mappa tramite il mapping basato su TRFM. In App per la logica il processo è simile. L'azione Trasforma esegue una mappa dall'account di integrazione. La differenza principale è che le mappe di App per la logica sono in formato XSLT. Il formato XSLT implica la possibilità di riutilizzare i file XSLT esistenti, incluse le mappe create per BizTalk Server che contengono i functoid. 

### <a name="routing-rules"></a>Regole di routing

Servizi BizTalk effettua una scelta di routing relativamente all'endpoint o al connettore per l'invio di messaggi o dati in ingresso. La possibilità di scegliere da endpoint preconfigurati viene garantita tramite l'opzione del filtro di routing:

![](media/logic-apps-move-from-mabs/route-filter.png)

In Servizi BizTalk, se sono disponibili solo due opzioni, l'uso di una *condizione* è il modo migliore per convertire filtri di routing in Servizi BizTalk stesso. Se ci sono più di due opzioni, usare uno **switch**.

App per la logica offre funzionalità di logica più sofisticate, un flusso di controllo avanzato e il routing con [istruzioni condizionali](../logic-apps/logic-apps-control-flow-conditional-statement.md) e [istruzioni switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Miglioramento

Nell'elaborazione di Servizi BizTalk la fase di miglioramento aggiunge proprietà al contesto dei messaggi associato ai dati ricevuti, ad esempio alzando di livello una proprietà da usare per il routing da una ricerca nel database o estraendo un valore tramite un'espressione XPath. App per la logica consente di accedere a tutti gli output di dati contestuali dalle azioni precedenti, semplificando la replica dello stesso comportamento. Ad esempio, se si usa l'azione di connessione SQL `Get Row`, vengono restituiti i dati di un database di SQL Server e vengono usati in un'azione di decisione per il routing. Analogamente, è possibile trattare le proprietà nei messaggi in ingresso del bus di servizio messi in coda da un trigger, nonché XPath usando l'espressione del linguaggio di definizione del flusso di lavoro xpath.

### <a name="run-custom-code"></a>Eseguire codice personalizzato

Servizi BizTalk permette di [eseguire codice personalizzato](https://msdn.microsoft.com/library/azure/dn232389.aspx) caricato negli assembly personali. Questa funzionalità viene implementata dall'interfaccia [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector). Ogni fase nel bridge include due proprietà, On Enter Inspector e On Exit Inspector, che indicano il tipo .NET creato che implementa questa interfaccia. Il codice personalizzato permette di eseguire un'elaborazione più complessa sui dati e di riutilizzare il codice esistente negli assembly che eseguono la logica di business comune. 

App per la logica offre due metodi principali per eseguire il codice personalizzato: Funzioni di Azure e App per le API. È possibile creare le funzioni di Azure e richiamarle dalle app per la logica. Vedere [Aggiungere ed eseguire un codice personalizzato per le app per la logica di Azure tramite Funzioni di Azure](../logic-apps/logic-apps-azure-functions.md). Usare App per le API, parte del Servizio app di Azure per creare i trigger e le azioni. Per altre informazioni, vedere [Creazione di un'API personalizzata da usare con App per la logica](../logic-apps/logic-apps-create-api-app.md). 

In presenza di codice personalizzato negli assembly che viene chiamato da Servizi BizTalk, è possibile spostarlo in Funzioni di Azure oppure creare API personalizzate con App per le API, a seconda dell'implementazione. Ad esempio, se è presente codice che esegue il wrapping di un altro servizio per cui App per la logica non include un connettore, creare un'app per le API e usare le azioni indicate dall'app per le API all'interno dell'app per la logica. Se si dispone di funzioni o raccolte di supporto, Funzioni di Azure è probabilmente la soluzione ottimale.

### <a name="edi-processing-and-trading-partner-management"></a>Gestione del partner commerciale e dell'elaborazione di EDI

Servizi BizTalk e App per la logica includono l'elaborazione EDI e B2B con supporto per AS2 (Applicability Statement 2), X12 ed EDIFACT. In Servizi BizTalk è possibile creare bridge EDI e creare o gestire i partner commerciali e i contratti nel portale di gestione e rilevamento dedicato.
In App per la logica questa funzionalità è inclusa in [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP offre un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e azioni B2B per l'elaborazione EDI e B2B. È possibile usare l'account di integrazione anche per creare e gestire [partner commerciali](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md). Dopo aver creato un account di integrazione, è possibile collegare una o più app per la logica all'account. È quindi possibile usare azioni B2B per accedere alle informazioni sui partner commerciali dall'app per la logica. È possibile eseguire le azioni seguenti:

* Codifica AS2
* Decodifica AS2
* Codifica X12
* Decodifica X12
* Codifica EDIFACT
* Decodifica EDIFACT

A differenza di Servizi BizTalk, queste azioni sono separate dai protocolli di trasporto. Di conseguenza, quando si creano le app per la logica, si ottiene maggiore flessibilità riguardo ai connettori da usare per inviare e ricevere dati. Ad esempio, è possibile ricevere file X12 come allegati di posta elettronica e quindi elaborarli in un'app per la logica. 

## <a name="manage-and-monitor"></a>Gestire e monitorare

In Servizi BizTalk un portale dedicato offre funzionalità di rilevamento per monitorare e risolvere i problemi. App per la logica offre funzionalità di monitoraggio e rilevamento più avanzate tramite il [portale di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md), insieme alla [soluzione B2B di Operations Management Suite](../logic-apps/logic-apps-monitor-b2b-message.md), che include un'app per dispositivi mobili per tenere sotto controllo tutti gli aspetti quando si è in viaggio.

## <a name="high-availability"></a>Disponibilità elevata

Per ottenere disponibilità elevata in Servizi BizTalk, è possibile condividere il carico di elaborazione usando più istanze in un'area specifica. App per la logica offre disponibilità elevata in una stessa area senza costi aggiuntivi. 

In Servizi BizTalk il ripristino di emergenza al di fuori dell'area per l'elaborazione B2B richiede un processo di backup e ripristino. Per motivi di continuità aziendale, App per la logica offre una [funzionalità di ripristino di emergenza](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) attivo/passivo tra aree diverse, che permette di sincronizzare i dati B2B tra account di integrazione in aree diverse.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)
* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md), oppure iniziare rapidamente usando un [modello predefinito](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Visualizzare tutti i connettori disponibili](../connectors/apis-list.md) che è possibile usare in app per la logica
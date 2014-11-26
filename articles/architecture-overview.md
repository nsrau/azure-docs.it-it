<properties linkid="develop-net-architecture sublanding" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="robb" solutions="" manager="johndaw" editor="mattshel" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb" />

# Architettura

In questo articolo viene descritto come implementare modelli di progettazione comuni in Azure.

### Set di simboli e icone di Azure

[Scaricare il set di simboli e icone di Azure][Scaricare il set di simboli e icone di Azure] per creare materiali tecnici che descrivono o usano Azure, ad esempio diagrammi di architettura, materiale didattico, presentazioni, fogli dati, infografica e white paper. È possibile scaricare i simboli nei formati PPT, Visio o PNG. Il parare degli utenti è importante per Microsoft. Per questo motivo, il download contiene istruzioni per l'invio di commenti e suggerimenti.

![Set di simboli e icone di Azure][Set di simboli e icone di Azure]

## Modelli di progettazione

### [Consumer concorrenti][Consumer concorrenti]

![Consumer concorrenti][1]

Consente di abilitare l'elaborazione dei messaggi ricevuti sullo stesso canale di messaggistica da parte di più consumer concorrenti. Questo modello consente a un sistema di elaborare più messaggi contemporaneamente per ottimizzare la velocità effettiva, migliorare la scalabilità e la disponibilità e per bilanciare il carico di lavoro.

### [Segregazione delle responsabilità di query e comandi][Segregazione delle responsabilità di query e comandi]

![Segregazione delle responsabilità di query e comandi][2]

Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate. Questo modello può di massimizzare le prestazioni, la scalabilità e la sicurezza, supportare l'evoluzione del sistema nel tempo grazie al miglioramento della flessibilità e impedire che i comandi di aggiornamento provochino conflitti di unione a livello di dominio.

### [Elezione del leader][Elezione del leader]

![Elezione del leader][3]

Consente di coordinare le azioni eseguite da un insieme di istanze di attività collaborative in un'applicazione distribuita attraverso l'elezione di una singola istanza come leader, che si assume la responsabilità di gestire le altre istanze. Questo modello contribuisce a garantire che le istanze di attività non entrino in conflitto, causando contese per le risorse condivise, né interferiscano accidentalmente con le operazioni eseguite dalle altre istanze di attività.

### [Pipe e filtri][Pipe e filtri]

![Pipe e filtri][4]

Consente di decomporre un'attività che esegue elaborazioni complesse in una serie di elementi separati che possono essere riutilizzati. Questo modello può migliorare le prestazioni, la scalabilità e la riusabilità, consentendo di distribuire e gestire la scalabilità degli elementi attività che eseguono l'elaborazione in modo indipendente.

### [Passepartout][Passepartout]

![Passepartout][5]

Consente di utilizzare un token o una chiave che fornisce ai client accesso diretto limitato a un servizio o a una risorsa specifica, per l'offload delle operazioni di trasferimento di dati dal codice dell'applicazione. Questo modello è particolarmente utile nelle applicazioni che utilizzano code o sistemi di archiviazione o ospitati nel cloud o code e contribuisce a minimizzare i costi e ottimizzare le prestazioni e la scalabilità.

### Indicazioni aggiuntive

Per informazioni sui modelli di progettazione più comuni in Azure, vedere l'argomento relativo ai [modelli di progettazione per il cloud][modelli di progettazione per il cloud].

  [Scaricare il set di simboli e icone di Azure]: http://www.microsoft.com/it-it/download/details.aspx?id=41937
  [Set di simboli e icone di Azure]: ./media/architecture-overview/AzureSymbols.png
  [Consumer concorrenti]: http://msdn.microsoft.com/it-it/library/dn568101.aspx
  [1]: ./media/architecture-overview/CompetingConsumers.png
  [Segregazione delle responsabilità di query e comandi]: http://msdn.microsoft.com/it-it/library/dn568103.aspx
  [2]: ./media/architecture-overview/CQRS.png
  [Elezione del leader]: http://msdn.microsoft.com/it-it/library/dn568104.aspx
  [3]: ./media/architecture-overview/LeaderElection.png
  [Pipe e filtri]: http://msdn.microsoft.com/it-it/library/dn568100.aspx
  [4]: ./media/architecture-overview/PipesAndFilters.png
  [Passepartout]: http://msdn.microsoft.com/it-it/library/dn568102.aspx
  [5]: ./media/architecture-overview/ValetKey.png
  [modelli di progettazione per il cloud]: http://msdn.microsoft.com/it-it/library/dn568099.aspx

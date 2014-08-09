<properties  linkid="" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="waltpo" solutions="" manager="bjsmith" editor="mattshel" />

# Architettura

In questo articolo viene descritto come implementare modelli di progettazione comuni in Azure.

### Set di simboli e icone di Azure

[Scaricare il set di simboli e icone di Azure][1] per creare materiali
tecnici che descrivono o utilizzano elementi di Azure, ad esempio diagrammi di architetture, materiale didattico, presentazioni, fogli dati, infografiche e white paper. È possibile scaricare i simboli nei formati PPT, Visio o PNG. Il parere degli utenti è importante per Microsoft. Per questo motivo, il download contiene istruzioni per l'invio di commenti e suggerimenti.

![Set di simboli e icone di
Azure](./media/architecture-overview/AzureSymbols.png)

## Modelli di progettazione

### [Consumer concorrenti][2]

![Consumer
concorrenti](./media/architecture-overview/CompetingConsumers.png)

Consente di abilitare l'elaborazione dei messaggi ricevuti sullo stesso canale di messaggistica da parte di più consumer concorrenti. Questo modello consente a un sistema di elaborare più messaggi contemporaneamente per ottimizzare la velocità effettiva, migliorare la scalabilità e la disponibilità e per bilanciare il carico di lavoro.

### [Segregazione delle responsabilità di query e comandi][3]

![Segregazione delle responsabilità di query e
comandi](./media/architecture-overview/CQRS.png)

Consente di segregare le operazioni di lettura dei dati dalle operazioni di aggiornamento dei dati attraverso l'utilizzo di interfacce separate. Questo modello può di massimizzare le prestazioni, la scalabilità e la sicurezza, supportare l'evoluzione del sistema nel tempo grazie al miglioramento della flessibilità e impedire che i comandi di aggiornamento provochino conflitti di unione a livello di dominio.

### [Elezione del leader][4]

![Elezione del leader](./media/architecture-overview/LeaderElection.png)

Consente di coordinare le azioni eseguite da un insieme di istanze di attività collaborative in un'applicazione distribuita attraverso l'elezione di una singola istanza come leader, che si assume la responsabilità di gestire le altre istanze. Questo modello contribuisce a garantire che le istanze di attività non entrino in conflitto, causando contese per le risorse condivise, né interferiscano accidentalmente con le operazioni eseguite dalle altre istanze di attività.

### [Pipe e filtri][5]

![Pipe e filtri](./media/architecture-overview/PipesAndFilters.png)

Consente di decomporre un'attività che esegue elaborazioni complesse in una serie di elementi separati che possono essere riutilizzati. Questo modello può migliorare le prestazioni, la scalabilità e la riusabilità, consentendo di distribuire e gestire la scalabilità degli elementi attività che eseguono l'elaborazione in modo indipendente.

### [Passepartout][6]

![Passepartout](./media/architecture-overview/ValetKey.png)

Consente di utilizzare un token o una chiave che fornisce ai client accesso diretto limitato a un servizio o a una risorsa specifica, per l'offload delle operazioni di trasferimento di dati dal codice dell'applicazione. Questo modello è particolarmente utile nelle applicazioni che utilizzano code o sistemi di archiviazione o ospitati nel cloud o code e contribuisce a minimizzare i costi e ottimizzare le prestazioni e la scalabilità.

### Indicazioni aggiuntive

Per informazioni sui modelli di progettazione più comuni in Azure, vedere l'argomento relativo ai [modelli di progettazione per il cloud][7].



[1]: http://www.microsoft.com/en-us/download/details.aspx?id=41937
[2]: http://msdn.microsoft.com/it-it/library/dn568101.aspx
[3]: http://msdn.microsoft.com/it-it/library/dn568103.aspx
[4]: http://msdn.microsoft.com/it-it/library/dn568104.aspx
[5]: http://msdn.microsoft.com/it-it/library/dn568100.aspx
[6]: http://msdn.microsoft.com/it-it/library/dn568102.aspx
[7]: http://msdn.microsoft.com/it-it/library/dn568099.aspx
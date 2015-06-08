<properties 	
	pageTitle="Factory di dati di Azure - esempi" 
	description="Fornisce informazioni dettagliate sugli esempi forniti con il servizio di Azure dati Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Factory di dati di Azure - esempi

## Negli esempi di portale di Azure
È possibile rapidamente distribuire, esaminare e testare un esempio di Factory di dati di Azure utilizzando il **pipeline di esempio** blade nel portale di Azure.

1. Creare una nuova factory di dati o aprire una factory di dati esistente. Vedere [Introduzione a Azure dati Factory][data-factory-get-started] per i passaggi per creare una factory di dati.
2. Nel **dati FACTORY** blade per la factory di dati, fare clic sui **pipeline di esempio** riquadro.

	![Tessera pipeline di esempio](./media/data-factory-samples/SamplePipelinesTile.png)

2. Nel **pipeline di esempio** blade, scegliere il **esempio** che si desidera distribuire.
	
	![Blade pipeline di esempio](./media/data-factory-samples/SampleTile.png)

3. Specificare le impostazioni di configurazione per l'esempio. Ad esempio, la chiave account e il nome dell'account di archiviazione Azure, nome del server SQL Azure, database, l'ID utente e password e così via...

	![Blade di esempio](./media/data-factory-samples/SampleBlade.png)

4. Dopo aver con le impostazioni di configurazione che specifica, fare clic su **Crea** per creare o distribuire la pipeline di esempio e servizi/tabelle collegate utilizzate dalle pipeline di.
5. Verrà visualizzato lo stato di distribuzione nella sezione esempio è stato scelto in precedenza il **pipeline di esempio** blade.

	![Stato di distribuzione](./media/data-factory-samples/DeploymentStatus.png)

6. Quando viene visualizzato il **distribuzione completata** messaggio sull'icona per l'esempio, chiudere il **pipeline di esempio** blade.
5. In **dati FACTORY** blade, vedrete che servizi collegati, set di dati e pipeline vengono aggiunti a una factory di dati.  

	![Blade Factory di dati](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

Nella tabella seguente vengono brevemente gli esempi disponibili nel **pipeline di esempio** riquadro.

Nome di esempio | description
----------- | -----------
Profilo clienti di gioco | Contoso è una società di giochi che crea i giochi per più piattaforme: gioco console, dispositivi portatile e personal computer (computer). Ognuno di questi giochi genera grandi quantità di log. Obiettivo di Contoso consiste nel raccogliere e analizzare i registri generati da questi giochi per ottenere informazioni sull'utilizzo, identificare le opportunità offerte speciali e cross-selling, lo sviluppo di nuove funzionalità interessanti e così via... per migliorare aziendali e offrire una migliore esperienza ai clienti. In questo esempio consente di raccogliere i log di esempio, processi e arricchisce li con dati di riferimento e trasforma i dati per valutare l'efficacia di Contoso ha recentemente lanciato una campagna di marketing.
 
## Esempi su GitHub
Il [archivio GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contiene molti esempi che consentono di rapidamente salita con servizio Factory di dati di Azure (o) modificare gli script e utilizzarlo nella propria applicazione. La cartella Samples\JSON contiene frammenti di JSON per scenari comuni.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir-->
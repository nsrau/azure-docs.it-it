<properties 	
	pageTitle="Data factory di Azure - Esempi" 
	description="Fornisce informazioni dettagliate sugli esempi inclusi nel servizio Data factory di Azure." 
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

# Data factory di Azure - Esempi

## Esempi nel portale di Azure
È possibile distribuire, esaminare e testare rapidamente un esempio di Data factory di Azure usando il pannello **Pipeline di esempio** nel portale di Azure.

1. Creare una nuova data factory o aprire una data factory esistente. Per informazioni su come creare una data factory, vedere [Introduzione a Data factory di Azure][data-factory-get-started].
2. Nel pannello **DATA FACTORY** per la data factory, fare clic sul riquadro **Pipeline di esempio**.

	![Riquadro Pipeline di esempio](./media/data-factory-samples/SamplePipelinesTile.png)

2. Nel pannello **Pipeline di esempio**, fare clic sull'**esempio** da distribuire.
	
	![Pannello Pipeline di esempio](./media/data-factory-samples/SampleTile.png)

3. Specificare le impostazioni di configurazione per l'esempio, ad esempio il nome dell'account di archiviazione di Azure e la chiave dell'account, il nome del server di Azure SQL, il database, l'ID utente, la password e così via.

	![Pannello Esempio](./media/data-factory-samples/SampleBlade.png)

4. Dopo avere specificato le impostazioni di configurazione, fare clic su **Crea** per creare/distribuire le pipeline di esempio e i servizi collegati o le tabelle usati dalle pipeline.
5. Lo stato della distribuzione sarà visualizzato sul riquadro dell'esempio selezionato in precedenza nel pannello **Pipeline di esempio**.

	![Stato della distribuzione](./media/data-factory-samples/DeploymentStatus.png)

6. Quando viene visualizzato il messaggio **Distribuzione riuscita** nel riquadro relativo all'esempio, chiudere il pannello **Pipeline di esempio**.
5. Nel pannello **DATA FACTORY** si potrà notare che i servizi collegati, i set di dati e le pipeline vengono aggiunti alla data factory.  

	![Pannello Data factory](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

La tabella seguente fornisce una breve descrizione degli esempi disponibili nel riquadro **Pipeline di esempio**.

Nome esempio | description
----------- | -----------
Profilatura dei clienti dei giochi | Contoso è una società di giochi online che crea giochi per più piattaforme: console di gioco, dispositivi palmari e personal computer (PC). Ognuno di questi giochi genera grandi quantità di log. L'obiettivo di Contoso è raccogliere e analizzare i log generati da questi giochi per ottenere informazioni sull'utilizzo, identificare opportunità di upselling e cross-selling, sviluppare nuove accattivanti funzionalità e così via, per migliorare il business e offrire una migliore esperienza ai clienti. In questo esempio, si raccoglieranno log di esempio, li si elaborerà e arricchirà con dati di riferimento e si trasformeranno i dati per valutare l'efficacia di una campagna di marketing lanciata di recente da Contoso.
 
## Esempi in GitHub
Il [repository Azure-DataFactory di GitHub](https://github.com/azure/azure-datafactory) include alcuni esempi che permettono di acquisire rapidamente familiarità con il servizio Data factory di Azure (o) modificare gli script e usarlo nella propria applicazione. La cartella Samples\\JSON include frammenti di codice JSON per scenari comuni.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=July15_HO3-->
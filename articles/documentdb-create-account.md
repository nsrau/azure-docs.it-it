<properties 
	pageTitle="Creazione di un account di database | Azure" 
	description="Informazioni su come creare un account del database di documenti DocumentDB e scegliere le impostazioni dell'account nel portale di anteprima di Azure." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2014" 
	ms.author="mimig"/>

#Creazione di un account di database
Per usare Microsoft Azure DocumentDB, è necessario creare un account DocumentDB.  Questo argomento descrive come creare un account DocumentDB nel portale di gestione di anteprima di Azure.  


1.	Accedere al [portale di gestione di anteprima](https://portal.azure.com/#gallery/Microsoft.DocumentDB).
2.	Fare clic su Nuovo -> Account DocumentDB.  
	![][1]  

	In alternativa, dalla Schermata iniziale accedere ad Azure Marketplace, selezionare la categoria "Raccolta e analisi dei dati", scegliere **DocumentDB** e quindi fare clic su **Crea**.  

	![][2]   

3. Nel pannello **Nuovo DocumentDB (anteprima)** specificare la configurazione desiderata per l'account DocumentDB. 
 
	![][3] 


	- Nella casella **ID** immettere un nome per identificare l'account DocumentDB. Questo valore diventa il nome host nell'URI. L'ID può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Tenere presente che al nome di endpoint scelto viene aggiunto documents.azure.com e il risultato finale sarà l'endpoint dell'account DocumentDB.

	- La sezione **Livello di prezzo** è bloccata, perché l'anteprima di DocumentDB supporta un unico livello di prezzo standard. Per altre informazioni, vedere [DocumentDB - Prezzi](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- La sezione **Configurazione facoltativa** consente di specificare la capacità iniziale allocata all'account DocumentDB.  DocumentDB viene offerto in unità di capacità, che consentono di scalare l'account DocumentDB in base alle esigenze. Ogni unità di capacità include velocità effettiva e spazio di archiviazione di database riservato.  Per impostazione predefinita, viene effettuato il provisioning di 1 unità di capacità.  È possibile modificare il numero di unità di capacità disponibili per l'account DocumentDB in qualsiasi momento mediante il [portale di gestione di anteprima](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Per informazioni sulla capacità e sulla velocità effettiva dell'account DocumentDB, vedere l'articolo [Gestire la capacità e le prestazioni di DocumentDB][documentdb-manage].

	- In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account DocumentDB.  Per impostazione predefinita, verrà creato un nuovo gruppo di risorse.  È tuttavia possibile selezionare un gruppo di risorse esistente al quale aggiungere l'account DocumentDB. Per altre informazioni, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/).

	- Per **Sottoscrizione** selezionare la sottoscrizione di Azure che si desidera usare per l'account DocumentDB. Se l'account ha una sola sottoscrizione, verrà selezionato automaticamente.*
 
	- Usare **Posizione** per specificare la posizione geografica in cui verrà ospitato l'account DocumentDB.   

3.	Dopo aver configurato le opzioni del nuovo account DocumentDB, fare clic su **Crea**.  La creazione dell'account DocumentDB può richiedere alcuni minuti.  Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale  
	![][4]  
  
	o dall'Hub di notifica.  

	![][5]  

	![][6]

4.	Dopo la creazione, l'account DocumentDB è pronto all'uso con le impostazioni predefinite.

	*Tenere presente che la coerenza predefinita dell'account DocumentDB sarà impostata su Sessione.  È possibile modificare le impostazioni di coerenza predefinite tramite il [portale di gestione di anteprima](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![][7]  

5.	È inoltre possibile accedere agli account DocumentDB esistenti dal pannello **Sfoglia**.  
	![][8]

##<a id="NextSteps"></a>Passaggi successivi
Per iniziare a usare Azure DocumentDB, vedere le risorse seguenti:

-	[Modello di risorse e concetti relativi a DocumentDB](/documentation/articles/documentdb-resources/)
-	[Interagire con risorse di DocumentDB](/documentation/articles/documentdb-interactions-with-resources/)
-	[Come creare un account DocumentDB](/documentation/articles/documentdb-create-account/)
-	[Introduzione a un account DocumentDB](/documentation/articles/documentdb-get-started/)

Per altre informazioni su DocumentDB, vedere la documentazione relativa ad Azure DocumentDB in [azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319)

[Procedura: Creare un account DocumentDB]: #Howto
[Passaggi successivi]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->

<!--HONumber=46--> 

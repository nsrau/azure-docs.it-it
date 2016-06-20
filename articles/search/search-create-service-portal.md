<properties
	pageTitle="Creare il servizio di Ricerca di Azure usando il portale di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Informazioni sul provisioning del servizio di Ricerca di Azure nel portale di Azure."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="06/06/2016"
	ms.author="ashmaka"/>

# Creare un servizio di Ricerca di Azure nel portale di Azure

Questa guida illustra il processo di creazione (o provisioning) di un servizio di Ricerca di Azure mediante il [portale di Azure](https://portal.azure.com/).

La guida presuppone che sia già stata eseguita la sottoscrizione di Azure e si possa accedere al portale di Azure.

## Individuare la Ricerca di Azure nel portale di Azure
1. Aprire il [portale di Azure](https://portal.azure.com/) ed eseguire l’accesso.
1. Fare clic sul segno più ("+") nell'angolo superiore sinistro.
2. Selezionare "Dati e archiviazione".
3. Selezionare "Ricerca di Azure".

![](./media/search-create-service-portal/find-search.png)

## Scegliere un nome e un endpoint dell'URL per il servizio
1. Il nome del servizio farà parte dell'URL dell'endpoint di servizio di Ricerca di Azure in cui si eseguiranno le chiamate API per gestire e utilizzare il servizio di ricerca.
2. Digitare il nome del servizio nel campo "URL". Il nome del servizio:
  * deve contenere solo lettere minuscole, cifre o trattini ("-")
  * non può contenere un trattino ("-") nei primi 2 caratteri o nell'ultimo carattere
  * non può contenere trattini consecutivi ("--")
  * è di lunghezza compresa tra 2 e 60 caratteri


## Selezionare una sottoscrizione in cui mantenere il servizio
Se è disponibile più di una sottoscrizione, è possibile selezionare quale sottoscrizione debba includere il servizio di Ricerca di Azure.

## Selezionare un gruppo di risorse per il servizio
Creare un nuovo gruppo di risorse o selezionarne uno esistente. Un gruppo di risorse è una raccolta di servizi e risorse di Azure utilizzate insieme. Se ad esempio si utilizza la Ricerca di Azure per l'indicizzazione di un database SQL, entrambi i servizi devono essere parte dello stesso gruppo di risorse.

## Selezionare il percorso in cui verrà ospitato il servizio
Ricerca di Azure, in qualità di servizio di Azure, può essere ospitato nei data center di tutto il mondo. Tenere presente che i [prezzi possono variare](https://azure.microsoft.com/pricing/details/search/) in funzione dell'area geografica.

## Selezionare il piano tariffario
[Ricerca di Azure attualmente è disponibile con vari piani tariffari](https://azure.microsoft.com/pricing/details/search/): Gratuito, Basic o Standard. Ogni piano tariffario prevede una specifica [capacità e limiti](search-limits-quotas-capacity.md).

In questo caso per il servizio è stato scelto il piano tariffario Standard.

## Selezionare il pulsante "Crea" per il provisioning del servizio

![](./media/search-create-service-portal/create-service.png)

## Ridimensionare il servizio

Al termine del provisioning del servizio è possibile ridimensionare il servizio per adattarlo alle proprie esigenze. Se è stato scelto il piano tariffario Standard per il servizio di Ricerca di Azure, è possibile ridimensionare il servizio in due dimensioni: partizioni e repliche. Se è stato scelto il piano Basic, è possibile aggiungere solo le repliche.

Le *__partizioni__* consentono di archiviare e di eseguire ricerche in un numero maggiore di documenti nel servizio.

Le *__repliche__* consentono al servizio di gestire un carico superiore di query di ricerca. [Un servizio richiede 2 repliche per ottenere un contratto di servizio di sola lettura e 3 repliche per ottenere una contratto di servizio di lettura/scrittura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Passare al pannello di gestione del servizio Ricerca di Azure nel portale di Azure.
2. Nel pannello "Impostazioni", selezionare "Ridimensiona".
3. È possibile ridimensionare il servizio mediante l'aggiunta di repliche o partizioni.
  * Non è possibile ridimensionare il servizio per oltre 36 unità di ricerca. Il numero totale di unità di ricerca è il prodotto delle partizioni e repliche (repliche * partizioni = unità di ricerca totali).
  * Se è stato scelto il piano Basic, è possibile ridimensionare soltanto 3 repliche. I servizi Basic sono associati a un'unica partizione.

![](./media/search-create-service-portal/scale-service.png)

## Avanti
Dopo il provisioning di un servizio di Ricerca di Azure, è possibile [definire un indice di Ricerca di Azure](search-what-is-an-index.md) per caricare e cercare i dati.

<!---HONumber=AcomDC_0608_2016-->
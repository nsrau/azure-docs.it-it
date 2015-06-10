
<properties 
	pageTitle="Personalizzare gli identificatori di operazione generati da Swashbuckle" 
	description="Informazioni su come personalizzare gli identificatori di operazione Swagger generati da Swashbuckle per un'app per le API nel servizio app di Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2015" 
	ms.author="bradyg"/>

# Personalizzare gli identificatori di operazione generati da Swashbuckle 

## Panoramica

Swashbuckle genera identificatori di operazione Swagger concatenando il nome del controller e il nome del metodo. Questo modello provoca problemi quando sono presenti più overload di un metodo: Swashbuckle genera ID operazione duplicati, ovvero JSON Swagger non valido.

Ad esempio, il codice seguente del controller provoca la generazione di tre ID operazione Contact_Get da parte di Swashbuckle.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

È possibile risolvere il problema manualmente specificando nomi univoci per i metodi, come illustrato nell'esempio seguente:

* Get
* GetById
* GetPage

In alternativa, è possibile estendere Swashbuckle in modo che generi automaticamente ID operazione univoci, come illustrato in questo argomento.

## Estendere Swashbuckle 

I passaggi seguenti illustrano come personalizzare Swashbuckle usando il file *SwaggerConfig.cs* incluso nel progetto dal modello di progetto di anteprima delle app per le API di Visual Studio. È anche possibile personalizzare Swashbuckle in un progetto API Web configurato per la distribuzione come app per le API.

1. Creare un'implementazione personalizzata di `IOperationFilter` 

	L'interfaccia `IOperationFilter` fornisce un punto di estendibilità per gli utenti di Swashbuckle che vogliono personalizzare diversi aspetti del processo di metadati di Swagger. Il codice seguente illustra un metodo di modifica del comportamento di generazione dell'ID operazione. Il codice aggiunge nomi di parametro alla fine del nome dell'ID operazione.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. Nel file *App_Start\SwaggerConfig.cs* chiamare il metodo `OperationFilter` in modo che Swashbuckle usi la nuova implementazione di `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Il file *SwaggerConfig.cs* fornito dal pacchetto Swashbuckle NuGet contiene molti esempi con commenti relativi ai punti di estendibilità. I commenti aggiuntivi non sono visualizzati in questo articolo.

	Dopo questa modifica, viene usata l'implementazione di `IOperationFilter`, che provoca la generazione di ID operazione univoci.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

## Passaggi successivi

Questo articolo ha illustrato come personalizzare Swashbuckle in modo da generare ID operazione univoci. Per altre informazioni, vedere [Swashbuckle su GitHub](https://github.com/domaindrivendev/Swashbuckle).

<!---HONumber=58-->
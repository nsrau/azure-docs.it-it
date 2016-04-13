<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2.9" 
   description="Note sulla versione di Azure SDK per .NET 2.9" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/29/2016"
   ms.author="juliako"/>


# Note sulla versione di Azure SDK per .NET 2.9

##Panoramica

Questo documento contiene le note sulla versione per Azure SDK per.NET 2.9.

Per informazioni dettagliate sugli aggiornamenti in questa versione, vedere il [post di annuncio di Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## Anteprima di Azure SDK 2.9 per Visual Studio 2015 Update 2 e Visual Studio "15"
 
Questo aggiornamento include le correzioni di bug seguenti:

- Problema correlato alla generazione di client API REST in cui la stringa "Tipo sconosciuto" verrà visualizzata come nome della cartella di generazione di codice e/o nome dello spazio dei nomi eliminato nel codice generato.
- Problema correlato ai processi Web pianificati in cui le informazioni di autenticazione non sono state fornite al processo di provisioning dell’utilità di pianificazione.

Questo aggiornamento include la nuova funzionalità seguente:

- Supporto per Servizi app secondari nella scheda "Servizi" della finestra di dialogo Provisioning del servizio app. 

##Strumenti di Azure Data Lake per Visual Studio 2015 Update 2
 
Questi aggiornamenti includono quanto segue:

- **Strumenti di Azure Data Lake** per Visual Studio è ora unito alla versione Azure SDK per .NET. Lo strumento viene installato automaticamente quando si installa Azure SDK. 

	Lo strumento viene aggiornato frequentemente, fare clic [qui](http://aka.ms/datalaketool) per ottenere gli aggiornamenti.

- **Esplora server** ora consente di visualizzare tutte le entità di metadati U-SQL e di crearne alcune. Per altre informazioni, vedere [questo]() blog.


##Strumenti HDInsight 

**Strumenti HDInsight** per Visual Studio ora supporta HDInsight versione 3.3, che include la visualizzazione di grafici Tez e altre correzioni della lingua.


##Gestione risorse di Azure 

Questa versione aggiunge il supporto [Insieme di credenziali delle chiavi](../resource-manager-keyvault-parameter.md) per i modelli ARM.

##Vedere anche

[Post di annuncio di Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

<!----HONumber=AcomDC_0330_2016-->
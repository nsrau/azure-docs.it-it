---
title: Note sulla versione di Azure SDK per .NET 2.9
description: Note sulla versione di Azure SDK per .NET 2.9
services: app-service\web
documentationcenter: .net
author: Juliako
manager: erikre
editor: ''

ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/17/2016
ms.author: juliako

---
# <a name="azure-sdk-for-.net-2.9-release-notes"></a>Note sulla versione di Azure SDK per .NET 2.9
## <a name="overview"></a>Panoramica
Questo documento contiene le note sulla versione per Azure SDK per.NET 2.9. 

Per informazioni dettagliate sugli aggiornamenti in questa versione, vedere il [post di annuncio di Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-2.9-for-visual-studio-2015-update-2-and-visual-studio-"15"-preview"></a>Anteprima di Azure SDK 2.9 per Visual Studio 2015 Update 2 e Visual Studio "15"
Questo aggiornamento include le correzioni di bug seguenti:

* Problema correlato alla generazione di client API REST in cui la stringa "Tipo sconosciuto" verrà visualizzata come nome della cartella di generazione di codice e/o nome dello spazio dei nomi eliminato nel codice generato.
* Problema correlato ai processi Web pianificati in cui le informazioni di autenticazione non sono state fornite al processo di provisioning dell’utilità di pianificazione.

Questo aggiornamento include la nuova funzionalità seguente:

* Supporto per Servizi app secondari nella scheda "Servizi" della finestra di dialogo Provisioning del servizio app. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Strumenti di Azure Data Lake per Visual Studio 2015 Update 2
Questi aggiornamenti includono quanto segue:

* **Strumenti di Azure Data Lake** per Visual Studio è ora unito alla versione Azure SDK per .NET. Lo strumento viene installato automaticamente quando si installa Azure SDK. 
  
    Lo strumento viene aggiornato frequentemente, fare clic [qui](http://aka.ms/datalaketool) per ottenere gli aggiornamenti.
* **Esplora server** ora consente di visualizzare tutte le entità di metadati U-SQL e di crearne alcune. Per altre informazioni, vedere [questo](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.

## <a name="hdinsight-tools"></a>Strumenti HDInsight
**Strumenti HDInsight** per Visual Studio ora supporta HDInsight versione 3.3, che include la visualizzazione di grafici Tez e altre correzioni della lingua.

## <a name="azure-resource-manager"></a>Gestione risorse di Azure
Questa versione aggiunge il supporto [Insieme di credenziali delle chiavi](../resource-manager-keyvault-parameter.md) per i modelli ARM.

## <a name="see-also"></a>Vedere anche
[Post di annuncio di Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

<!--HONumber=Oct16_HO2-->



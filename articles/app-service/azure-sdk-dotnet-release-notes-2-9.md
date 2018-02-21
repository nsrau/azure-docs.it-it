---
title: Note sulla versione di Azure SDK per .NET 2.9
description: Note sulla versione di Azure SDK per .NET 2.9
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 199f0906f73d693d7cd4b73c928f23ae83b99596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Note sulla versione di Azure SDK per .NET 2.9

Questo argomento contiene le note sulle versioni 2.9 e 2.9.6 di Azure SDK per .NET.

##<a name="azure-sdk-for-net-296-release-summary"></a>Riepilogo sulla versione Azure SDK per .NET 2.9.6

Data di rilascio: 16/11/2016
 
In questa versione non sono state introdotte modifiche di rilievo ad Azure SDK 2.9. Per sfruttare questo SDK con progetti di servizio cloud esistenti, inoltre, non è necessario alcun processo di aggiornamento.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate

- In Visual Studio 2017 RC, questa versione di Azure SDK per .NET è integrata nel carico di lavoro di Azure. Tutti gli strumenti necessari per lo sviluppo per Azure saranno in futuro parte di Visual Studio 2017 RC. Per Visual Studio 2015 e Visual Studio 2013, l'SDK sarà ancora disponibile tramite WebPI. Le versioni per Visual Studio 2013 di Azure SDK per .NET verranno sospese quando Visual Studio 2017 verrà rilasciato come prodotto finale. Fare clic su questo collegamento per scaricare Visual Studio 2017 RC: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Diagnostica Azure

- Il comportamento è stato modificato in modo che venga archiviata solo una stringa di connessione parziale e che la chiave venga sostituita da un token per la stringa di connessione dell'archiviazione di diagnostica di Servizi cloud. La chiave di archiviazione effettiva viene ora archiviata nella cartella del profilo utente per poterne controllare l'accesso. Visual Studio leggerà la chiave di archiviazione dalla cartella del profilo utente per il processo locale di debug e pubblicazione. 
- In risposta alla modifica descritta sopra, il team di Visual Studio Online ha migliorato il modello di attività per la distribuzione di Servizi cloud di Azure in modo che gli utenti possano specificare la chiave di archiviazione per impostare l'estensione di diagnostica in caso di pubblicazione in Azure con l'integrazione e la distribuzione continua.
- È stata introdotta la possibilità di archiviare la stringa di connessione protetta e la tokenizzazione per Diagnostica di Azure (WAD) per consentire agli utenti di risolvere i problemi relativi alla configurazione in diversi ambienti.
 
### <a name="windows-server-2016-virtual-machines"></a>Macchine virtuali Windows Server 2016

- Visual Studio supporta ora la distribuzione di Servizi cloud in macchine virtuali con la famiglia di sistemi operativi 5 (Windows Server 2016). Per i servizi cloud esistenti, è possibile modificare le impostazioni specificando come destinazione la nuova famiglia di sistemi operativi. In caso di creazione di nuovi servizi cloud, se si sceglie di creare il servizio con .NET 4.6 o versioni successive verrà usata per impostazione predefinita la famiglia di sistemi operativi 5.  Per altre informazioni, vedere la [tabella relativa al supporto per le famiglie di sistemi operativi guest](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Problemi noti

- Azure .NET SDK 2.9.6 ha introdotto una restrizione che blocca la distribuzione di progetti tramite framework .NET non supportati (ad esempio .NET 4.6) verso le famiglie di sistemi operativi < 5. Una soluzione alternativa viene fornita [qui](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Cache nel ruolo di Azure 

- Il supporto per Cache nel ruolo di Azure termina il 30 novembre 2016. Per altri dettagli, fare clic [qui](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Modelli di Azure Resource Manager per Azure Stack

- Azure Stack è stato introdotto come destinazione di distribuzione per i modelli di Azure Resource Manager.


## <a name="azure-sdk-for-net-29-summary"></a>Riepilogo su Azure SDK per .NET 2.9

## <a name="overview"></a>Panoramica
Questo documento contiene le note sulla versione per Azure SDK per.NET 2.9. 

Per informazioni dettagliate sugli aggiornamenti in questa versione, vedere il [post di annuncio di Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Anteprima di Azure SDK 2.9 per Visual Studio 2015 Update 2 e Visual Studio "15"
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
In questa versione è stato aggiunto il supporto di [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) per i modelli di Resource Manager.

## <a name="see-also"></a>Vedere anche 
[Post di annuncio di Azure SDK 2.9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)


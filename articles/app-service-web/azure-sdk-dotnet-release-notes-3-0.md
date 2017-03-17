---
title: Note sulla versione di Azure SDK per .NET 3.0 | Microsoft Docs
description: Note sulla versione di Azure SDK per .NET 3.0
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
ms.date: 03/07/2017
ms.author: juliako;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: a3780d296548f4e00841563c65a99fb88ce047fe
ms.lasthandoff: 03/08/2017


---
# <a name="azure-sdk-for-net-30-release-notes"></a>Note sulla versione di Azure SDK per .NET 3.0

Questo argomento contiene le note sulla versione 3.0 di Azure SDK per .NET.

##<a name="azure-sdk-for-net-30-release-summary"></a>Riepilogo sulla versione Azure SDK per .NET 3.0

Data di rilascio: 07/03/2017
 
In questa versione non sono state introdotte modifiche di rilievo ad Azure SDK 3.0. Per sfruttare questo SDK con progetti di servizio cloud esistenti, inoltre, non è necessario alcun processo di aggiornamento.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- In Visual Studio 2017 questa versione di Azure SDK per .NET è integrata nel carico di lavoro di Azure. In futuro, tutti gli strumenti necessari per eseguire operazioni di sviluppo in Azure saranno integrati in Visual Studio 2017. Per Visual Studio 2015, l'SDK sarà ancora disponibile tramite WebPI. Con il rilascio di Visual Studio 2017 sono state sospese le versioni di Azure SDK per .NET per Visual Studio 2013.

### <a name="azure-diagnostics"></a>Diagnostica Azure

- Il comportamento è stato modificato in modo che venga archiviata solo una stringa di connessione parziale e che la chiave venga sostituita da un token per la stringa di connessione dell'archiviazione di diagnostica di Servizi cloud. La chiave di archiviazione effettiva viene ora archiviata nella cartella del profilo utente per poterne controllare l'accesso. Visual Studio leggerà la chiave di archiviazione dalla cartella del profilo utente per il processo locale di debug e pubblicazione. 
- In risposta alla modifica descritta sopra, il team di Visual Studio Online ha migliorato il modello di attività per la distribuzione di Servizi cloud di Azure in modo che gli utenti possano specificare la chiave di archiviazione per impostare l'estensione di diagnostica in caso di pubblicazione in Azure con l'integrazione e la distribuzione continua.
- È stata introdotta la possibilità di archiviare la stringa di connessione protetta e la tokenizzazione per Diagnostica di Azure (WAD) per consentire agli utenti di risolvere i problemi relativi alla configurazione in diversi ambienti.
 
### <a name="windows-server-2016-virtual-machines"></a>Macchine virtuali Windows Server 2016

- Visual Studio supporta ora la distribuzione di Servizi cloud in macchine virtuali con la famiglia di sistemi operativi 5 (Windows Server 2016). Per i servizi cloud esistenti, è possibile modificare le impostazioni specificando come destinazione la nuova famiglia di sistemi operativi. In caso di creazione di nuovi servizi cloud, se si sceglie di creare il servizio con .NET 4.6 o versioni successive verrà usata per impostazione predefinita la famiglia di sistemi operativi 5.  Per altre informazioni, vedere la [tabella relativa al supporto per le famiglie di sistemi operativi guest](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Problemi noti

- In Azure .NET SDK 3.0 si verifica un problema quando si rimuove Visual Studio 2017 in una configurazione side-by-side con Visual Studio 2015.  Se è installato Azure SDK per Visual Studio 2015 e si disinstalla Visual Studio 2017, verranno eliminati sia l'emulatore di archiviazione di Microsoft Azure sia l'emulatore di calcolo di Microsoft Azure.  Di conseguenza, durante la creazione e il debug di nuovi progetti di servizi cloud in Visual Studio 2015 verrà generato un errore. Per risolvere questo problema, reinstallare Azure SDK dall'Installazione guidata piattaforma Web.  Il problema verrà risolto in un futuro aggiornamento di Visual Studio 2017.  .

 
### <a name="azure-in-role-cache"></a>Cache nel ruolo di Azure 

- Il supporto per Cache nel ruolo di Azure è terminato il 30 novembre 2016. Per altri dettagli, fare clic [qui](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).






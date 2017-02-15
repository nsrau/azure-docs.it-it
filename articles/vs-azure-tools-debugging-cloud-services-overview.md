---
title: Debug di servizi cloud di Azure | Documentazione Microsoft
description: Debug di servizi cloud di Azure
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f10ede0a6c30c5a8ae593a5dba0f3db77134d50b


---
# <a name="debugging-cloud-services"></a>Debug di servizi cloud
È possibile usare diversi approcci per eseguire il debug di un'applicazione Azure usando gli strumenti di Azure per Microsoft Visual Studio e Azure SDK:

* È possibile eseguire il debug un'applicazione Azure da Visual Studio quando la si sviluppa, esattamente come si farebbe per qualsiasi applicazione Visual C# o Visual Basic. Per ulteriori informazioni, vedere il [Debug del servizio cloud nel computer locale](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).
* È possibile usare la diagnostica di Azure per registrare informazioni dettagliate dal codice eseguito all'interno dei ruoli, sia che essi siano eseguiti nell'ambiente di sviluppo o che in Azure. Per altre informazioni, vedere [Raccolta dei dati di registrazione mediante la diagnostica di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).
* Se si usa Visual Studio Enterprise per scrivere ruoli destinati a .NET Framework 4 o .NET Framework 4.5, è possibile abilitare IntelliTrace nel momento in cui si distribuisce un servizio cloud di Azure da Visual Studio. IntelliTrace fornisce un log che è possibile usare con Visual Studio per il debug dell'applicazione come se fosse in esecuzione in Azure. Per altre informazioni, vedere [Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).
* È possibile abilitare il debug remoto nei servizi cloud nel momento in cui si distribuisce il servizio cloud da Visual Studio. Se si sceglie di abilitare il debug remoto per una distribuzione, i servizi di debug remoto vengono installati nelle macchine virtuali che eseguono ogni istanza del ruolo. Questi servizi, ad esempio msvsmon.exe, non influiscono sulle prestazioni né producono costi aggiuntivi. Per ulteriori informazioni, vedere [il Debug di un servizio cloud in Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).




<!--HONumber=Nov16_HO3-->



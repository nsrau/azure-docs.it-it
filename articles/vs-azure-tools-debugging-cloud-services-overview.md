---
title: Opzioni per il debug dei servizi cloud di Azure | Microsoft Docs
description: Debug dei servizi cloud di Azure
services: visual-studio-online
documentationcenter: n/a
author: kraigb
manager: ghogen
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: kraigb
ms.openlocfilehash: cdcd4ca1fbc7e0a2f24122b32148cbda3d6951a0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Informazioni sui vari modi per eseguire il debug di un servizio cloud di Azure
Questo articolo include collegamenti ai vari modi per eseguire il debug di un servizio cloud di Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Debug di un servizio cloud di Azure in Visual Studio
È possibile risparmiare tempo e denaro usando l'emulatore di calcolo di Azure per il debug del servizio cloud su un computer locale. Eseguendo il debug di un servizio in locale prima della distribuzione, è possibile migliorare l'affidabilità e le prestazioni senza pagare per il tempo di calcolo. È tuttavia possibile che si verifichino alcuni errori solo quando si esegue un servizio cloud in Azure. Gli errori che si verificano solo quando si esegue un servizio cloud in Azure possono essere sottoposti a debug abilitando il debug remoto in fase di pubblicazione del servizio e quindi collegando il debugger a un'istanza del ruolo. Per ulteriori informazioni, vedere il [Debug del servizio cloud nel computer locale](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-azure-diagnostics"></a>Uso di Diagnostica di Azure 
È possibile usare la diagnostica di Azure per registrare informazioni dettagliate dal codice eseguito all'interno dei ruoli, sia che essi siano eseguiti nell'ambiente di sviluppo o che in Azure. Per altre informazioni, vedere [Abilitazione di Diagnostica di Azure in servizi cloud di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

## <a name="using-intellitrace"></a>Uso di IntelliTrace 
Se si usa Visual Studio Enterprise per scrivere ruoli destinati a .NET Framework 4.5, è possibile abilitare IntelliTrace nel momento in cui si distribuisce un servizio cloud di Azure da Visual Studio. IntelliTrace fornisce un log che è possibile usare con Visual Studio per il debug dell'applicazione come se fosse in esecuzione in Azure. Per altre informazioni, vedere [Debug di un servizio cloud pubblicato con IntelliTrace e Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Debug remoto 
È possibile abilitare il debug remoto nei servizi cloud nel momento in cui si distribuisce il servizio cloud da Visual Studio. Se si sceglie di abilitare il debug remoto per una distribuzione, i servizi di debug remoto vengono installati nelle macchine virtuali che eseguono ogni istanza del ruolo. Questi servizi, ad esempio `msvsmon.exe`, non influiscono sulle prestazioni né producono costi aggiuntivi. Per ulteriori informazioni, vedere [il Debug di un servizio cloud in Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Passaggi successivi
- [Debug di un servizio cloud di Azure o di una macchina virtuale in Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md): informazioni dettagliate su come eseguire il debug di servizi cloud di Azure.
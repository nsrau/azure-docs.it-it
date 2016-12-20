---
title: Installare l&quot;emulatore Express per eseguire il debug di applicazioni di Servizi cloud in Visual Studio | Documentazione Microsoft
description: Descrive come installare C++ Redistributable per abilitare l&quot;emulatore Express in Visual Studio
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 22b20f7a-23f4-4f7f-b536-3bf1e01adcd1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 05d672dcb1335c617bb8d8cae43947bcd5e9ab3d

---
# <a name="use-emulator-express-to-debug-cloud-services-application-in-vs-2017"></a>Usare l'emulatore Express per eseguire il debug di applicazioni di Servizi cloud in Visual Studio 2017
Questo articolo spiega come usare l'emulatore Express per eseguire il debug di applicazioni di Servizi cloud in Visual Studio 2017.

## <a name="background-context"></a>Contesto di fondo
L'emulatore Express viene usato per impostazione predefinita per il debug di ruoli Web e di lavoro di Servizi cloud in Visual Studio. Questa impostazione viene specificata nella pagina delle proprietà del progetto di Servizi cloud.

![Aprire le proprietà del progetto][0]

![L'emulatore Express è selezionato per impostazione predefinita][1]

L'emulatore Express richiede [Visual C++ Redistributable][Visual C++ Redistributable] per Visual Studio. Non viene attualmente installato con il carico di lavoro di Azure. Quando si preme F5 per eseguire il debug di applicazioni di Servizi cloud, Visual Studio chiederà di installare il componente e procedere con il debug.

![Prompt per l'installazione di C++ Redistributable][2]

Fare clic su Sì per installare C++ Redistributable.

![Installare C++ Redistributable][3]

Premere di nuovo F5 per avviare le sessioni di debug.

![Avviare il debug][4]

![Debug completato][5]

> Nota: l'installazione di Visual C++ Redistributable viene eseguita una sola volta. Se venisse effettuato l'aggiornamento da una versione precedente di Azure SDK e l'emulatore Express fosse già installato, questo problema non si verificherebbe.
> 
> 

## <a name="manual-workaround"></a>Soluzione alternativa manuale
È anche possibile installare [Visual C++ Redistributable][Visual C++ Redistributable] manualmente per ottenere lo stesso effetto che si avrebbe con l'installazione da parte di Visual Studio.

[vcredist_x86.exe][vcredist_x86.exe]

[vcredist_x64.exe][vcredist_x64.exe]

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull'uso dell'emulatore di calcolo di Azure per eseguire il debug delle applicazioni di Servizi cloud in Visual Studio: [Uso dell'emulatore Express per l'esecuzione e il debug di un servizio cloud in un computer locale][Uso dell'emulatore Express per l'esecuzione e il debug di un servizio cloud in un computer locale]

[Visual C++ Redistributable]:https://www.microsoft.com/en-us/download/details.aspx?id=30679
[vcredist_x86.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x86.exe
[vcredist_x64.exe]:https://download.microsoft.com/download/1/6/B/16B06F60-3B20-4FF2-B699-5E9B7962F9AE/VSU_4/vcredist_x64.exe
[Uso dell'emulatore Express per l'esecuzione e il debug di un servizio cloud in un computer locale]:https://azure.microsoft.com/en-us/documentation/articles/vs-azure-tools-emulator-express-debug-run/

[0]: ./media/cloud-services-emulator-express-fix/vs-05.png
[1]: ./media/cloud-services-emulator-express-fix/vs-06.png
[2]: ./media/cloud-services-emulator-express-fix/vs-01.png
[3]: ./media/cloud-services-emulator-express-fix/vs-02.png
[4]: ./media/cloud-services-emulator-express-fix/vs-03.png
[5]: ./media/cloud-services-emulator-express-fix/vs-04.png



<!--HONumber=Nov16_HO3-->



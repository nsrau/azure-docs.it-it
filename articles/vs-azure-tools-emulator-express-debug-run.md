---
title: Uso dell&quot;emulatore Express per l&quot;esecuzione e il debug di un servizio cloud in un computer locale | Documentazione Microsoft
description: Uso di Emulator Express per l&quot;esecuzione e il debug di un servizio cloud in un computer locale
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b09fb0be256fc4cc832822f676b6d1f9de1813cb


---
# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Uso di Emulator Express per l'esecuzione e il debug di un servizio cloud in un computer locale
Con l'emulatore Express, è possibile testare ed eseguire il debug di un servizio cloud senza eseguire Visual Studio come amministratore. È possibile configurare le impostazioni del progetto per usare l'emulatore Express o l'emulatore completo, in base ai requisiti del servizio cloud. Per altre informazioni sull'emulatore completo, vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](storage/storage-use-emulator.md). L'emulatore Express è stato incluso per la prima volta in Azure SDK 2.1 e a partire da Azure SDK 2.3 è l'emulatore predefinito.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Uso dell'emulatore Express nell'IDE di Visual Studio
Quando si crea un nuovo progetto in Azure SDK 2.3 o versione successiva, l'emulatore Express è già selezionato. Per i progetti esistenti creati con una versione precedente dell’SDK, attenersi alla procedura seguente per selezionare l'emulatore Express.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Per configurare un progetto per l'uso dell'emulatore Express
1. Nel menu di scelta rapida per il progetto Azure, scegliere **Proprietà**, quindi la scheda **Web**.
2. In **Server di sviluppo locale**, scegliere il pulsante **Usa l'opzione IIS Express**. L'emulatore Express non è compatibile con il Server Web IIS.
3. In **Emulatore** scegliere il pulsante di opzione **Usa emulatore Express**.
   
    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Avvio dell'emulatore Express in un prompt dei comandi
Dal prompt dei comandi è possibile avviare la versione express dell'emulatore di calcolo di Azure, csrun.exe, usando l'opzione /useemulatorexpress.

## <a name="limitations"></a>Limitazioni
Prima di usare l'emulatore Express, è necessario tenere presenti alcune limitazioni:

* L'emulatore Express non è compatibile con il Server Web IIS.
* Il servizio cloud può contenere più ruoli, ma ogni ruolo è limitato a un'istanza.
* È possibile accedere ai numeri di porta inferiori a 1000. Se si usa un provider di autenticazione che in genere usa una porta inferiore a 1000, ad esempio, potrebbe essere necessario modificare questo valore per un numero di porta superiore a 1000.
* Qualsiasi limitazione dell'emulatore di calcolo di Azure si applica anche all'emulatore Express. Ad esempio, non si può disporre di più di 50 istanze del ruolo per ogni distribuzione. Vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Passaggi successivi
[Debug di servizi cloud](https://msdn.microsoft.com/library/azure/ee405479.aspx)




<!--HONumber=Nov16_HO3-->



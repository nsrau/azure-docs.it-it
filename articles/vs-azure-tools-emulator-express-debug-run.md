---
title: Uso dell'emulatore Express per l'esecuzione e il debug di un servizio cloud di Azure in un computer locale | Documentazione Microsoft
description: Uso di Emulator Express per l'esecuzione e il debug di un servizio cloud in un computer locale
services: visual-studio-online
documentationcenter: n/a
author: kraigb
manager: ghogen
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fe31dcb78c6713d93bf87e92ac12341d8692f679
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Uso dell'emulatore Express per l'esecuzione e il debug di un servizio cloud di Azure in un computer locale
Con l'emulatore Express, è possibile testare ed eseguire il debug di un servizio cloud senza eseguire Visual Studio come amministratore. È possibile configurare le impostazioni del progetto per usare l'emulatore Express o l'emulatore completo, in base ai requisiti del servizio cloud. Per altre informazioni sull'emulatore completo, vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Uso dell'emulatore Express in Visual Studio
Quando si crea un progetto di Azure in Azure SDK 2.3 o versione successiva, l'emulatore Express è selezionato automaticamente. Per i progetti esistenti creati con una versione precedente dell'SDK di Azure, attenersi alla procedura seguente per selezionare l'emulatore Express:

1. Creare o aprire un progetto del servizio cloud di Azure in Visual Studio.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Proprietà** dal menu di scelta rapida.

1. Nelle pagine delle proprietà di progetti, selezionare la scheda **Web**.

    ![Proprietà di un progetto di servizio cloud di Azure](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. In **Server di sviluppo locale**, scegliere **Usa l'opzione IIS Express**.

1. In **Emulatore**selezionare **Usa emulatore Express**.
   
1. Per avviare l'emulatore Express, eseguire il comando seguente al prompt dei comandi: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Limitazioni dell'emulatore Express
Di seguito sono indicati alcuni problemi causati da limiti noti dell'emulatore Express: 

- L'emulatore Express non è compatibile con il Server Web IIS.
- Il servizio cloud può contenere più ruoli, ma ogni ruolo è limitato a un'istanza.
- È possibile accedere ai numeri di porta inferiori a 1000. Se si usa un provider di autenticazione che in genere usa una porta inferiore a 1000, potrebbe essere necessario modificare questo valore per i numeri di porta superiori a 1000.
- Qualsiasi limitazione dell'emulatore di calcolo di Azure si applica anche all'emulatore Express. Ad esempio, non si può disporre di più di 50 istanze del ruolo per ogni distribuzione. Per altre informazioni sull'emulatore completo di Azure, vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Passaggi successivi
[Debug dei servizi cloud di Azure](https://msdn.microsoft.com/library/azure/ee405479.aspx)


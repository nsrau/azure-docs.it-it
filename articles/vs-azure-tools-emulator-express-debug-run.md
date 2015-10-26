<properties
   pageTitle="Uso dell'emulatore Express per l'esecuzione e il debug di un servizio cloud in un computer locale | Microsoft Azure"
   description="Uso di Emulator Express per l'esecuzione e il debug di un servizio cloud in un computer locale"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="patshea" />


# Uso di Emulator Express per l'esecuzione e il debug di un servizio cloud in un computer locale

Con l'emulatore Express, è possibile testare ed eseguire il debug di un servizio cloud senza eseguire Visual Studio come amministratore. È possibile configurare le impostazioni del progetto per usare l'emulatore Express o l'emulatore completo, in base ai requisiti del servizio cloud. Per altre informazioni sull'emulatore completo, vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](../storage/storage-use-emulator.md). L'emulatore Express è stato incluso per la prima volta in Azure SDK 2.1 e a partire da Azure SDK 2.3 è l'emulatore predefinito.

## Uso dell'emulatore Express nell'IDE di Visual Studio

Quando si crea un nuovo progetto in Azure SDK 2.3 o versione successiva, l'emulatore Express è già selezionato. Per i progetti esistenti creati con una versione precedente dell’SDK, attenersi alla procedura seguente per selezionare l'emulatore Express.

### Per configurare un progetto per l'uso dell'emulatore Express

1. Nel menu di scelta rapida per il progetto di Azure, scegliere **Proprietà**, quindi scegliere la scheda **Web**.

1. In **Server di sviluppo locale** scegliere il pulsante di opzione **Usa IIS Express**. L'emulatore Express non è compatibile con il Server Web IIS.

1. In **Emulatore** scegliere il pulsante di opzione **Usa emulatore Express**.

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## Avvio dell'emulatore Express in un prompt dei comandi

Dal prompt dei comandi è possibile avviare la versione express dell'emulatore di calcolo di Azure, csrun.exe, usando l'opzione /useemulatorexpress.

## Limitazioni

Prima di usare l'emulatore Express, è necessario tenere presenti alcune limitazioni:

- L'emulatore Express non è compatibile con il Server Web IIS.

- Il servizio cloud può contenere più ruoli, ma ogni ruolo è limitato a un'istanza.

- È possibile accedere ai numeri di porta inferiori a 1000. Se si usa un provider di autenticazione che in genere usa una porta inferiore a 1000, ad esempio, potrebbe essere necessario modificare questo valore per un numero di porta superiore a 1000.

- Qualsiasi limitazione dell'emulatore di calcolo di Azure si applica anche all'emulatore Express. Ad esempio, non si può disporre di più di 50 istanze del ruolo per ogni distribuzione. Vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## Passaggi successivi

[Debug di servizi cloud](https://msdn.microsoft.com/library/azure/ee405479.aspx)

<!---HONumber=Oct15_HO3-->
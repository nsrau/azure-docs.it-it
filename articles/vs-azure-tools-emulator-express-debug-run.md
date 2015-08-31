<properties 
   pageTitle="Utilizzo di Emulator Express per l'esecuzione e il debug in locale di un servizio cloud"
   description="Utilizzo di Emulator Express per l'esecuzione e il debug in locale di un servizio cloud"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/12/2015"
   ms.author="patshea" />

# Utilizzo di Emulator Express per l'esecuzione e il debug in locale di un servizio cloud

##Panoramica

Con Emulator Express, è possibile testare ed eseguire il debug di un servizio cloud senza eseguire Visual Studio come amministratore. È possibile configurare le impostazioni del progetto per utilizzare Emulator Express o l'emulatore completo, in base ai requisiti del servizio cloud. Per ulteriori informazioni sull'emulatore completo, vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo]http://go.microsoft.com/fwlink/p/?LinkId=623050). Emulator Express è stato incluso per la prima volta in Azure SDK 2.1 e a partire da Azure SDK 2.3 è l'emulatore predefinito.

## Utilizzo di Emulator Express nell'IDE di Visual Studio

Quando si crea un nuovo progetto in Azure SDK 2.3 o versione successiva, Emulator Express è già selezionato. Per i progetti esistenti creati con una versione precedente dell’SDK, attenersi alla procedura seguente per selezionare Emulator Express.

### Per configurare un progetto per l'utilizzo di Emulator Express

1. Nel menu di scelta rapida per il progetto di Azure, scegliere **Proprietà**, quindi scegliere la scheda **Web**.

1. In **Server di sviluppo locale**, scegliere il pulsante **Usa l’opzione IIS Express**. Emulator Express non è compatibile con il Server Web IIS.

1. In **Emulatore**, scegliere il pulsante di opzione **Usa Emulator Express**.

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## Avvio di Emulator Express in un prompt dei comandi

Dal prompt dei comandi è possibile avviare la versione express dell'emulatore di calcolo di Azure, csrun.exe, utilizzando l'opzione /useemulatorexpress.

## Limitazioni

Prima di utilizzare Emulator Express, è necessario tenere presenti alcune limitazioni:

- Emulator Express non è compatibile con il Server Web IIS.

- Il servizio cloud può contenere più ruoli, ma ogni ruolo è limitato a un'istanza.

- È possibile accedere ai numeri di porta inferiori a 1000. Se si utilizza un provider di autenticazione che in genere utilizza una porta inferiore a 1000, ad esempio, potrebbe essere necessario modificare questo valore per un numero di porta superiore a 1000.

- Qualsiasi limitazione dell'emulatore di calcolo di Azure si applica anche ad Emulator Express. Ad esempio, non si può disporre di più di 50 istanze del ruolo per ogni distribuzione. Vedere [Eseguire un'applicazione Azure nell'emulatore di calcolo](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## Passaggi successivi

[Debug di Servizi cloud](http://go.microsoft.com/fwlink/p/?LinkId=623041)

<!---HONumber=August15_HO8-->
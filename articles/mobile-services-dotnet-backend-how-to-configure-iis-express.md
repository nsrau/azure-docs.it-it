<properties pageTitle="Configure IIS Express for local testing of Mobile Service" metaKeywords="Azure Mobile Services, .NET Backend, IIS Express" description="Learn how to configure IIS Express to allow connections to a local mobile service project for testing." authors="glenga" title="Configure the local web server to allow connections to a local mobile service" />

Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale.
===============================================================================================

Servizi mobili di Azure consente di creare il servizio mobile in Visual Studio utilizzando uno dei linguaggi .NET supportati e quindi di pubblicarlo in Azure. Uno dei principali vantaggi dell'utilizzo di un back-end .NET per il servizio mobile è la possibilità di eseguire, testare ed eseguire il debug del servizio mobile sul computer locale o sulla macchina virtuale prima di pubblicarlo in Azure.

Per poter testare un servizio mobile localmente con i client in esecuzione su un emulatore, una macchina virtuale o una workstation separata, è necessario configurare il server Web locale e il computer host in modo da consentire le connessioni all'indirizzo e alla porta IP della workstation. In questo argomento viene descritto come configurare IIS Express in modo da abilitare le connessioni al servizio mobile ospitato localmente.

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]


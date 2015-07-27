<properties 
	pageTitle="Configurare IIS Express per i test locali del servizio mobile" 
	description="Informazioni su come configurare IIS Express per consentire le connessioni a un progetto di servizio mobile locale da usare per i test." 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	documentationCenter="" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>

# Configurazione del server Web locale per consentire le connessioni a un servizio mobile locale. 

Servizi mobili di Azure consente di creare il servizio mobile in Visual Studio utilizzando uno dei linguaggi .NET supportati e quindi di pubblicarlo in Azure. Uno dei principali vantaggi dell'utilizzo di un back-end .NET per il servizio mobile è la possibilità di eseguire, testare ed eseguire il debug del servizio mobile sul computer locale o sulla macchina virtuale prima di pubblicarlo in Azure.

Per poter testare un servizio mobile localmente con i client in esecuzione su un emulatore, una macchina virtuale o una workstation separata, è necessario configurare il server Web locale e il computer host in modo da consentire le connessioni all'indirizzo e alla porta IP della workstation. Questo argomento descrive come configurare IIS Express in modo da abilitare le connessioni al servizio mobile ospitato localmente.

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

 

<!---HONumber=July15_HO3-->
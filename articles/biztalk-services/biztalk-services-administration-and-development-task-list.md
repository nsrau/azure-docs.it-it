<properties
	pageTitle="Elenco delle attività di amministrazione e sviluppo nei servizi BizTalk | Microsoft Azure" 
	description="Pianificazione e processo di aiuto per la distribuzione di servizi BizTalk di Azure."
	services="biztalk-services"
	documentationCenter=""
	authors="msftman"
	manager="erikre"
	editor=""/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="deonhe"/>

# Elenco dell’attività di Amministrazione e Sviluppo in Servizi BizTalk  

## Introduzione
Quando si lavora con i servizi BizTalk di Microsoft Azure, esistono diversi componenti in locale e basati su cloud da considerare. Per iniziare, considerare il seguente flusso del processo:

|Passaggio|Chi è il responsabile|Attività|Collegamenti correlati|
|----|----|----|----|
1\.|Amministratore|Creare la sottoscrizione di Microsoft Azure mediante un account Microsoft o un account aziendale|[Portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=213885)|
|2\.|Amministratore|Creare o eseguire il provisioning di un servizio BizTalk|[Creare un servizio BizTalk mediante il portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=302280)|
|3\.|Amministratore|Registrazione della distribuzione dei servizi BizTalk dell’azienda|[Registrazione e aggiornamento di una distribuzione di servizio BizTalk nel portale dei servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx)|
|4\.|Amministratore|Si applica se l'applicazione usa il Servizio Adapter BizTalk per connettersi a un sistema Line-of-Business (LOB) in locale o usa destinazione Coda o Argomento. Creare lo spazio dei nomi del bus di servizio di Azure Assegnare lo spazio dei nomi, il nome dell'autorità emittente del bus di servizio e i valori della chiave dell'autorità emittente del bus di servizio agli sviluppatori.|[Procedura: creare o modificare uno spazio dei nomi del servizio Bus di servizio](../service-bus/service-bus-dotnet-get-started-with-queues.md) e [Ottenere il Nome dell’autorità emittente e i Valori della chiave dell'autorità emittente](biztalk-issuer-name-issuer-key.md)|
|5\.|Developer|Installare l’SDK e creare il progetto Servizio BizTalk in Visual Studio.|[Installare l’SDK dei Servizi BizTalk di Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx) e [Creare endpoint di messaggistica avanzate in Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx)|
|6\.|Developer|Distribuire il progetto dei Servizi BizTalk nel Servizio BizTalk ospitato in Azure.|[Distribuzione e Aggiornamento del progetto di Servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689881.aspx)|
|7\.|Amministratore|Si applica se usa EDI: È possibile aggiungere partner e creare contratti nel portale Servizi BizTalk di Microsoft Azure Quando si crea un contratto, è possibile aggiungere il bridge e/o le trasformazioni create dallo sviluppatore per le impostazioni del contratto.|[Configurazione di EDI, AS2 ed EDIFACT nel portale dei Servizi BizTalk](https://msdn.microsoft.com/library/azure/hh689853.aspx)|
|8\.|Amministratore|Nel portale di Azure classico, monitorare l'integrità del servizio BizTalk, comprese le metriche delle prestazioni.|[Servizi BizTalk: Schede Dashboard, Monitoraggio, Scalabilità](http://go.microsoft.com/fwlink/p/?LinkID=302281)|
|9\.|Amministratore|Tramite il portale di servizi BizTalk di Microsoft Azure, è possibile gestire gli elementi usati dai Servizi BizTalk e tenere traccia dei messaggi man mano che vengono elaborati dai file di bridge.|[Uso del portale di Servizi BizTalk](https://msdn.microsoft.com/library/azure/dn874043.aspx)|
|10\.|Amministratore|Creare un piano di backup per eseguire il backup del Servizio BizTalk.|[Continuità aziendale e ripristino di emergenza nei servizi BizTalk](https://msdn.microsoft.com/library/azure/dn509557.aspx) |  
## Passaggi successivi
[Esercitazioni ed esempi](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Creare il progetto in Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installare l’SDK dei Servizi BizTalk di Azure](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## Concetti
[Creare il progetto in Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx) [Messaggistica di EDI, AS2 ed EDIFACT (Business to Business)](https://msdn.microsoft.com/library/azure/hh689898.aspx)
## Altre risorse  
[Aggiungi Origine, Destinazione ed Endpoint della messaggistica Bridge](https://msdn.microsoft.com/library/azure/hh689877.aspx) [Informazioni per la creazione della mappatura dei messaggi e delle trasformazioni](https://msdn.microsoft.com/library/azure/hh689905.aspx) [Usando il servizio Adapter BizTalk (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx) [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

<!---HONumber=AcomDC_0608_2016-->
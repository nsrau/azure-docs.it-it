<properties 
	pageTitle="Informazioni sul connettore Encode AS2 Message ed Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Informazioni su come usare i partner con Enterprise Integration Pack e le app per la logica" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="padmavc" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="padmavc"/>

# Introduzione a Encode AS2 Message

Connettersi a Encode AS2 Message per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi. Offre funzionalità di firma digitale, decrittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi, con il conseguente supporto del database di non ripudio.

## Creare la connessione

### Prerequisiti

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)

* Per usare il connettore Encode AS2 Message, è necessario un account di integrazione. Visualizzare i dettagli su come creare un [account integrazione](./app-service-logic-enterprise-integration-create-integration-account.md), un [partner](./app-service-logic-enterprise-integration-partners.md) e un [contratto AS2](./app-service-logic-enterprise-integration-as2.md)

### Connettersi a Encode AS2 Message usando questa procedura:

1. La sezione [Creare un'app per la logica](./app-service-logic-create-a-logic-app.md) illustra un esempio.

2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta. Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione. Selezionare Mostra API gestite da Microsoft nell'elenco a discesa e quindi immettere "AS2" nella casella di ricerca. Selezionare AS2 - Encode AS2 Message.

	![Ricerca di AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione.
	
	![creare una connessione all'account di integrazione](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)

4. Immettere i dettagli dell'account di integrazione. Le proprietà con l'asterisco sono obbligatorie.

	| Proprietà | Dettagli |
	| --------   | ------- |
	| Nome connessione* | Immettere un nome per la connessione. |
	| Account di integrazione * | Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

  	Al termine, i dettagli della connessione saranno simili ai seguenti:

  	![connessione di integrazione stabilita](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)

5. Selezionare **Crea**.

6. La connessione è stata creata. Fornire gli identificatori per AS2-From, AS2-To (come configurato nel contratto) e i dettagli del corpo (il payload del messaggio).

	![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## AS2 Encode esegue queste operazioni

* Elabora le intestazioni AS2/HTTP
* Firma i messaggi in uscita (se configurata)
* Crittografa i messaggi in uscita (se configurata)
* Comprime i messaggi (se configurata)

##Prova

Per fare una prova, fare clic [qui](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) per distribuire un'app per la logica personalizzata completamente operativa usando le funzionalità AS2 dell'app per la logica.

## Passaggi successivi

[Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

<!---HONumber=AcomDC_0914_2016-->
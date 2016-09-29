<properties 
	pageTitle="Informazioni sul connettore Decode AS2 Message ed Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
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

# Introduzione a Decode AS2 Message

Connettersi a Decode AS2 Message per stabilire affidabilità e sicurezza durante la trasmissione dei messaggi. Offre funzionalità di firma digitale, decrittografia e riconoscimenti tramite notifiche sulla ricezione di messaggi.

## Creare la connessione

### Prerequisiti

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)

* Per usare il connettore Decode AS2 Message, è necessario un account di integrazione. Visualizzare i dettagli su come creare un [account integrazione](./app-service-logic-enterprise-integration-create-integration-account.md), un [partner](./app-service-logic-enterprise-integration-partners.md) e un [contratto AS2](./app-service-logic-enterprise-integration-as2.md)

### Connettersi a Decode AS2 Message usando questa procedura:

1. Un esempio è disponibile in[Creare un'app per la logica](./app-service-logic-create-a-logic-app.md).

2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta. Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione. Selezionare Mostra API gestite da Microsoft nell'elenco a discesa e quindi immettere "AS2" nella casella di ricerca. Selezionare AS2 - Decode AS2 Message.

	![Ricerca di AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione.

	![Create una connessione di integrazione](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. Immettere i dettagli dell'account di integrazione. Le proprietà con l'asterisco sono obbligatorie.

	| Proprietà | Dettagli |
	| --------   | ------- |
	| Nome connessione* | Immettere un nome per la connessione. |
	| Account di integrazione * | Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |

  	Al termine, i dettagli della connessione saranno simili ai seguenti:

  	![connessione di integrazione](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. Selezionare **Crea**.
	
6. La connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app per la logica.

	![connessione di integrazione creata](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png)

7. Selezionare Corpo e Intestazioni dagli output della richiesta

	![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png)

## AS2 Decode esegue queste operazioni

* Elabora le intestazioni AS2/HTTP
* Verifica la firma (se configurata)
* Decrittografa i messaggi (se configurata)
* Decomprime i messaggi (se configurata)
* Riconcilia una notifica sulla ricezione del messaggio ricevuta con il messaggio in uscita originale
* Aggiorna e mette in correlazione i record nel database di non ripudio
* Scrive i record per la creazione di report di stato su AS2
* Il contenuto del payload di output è codificato con codifica Base 64
* Determina se una notifica sulla ricezione del messaggio è obbligatoria, se deve essere sincrona o asincrona in base alla configurazione nel contratto AS2
* Genera una notifica sulla ricezione del messaggio sincrona o asincrona, in base alle configurazioni nel contratto
* Imposta le proprietà e il token di correlazione nella notifica sulla ricezione del messaggio

##Prova

Per fare una prova, fare clic [qui](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) per distribuire un'app per la logica personalizzata completamente operativa usando le funzionalità AS2 dell'app per la logica.

## Passaggi successivi

[Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

<!---HONumber=AcomDC_0914_2016-->
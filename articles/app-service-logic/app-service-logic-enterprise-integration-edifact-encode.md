---
title: Informazioni sul connettore Encode EDIFACT Message ed Enterprise Integration Pack | Microsoft Docs
description: Informazioni su come usare i partner con Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc

---
# Introduzione a Encode EDIFACT Message
Convalida le proprietà EDI e specifiche del partner.

## Creare la connessione
### Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Encode EDIFACT Message, è necessario un account di integrazione. Visualizzare i dettagli su come creare un [account integrazione](app-service-logic-enterprise-integration-create-integration-account.md), un [partner](app-service-logic-enterprise-integration-partners.md) e un [contratto EDIFACT](app-service-logic-enterprise-integration-edifact.md)

### Connettersi a Decode EDIFACT Message usando questa procedura:
1. Un esempio è disponibile in[Creare un'app per la logica](app-service-logic-create-a-logic-app.md).
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta. Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione. Selezionare Mostra API gestite da Microsoft nell'elenco a discesa, quindi immettere "EDIFACT" nella casella di ricerca. Selezionare Encode EDIFACT Message by agreement name o Encode to EDIFACT message by identities.
   
    ![ricerca di EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione.
   
    ![creare connessione all'account di integrazione](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage1.png)
4. Immettere i dettagli dell'account di integrazione. Le proprietà con l'asterisco sono obbligatorie.
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
    Al termine, i dettagli della connessione saranno simili ai seguenti:
   
    ![connessione all'account di integrazione](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage2.png)
5. Selezionare **Crea**.
6. La connessione è stata creata.
   
    ![dettagli della connessione all'account di integrazione](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage4.png)

#### Encode EDIFACT Message by agreement name
1. Fornire il nome del contratto EDIFACT e un messaggio XML da codificare.
   
   ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage6.png)

#### Encode EDIFACT Message by identities
1. Fornire identificatore del mittente e qualificatore del mittente, identificatore del ricevitore e qualificatore del ricevitore come configurati nel contratto EDIFACT. Selezionare il messaggio XML da codificare.
   
    ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactencodeimage7.png)

## EDIFACT Encode esegue queste operazioni
* Risolvere il contratto associando il qualificatore e l'identificatore del mittente e il qualificatore e l'identificatore del ricevitore.
* Serializza l'interscambio EDI, conversione dei messaggi con codifica XML in set di transazioni EDI nell'interscambio.
* Si applica ai segmenti di intestazione e finali del set di transazioni.
* Genera un numero di controllo di interscambio, un numero di controllo di gruppo e un numero di controllo del set di transazioni per ogni interscambio in uscita.
* Sostituisce i separatori nei dati del payload.
* Convalida le proprietà EDI e specifiche del partner.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
  * Convalida estesa eseguita sugli elementi dati del set di transazioni.
* Genera un documento XML per ogni set di transazioni.
* Richiede un riconoscimento tecnico e/o funzionale (se configurata).
  * Come riconoscimento tecnico, il messaggio CONTRL indica la ricezione di un interscambio.
  * Come riconoscimento funzionale, il messaggio CONTRL indica l'accettazione o il rifiuto dell'interscambio, del gruppo o del messaggio ricevuto, con un elenco di errori o funzionalità non supportate.

## Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

<!---HONumber=AcomDC_0824_2016-->
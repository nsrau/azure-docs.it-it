---
title: Informazioni sul connettore Encode X12 Message ed Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare i partner con Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 54626bdb7b07a91e5a2b1d16fed10be6cccacc74


---
# <a name="get-started-with-encode-x12-message"></a>Introduzione a Encode X12 Message
Convalida le proprietà EDI e specifiche del partner, converte i messaggi con codifica XML in set di transazioni EDI nell'interscambio e richiede un riconoscimento tecnico e/o funzionale.

## <a name="create-the-connection"></a>Creare la connessione
### <a name="prerequisites"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Per usare il connettore Encode x12 Message, è necessario un account di integrazione. Vedere i dettagli su come creare un [account di integrazione](app-service-logic-enterprise-integration-create-integration-account.md), [partner](app-service-logic-enterprise-integration-partners.md) e un [contratto X12](app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>Connettersi a Encode X12 Message usando questa procedura:
1. [Creare un'app per la logica](app-service-logic-create-a-logic-app.md) illustra un esempio
2. Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione dell'app per la logica aggiungere un trigger e un'azione.  Selezionare Mostra API gestite da Microsoft nell'elenco a discesa, quindi immettere "x12" nella casella di ricerca.  Selezionare X12 - Encode X12 Message by agreement name o X12 - Encode to X 12 message by identities.  
   
    ![ricerca x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 
3. Se in precedenza non sono state create connessioni all'account di archiviazione, vengono richiesti i dettagli della connessione.
   
    ![connessione all'account di integrazione](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 
4. Immettere i dettagli dell'account di integrazione.  Le proprietà con l'asterisco sono obbligatorie.
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione* |Immettere un nome per la connessione. |
   | Account di integrazione * |Immettere il nome dell'account di integrazione. Assicurarsi che l'account di integrazione e l'app per la logica si trovino nella stessa località di Azure. |
   
    Al termine, i dettagli della connessione saranno simili ai seguenti:
   
    ![connessione all'account di integrazione creata](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 
5. Selezionare **Crea**
6. La connessione è stata creata.
   
    ![dettagli della connessione all'account di integrazione](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12 - Encode X12 Message by agreement name
1. Selezionare il contratto X12 dall'elenco a discesa e il messaggio MXL da codificare.
   
    ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12 - Encode X12 Message by identities
1. Fornire identificatore del mittente e qualificatore del mittente, identificatore del ricevitore e qualificatore del ricevitore come configurati nel contratto X12.  Selezionare il messaggio XML da codificare.
   
   ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>X12 Encode esegue queste operazioni
* Risoluzione del contratto associando le proprietà del contesto di mittente e del destinatario.
* Serializza l'interscambio EDI, conversione dei messaggi con codifica XML in set di transazioni EDI nell'interscambio.
* Si applica ai segmenti di intestazione e finali del set di transazioni.
* Genera un numero di controllo di interscambio, un numero di controllo di gruppo e un numero di controllo del set di transazioni per ogni interscambio in uscita.
* Sostituisce i separatori nei dati del payload.
* Convalida le proprietà EDI e specifiche del partner.
  * Convalida dello schema degli elementi dati del set di transazioni rispetto allo schema del messaggio.
  * Convalida EDI eseguita sugli elementi dati del set di transazioni.
  * Convalida estesa eseguita sugli elementi dati del set di transazioni.
* Richiede un riconoscimento tecnico e/o funzionale (se configurata).
  * Un riconoscimento tecnico viene generato in seguito alla convalida dell'intestazione. Il riconoscimento tecnico segnala lo stato dell'elaborazione di un'intestazione e finale di interscambio in base all'indirizzo del ricevitore.
  * Un riconoscimento funzionale viene generato in seguito alla convalida del corpo. Il riconoscimento funzionale segnala ogni errore rilevato durante l'elaborazione del documento ricevuto.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->



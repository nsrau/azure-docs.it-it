---
title: Aggiungere il connettore Oracle Database in App per la logica di Azure | Microsoft Docs
description: Usare il connettore Oracle Database in un'app per la logica
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: mandia; ladocs
ms.openlocfilehash: 4119e72abd568c9b53245a872691f06329b040de
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-oracle-database-connector"></a>Introduzione al connettore Oracle Database

Usando il connettore Oracle Database, è possibile creare flussi di lavoro dell'organizzazione che usano i dati nel database esistente. Questo connettore può connettersi a un'istanza locale di Oracle Database o a una macchina virtuale di Azure con Oracle Database installato. Con questo connettore è possibile:

* Creare il flusso di lavoro aggiungendo un nuovo cliente in un database di clienti o aggiornando un ordine in un database di ordini.
* Usare le azioni per ottenere una riga di dati, inserire una nuova riga e persino eliminare una riga. Quando ad esempio viene creato un record in Dynamics CRM Online (trigger), è possibile inserire una riga in Oracle Database (azione). 

Questo argomento illustra come usare il connettore Oracle Database in un'app per la logica.

## <a name="prerequisites"></a>prerequisiti

* Versioni di Oracle supportate: 
    * Oracle 9 e versioni successive
    * Software client Oracle 8.1.7 e versioni successive

* Installare il gateway dati locale. Questi passaggi sono illustrati in [Connettersi ai dati locali dalle app per la logica](../logic-apps/logic-apps-gateway-connection.md). Il gateway è necessario per connettersi a un'istanza locale di Oracle Database o a una VM di Azure con Oracle DB installato. 

    > [!NOTE]
    > Il gateway dati locale svolge la funzione di bridge e consente il trasferimento sicuro dei dati tra i dati locali (non nel cloud) e le app per la logica. Lo stesso gateway può essere anche usato con più servizi e più origini dati. Potrebbe quindi essere necessario installare il gateway una sola volta.

* Installare il client Oracle nella macchina in cui è stato installato il gateway dati locale. Assicurarsi di installare il provider di dati Oracle a 64 bit per .NET da Oracle:  

  [ODAC 64 bit 12c versione 4 (12.1.0.2.4) per Windows x64](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Se il client Oracle non è installato, si verifica un errore quando si cerca di creare o usare la connessione. Vedere gli errori comuni in questo argomento.


## <a name="add-the-connector"></a>Aggiungere il connettore

> [!IMPORTANT]
> Questo connettore non include trigger. Ha solo azioni. Quando si crea l'app per la logica, aggiungere quindi un altro trigger per avviare l'app per la logica, ad esempio **Pianificazione - Ricorrenza** o **Richiesta/Risposta - Risposta**. 

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica vuota.

2. All'avvio dell'app per la logica, selezionare il trigger **Richiesta/Risposta - Risposta**: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selezionare **Salva**. Quando si salva, viene generato automaticamente un URL di richiesta. 

4. Selezionare **Nuovo passaggio** e quindi **Aggiungi un'azione**. Digitare `oracle` per visualizzare le azioni disponibili: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Questo è anche il modo più rapido per visualizzare i trigger e le azioni disponibili per i connettori. Digitare parte del nome del connettore, ad esempio `oracle`. La finestra di progettazione elenca i trigger e le azioni. 

5. Selezionare una delle azioni, ad esempio **Oracle Database - Ottieni riga**. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale). Immettere il nome del server Oracle, il metodo di autenticazione, il nome utente e la password e selezionare il gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Una volta stabilita la connessione, selezionare una tabella nell'elenco e immettere l'ID di riga per la tabella. È necessario conoscere l'identificatore della tabella. Se non lo si conosce, contattare l'amministratore di Oracle DB e ottenere l'output di `select * from yourTableName`. In questo modo si otterranno le informazioni di identificazione necessarie per procedere.

    Nell'esempio seguente i dati del processo vengono restituiti da un database delle risorse umane: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Nel passaggio successivo è possibile usare uno qualsiasi degli altri connettori per creare il flusso di lavoro. Se si vuole provare a recuperare i dati da Oracle, inviare a se stessi un messaggio di posta elettronica con i dati di Oracle usando uno dei connettori di invio di posta elettronica, ad esempio Office 365 o Gmail. Usare i token dinamici della tabella Oracle per creare `Subject` e `Body` del messaggio di posta elettronica:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Salvare** l'app per la logica e quindi selezionare **Esegui**. Chiudere la finestra di progettazione ed esaminare lo stato nella cronologia delle esecuzioni. In caso di esito negativo, selezionare la riga relativa al messaggio non inviato. La finestra di progettazione viene aperta e mostra il passaggio non riuscito, con le informazioni sull'errore. In caso di esito positivo, si dovrebbe ricevere un messaggio di posta elettronica con le informazioni aggiunte.


### <a name="workflow-ideas"></a>Idee per i flussi di lavoro

* Si vuole monitorare l'hashtag #oracle e inserire i Tweet in un database in modo che sia possibile eseguire query su di essi e usarli in altre applicazioni. In un'app per la logica aggiungere il trigger `Twitter - When a new tweet is posted` e immettere l'hashtag **#oracle**. Aggiungere quindi l'azione `Oracle Database - Insert row` e selezionare la tabella:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Messaggi inviati a una coda del bus di servizio. Si vuole ottenere questi messaggi e inserirli in un database. In un'app per la logica aggiungere il trigger `Service Bus - when a message is received in a queue` e selezionare la coda. Aggiungere quindi l'azione `Oracle Database - Insert row` e selezionare la tabella:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Errori comuni

#### <a name="error-cannot-reach-the-gateway"></a>**Errore**: Non è possibile raggiungere il gateway

**Causa**: il gateway dati locale non è in grado di connettersi al cloud. 

**Mitigazione**: assicurarsi che il gateway sia in esecuzione nel computer locale in cui è stato installato e che sia in grado di connettersi a Internet.  È consigliabile non installare il gateway in un computer che potrebbe venire spento o andare in sospensione. È anche possibile riavviare il servizio gateway dati locale (PBIEgwService).

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-please-visit-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Errore**: Il provider usato è deprecato: 'System.Data.OracleClient richiede il software client Oracle versione 8.1.7 o versione successiva.' Visitare il sito [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) per installare il provider ufficiale.

**Causa**: Oracle client SDK non è installato nel computer in cui è in esecuzione il gateway dati locale.  

**Risoluzione**: scaricare e installare Oracle client SDK nello stesso computer del gateway dati locale.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Errore**: La tabella '[NomeTabella]' non definisce alcuna colonna chiave

**Causa**: la tabella non ha alcuna chiave primaria.  

**Risoluzione**: il connettore Oracle Database richiede l'uso di una tabella con una colonna chiave primaria.

#### <a name="currently-not-supported"></a>Attualmente non supportati

* Viste e stored procedure 
* Tabelle con chiavi composte
* Tipi di oggetti annidati nelle tabelle
 
## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/oracle/). 

## <a name="get-some-help"></a>Ottenere aiuto

Il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) è il posto ideale per porre domande, fornire risposte e vedere cosa stanno facendo gli altri utenti di App per la logica. 

È possibile migliorare App per la logica e i connettori votando e inviando le idee nella pagina [http://aka.ms/logicapps-wish](http://aka.ms/logicapps-wish). 


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) e scoprire i connettori disponibili in App per la logica nell'[elenco di API](apis-list.md).

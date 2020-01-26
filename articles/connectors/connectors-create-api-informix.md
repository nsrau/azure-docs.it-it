---
title: Connettersi a un database IBM Informix
description: Automatizzare le attività e i flussi di lavoro che gestiscono le risorse archiviate in IBM Informix usando app per la logica di Azure
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76757969"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Gestire le risorse del database IBM Informix usando app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il [connettore Informix](/connectors/informix/)è possibile creare attività e flussi di lavoro automatizzati per la gestione delle risorse in un database IBM Informix. Questo connettore include un client Microsoft che comunica con computer Server Informix remoti attraverso una rete TCP/IP, inclusi i database basati su cloud come IBM Informix per Windows in esecuzione nella virtualizzazione di Azure e i database locali quando si usa il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). È possibile connettersi a queste piattaforme e versioni Informix se sono configurate per supportare connessioni client DRDA (Distributed Relational Database Architecture):

* IBM Informix 12.1
* IBM Informix 11.7

Questo argomento illustra come usare il connettore in un'app per la logica per elaborare operazioni su database.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Per i database locali, [scaricare e installare il gateway dati](../logic-apps/logic-apps-gateway-install.md) locale in un computer locale e quindi [creare una risorsa del gateway dati di Azure nella portale di Azure](../logic-apps/logic-apps-gateway-connection.md).

* App per la logica in cui è necessario accedere al database di Informix. Questo connettore fornisce solo azioni, quindi l'app per la logica deve essere già avviata con un trigger, ad esempio il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Aggiungere un'azione Informix

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Nel passaggio in cui si vuole aggiungere l'azione Informix selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. Selezionare il segno più ( **+** ) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere `informix` come filtro. Nell'elenco azioni selezionare l'azione desiderata, ad esempio:

   ![Selezionare l'azione Informix da eseguire](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Il connettore fornisce queste azioni, che eseguono le operazioni di database corrispondenti:

   * Get tables-list Database Tables by using an `CALL` Statement
   * Ottenere righe: leggere tutte le righe usando un'istruzione `SELECT *`
   * Get Row: legge una riga usando un'istruzione `SELECT WHERE`
   * Aggiungere una riga usando un'istruzione `INSERT`
   * Modificare una riga usando un'istruzione `UPDATE`
   * Eliminare una riga usando un'istruzione `DELETE`

1. Se viene richiesto di specificare i dettagli della connessione per il database Informix, seguire i [passaggi per creare la connessione](#create-connection)e continuare con il passaggio successivo.

1. Fornire le informazioni per l'azione selezionata:

   | Azione | Description | Proprietà e descrizioni |
   |--------|-------------|-----------------------------|
   | **Ottenere tabelle** | Elencare le tabelle di database eseguendo un'istruzione di chiamata Informix. | Nessuno |
   | **Ottieni righe** | Recuperare tutte le righe nella tabella specificata eseguendo un'istruzione `SELECT *` Informix. | **Nome tabella**: il nome della tabella Informix desiderata <p><p>Per aggiungere altre proprietà a questa azione, selezionarle nell'elenco **Aggiungi nuovo parametro** . Per altre informazioni, vedere l' [argomento di riferimento del connettore](/connectors/informix/). |
   | **Ottieni riga** | Eseguire un'istruzione `SELECT WHERE` Informix per recuperare una riga dalla tabella specificata. | **nome tabella**- : il nome della tabella Informix desiderata <br>**ID di riga**- : ID univoco per la riga, ad esempio `9999` |
   | **Inserisci riga** | Aggiungere una riga alla tabella Informix specificata eseguendo un'istruzione `INSERT` Informix. | **nome tabella**- : il nome della tabella Informix desiderata <br>**elemento**- : la riga con i valori da aggiungere |
   | **Aggiorna riga** | Modificare una riga nella tabella Informix specificata eseguendo un'istruzione `UPDATE` Informix. | **nome tabella**- : il nome della tabella Informix desiderata <br>**ID di riga**- : ID univoco della riga da aggiornare, ad esempio `9999` <br>- **Row**: la riga con i valori aggiornati, ad esempio `102` |
   | **Elimina riga** | Rimuovere una riga dalla tabella Informix specificata eseguendo un'istruzione `DELETE` Informix. | **nome tabella**- : il nome della tabella Informix desiderata <br>**ID di riga**- : ID univoco della riga da eliminare, ad esempio `9999` |
   ||||

1. Salvare l'app per la logica. A questo punto, è possibile [testare l'app per la logica](#test-logic-app) o continuare a compilare l'app per la logica.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Connetti a Informix

1. Se l'app per la logica si connette a un database locale, selezionare **Connetti tramite gateway dati locale**.

1. Specificare le informazioni di connessione e quindi selezionare **Crea**.

   | Proprietà | Proprietà JSON | Obbligatorio | Valore di esempio | Description |
   |----------|---------------|----------|---------------|-------------|
   | Nome connessione | `name` | Sì | `informix-demo-connection` | Nome da utilizzare per la connessione al database di Informix |
   | Server | `server` | Sì | -Cloud: `informixdemo.cloudapp.net:9089` <br>-Locale: `informixdemo:9089` | Indirizzo TCP/IP o alias in formato IPv4 o IPv6, seguito da due punti e da un numero di porta TCP/IP |
   | Database | `database` | Sì | `nwind` | Il nome del database relazionale DRDA (RDBNAM) o il nome del database Informix (DBName). Informix accetta una stringa a 128 byte. |
   | Autenticazione | `authentication` | Solo in locale | **Basic** o **Windows** (Kerberos) | Tipo di autenticazione richiesto dal database Informix. Questa proprietà viene visualizzata solo quando si seleziona **Connetti tramite gateway dati locale**. |
   | Username | `username` | No | <*database-nome utente*> | Nome utente del database |
   | Password | `password` | No | <*database-> password* | Una password per il database |
   | Gateway | `gateway` | Solo in locale | -<*Azure-subscription*> <br>-<*Azure-on-premises-Data-Gateway-resource*> | La sottoscrizione di Azure e il nome della risorsa di Azure per il gateway dati locale creato nel portale di Azure. La proprietà e le sottoproprietà del **gateway** vengono visualizzate solo quando si seleziona **Connetti tramite il gateway dati locale**. |
   ||||||

   Ad esempio:

   * **Database cloud**

     ![Informazioni di connessione al database cloud](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Database locale**

     ![Informazioni di connessione al database locale](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Salvare l'app per la logica.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Nella barra degli strumenti di progettazione app per la logica selezionare **Esegui**. Dopo l'esecuzione dell'app per la logica, è possibile visualizzare gli output di tale esecuzione.

1. Scegliere **Panoramica**dal menu dell'app per la logica. Nel riquadro Panoramica, in **riepilogo** > **esecuzioni cronologia**, selezionare l'esecuzione più recente.

1. In **esecuzione App**per la logica selezionare **Dettagli esecuzione**.

1. Nell'elenco azioni selezionare l'azione con gli output che si desidera visualizzare, ad esempio **Get_tables**.

   Se l'azione ha avuto esito positivo, la relativa proprietà **status** è contrassegnata come **succeeded**.

1. Per visualizzare gli input, in **input collegamento**selezionare il collegamento URL. Per visualizzare gli output, in collegamento a **collegamento output** selezionare il collegamento URL. Ecco alcuni output di esempio:

   * **Get_tables** Visualizza un elenco di tabelle:

     ![Output dall'azione "Ottieni tabelle"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** Visualizza un elenco di righe:

     ![Output dall'azione "Ottieni righe"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** Mostra la riga specificata:

     ![Output dall'azione "Ottieni riga"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** Mostra la nuova riga:

     ![Output dall'azione "Inserisci riga"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** Mostra la riga aggiornata:

     ![Output dall'azione "Aggiorna riga"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** Mostra la riga eliminata:

     ![Output dall'azione "Elimina riga"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche su trigger, azioni e limiti, descritti dalla descrizione di spavalderia del connettore, vedere la [pagina di riferimento del connettore](/connectors/informix/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](apis-list.md)

---
title: Connettersi al database IBM Informix
description: Automatizzare le attività e i flussi di lavoro che gestiscono le risorse archiviate in IBM Informix tramite Le app per la logica di AzureAutomate tasks and workflows that manage resources stored in IBM Informix by using Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757969"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Gestire le risorse del database IBM Informix usando le app per la logica di AzureManage IBM Informix database resources by using Azure Logic Apps

Con [Le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e il [connettore Informix](/connectors/informix/), è possibile creare attività e flussi di lavoro automatizzati che gestiscono le risorse in un database IBM Informix. Questo connettore include un client Microsoft che comunica con computer server Informix remoti attraverso una rete TCP/IP, inclusi database basati su cloud, ad esempio IBM Informix per Windows, in esecuzione nella virtualizzazione di Azure e database locali quando si utilizza il [gateway dati locale.](../logic-apps/logic-apps-gateway-connection.md) È possibile connettersi a queste piattaforme e versioni Diformix se sono configurate per supportare le connessioni client DISTRIBUTED Relational Database Architecture (DRDA):

* IBM Informix 12.1
* IBM Informix 11.7

Questo argomento illustra come usare il connettore in un'app per la logica per elaborare operazioni su database.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Per i database locali, [scaricare e installare il gateway dati locale in](../logic-apps/logic-apps-gateway-install.md) un computer locale e quindi creare una risorsa del gateway dati di Azure nel portale di [Azure.](../logic-apps/logic-apps-gateway-connection.md)

* L'app per la logica in cui è necessario accedere al database Informix. Questo connettore fornisce solo azioni, pertanto l'app per la logica deve essere già avviata con un trigger, ad esempio il [trigger Ricorrenza](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Aggiungere un'azione Informix

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Nel passaggio in cui si desidera aggiungere l'azione Informix selezionare **Nuovo passaggio**.

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere `informix` come filtro. Dall'elenco Azioni selezionare l'azione desiderata, ad esempio:

   ![Selezionare l'azione Informix da eseguire](./media/connectors-create-api-informix/select-informix-connector-action.png)

   Il connettore fornisce queste azioni, che eseguono le operazioni di database corrispondenti:The connector provides these actions, which run the corresponding database operations:

   * Ottenere tabelle - Elencare `CALL` le tabelle di database utilizzando un'istruzioneGet tables - List database tables by using a statement
   * Ottenere righe - Leggere tutte `SELECT *` le righe utilizzando un'istruzioneGet rows - Read all rows by using a statement
   * Get row - Leggere una `SELECT WHERE` riga utilizzando un'istruzioneGet row - Read a row by using a statement
   * Aggiungere una riga `INSERT` utilizzando un'istruzioneAdd a row by using an statement
   * Modificare una riga `UPDATE` utilizzando un'istruzione
   * Eliminare una riga `DELETE` utilizzando un'istruzioneDelete a row by using a statement

1. Se viene richiesto di fornire i dettagli della connessione per il database Informix, seguire i [passaggi per creare la connessione](#create-connection)e quindi continuare con il passaggio successivo.

1. Fornire le informazioni per l'azione selezionata:

   | Azione | Descrizione | Proprietà e descrizioni |
   |--------|-------------|-----------------------------|
   | **Ottieni tabelle** | Elencare le tabelle di database eseguendo un'istruzione CALL di Informix. | nessuno |
   | **Ottieni righe** | Recuperare tutte le righe nella tabella specificata `SELECT *` eseguendo un'istruzione Informix. | **Nome tabella**: Il nome della tabella Informix che si desidera <p><p>Per aggiungere altre proprietà a questa azione, selezionarle dall'elenco **Aggiungi nuovo parametro.** Per ulteriori informazioni, vedere [l'argomento di riferimento del connettore.](/connectors/informix/) |
   | **Ottenere la riga** | Ottenere una riga dalla tabella specificata eseguendo `SELECT WHERE` un'istruzione Informix. | - **Nome tabella**: Il nome della tabella Informix che si desidera <br>- **ID riga**: l'ID univoco per la riga, ad esempio,`9999` |
   | **Inserisci riga** | Aggiungere una riga alla tabella Informix specificata `INSERT` eseguendo un'istruzione Informix. | - **Nome tabella**: Il nome della tabella Informix che si desidera <br>- **item**: La riga con i valori da aggiungere |
   | **Aggiorna riga** | Modificare una riga nella tabella Informix specificata `UPDATE` eseguendo un'istruzione Informix. | - **Nome tabella**: Il nome della tabella Informix che si desidera <br>- **ID riga**: l'ID univoco della riga da aggiornare, ad esempio,`9999` <br>- **Riga**: La riga con i valori aggiornati, ad esempio,`102` |
   | **Elimina riga** | Rimuovere una riga dalla tabella Informix specificata `DELETE` eseguendo un'istruzione Informix. | - **Nome tabella**: Il nome della tabella Informix che si desidera <br>- **ID riga**: L'ID univoco della riga da eliminare, ad esempio,`9999` |
   ||||

1. Salvare l'app per la logica. A questo punto, [testare l'app per la logica](#test-logic-app) o continuare a compilare l'app per la logica.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Collegamento a Informix

1. Se l'app per la logica si connette a un database locale, selezionare **Connetti tramite gateway dati locale.**

1. Fornire queste informazioni di connessione e quindi selezionare **Crea**.

   | Proprietà | Proprietà JSON | Obbligatoria | Valore di esempio | Descrizione |
   |----------|---------------|----------|---------------|-------------|
   | Nome connessione | `name` | Sì | `informix-demo-connection` | Il nome da utilizzare per la connessione al database Informix |
   | Server | `server` | Sì | - Nuvola:`informixdemo.cloudapp.net:9089` <br>- Locale:`informixdemo:9089` | L'indirizzo TCP/IP o l'alias in formato IPv4 o IPv6, seguito da due punti e da un numero di porta TCP/IP |
   | Database | `database` | Sì | `nwind` | Il nome del database relazionale DRDA (RDBNAM) o il nome del database Informix (dbname). Informix accetta una stringa di 128 byte. |
   | Authentication | `authentication` | Solo locale | **Di base** o **Windows** (kerberos) | Tipo di autenticazione richiesto dal database Informix. Questa proprietà viene visualizzata solo quando si seleziona **Connetti tramite gateway dati locale**. |
   | Username | `username` | No | <*database-nome utente*> | Un nome utente per il database |
   | Password | `password` | No | <*database-password*> | Una password per il database |
   | Gateway | `gateway` | Solo locale | - <*sottoscrizione di Azure-create a Azure-subscription*> <br>- <*Azure-on-premises-data-gateway-resource*> | La sottoscrizione di Azure e il nome della risorsa di Azure per il gateway dati locale creato nel portale di Azure.The Azure subscription and Azure resource name for the on-premises data gateway that you created in the Azure portal. Le proprietà e le proprietà secondarie **del gateway** vengono visualizzate solo quando si seleziona **Connetti tramite gateway dati locale**. |
   ||||||

   Ad esempio:

   * **Database cloud**

     ![Informazioni sulla connessione al database cloud](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **Database locale**

     ![Informazioni sulla connessione al database locale](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Salvare l'app per la logica.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Nella barra degli strumenti di Progettazione app per la logica selezionare **Esegui**. Dopo l'esecuzione dell'app per la logica, è possibile visualizzare gli output dell'esecuzione.

1. Dal menu dell'app per la logica selezionare **Panoramica**. Nel riquadro Panoramica, in**Cronologia esecuzioni** **di riepilogo,** > selezionare l'esecuzione più recente.

1. In **Esecuzione app per la logica**selezionare Dettagli **esecuzione**.

1. Nell'elenco delle azioni selezionare l'azione con gli output che si desidera visualizzare, ad esempio **Get_tables**.

   Se l'azione ha esito positivo, la relativa proprietà **Status** viene contrassegnata come **Succeeded**.

1. Per visualizzare gli input, in **Collegamento input**selezionare il collegamento URL. Per visualizzare gli output, in **Collegamento output** selezionare il collegamento URL. Ecco alcuni output di esempio:Here are some example outputs:

   * **Get_tables** mostra un elenco di tabelle:

     ![Output dall'azione "Ottieni tabelle"](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** mostra un elenco di righe:

     ![Output dall'azione "Ottieni righe"](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** mostra la riga specificata:

     ![Output dall'azione "Ottieni riga"](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** mostra la nuova riga:

     ![Output dall'azione "Inserisci riga"](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** mostra la riga aggiornata:

     ![Output dall'azione "Aggiorna riga"](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** mostra la riga eliminata:

     ![Output dall'azione "Elimina riga"](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti descritti dalla descrizione swagger del connettore, consultare la pagina di [riferimento del connettore.](/connectors/informix/)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](apis-list.md)

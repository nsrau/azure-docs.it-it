---
title: Avvisi e notifiche di installazione per Istanza gestita (portale di Azure)
description: Usare il portale di Azure per creare avvisi SQL Istanza gestita, che possono attivare notifiche o automazione quando vengono soddisfatte le condizioni specificate.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: ae139dd65242be9456f3498c494e1a7c5a29402f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695695"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Creare avvisi per Istanza gestita SQL di Azure usando il portale di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra come configurare gli avvisi per i database nel database SQL di Azure Istanza gestita usando il portale di Azure. Gli avvisi possono inviare un messaggio di posta elettronica, chiamare un webhook, eseguire funzioni di Azure, Runbook, chiamare un sistema di ticket compatibile con ITSM esterno, chiamare il telefono o inviare un SMS quando alcune metriche, ad esempio le dimensioni di archiviazione dell'istanza o l'utilizzo della CPU, raggiungono una soglia predefinita. Questo articolo include anche le procedure consigliate per impostare i periodi di avviso.


## <a name="overview"></a>Panoramica

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metrici** : l'avviso si attiva quando il valore di una metrica specificata supera una soglia assegnata in entrambe le direzioni. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.

È possibile configurare un avviso affinché esegua le operazioni seguenti al momento dell'attivazione:

* Inviare notifiche tramite posta elettronica all'amministratore del servizio e ai coamministratori
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiama un numero di telefono con la richiesta vocale
* Invia SMS a un numero di telefono
* Chiamare un webhook
* Chiamare la funzione di Azure
* Chiama Azure Runbook
* Chiamare un sistema compatibile con ITSM con ticket esterno

È possibile configurare e ottenere informazioni sulle regole di avviso usando [il portale di Azure, PowerShell o l'interfaccia della](../../azure-monitor/platform/alerts-classic-portal.md) riga di comando di Azure o l' [API REST di monitoraggio di Azure](/rest/api/monitor/alertrules). 

## <a name="alerting-metrics-available-for-managed-instance"></a>Metriche di avviso disponibili per l'istanza gestita

> [!IMPORTANT]
> Le metriche di avviso sono disponibili solo per l'istanza gestita. Le metriche di avviso per i singoli database nell'istanza gestita non sono disponibili. I dati di telemetria di diagnostica del database sono d'altra parte disponibili sotto forma di [log di diagnostica](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export). Gli avvisi nei log di diagnostica possono essere impostati dall'interno del prodotto [SQL Analytics](../../azure-monitor/insights/azure-sql.md) usando gli [script di avviso del log](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) per l'istanza gestita.

Per la configurazione degli avvisi sono disponibili le metriche dell'istanza gestita seguenti:

| Metrica | Descrizione | Unità di misura \ valori possibili |
| :--------- | --------------------- | ----------- |
| Percentuale CPU Media | Percentuale media di utilizzo della CPU nel periodo di tempo selezionato. | 0-100 (percentuale) |
| Byte di I/O letti | Byte IO letti nel periodo di tempo selezionato. | Byte |
| Byte di I/O scritti | Byte i/o scritti nel periodo di tempo selezionato. | Byte |
| Numero di richieste I/O | Numero di richieste IO nel periodo di tempo selezionato. | Numerico |
| Spazio di archiviazione riservato | Numero massimo corrente spazio di archiviazione riservato per l'istanza gestita. Modifiche con l'operazione di ridimensionamento delle risorse. | MB (megabyte) |
| Spazio di archiviazione usato | Spazio di archiviazione utilizzato nel periodo selezionato. Modifiche con utilizzo di archiviazione da parte dei database e dell'istanza di. | MB (megabyte) |
| Numero di core virtuali | Vcore di cui è stato effettuato il provisioning per l'istanza gestita. Modifiche con l'operazione di ridimensionamento delle risorse. | 4-80 (VCore) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creare una regola di avviso in base a una metrica con il portale di Azure

1. Nel [portale](https://portal.azure.com/)di Azure individuare l'istanza gestita di cui si è interessati per il monitoraggio e selezionarla.

2. Selezionare la voce di menu **metrica** nella sezione monitoraggio.

   ![Monitoraggio](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. Nel menu a discesa selezionare una delle metriche su cui si vuole configurare l'avviso. nell'esempio viene visualizzato lo spazio di archiviazione usato.

4. Selezionare il periodo di aggregazione: media, minima o massima raggiunta nel periodo di tempo specificato (AVG, min o max). 

5. Seleziona **nuova regola di avviso**

6. Nel riquadro Crea regola di avviso fare clic sul **nome della condizione** . nell'esempio viene visualizzato lo spazio di archiviazione usato.

   ![Definire la condizione](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. Nel riquadro Configura logica del segnale, Definisci operatore, tipo di aggregazione e valore soglia

   * Le opzioni del tipo di operatore sono maggiori di, uguali e minori di (valore soglia)
   * Le opzioni del tipo di aggregazione sono min, Max o Average (nel periodo di granularità dell'aggregazione)
   * Il valore di soglia è il valore di avviso che verrà valutato in base ai criteri di operatore e di aggregazione
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   Nell'esempio illustrato nella schermata viene usato il valore 1840876 MB che rappresenta un valore soglia di 1,8 TB. Poiché l'operatore nell'esempio è impostato su un valore maggiore di, l'avviso verrà creato se il consumo di spazio di archiviazione nell'istanza gestita supera 1,8 TB. Si noti che il valore soglia per le metriche dello spazio di archiviazione deve essere espresso in MB.

8. Impostare il periodo di valutazione, ovvero la granularità delle aggregazioni in minuti e la frequenza di valutazione. La frequenza di valutazione indica il momento in cui il sistema di avvisi verificherà periodicamente se è stata soddisfatta la condizione di soglia.

9. Selezionare il gruppo di azioni. Il riquadro gruppo di azioni verrà visualizzato attraverso il quale sarà possibile selezionare un esistente o creare una nuova azione. Questa azione definisce la causa dell'attivazione di un avviso, ad esempio l'invio di un messaggio di posta elettronica, la chiamata al telefono, l'esecuzione di un webhook, di una funzione di Azure o di un Runbook, ad esempio.

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Per creare un nuovo gruppo di azione, selezionare **+ Crea gruppo di azione**

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Definire come si desidera ricevere avvisi: immettere il nome del gruppo di azioni, il nome breve, il nome dell'azione e il tipo di azione SELECT. Il tipo di azione definisce se verrà inviata una notifica tramite posta elettronica, SMS, chiamata vocale o, se possibile, webhook, funzione di Azure, Runbook verrà eseguito oppure il ticket ITSM verrà creato nel sistema compatibile.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Inserire i dettagli della regola di avviso per i record, quindi selezionare il tipo di gravità.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Per completare la creazione della regola di avviso, fare clic sul pulsante **Crea regola di avviso** .

La nuova regola di avviso diventerà attiva entro pochi minuti e verrà attivata in base alle impostazioni.

## <a name="verifying-alerts"></a>Verifica degli avvisi

> [!NOTE]
> Per eliminare gli avvisi fastidiosi, vedere [Supression degli avvisi con le regole di azione](../../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts).

Quando si configura una regola di avviso, verificare che il trigger di avviso e la relativa frequenza siano soddisfatti. Per l'esempio illustrato in questa pagina per la configurazione di un avviso per lo spazio di archiviazione usato, se l'opzione di avviso è stata posta elettronica, è possibile ricevere un messaggio di posta elettronica simile a quello riportato di seguito.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

Il messaggio di posta elettronica indica il nome dell'avviso, i dettagli della soglia e il motivo per cui l'avviso è stato attivato, per consentire la verifica e la risoluzione dei problemi dell'avviso. Per visualizzare gli avvisi ricevuti tramite posta elettronica portale di Azure, è possibile usare il pulsante **Visualizza in portale di Azure** . 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Visualizzare, sospendere, attivare, modificare ed eliminare le regole di avviso esistenti

> [!NOTE]
> Gli avvisi esistenti devono essere gestiti dal menu avvisi da portale di Azure Dashboard. Gli avvisi esistenti non possono essere modificati dal pannello Istanza gestita risorsa.

Per visualizzare, sospendere, attivare, modificare ed eliminare gli avvisi esistenti:

1. Cerca gli avvisi usando portale di Azure ricerca. Fare clic su avvisi.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   In alternativa, è anche possibile fare clic su avvisi sulla barra di spostamento di Azure, se è stato configurato.

2. Nel riquadro avvisi selezionare Gestisci regole di avviso.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   Viene visualizzato un elenco di avvisi esistenti. Selezionare una singola regola di avviso esistente per gestirla. Le regole attive esistenti possono essere modificate e ottimizzate per le proprie preferenze. Le regole attive possono anche essere sospese senza essere eliminate. 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul sistema di avvisi di monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure](../../azure-monitor/platform/alerts-overview.md)
* Per altre informazioni sugli avvisi delle metriche, vedere informazioni sul [funzionamento degli avvisi delle metriche in monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-overview.md)
* Per informazioni sulla configurazione di un webhook negli avvisi, vedere [chiamare un webhook con un avviso di metrica classico](../../azure-monitor/platform/alerts-webhooks.md)
* Per informazioni sulla configurazione e la gestione degli avvisi con PowerShell, vedere [regole di azione](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)
* Per informazioni sulla configurazione e la gestione degli avvisi, vedere informazioni di [riferimento sull'API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/) 

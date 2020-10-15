---
title: Configurare gli avvisi delle metriche-portale di Azure-database di Azure per MySQL-server flessibile
description: Questo articolo descrive come configurare e accedere agli avvisi delle metriche per il server flessibile database di Azure per MySQL dalla portale di Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: d4385ccda665e9acd2d2f9fd340e675b8a9dfe6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940518"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Usare il portale di Azure per configurare gli avvisi relativi alle metriche per database di Azure per MySQL-server flessibili 

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo mostra come configurare avvisi di Database di Azure per MySQL usando il portale di Azure. È possibile ricevere un avviso in base alle metriche di monitoraggio per i servizi di Azure.

L'avviso viene attivato quando il valore di una metrica specifica supera una soglia assegnata. L'avviso viene attivato sia quando la condizione viene inizialmente soddisfatta che successivamente, quando tale condizione non è più soddisfatta. Gli avvisi delle metriche sono avvisi con stato, ovvero inviano notifiche solo quando lo stato cambia.

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Azure portal](../../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfaccia della riga di comando di Azure](../../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure
1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server di database di Azure per MySQL flessibile che si vuole monitorare.
2. Nella sezione **monitoraggio** della barra laterale selezionare **avvisi**.
3. Selezionare **+ Nuova regola di avviso**.
4. Viene visualizzata la pagina **Crea regola**. Specificare le informazioni necessarie:
5. Nella sezione **condizione** scegliere **Seleziona condizione**.
6. Viene visualizzato un elenco di segnali supportati, selezionare la metrica su cui si vuole creare un avviso. Ad esempio, selezionare "percentuale archiviazione".
7. Viene visualizzato un grafico per la metrica per le ultime sei ore. Utilizzare l'elenco a discesa **periodo grafico** per selezionare la cronologia più lunga per la metrica.
8. Selezionare il tipo di **soglia** (ad esempio **Operatore** "static" o "Dynamic"), ad esempio "Maggiore di") e **tipo di aggregazione** (ad esempio Media). In questo modo verrà determinata la logica che verrà valutata dalla regola di avviso della metrica.
    - Se si usa una soglia **statica** , continuare a definire un **valore soglia** (ad esempio, 85%). Il grafico delle metriche può essere utile per determinare quale sia una soglia ragionevole.
    - Se si usa una soglia **dinamica** , continuare a definire la **sensibilità della soglia**. Il grafico delle metriche mostrerà le soglie calcolate in base ai dati recenti. [Altre informazioni sulle opzioni di sensibilità e i tipi di condizione delle soglie dinamiche](../../azure-monitor/platform/alerts-dynamic-thresholds.md).
9. Perfezionare la condizione modificando l'intervallo di **granularità (periodo) di aggregazione** su cui vengono raggruppati i punti dati utilizzando la funzione del tipo di aggregazione (ad esempio, "30 minuti") e **frequenza** (ad esempio "ogni 15 minuti").
10. Fare clic su **Fine**.
11. Aggiungere un gruppo di azione. Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Nella sezione **gruppi di azione** scegliere **Seleziona gruppo di azioni** per selezionare un gruppo di azioni già esistente da allineare alla regola di avviso.
12. È anche possibile creare un nuovo gruppo di azioni per ricevere le notifiche relative all'avviso. Per ulteriori informazioni, vedere [creare e gestire il gruppo di azioni](../../azure-monitor/platform/action-groups.md) .
13. Per creare un nuovo gruppo di azioni, scegliere **+ Crea gruppo di azioni**. Compilare il modulo "Crea gruppo di azioni" con una **sottoscrizione**, un **gruppo di risorse**, il nome del gruppo di **azioni** e il **nome visualizzato**.
14. Configurare le **notifiche** per il gruppo di azioni.
    
    In **tipo di notifica**scegliere "posta elettronica Azure Resource Manager ruolo" per selezionare i proprietari, i collaboratori e i lettori della sottoscrizione per ricevere le notifiche. Scegliere il **ruolo Azure Resource Manager** per l'invio del messaggio di posta elettronica.
    È anche possibile scegliere **messaggio di posta elettronica/SMS/push/Voice** per inviare notifiche a destinatari specifici.

    Specificare **nome** per il tipo di notifica e selezionare **Verifica + crea** al termine.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Specificare **i dettagli della regola** di avviso, ad esempio **Nome regola di avviso**, **Descrizione**, **Salva regola di avviso in gruppo di risorse** e **gravità**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Selezionare **Crea regola di avviso** per creare l'avviso.
    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.
## <a name="manage-your-alerts"></a>Gestire gli avvisi
Dopo aver creato un avviso, è possibile selezionarlo ed eseguire queste azioni:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull' [impostazione dell'avviso per le metriche](../../azure-monitor/platform/alerts-metric.md).
- Scopri di più sulle [metriche disponibili nel database di Azure per il server flessibile MySQL](./concepts-monitoring.md).
- [Informazioni sul funzionamento degli avvisi delle metriche in monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-overview.md)

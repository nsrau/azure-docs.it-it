---
title: Risolvere i problemi delle modifiche in una macchina virtuale di Azure | Microsoft Docs
description: Usare Rilevamento modifiche per risolvere i problemi relativi alle modifiche in una macchina virtuale di Azure.
services: automation
keywords: modifica, rilevamento, automazione
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/28/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f0af493036740b854609cea07e01136aac808579
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="troubleshoot-changes-in-your-environment"></a>Risolvere i problemi delle modifiche nell'ambiente

Questa esercitazione illustra come risolvere i problemi relativi alle modifiche in una macchina virtuale di Azure. Abilitando il rilevamento delle modifiche è possibile tenere traccia delle modifiche apportate a software, file, daemon Linux, servizi di Windows e chiavi del Registro di sistema di Windows presenti nei computer.
Identificando le modifiche apportate alla configurazione è possibile localizzare eventuali problemi operativi nell'ambiente.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Caricare una VM per Rilevamento modifiche e inventario
> * Cercare i servizi arrestati nei log di modifica
> * Configurare il rilevamento delle modifiche
> * Abilitare la connessione al log attività
> * Attivare un evento
> * Visualizzare le modifiche

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un [account di automazione](automation-offering-get-started.md) per contenere i runbook watcher e azione e l'attività watcher.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare Rilevamento modifiche e inventario

Per questa esercitazione, prima di tutto è necessario abilitare Rilevamento modifiche e inventario per la VM. Se è già stata abilitata un'altra soluzione di automazione per una VM, questo passaggio non è necessario.

1. Nel menu a sinistra selezionare **Macchine virtuali** e selezionare una macchina virtuale dall'elenco
1. Nel menu a sinistra fare clic su **Inventario** nella sezione **OPERAZIONI**. Verrà visualizzata la pagina **Rilevamento modifiche**.

![Abilitare Rilevamento modifiche](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) Verrà visualizzata la schermata **Rilevamento modifiche**. Configurare la posizione, l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione.

L'area di lavoro di [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Inventario.
L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.

Durante l'onboarding, il provisioning della macchina virtuale viene eseguito con Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido.
L'agente consente di comunicare con la macchina virtuale e ottenere informazioni sul software installato.

L'abilitazione della soluzione può richiedere fino a 15 minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser.
Dopo l'abilitazione della soluzione, le informazioni sul software installato e sulle modifiche nella macchina virtuale passano a Log Analytics.
Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

## <a name="using-change-tracking-in-log-analytics"></a>Uso di Rilevamento modifiche in Log Analytics

Rilevamento modifiche genera dati di log che vengono inviati a Log Analytics. Per eseguire ricerche nei log tramite l'esecuzione di query, selezionare **Log Analytics** nella parte superiore della finestra **Rilevamento modifiche**.
I dati di Rilevamento modifiche vengono archiviati sotto il tipo **ConfigurationChange**. L'esempio di query di Log Analytics seguente restituisce tutti i servizi di Windows arrestati.

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Per altre informazioni sull'esecuzione e sulla ricerca nei file di log in Log Analytics, vedere [Azure Log Analytics](https://docs.loganalytics.io/index).

## <a name="configure-change-tracking"></a>Configurare Rilevamento modifiche

Rilevamento modifiche consente di tenere traccia delle modifiche apportate alla configurazione nella macchina virtuale. I passaggi seguenti illustrano come configurare il rilevamento di chiavi del Registro di sistema e file.
 
Per scegliere i file e le chiavi del Registro di sistema di cui tenere traccia, selezionare **Modifica impostazioni** nella parte superiore della pagina **Rilevamento modifiche**.

> [!NOTE]
> Inventario e Rilevamento modifiche usano le stesse impostazioni di raccolta e vengono configurati a livello di area di lavoro.

Nella finestra **Configurazione dell'area di lavoro** aggiungere le chiavi del Registro di sistema di Windows, i file di Windows o Linux da rilevare, come descritto nelle tre sezioni successive.

### <a name="add-a-windows-registry-key"></a>Aggiungere una chiave del Registro di sistema di Windows

1. Nella scheda **Registro di sistema di Windows** selezionare **Aggiungi**.
    Verrà visualizzata la finestra **Aggiungi Registro di sistema di Windows per Rilevamento modifiche**.

3. In **Aggiungi Registro di sistema di Windows per Rilevamento modifiche** immettere le informazioni per la chiave da rilevare e fare clic su **Salva**

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Item Name     | Nome descrittivo del file da rilevare        |
|Group     | Nome del gruppo per il raggruppamento logico dei file        |
|Chiave del Registro di sistema di Windows   | Percorso in cui cercare il file, ad esempio "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Aggiungere un file di Windows

1. Nella scheda **File Windows** selezionare **Aggiungi**. Verrà visualizzata la finestra **Aggiungi file Windows per Rilevamento modifiche**.

1. In **Aggiungi file Windows per Rilevamento modifiche** immettere le informazioni per il file o la directory da rilevare e fare clic su **Salva**

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Item Name     | Nome descrittivo del file da rilevare        |
|Group     | Nome del gruppo per il raggruppamento logico dei file        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio "c:\temp\myfile.txt"       |

### <a name="add-a-linux-file"></a>Aggiungere un file di Linux

1. Nella scheda **File Linux** selezionare **Aggiungi**. Verrà visualizzata la finestra **Aggiungi file Linux per Rilevamento modifiche**.

1. In **Aggiungi file Linux per Rilevamento modifiche** immettere le informazioni per il file o la directory da rilevare e fare clic su **Salva**

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Item Name     | Nome descrittivo del file da rilevare        |
|Group     | Nome del gruppo per il raggruppamento logico dei file        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio "/etc/*.conf"       |
|Tipo di percorso     | Tipo di elemento da rilevare. I valori possibili sono File e Directory        |
|Ricorsione     | Determina se viene usata la ricorsione per la ricerca dell'elemento da rilevare.        |
|Usa Sudo     | Questa impostazione determina se viene usato sudo per la ricerca dell'elemento.         |
|Collegamenti     | Questa impostazione determina come vengono gestiti i collegamenti simbolici durante l'attraversamento delle directory.<br> **Ignora**: ignora i collegamenti simbolici e non include i file e le directory a cui viene fatto riferimento<br>**Segui**: segue i collegamenti simbolici durante la ricorsione e include anche i file e le directory a cui viene fatto riferimento<br>**Gestisci**: segue i collegamenti simbolici e consente la modifica del trattamento del contenuto restituito      |

   > [!NOTE]   
   > Questa opzione dei collegamenti "Gestisci" non è consigliata. Il recupero del contenuto del file non è supportato.

## <a name="enable-activity-log-connection"></a>Abilitare la connessione al log attività

Dalla pagina **Rilevamento modifiche** della VM, selezionare **Gestisci connessione al log attività**. Si aprirà la pagina **Log attività di Azure**. Selezionare **Connetti** per connettere Rilevamento modifiche al log attività di Azure per la VM.

Con questa impostazione abilitata, passare alla pagina **Panoramica** della VM e selezionare **Arresta** per arrestare la macchina virtuale. Quando richiesto, selezionare **Sì** per arrestare la VM. Quando la VM è deallocata, selezionare **Avvia** per riavviarla.

Con l'arresto e l'avvio di una macchina virtuale viene registrato un evento nel registro attività. Tornare alla pagina **Rilevamento modifiche**. Selezionare la scheda **Eventi** nella parte inferiore della pagina. Dopo poco, gli eventi verranno visualizzati nel grafico e nella tabella. Come nel passaggio precedente, è possibile selezionare ogni evento per visualizzare le relative informazioni dettagliate.

![Visualizzazione dei dettagli delle modifiche nel portale](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Visualizzare le modifiche

Dopo avere abilitato la soluzione Rilevamento modifiche e inventario, è possibile visualizzare i risultati nella pagina **Rilevamento modifiche**.

Dalla VM selezionare **Rilevamento modifiche** in **OPERAZIONI**.

![Schermata che mostra l'elenco delle modifiche alla VM](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

Il grafico mostra le modifiche nel tempo.
Dopo aver aggiunto una connessione al log attività, il grafico a linee nella parte superiore visualizza gli eventi del log attività di Azure.
Ogni riga dei grafici a barre rappresenta un tipo di modifica tracciabile diverso.
I tipi sono daemon Linux, file, chiavi del Registro di sistema di Windows, software e servizi di Windows.
La scheda delle modifiche visualizza i dettagli delle modifiche in ordine decrescente in base alla data della modifica, a partire dalla più recente.
La tabella della scheda **Eventi** visualizza gli eventi del log attività connesso e i relativi dettagli, a partire dall'evento più recente.

Nei risultati è possibile vedere che sono state apportate più modifiche al sistema, incluse modifiche ai servizi e al software. È possibile usare i filtri nella parte superiore della pagina per filtrare i risultati per **tipo di modifica** o in base a un intervallo di tempo.

Selezionare una modifica **WindowsServices** per aprire la finestra **Dettagli modifiche**. La finestra dei dettagli visualizza informazioni dettagliate sulla modifica e i valori prima e dopo la modifica stessa. In questo caso è stato arrestato il servizio di protezione software.

![Visualizzazione dei dettagli delle modifiche nel portale](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Caricare una VM per Rilevamento modifiche e inventario
> * Cercare i servizi arrestati nei log di modifica
> * Configurare il rilevamento delle modifiche
> * Abilitare la connessione al log attività
> * Attivare un evento
> * Visualizzare le modifiche

Per altre informazioni, passare alla panoramica della soluzione Rilevamento modifiche e inventario.

> [!div class="nextstepaction"]
> [Soluzione di gestione del cambiamento e inventario](automation-change-tracking.md)

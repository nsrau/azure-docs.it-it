---
title: Monitorare gli eventi pianificati per le macchine virtuali Windows in Azure
description: Informazioni su come monitorare gli eventi pianificati delle macchine virtuali di Azure.
author: mysarn
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 0d1edde5ac1b83feab458eb5d12d524163d3ffb1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483301"
---
# <a name="monitoring-scheduled-events"></a>Monitoraggio di Eventi pianificati

Ogni giorno, vengono applicati aggiornamenti a diverse parti di Azure per mantenere i servizi in esecuzione sempre sicuri e aggiornati. Oltre agli aggiornamenti pianificati, possono verificarsi anche eventi non pianificati. Ad esempio, se vengono rilevati errori o riduzioni nelle prestazioni hardware, i servizi di Azure potrebbero eseguire una manutenzione non pianificata. Usando la migrazione in tempo reale, con utilizzo limitato di memoria da parte degli aggiornamenti e in generare limitandone l'impatto, nella maggior parte dei casi questi eventi sono quasi trasparenti e non hanno alcun effetto per i clienti o, al massimo causano, alcuni secondi di blocco della macchina virtuale. Tuttavia, per alcune applicazioni, anche alcuni secondi di blocco della macchina virtuale potrebbero avere un impatto negativo. Per garantire la migliore esperienza per le applicazioni, è utile conoscere con anticipo la tempistica delle operazioni di manutenzione previste. Il [servizio Eventi pianificati](scheduled-events.md) offre un'interfaccia programmatica che consente di ricevere notifiche sugli eventi di manutenzione imminenti e consente di gestirli in modo appropriato. 

Questo articolo illustra come usare il servizio Eventi pianificati per ricevere notifiche sugli eventi di manutenzione che potrebbero influire sulle macchine virtuali e per creare un'automazione di base utile per il monitoraggio e l'analisi.


## <a name="routing-scheduled-events-to-log-analytics"></a>Routing di Eventi pianificati a Log Analytics

Eventi pianificati è disponibile come parte del [servizio metadati dell'istanza di Azure](instance-metadata-service.md), presente in ogni macchina virtuale di Azure. I clienti possono scrivere l'automazione per eseguire query sull'endpoint delle macchine virtuali, individuare le notifiche di manutenzione pianificata e impostare delle mitigazioni, ad esempio il salvataggio dello stato o l'esclusione della macchina virtuale. È consigliabile creare l'automazione per registrare gli eventi pianificati in modo da disporre di un log di controllo degli eventi di manutenzione di Azure. 

In questo articolo verrà illustrato come acquisire gli eventi pianificati di manutenzione per Log Analytics. Quindi, verranno attivate alcune azioni di notifica di base, come l'invio di email al team e la visualizzazione cronologica di tutti gli eventi che hanno interessato le macchine virtuali. Per l'aggregazione e l'automazione degli eventi viene usato [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md), ma è possibile usare qualsiasi soluzione di monitoraggio per la raccolta dei log e attivazione dell'automazione.

![Diagramma che mostra il ciclo di vita dell'evento](./media/notifications/events.png)

## <a name="prerequisites"></a>Prerequisiti

Ai fini di questo esempio è necessario creare una [macchina virtuale Windows in un set di disponibilità](tutorial-availability-sets.md). Eventi pianificati fornisce notifiche sulle modifiche che possono influire su qualsiasi macchina virtuale in set di disponibilità, servizio cloud, set di scalabilità di macchine virtuali o in macchine virtuali autonome. Nell'esempio, viene eseguito un [servizio](https://github.com/microsoft/AzureScheduledEventsService) per il polling degli eventi pianificati in una delle macchine virtuali che opera come agente di raccolta per ottenere gli eventi per tutte le altre macchine virtuali del set di disponibilità.    

Non eliminare il gruppo di risorse di gruppo alla fine dell'esercitazione.

Sarà anche necessario [creare un'area di lavoro di Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) da usare per aggregare le informazioni delle macchine virtuali del set di disponibilità.

## <a name="set-up-the-environment"></a>Configurare l'ambiente

A questo punto dovrebbero essere presenti due macchine virtuali iniziali in un set di disponibilità. Ora è necessario creare nello stesso set di disponibilità una terza macchina virtuale denominata myCollectorVM. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Scaricare il file zip di installazione del progetto da [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Connettersi a **myCollectorVM**, copiare il file ZIP nella macchina virtuale ed estrarre tutti i file. Nella macchina virtuale aprire un prompt di PowerShell. Spostare il prompt nella cartella contenente `SchService.ps1`, ad esempio `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`, e configurare il servizio.

```powershell
.\SchService.ps1 -Setup
```

Avviare il servizio.

```powershell
.\SchService.ps1 -Start
```

A questo punto, il servizio avvia il polling ogni 10 secondi per tutti gli eventi pianificati e approva gli eventi per velocizzare la manutenzione.  Blocchi, riavvii, ridistribuzioni e interruzioni sono gli eventi acquisiti da Eventi pianificati.   Si noti che è possibile estendere lo script per attivare alcune mitigazioni prima di approvare l'evento.

Convalidare lo stato del servizio e verificare che sia in esecuzione.

```powershell
.\SchService.ps1 -status  
```

Il valore restituito dovrebbe essere `Running`.

A questo punto, il servizio avvia il polling ogni 10 secondi per tutti gli eventi pianificati e approva gli eventi per velocizzare la manutenzione.  Blocchi, riavvii, ridistribuzioni e interruzioni sono gli eventi acquisiti da Eventi pianificati. È possibile estendere lo script per attivare alcune mitigazioni prima di approvare l'evento.

Quando uno qualsiasi degli eventi di cui sopra viene acquisito dal servizio Eventi pianificati, viene registrato nel log eventi dell'applicazione con informazioni su stato, tipo di evento, risorse (nomi delle macchine virtuali) e valore NotBefore (periodo di preavviso minimo). Nel log eventi dell'applicazione gli eventi, è possibile individuare con ID 1234.

Una volta configurato e avviato il servizio, gli eventi vengono registrati nel registro applicazioni di Windows.   Per verificarne il funzionamento, riavviare una delle macchine virtuali del set di disponibilità. Nel Visualizzatore eventi, si dovrebbe visualizzare la registrazione di un evento nei registri di Windows > Registro applicazioni che mostra la macchina virtuale riavviata. 

![Screenshot del Visualizzatore eventi.](./media/notifications/event-viewer.png)

Quando gli eventi vengono acquisiti dal servizio Eventi pianificati, vengono registrati nel log eventi dell'applicazione con informazioni su stato, tipo di evento, risorse (nomi delle macchine virtuali) e valore NotBefore (periodo di preavviso minimo). Nel log eventi dell'applicazione gli eventi, è possibile individuare con ID 1234.

> [!NOTE] 
> In questo esempio, le macchine virtuali si trovano in un set di disponibilità, questo ha consentito di designare una singola macchina virtuale come agente di raccolta per ascoltare ed effettuare il routing degli eventi pianificati all'area di lavoro Log Analytics. Se si dispone di macchine virtuali autonome, è possibile eseguire il servizio in ogni macchina virtuale e quindi connetterle singolarmente all'area di lavoro Log Analytics.
>
> Per la configurazione di esempio, è stato scelto Windows, ma è possibile progettare una soluzione simile in Linux.

È possibile arrestare/rimuovere il servizio Eventi pianificati in qualsiasi momento usando le opzioni `–stop` e `–remove`.

## <a name="connect-to-the-workspace"></a>Connettersi all'area di lavoro


A questo punto si connette un'area di lavoro Log Analytics alla macchina virtuale dell'agente di raccolta. L'area di lavoro Log Analytics opera come repository e verrà configurata la raccolta dei registri eventi per acquisire i registri applicazioni dalla macchina virtuale dell'agente di raccolta. 

 Per il routing di Eventi pianificati al registro eventi, che viene salvato come registro applicazioni dal servizio, è necessario connettere la macchina virtuale all'area di lavoro Log Analytics.  
 
1. Aprire la pagina dell'area di lavoro creata.
1. In **Connettersi a un'origine dati** selezionare **macchine virtuali di Azure**.

    ![Connettersi a una macchina virtuale come origine dati](./media/notifications/connect-to-data-source.png)

1. Cercare e selezionare **myCollectorVM**. 
1. Nella nuova pagina **myCollectorVM** selezionare **Connetti**.

Viene installato [Microsoft Monitoring Agent](../extensions/oms-windows.md) nella macchina virtuale. La connessione della macchina virtuale all'area di lavoro e l'installazione dell'estensione richiedono alcuni minuti. 

## <a name="configure-the-workspace"></a>Configurare l'area di lavoro

1. Aprire la pagina per l'area di lavoro e selezionare le **impostazioni avanzate**.
1. Selezionare **Dati** e quindi selezionare **Log eventi Windows** dal menu a sinistra.
1. In **Raccogli eventi dai log eventi** seguenti iniziare a digitare *applicazione* e quindi selezionare **Applicazione** dall'elenco.

    ![Selezionare le impostazioni avanzate](./media/notifications/advanced.png)

1. Lasciare selezionati **ERRORE**, **AVVISO** e **INFORMAZIONI** e quindi selezionare **Salva** per salvare le impostazioni.


> [!NOTE]
> Si verificherà un ritardo e potrebbero essere necessari fino a 10 minuti prima che il log sia disponibile. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Creare di una regola di avviso con Monitoraggio di Azure 


Una volta effettuato il push degli eventi in Log Analytics, è possibile eseguire questa [query](../../azure-monitor/log-query/log-analytics-tutorial.md) per cercare gli eventi di pianificazione.

1. Nella parte superiore della pagina selezionare **Log** e incollare il codice seguente nella casella di testo:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Selezionare **Salva**, quindi digitare *logQuery* per il nome, lasciare **query** come tipo, digitare *VMLogs* come **categoria** e selezionare **Salva**. 

    ![Salvare la query](./media/notifications/save-query.png)

1. Selezionare **Nuova regola di avviso**. 
1. Nella pagina **Crea regola** lasciare `collectorworkspace` come **risorsa**.
1. In **Condizione** selezionare la voce *Ogni volta che la ricerca log del cliente è <login undefined>* . Viene visualizzata la pagina **Configura logica dei segnali**.
1. In **Valore soglia** immettere *0* e quindi selezionare **Operazione completata**.
1. Da **Azioni** selezionare **Crea gruppo di azioni**. Viene visualizzata la pagina **Aggiungi gruppo di azione**.
1. In **Nome gruppo di azioni** digitare *myActionGroup*.
1. In **Nome breve** digitare **myActionGroup**.
1. In **Gruppo di risorse** selezionare **myResourceGroupAvailability**.
1. In Azioni digitare **Email** per **NOME AZIONE** quindi selezionare **Email/SMS/Push/Voice** (E-mail/SMS/Push/Voce). Viene visualizzata la pagina **Email/SMS/Push/Voice (Posta elettronica/SMS/Push/Voce)** .
1. Selezionare **Email**, digitare l'indirizzo di posta elettronica, quindi selezionare **OK**.
1. Nella pagina **Aggiungi gruppo di azioni** selezionare **OK**. 
1. Nella pagina **Crea regola** in **DETTAGLI AVVISO** digitare *myAlert* come **Nome regola di avviso**, quindi digitare *regola di avviso posta elettronica* come **descrizione**.
1. Al termine selezionare **Crea regola di avviso**.
1. Riavviare una delle macchine virtuali nel set di disponibilità. Entro pochi minuti, si dovrebbe ricevere un messaggio di posta elettronica in cui è stato attivato l'avviso.

Per gestire le regole di avviso, passare al gruppo di risorse, selezionare **Avvisi** dal menu a sinistra e quindi **Gestisci regole di avviso** nella parte superiore della pagina.

     
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la pagina relativa al [servizio Eventi pianificati](https://github.com/microsoft/AzureScheduledEventsService) su GitHub.
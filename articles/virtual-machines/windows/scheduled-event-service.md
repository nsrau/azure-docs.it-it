---
title: Monitorare gli eventi pianificati per le macchine virtuali Windows in AzureMonitor scheduled events for your Windows VMs in Azure
description: Informazioni su come monitorare le macchine virtuali di Azure per gli eventi pianificati.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073309"
---
# <a name="monitoring-scheduled-events"></a>Monitoraggio degli eventi pianificatiMonitoring Scheduled Events

Gli aggiornamenti vengono applicati a diverse parti di Azure ogni giorno, per mantenere i servizi in esecuzione su di essi in modo sicuro e aggiornato. Oltre agli aggiornamenti pianificati, possono verificarsi anche eventi non pianificati. Ad esempio, se viene rilevata una riduzione o un errore hardware, potrebbe essere necessario eseguire la manutenzione non pianificata. Utilizzando la migrazione in tempo reale, conservando gli aggiornamenti della memoria e generalmente mantenendo una barra rigorosa sull'impatto degli aggiornamenti, nella maggior parte dei casi questi eventi sono quasi trasparenti per i clienti e non hanno alcun impatto o al massimo causano alcuni secondi di blocco della macchina virtuale. Tuttavia, per alcune applicazioni, anche alcuni secondi di blocco della macchina virtuale potrebbero causare un impatto. Conoscere in anticipo la prossima manutenzione di Azure è importante per garantire la migliore esperienza per tali applicazioni. [Il servizio Eventi pianificati](scheduled-events.md) fornisce un'interfaccia a livello di codice per ricevere una notifica sulla manutenzione imminente e consente di gestire normalmente la manutenzione. 

In questo articolo verrà illustrato come è possibile usare gli eventi pianificati per ricevere una notifica sugli eventi di manutenzione che potrebbero influire sulle macchine virtuali e creare alcune operazioni di automazione di base utili per il monitoraggio e l'analisi.


## <a name="routing-scheduled-events-to-log-analytics"></a>Routing scheduled events to Log Analytics

Gli eventi pianificati sono disponibili come parte del [servizio metadati dell'istanza](instance-metadata-service.md)di Azure, disponibile in ogni macchina virtuale di Azure.Scheduled Events is available as part of the Azure Instance Metadata Service , which is available on every Azure virtual machine. I clienti possono scrivere l'automazione per interrogare l'endpoint delle macchine virtuali per trovare notifiche di manutenzione pianificata ed eseguire attenuazioni, ad esempio salvare lo stato ed escludere la macchina virtuale dalla rotazione. È consigliabile creare l'automazione per registrare gli eventi pianificati in modo da poter avere un log di controllo degli eventi di manutenzione di Azure.We recommend building automation to record the Scheduled Events so you can have an auditing log of Azure maintenance events. 

In questo articolo verrà illustrato come acquisire eventi pianificati di manutenzione in Log Analytics. Quindi, verranno attivate alcune azioni di notifica di base, ad esempio l'invio di un messaggio di posta elettronica al team e ottenere una visualizzazione cronologica di tutti gli eventi che hanno interessato le macchine virtuali. Per l'aggregazione e l'automazione degli eventi verrà utilizzata [Log Analytics](/azure/azure-monitor/learn/quick-create-workspace), ma è possibile utilizzare qualsiasi soluzione di monitoraggio per raccogliere questi log e attivare l'automazione.

![Diagramma che mostra il ciclo di vita degli eventi](./media/notifications/events.png)

## <a name="prerequisites"></a>Prerequisiti

Per questo esempio, è necessario creare una [macchina virtuale Windows in un set](tutorial-availability-sets.md)di disponibilità . Gli eventi pianificati forniscono notifiche sulle modifiche che possono influire su qualsiasi macchina virtuale nel set di disponibilità, nel servizio cloud, nel set di scalabilità di macchine virtuali o nelle macchine virtuali autonome. Verrà eseguito un [servizio](https://github.com/microsoft/AzureScheduledEventsService) che esegue il polling degli eventi pianificati in una delle macchine virtuali che fungeranno da agente di raccolta per ottenere eventi per tutte le altre macchine virtuali nel set di disponibilità.    

Non eliminare il gruppo di risorse del gruppo alla fine dell'esercitazione.

Sarà inoltre necessario [creare un'area](/azure/azure-monitor/learn/quick-create-workspace) di lavoro di Log Analytics che verrà usata per aggregare le informazioni dalle macchine virtuali nel set di disponibilità.

## <a name="set-up-the-environment"></a>Configurare l'ambiente

A questo punto dovrebbero essere disponibili 2 macchine virtuali iniziali in un set di disponibilità. A questo punto è necessario creare una terza macchina virtuale, denominata myCollectorVM, nello stesso set di disponibilità. 

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
 

Scaricare il file .zip di installazione del progetto da [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Connettersi a **myCollectorVM** ed copiare il file .zip nella macchina virtuale ed estrarre tutti i file. Nella macchina virtuale aprire un prompt di PowerShell.On your VM, open a PowerShell prompt. Spostare il prompt `SchService.ps1`nella cartella `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`contenente , ad esempio: e impostare il servizio.

```powershell
.\SchService.ps1 -Setup
```

Avviare il servizio.

```powershell
.\SchService.ps1 -Start
```

Il servizio avvierà ora il polling ogni 10 secondi per tutti gli eventi pianificati e approverà gli eventi per accelerare la manutenzione.  Blocca, Riavvia, Ridistribuisci e Preempt sono gli eventi acquisiti dagli eventi Schedule.   Si noti che è possibile estendere lo script per attivare alcune attenuazioni prima di approvare l'evento.

Convalidare lo stato del servizio e assicurarsi che sia in esecuzione.

```powershell
.\SchService.ps1 -status  
```

Questo dovrebbe `Running`restituire .

Il servizio avvierà ora il polling ogni 10 secondi per tutti gli eventi pianificati e approverà gli eventi per accelerare la manutenzione.  Blocca, Riavvia, Ridistribuisci e Preempt sono gli eventi acquisiti dagli eventi Schedule. È possibile estendere lo script per attivare alcune attenuazioni prima di approvare l'evento.

Quando uno degli eventi precedenti viene acquisito dal servizio Eventi di pianificazione, verrà registrato nello stato dell'evento del registro eventi dell'applicazione, nel tipo di evento, nelle risorse (nomi di computer virtuali) e in NotBefore (periodo di preavviso minimo). È possibile individuare gli eventi con ID 1234 nel registro eventi applicazioni.

Una volta che il servizio è stato impostato e avviato, registrerà gli eventi nei registri applicazioni di Windows.   Per verificare il problema, riavviare una delle macchine virtuali nel set di disponibilità e dovrebbe essere visualizzato un evento in fase di registrazione nel Visualizzatore eventi in Registri di Windows > registro applicazioni che mostra la macchina virtuale riavviata. 

![Screenshot del Visualizzatore eventi.](./media/notifications/event-viewer.png)

Quando gli eventi vengono acquisiti dal servizio Pianifica evento, verranno registrati nel registro dell'applicazione anche con stato evento, tipo di evento, risorse (nome VM) e NotBefore (periodo di preavviso minimo). È possibile individuare gli eventi con ID 1234 nel registro eventi applicazioni.

> [!NOTE] 
> In questo esempio, le macchine virtuali si trovavano in un set di disponibilità, che ci ha permesso di designare una singola macchina virtuale come agente di raccolta per l'ascolto e l'instradamento degli eventi pianificati al nostro spazio di lavoro di analisi dei log. Se si dispone di macchine virtuali autonome, è possibile eseguire il servizio in ogni macchina virtuale e quindi connetterle singolarmente all'area di lavoro di analisi dei log.
>
> Per la nostra configurazione, abbiamo scelto Windows, ma è possibile progettare una soluzione simile su Linux.

In qualsiasi momento è possibile arrestare/rimuovere il `–stop` `–remove`servizio eventi pianificati utilizzando gli switch e .

## <a name="connect-to-the-workspace"></a>Connettersi all'area di lavoro


Ora vogliamo connettere un'area di lavoro di Log Analytics alla macchina virtuale dell'agente di raccolta. L'area di lavoro di Log Analytics funge da repository e verrà configurata la raccolta dei log eventi per acquisire i log dell'applicazione dalla macchina virtuale dell'agente di raccolta. 

 Per instradare il registro eventi pianificati al registro eventi, che verrà salvato come registro applicazioni dal servizio, è necessario connettere la macchina virtuale all'area di lavoro di Log Analytics.  
 
1. Aprire la pagina per l'area di lavoro creata.
1. In **Connetti a un'origine dati** selezionare Macchine virtuali di Azure.Under Connect to a data source select Azure virtual machines **(VMs)**.

    ![Connettersi a una macchina virtuale come origine datiConnect to a VM as a data source](./media/notifications/connect-to-data-source.png)

1. Cercare e selezionare **myCollectorVM**. 
1. Nella nuova pagina di **myCollectorVM**selezionare **Connect**.

Verrà installato [l'agente di monitoraggio Microsoft](/azure/virtual-machines/extensions/oms-windows) nella macchina virtuale. La connessione della macchina virtuale all'area di lavoro e l'installazione dell'estensione richiederanno alcuni minuti. 

## <a name="configure-the-workspace"></a>Configurare l'area di lavoro

1. Aprire la pagina dell'area di lavoro e selezionare **Impostazioni avanzate**.
1. Selezionare **Dati** dal menu a sinistra , quindi selezionare **Registri eventi di Windows**.
1. In **Raccogli dai registri eventi seguenti**iniziare a digitare *l'applicazione* e quindi selezionare **Applicazione** dall'elenco.

    ![Selezionare Impostazioni avanzate](./media/notifications/advanced.png)

1. Lasciare **selezionata l'opzione ERROR**, **AVVISO**e **INFORMATION** , quindi selezionare **Salva** per salvare le impostazioni.


> [!NOTE]
> Ci sarà un certo ritardo e potrebbero essere votì fino a 10 minuti prima che il registro sia disponibile. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Creazione di una regola di avviso con Monitoraggio di AzureCreating an alert rule with Azure Monitor 


Una volta inviati gli eventi a Log Analytics, è possibile eseguire la [query](/azure/azure-monitor/log-query/get-started-portal) seguente per cercare gli eventi di pianificazione.

1. Nella parte superiore della pagina selezionare **Registri** e incollare quanto segue nella casella di testo:

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

1. Selezionare **Salva**e quindi digitare *logQuery* come nome, lasciare **Query** come tipo, digitare *VMLogs* come **Category**e quindi selezionare **Save**. 

    ![Salvare la query](./media/notifications/save-query.png)

1. Selezionare **Nuova regola di avviso**. 
1. Nella pagina **Crea** regola `collectorworkspace` lasciare risorsa **.**
1. In **Condizione**selezionare la voce *Ogni volta che la ricerca nel log del cliente è <login undefined> *. Si aprirà la pagina **Configura logica del segnale.**
1. In **Valore soglia**immettere *0* e quindi selezionare **Fatto**.
1. In **Azioni**selezionare **Crea gruppo di azioni.** Verrà aperta la pagina **Aggiungi gruppo di azioni.**
1. In **Nome gruppo di**azioni digitare *myActionGroup*.
1. In **Nome breve**digitare **myActionGroup**.
1. In **Gruppo di risorse**selezionare **myResourceGroupAvailability**.
1. In Azioni, in **ACTION NAME** digitare **Email**, quindi selezionare **Email/SMS/Push/Voice**. Si aprirà la pagina **Email/SMS/Push/Voice.**
1. Selezionare **E-mail**, digitare l'indirizzo di posta elettronica, quindi scegliere **OK**.
1. Nella pagina **Aggiungi gruppo di azioni** selezionare **OK**. 
1. Nella pagina **Crea regola,** in **ALERT DETAILS**, digitare *myAlert* come **Nome regola di avviso**e quindi digitare Regola di avviso *tramite posta elettronica* per la **descrizione**.
1. Al termine, selezionare **Crea regola di avviso**.
1. Riavviare una delle macchine virtuali nel set di disponibilità. Entro pochi minuti, si dovrebbe ottenere un'e-mail che l'avviso è stato attivato.

Per gestire le regole di avviso, passare al gruppo di risorse, selezionare **Avvisi** dal menu a sinistra e quindi selezionare **Gestisci regole** di avviso nella parte superiore della pagina.

     
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la pagina [Servizio eventi pianificati](https://github.com/microsoft/AzureScheduledEventsService) in GitHub.To learn more, see the Scheduled events service page on GitHub.

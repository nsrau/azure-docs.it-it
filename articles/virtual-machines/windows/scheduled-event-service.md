---
title: Monitorare gli eventi pianificati per le macchine virtuali Windows in Azure | Microsoft Docs
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
ms.openlocfilehash: 49c82339e5a3774cd286d700d709371d46cf0571
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051848"
---
# <a name="monitoring-scheduled-events"></a>Eventi pianificati di monitoraggio

Gli aggiornamenti vengono applicati a diverse parti di Azure ogni giorno, per garantire la sicurezza e l'aggiornamento dei servizi. Oltre agli aggiornamenti pianificati, possono verificarsi anche eventi non pianificati. Se, ad esempio, viene rilevato un calo o un errore hardware, i servizi di Azure potrebbero dover eseguire una manutenzione non pianificata. Usando la migrazione in tempo reale, gli aggiornamenti con mantenimento della memoria e che in genere mantengono una rigida barra sull'effetto degli aggiornamenti, nella maggior parte dei casi questi eventi sono quasi trasparenti per i clienti e non hanno alcun effetto o al massimo provocano pochi secondi di blocco della macchina virtuale. Tuttavia, per alcune applicazioni, anche alcuni secondi di blocco della macchina virtuale possono causare un certo effetto. Conoscere in anticipo la prossima manutenzione di Azure è importante per garantire la migliore esperienza per tali applicazioni. [Eventi pianificati servizio](scheduled-events.md) fornisce un'interfaccia a livello di codice per ricevere notifiche sulla manutenzione imminente e consente di gestire correttamente la manutenzione. 

Questo articolo illustra come è possibile usare gli eventi pianificati per ricevere notifiche sugli eventi di manutenzione che potrebbero influire sulle VM e creare un'automazione di base che può essere utile per il monitoraggio e l'analisi.


## <a name="routing-scheduled-events-to-log-analytics"></a>Routing degli eventi pianificati a Log Analytics

Eventi pianificati è disponibile come parte del [servizio metadati dell'istanza di Azure](instance-metadata-service.md), disponibile in ogni macchina virtuale di Azure. I clienti possono scrivere l'automazione per eseguire query sull'endpoint delle proprie macchine virtuali per individuare le notifiche di manutenzione pianificate ed eseguire le mitigazioni, ad esempio il salvataggio dello stato e l'uscita dalla rotazione della macchina virtuale. Si consiglia di creare l'automazione per registrare il Eventi pianificati in modo che sia possibile avere un log di controllo degli eventi di manutenzione di Azure. 

In questo articolo verrà illustrato come acquisire Eventi pianificati di manutenzione per Log Analytics. Si attiveranno quindi alcune azioni di notifica di base, ad esempio l'invio di un messaggio di posta elettronica al team e l'acquisizione di una visualizzazione cronologica di tutti gli eventi che hanno interessato le macchine virtuali. Per l'aggregazione e l'automazione degli eventi si userà [log Analytics](/azure/azure-monitor/learn/quick-create-workspace), ma è possibile usare qualsiasi soluzione di monitoraggio per raccogliere questi log e attivare l'automazione.

![Diagramma che mostra il ciclo di vita dell'evento](./media/notifications/events.png)

## <a name="prerequisites"></a>Prerequisiti

Per questo esempio, sarà necessario creare una [macchina virtuale Windows in un set di disponibilità](tutorial-availability-sets.md). Eventi pianificati forniscono notifiche sulle modifiche che possono influire su qualsiasi macchina virtuale nel set di disponibilità, nel servizio cloud, nel set di scalabilità di macchine virtuali o nelle VM autonome. Verrà eseguito un [servizio](https://github.com/microsoft/AzureScheduledEventsService) che esegue il polling degli eventi pianificati in una delle macchine virtuali che fungeranno da agente di raccolta, per ottenere gli eventi per tutte le altre macchine virtuali nel set di disponibilità.    

Non eliminare il gruppo di risorse gruppo alla fine dell'esercitazione.

Sarà inoltre necessario [creare un'area di lavoro log Analytics](/azure/azure-monitor/learn/quick-create-workspace) che si utilizzerà per aggregare le informazioni dalle macchine virtuali nel set di disponibilità.

## <a name="set-up-the-environment"></a>Configurare l'ambiente

A questo punto dovrebbero essere presenti 2 macchine virtuali iniziali in un set di disponibilità. A questo punto è necessario creare una terza macchina virtuale, denominata myCollectorVM, nello stesso set di disponibilità. 

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

Connettersi a **myCollectorVM** e copiare il file con estensione zip nella macchina virtuale ed estrarre tutti i file. Nella macchina virtuale aprire un prompt di PowerShell. Spostare il prompt nella cartella contenente `SchService.ps1`, ad `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`esempio, e configurare il servizio.

```powershell
.\SchService.ps1 -Setup
```

Avviare il servizio.

```powershell
.\SchService.ps1 -Start
```

Il servizio avvierà ora il polling ogni 10 secondi per tutti gli eventi pianificati e approverà gli eventi per velocizzare la manutenzione.  I blocchi, il riavvio, la ridistribuzione e l'interruzione sono gli eventi acquisiti dagli eventi di pianificazione.   Si noti che è possibile estendere lo script per attivare alcune attenuazioni prima di approvare l'evento.

Convalidare lo stato del servizio e verificare che sia in esecuzione.

```powershell
.\SchService.ps1 -status  
```

Questa operazione dovrebbe `Running`restituire.

Il servizio avvierà ora il polling ogni 10 secondi per tutti gli eventi pianificati e approverà gli eventi per velocizzare la manutenzione.  Il blocco, il riavvio, la ridistribuzione e l'interruzione sono gli eventi acquisiti dagli eventi di pianificazione. È possibile estendere lo script per attivare alcune attenuazioni prima di approvare l'evento.

Quando uno qualsiasi degli eventi precedenti viene acquisito tramite Pianifica servizio eventi, viene registrato nello stato dell'evento del registro eventi dell'applicazione, nel tipo di evento, nelle risorse (nomi di macchine virtuali) e in NotBefore (periodo di preavviso minimo). È possibile individuare gli eventi con ID 1234 nel registro eventi dell'applicazione.

Una volta configurato e avviato il servizio, gli eventi vengono registrati nei registri applicazioni di Windows.   Per verificarne il funzionamento, riavviare una delle macchine virtuali nel set di disponibilità. verrà visualizzato un evento registrato nel Visualizzatore eventi nei registri di Windows > Registro applicazioni che mostra la macchina virtuale riavviata. 

![Screenshot del Visualizzatore eventi.](./media/notifications/event-viewer.png)

Quando gli eventi vengono acquisiti dal servizio eventi Schedule, verranno registrati nell'applicazione anche log con lo stato dell'evento, il tipo di evento, le risorse (nome della macchina virtuale) e NotBefore (periodo di preavviso minimo). È possibile individuare gli eventi con ID 1234 nel registro eventi dell'applicazione.

> [!NOTE] 
> In questo esempio, le macchine virtuali si trovano in un set di disponibilità, che ci ha consentito di designare una singola macchina virtuale come agente di raccolta per ascoltare e indirizzare gli eventi pianificati allo spazio di lavoro di log Analytics. Se si dispone di macchine virtuali autonome, è possibile eseguire il servizio in ogni macchina virtuale e quindi connetterle singolarmente all'area di lavoro di log Analytics.
>
> Per la nostra configurazione, abbiamo scelto Windows, ma è possibile progettare una soluzione simile in Linux.

In qualsiasi momento è possibile arrestare/rimuovere il servizio eventi pianificato utilizzando le opzioni `–stop` e `–remove`.

## <a name="connect-to-the-workspace"></a>Connettersi all'area di lavoro


A questo punto si vuole connettere un'area di lavoro Log Analytics alla macchina virtuale dell'agente di raccolta. L'area di lavoro Log Analytics funge da repository e la raccolta dei log eventi viene configurata per acquisire i registri applicazioni dalla macchina virtuale dell'agente di raccolta. 

 Per instradare il Eventi pianificati al log eventi, che verrà salvato come registro applicazioni dal servizio, sarà necessario connettere la macchina virtuale all'area di lavoro di Log Analytics.  
 
1. Aprire la pagina per l'area di lavoro creata.
1. In **Connetti a un'origine dati** selezionare **macchine virtuali (VM) di Azure**.

    ![Connettersi a una macchina virtuale come origine dati](./media/notifications/connect-to-data-source.png)

1. Cercare e selezionare **myCollectorVM**. 
1. Nella pagina nuova per **myCollectorVM**selezionare **Connetti**.

Verrà installato [Microsoft Monitoring Agent](/azure/virtual-machines/extensions/oms-windows) nella macchina virtuale. La connessione della macchina virtuale all'area di lavoro e l'installazione dell'estensione sono necessari alcuni minuti. 

## <a name="configure-the-workspace"></a>Configurare l'area di lavoro

1. Aprire la pagina per l'area di lavoro e selezionare **Impostazioni avanzate**.
1. Selezionare **dati** dal menu a sinistra, quindi selezionare **registri eventi di Windows**.
1. In **Raccogli dai registri eventi seguenti**, iniziare a digitare *applicazione* , quindi selezionare **applicazione** nell'elenco.

    ![Selezionare le impostazioni avanzate](./media/notifications/advanced.png)

1. Lasciare selezionata l'opzione **errore**, **avviso**e **informazioni** , quindi selezionare **Salva** per salvare le impostazioni.


> [!NOTE]
> Si verifica un ritardo e potrebbero essere necessari fino a 10 minuti prima che il log sia disponibile. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Creazione di una regola di avviso con monitoraggio di Azure 


Una volta effettuato il push degli eventi in Log Analytics, è possibile eseguire la [query](/azure/azure-monitor/log-query/get-started-portal) seguente per cercare gli eventi di pianificazione.

1. Nella parte superiore della pagina selezionare **logs** e incollare il codice seguente nella casella di testo:

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

1. Selezionare **Save (Salva**) e quindi digitare *logQuery* per nome, lasciare **query** come tipo, digitare *VMLogs* come **Category**e quindi selezionare Save ( **Salva**). 

    ![Salvare la query](./media/notifications/save-query.png)

1. Selezionare **nuova regola di avviso**. 
1. Nella pagina **Crea regola** lasciare `collectorworkspace` come **risorsa**.
1. In **condizione**selezionare la voce *ogni volta che la ricerca nei log <login undefined>del cliente è* . Viene visualizzata la pagina **Configura logica di segnalazione** .
1. In **valore soglia**immettere *0* e quindi fare clic su **fine**.
1. In **azioni**selezionare **Crea gruppo di azioni**. Viene visualizzata la pagina **Aggiungi gruppo di azioni** .
1. Digitare *myActionGroup*in **nome gruppo di azioni**.
1. In **nome breve**digitare **myActionGroup**.
1. In **gruppo di risorse**selezionare * myResourceGroupAvailability * *.
1. In azioni, in **nome azione** digitare **posta elettronica**, quindi selezionare **posta elettronica/SMS/push/voce**. Viene visualizzata la pagina **posta elettronica/SMS/push/Voice** .
1. Selezionare **e-mail**, digitare l'indirizzo di posta elettronica, quindi fare clic su **OK**.
1. Nella pagina **Aggiungi gruppo di azione** fare clic su **OK**. 
1. Nella pagina **Crea regola** , in **Dettagli avviso**, digitare *avviso* per **Nome regola di avviso**, quindi digitare regola di *avviso posta elettronica* per la **Descrizione**.
1. Al termine, selezionare **Crea regola di avviso**.
1. Riavviare una delle macchine virtuali nel set di disponibilità. Entro pochi minuti, si riceverà un messaggio di posta elettronica in cui è stato attivato l'avviso.

Per gestire le regole di avviso, passare al gruppo di risorse, selezionare **avvisi** dal menu a sinistra e quindi selezionare **Gestisci regole di avviso** nella parte superiore della pagina.

     
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la pagina del [servizio eventi pianificati](https://github.com/microsoft/AzureScheduledEventsService) su GitHub.

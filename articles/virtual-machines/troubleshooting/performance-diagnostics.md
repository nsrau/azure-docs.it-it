---
title: Diagnostica delle prestazioni per le macchine virtuali di Azure | Microsoft Docs
description: Introduzione alla diagnostica delle prestazioni di Azure per Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 67778a269aa962b3184bf55c692154570e8357ba
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387390"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostica delle prestazioni per le macchine virtuali di Azure

Lo strumento di diagnostica delle prestazioni consente di risolvere i problemi di prestazioni che possono interessare una macchina virtuale Windows (VM). Tra gli scenari di risoluzione dei problemi supportati figura un controllo rapido dei problemi noti e delle procedure consigliate, nonché dei problemi complessi che coinvolgono il rallentamento delle prestazioni della macchina virtuale o un utilizzo elevato della CPU, dello spazio su disco o della memoria. 

È possibile eseguire la diagnostica delle prestazioni direttamente dal portale di Azure, in cui sono disponibili anche informazioni dettagliate e un registro dei vari log, la configurazione avanzata e i dati di diagnostica. È consigliato eseguire la diagnostica delle prestazioni ed esaminare i dati di diagnostica e le informazioni dettagliate prima di contattare il supporto tecnico Microsoft.

> [!NOTE]
> La diagnostica delle prestazioni è attualmente supportata nelle macchine virtuali Windows in cui è installato .NET versione SDK 4.5 o successiva. Per informazioni su come eseguire la diagnostica delle prestazioni per macchine virtuali classiche, vedere [Estensione della diagnostica delle prestazioni di Azure per macchine virtuali](performance-diagnostics-vm-extension.md).

### <a name="supported-operating-systems"></a>Sistemi operativi supportati
Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Installare ed eseguire la diagnostica delle prestazioni della macchina virtuale
La diagnostica delle prestazioni consente di installare sulla macchina virtuale un'estensione che esegue uno strumento di diagnostica denominato [PerfInsights](https://aka.ms/perfinsights). Per installare lo strumento ed eseguire la diagnostica delle prestazioni, seguire questa procedura:
1.  Nella colonna sinistra dei comandi, selezionare **Macchine virtuali**.
1.  Dall'elenco di nomi delle macchine virtuali, selezionare la macchina virtuale per cui si desidera eseguire la diagnostica.
1.  Nella colonna a destra dei comandi, selezionare **Diagnostica prestazioni**.

    ![Screenshot del portale di Azure che mostra il pulsante di installazione della diagnostica delle prestazioni evidenziato](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > In questa schermata, il pannello di nomi delle macchine virtuali è nascosto.
1. Selezionare un account di archiviazione (facoltativo)

    Se si desidera usare un singolo account di archiviazione per archiviare i risultati di diagnostica delle prestazioni per più macchine virtuali, è possibile selezionare un account di archiviazione facendo clic sul pulsante **Impostazioni** sulla barra degli strumenti. Fare clic sul pulsante **OK** dopo aver selezionato l'account di archiviazione.

    Se non si specifica un account di archiviazione, per impostazione predefinita verrà creato un nuovo account di archiviazione.

    ![Screenshot del pannello Diagnostica prestazioni che mostra il pulsante Impostazioni della barra degli strumenti evidenziato](media/performance-diagnostics/settings-button.png)

    ![Screenshot della selezione dell’account di archiviazione dal pannello delle impostazioni di Diagnostica prestazioni](media/performance-diagnostics/select-storage-account.png)

1. Selezionare il pulsante **Installa la diagnostica delle prestazioni**.
1. Selezionare la casella di controllo **Esegui diagnostica** se si desidera eseguire la diagnostica dopo il completamento dell'installazione. Se si seleziona questa casella di controllo, sarà possibile scegliere lo scenario di analisi delle prestazioni e le opzioni correlate.

    ![Screenshot del pulsante di installazione di Diagnostica prestazioni](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Selezionare uno scenario di analisi da eseguire

I seguenti scenari di analisi sono disponibili sul portale di Azure. Selezionare un'analisi, a seconda del problema di prestazioni riscontrato. Selezionare le opzioni di durata e di traccia in base alle esigenze di analisi.

* **Analisi rapida delle prestazioni**  
    Verifica la presenza di problemi noti, analizza le procedure consigliate e raccoglie i dati di diagnostica. L'esecuzione di questa analisi richiede alcuni minuti. [Altre informazioni](https://aka.ms/perfinsights/quick)

* **Analisi delle prestazioni**  
    Include tutti i controlli nell'analisi rapida delle prestazioni e consente di monitorare il consumo elevato di risorse. Usare questa versione per risolvere i problemi generali relativi alle prestazioni, ad esempio un utilizzo elevato della CPU, della memoria e dello spazio su disco. Questa analisi richiede tra 30 secondi e 15 minuti, a seconda della durata selezionata. [Altre informazioni](https://aka.ms/perfinsights/vmslow) 
    
* **Analisi avanzata delle prestazioni**  
    Include tutti i controlli nell'analisi delle prestazioni e raccoglie una o più tracce, come indicato nelle sezioni seguenti. Usare questo scenario per risolvere problemi complessi che richiedono tracce supplementari. L’esecuzione di questo scenario per periodi più lunghi aumenterà le dimensioni complessive dell'output di diagnostica, a seconda delle dimensioni della macchina virtuale e delle opzioni di traccia selezionate. L'esecuzione di questa analisi richiede tra 30 secondi e 15 minuti, a seconda della durata selezionata. [Altre informazioni](https://aka.ms/perfinsights/advanced) 
    
* **Analisi di File di Azure**  
    Include tutti i controlli nell'analisi delle prestazioni e consente di acquisire una traccia della rete e i contatori SMB. Usare questo scenario per risolvere i problemi di prestazione dei file di Azure. L'esecuzione di questa analisi richiede tra 30 secondi e 15 minuti, a seconda della durata selezionata. [Altre informazioni](https://aka.ms/perfinsights/azurefiles)


![Screenshot del riquadro Esegui diagnostica nel pannello Diagnostica prestazioni](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Indicare i sintomi (facoltativo)
Selezionare eventuali sintomi tra quelli presenti nell'elenco oppure aggiungere sintomi nuovi. Ciò consente di migliorare l'analisi in futuro. 

### <a name="provide-support-request-number-if-available-optional"></a>Specificare il numero di richiesta di supporto, se disponibile (facoltativo)
Se si lavora con un tecnico del supporto Microsoft su un ticket di supporto esistente, specificare il numero di ticket di supporto. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Esaminare l'informativa sulla privacy e le note legali e selezionare la casella di controllo per confermare (obbligatorio)
Per eseguire la diagnostica, è necessario accettare le condizioni legali e l'informativa sulla privacy.

### <a name="select-ok-to-run-the-diagnostics"></a>Selezionare OK per eseguire la diagnostica 
Viene visualizzata una notifica all'avvio dell'installazione della diagnostica delle prestazioni. Al termine dell'installazione, viene visualizzata una notifica che indica che l'installazione è avvenuta con successo. L'analisi selezionata viene quindi eseguita per la durata specificata. Questo rappresenta il momento opportuno per riprodurre il problema di prestazione in modo che i dati di diagnostica possano essere acquisiti al momento giusto. 

Al termine dell'analisi, i seguenti elementi vengono caricati nelle tabelle di Azure e nel contenitore BLOB (oggetto binario di grandi dimensioni) relativi all'account di archiviazione specificato:

*   Tutte le informazioni dettagliate e le informazioni correlate dell'esecuzione
*   Un file compresso (zip) di output (denominato **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**) che contiene i file di log
*   Un report HTML

Dopo il caricamento, viene elencato un nuovo report di diagnostica nel portale di Azure.

![Screenshot di un elenco di report di diagnostica nel pannello Diagnostica prestazioni](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Come modificare le impostazioni di diagnostica delle prestazioni
Usare il pulsante **Impostazioni** della barra degli strumenti per modificare l'account di archiviazione in cui le informazioni di diagnostica e l’output possono essere archiviati. È possibile usare lo stesso account di archiviazione per più macchine virtuali che usano lo strumento di diagnostica delle prestazioni. Quando si modifica l'account di archiviazione, i report e le informazioni dettagliate precedenti non vengono eliminati. Tuttavia, non saranno più visualizzati nell'elenco dei report di diagnostica. 

## <a name="review-insights-and-performance-diagnostics-report"></a>Esaminare le informazioni dettagliate e il report di diagnostica delle prestazioni
Ogni diagnostica eseguita contiene un elenco di informazioni dettagliate e di elementi consigliati, di risorse interessate, di file di log e altre informazioni di diagnostica avanzata raccolte, oltre a un report per la visualizzazione offline. Per un elenco completo di tutti i dati di diagnostica raccolti, vedere [Tipo di informazioni raccolte da PerfInsights](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights) 

### <a name="select-a-performance-diagnostics-report"></a>Selezionare un report di diagnostica delle prestazioni
È possibile usare l'elenco dei report di diagnostica per trovare tutti i report di diagnostica che sono stati eseguiti. L'elenco include dettagli sull'analisi che è stata usata, le informazioni dettagliate che sono state trovate e i relativi livelli di impatto. Selezionare una riga per visualizzare altri dettagli.

![Screenshot della selezione di un elenco di report di diagnostica nel pannello Diagnostica prestazioni](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Esaminare un report di diagnostica delle prestazioni
Ogni report di diagnostica delle prestazioni può contenere numerose informazioni dettagliate e indica un livello di impatto Alto, Medio o Basso. Ogni informazione dettagliata contiene anche elementi consigliati che contribuiscono a ridurre il problema. Le informazioni dettagliate sono raggruppate per essere filtrate facilmente. 

I livelli di impatto rappresentano il potenziale dei problemi di prestazione, in base a fattori quali errori di configurazione, problemi noti o problemi segnalati da altri utenti. Uno o più dei problemi elencati potrebbero non ancora verificarsi. Ad esempio, nello stesso disco dati potrebbero essere presenti i file di log SQL e i file di database. Questa condizione dispone di un potenziale alto per colli di bottiglia e altri problemi di prestazioni se l'utilizzo del database è elevato, mentre un problema potrebbe passare inosservato se l'utilizzo è basso.

![Screenshot della panoramica del report nel pannello Diagnostica prestazioni](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Revisione delle informazioni dettagliate e degli elementi consigliati della diagnostica delle prestazioni
È possibile selezionare un'informazione dettagliata per visualizzare altri dettagli sulle risorse interessate, mitigazioni suggerite e collegamenti ipertestuali di riferimento. 

![Screenshot di un’informazione dettagliata di Diagnostica prestazioni](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Scaricare ed esaminare il rapporto completo di diagnostica delle prestazioni
È possibile usare il pulsante **Scarica report** per scaricare un report HTML che contiene altre informazioni di diagnostica avanzate, quali l’archiviazione e la configurazione di rete, i contatori delle prestazioni, le tracce, l’elenco dei processi e i log. Il contenuto varia a seconda dell'analisi selezionata. Per la risoluzione avanzata dei problemi, il report può contenere informazioni aggiuntive e grafici interattivi che riguardano l'utilizzo della CPU, l’utilizzo elevato del disco e i processi che richiedono un uso eccessivo della memoria. Per altre informazioni sul report di diagnostica delle prestazioni, vedere [Esaminare i report di diagnostica](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Gestire un report di diagnostica delle prestazioni
È possibile eliminare uno o più report di diagnostica delle prestazioni tramite il pulsante **Elimina report**.

## <a name="how-to-uninstall-performance-diagnostics"></a>Come disinstallare la diagnostica delle prestazioni
È possibile disinstallare la diagnostica delle prestazioni da una macchina virtuale. Questa azione rimuove l'estensione dalla macchina virtuale ma non gli eventuali dati di diagnostica nell'account di archiviazione. 

![Screenshot del pannello Diagnostica prestazioni che mostra il pulsante di disinstallazione della barra degli strumenti evidenziato](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Dove sono archiviati i dati di diagnostica dalla macchina virtuale? 
Tutte le informazioni dettagliate di diagnostica delle prestazioni e i report vengono archiviati nel proprio account di archiviazione. Le informazioni dettagliate vengono archiviate all'interno delle tabelle di Azure. Il file compresso di report viene archiviato in un contenitore di oggetto binario di grandi dimensioni (BLOB) denominato azdiagextnresults.

È possibile visualizzare le informazioni sull'account di archiviazione usando il pulsante Impostazioni sulla barra degli strumenti. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Come si condividono i dati con il Supporto tecnico Microsoft? 
Esistono diversi modi per condividere il report di diagnostica con Microsoft.

**Opzione 1:** condividere automaticamente il report più recente  
Quando si apre un ticket di supporto con Microsoft, è importante condividere il report di diagnostica delle prestazioni. Se si sceglie di condividere queste informazioni con Microsoft mentre si esegue la diagnostica (selezionando la casella di controllo "**Accetto di condividere le informazioni di diagnostica con Microsoft**"), Microsoft sarà in grado di accedere al report nell’account di archiviazione usando un collegamento SAS al file ZIP di output fino a 30 giorni dalla data di esecuzione. Il tecnico del supporto avrà accesso solo al report più recente. 

**Opzione 2:** generare una firma di accesso condiviso per il file ZIP contenente i dati di diagnostica  
È possibile condividere un collegamento al file ZIP del report usando una firma di accesso condiviso. A questo scopo, seguire questa procedura: 
1.  Nel portale di Azure, passare all'account di archiviazione in cui i dati di diagnostica vengono archiviati.
1.  Selezionare **BLOB** nella sezione **Servizio BLOB**. 
1.  Selezionare il contenitore **azdiagextnresults**.
1.  Selezionare il file ZIP di output contenente le prestazioni diagnostica che si desidera condividere.
1.  Nella scheda **Genera firma di accesso condiviso**, selezionare i criteri per la condivisione. 
1.  Fare clic su **Genera token di firma di accesso condiviso e URL**.
1.  Copiare l’**URL di firma di accesso condiviso al BLOB** e condividerlo con il tecnico del supporto. 

**Opzione 3:** scaricare il report dall'account di archiviazione

È anche possibile individuare il file ZIP di report di diagnostica delle prestazioni usando i passaggi 1-4 dell'opzione 2. Selezionare questa opzione per scaricare il file e quindi condividerlo tramite posta elettronica, o chiedere al tecnico del supporto le istruzioni da seguire per caricare il file.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Come si acquisiscono i dati di diagnostica al momento giusto?
L’esecuzione di ogni diagnostica delle prestazioni presenta due fasi: 
1.  Installazione o aggiornamento dell’estensione di diagnostica delle prestazioni per macchine virtuali.
1.  Esecuzione della diagnostica per la durata specificata.

Attualmente non vi è alcun modo semplice per sapere con esattezza quando l'installazione dell'estensione nella macchina virtuale è stata completata. L’installazione dell’estensione sulla macchina virtuale richiede in genere circa tra 45 secondi e 1 minuto. Dopo aver installato l'estensione sulla macchina virtuale, è possibile eseguire i passaggi di ripetizione bug per permettere allo strumento di diagnostica delle prestazioni di acquisire il set corretto di dati necessari per la risoluzione dei problemi. 

## <a name="next-steps"></a>Passaggi successivi
Dopo avere esaminato le informazioni dettagliate di diagnostica delle prestazioni e il report, se non è ancora possibile stabilire la causa del problema è si necessita di ulteriore assistenza, è possibile aprire un ticket di supporto con il Supporto tecnico Microsoft. 

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiesta di supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

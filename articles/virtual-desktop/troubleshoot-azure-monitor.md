---
title: Risolvere i problemi di monitoraggio desktop virtuale di Windows-Azure
description: Come risolvere i problemi relativi a monitoraggio di Azure per desktop virtuale di Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467672"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Risolvere i problemi di monitoraggio di Azure per desktop virtuale Windows (anteprima)

>[!IMPORTANT]
>Monitoraggio di Azure per desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo presenta i problemi noti e le soluzioni per i problemi comuni in monitoraggio di Azure per desktop virtuale Windows (anteprima).

## <a name="the-configuration-workbook-isnt-working-properly"></a>La cartella di lavoro di configurazione non funziona correttamente

Se la cartella di lavoro di configurazione di monitoraggio di Azure non funziona, è possibile usare queste risorse per impostarne le parti manualmente:

- Per abilitare manualmente la diagnostica o accedere all'area di lavoro Log Analytics, vedere [inviare diagnostica desktop virtuali Windows a log Analytics](diagnostics-log-analytics.md).
- Per installare manualmente l'estensione Log Analytics in un host, vedere [log Analytics estensione macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md).
- Per configurare una nuova area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md).
- Per aggiungere o rimuovere i contatori delle prestazioni, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/platform/data-sources-performance-counters.md).
- Per configurare gli eventi per un'area di lavoro Log Analytics, vedere [raccogliere origini dati del registro eventi di Windows con log Analytics Agent](../azure-monitor/platform/data-sources-windows-events.md).

In alternativa, il problema potrebbe essere dovuto a una mancanza di risorse o a non avere le autorizzazioni necessarie.

Se la sottoscrizione non dispone di risorse di desktop virtuali Windows, non verrà visualizzata nel parametro *Subscription* .

Se non si dispone dell'accesso in lettura alle sottoscrizioni corrette, queste non verranno visualizzate nel parametro *Subscription* e i relativi dati non verranno visualizzati nel dashboard. Per risolvere questo problema, contattare il proprietario della sottoscrizione e richiedere l'accesso in lettura.

## <a name="my-data-isnt-displaying-properly"></a>I dati non vengono visualizzati correttamente

Se i dati non vengono visualizzati correttamente, potrebbe essersi verificato un problema durante il processo di configurazione di monitoraggio di Azure. Assicurarsi prima di tutto che siano stati compilati tutti i campi nella cartella di lavoro di configurazione, come descritto in [usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md). È possibile modificare le impostazioni per gli ambienti nuovi ed esistenti in qualsiasi momento. Se mancano contatori o eventi, i dati associati non verranno visualizzati nel portale di Azure.

Se non sono presenti informazioni ma i dati non sono ancora visualizzati correttamente, potrebbe essersi verificato un problema nella query o nelle origini dati. 

Se non vengono visualizzati errori di installazione e non vengono ancora visualizzati i dati previsti, potrebbe essere necessario attendere 15 minuti e aggiornare il feed. Monitoraggio di Azure prevede un periodo di latenza di 15 minuti per il popolamento dei dati di log. Per altre informazioni, vedere [tempo di inserimento dei dati di log in monitoraggio di Azure](../azure-monitor/platform/data-ingestion-time.md).

Infine, se non sono presenti informazioni ma i dati non sono ancora visualizzati, potrebbe essersi verificato un problema nella query o nelle origini dati. È possibile che sia necessario contattare il supporto tecnico per risolvere il problema, se questo è il caso.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Si vuole personalizzare monitoraggio di Azure per desktop virtuale Windows

Monitoraggio di Azure per desktop virtuale Windows usa le cartelle di lavoro di monitoraggio di Azure. Le cartelle di lavoro di consentono di salvare una copia del modello di cartella di lavoro di desktop virtuale Windows e di creare personalizzazioni personalizzate.

I modelli personalizzati non verranno aggiornati quando il gruppo di prodotti aggiorna il modello originale. Questa operazione è progettata nello strumento cartelle di lavoro, è necessario salvare una copia del modello aggiornato e ricompilare le personalizzazioni per l'adozione degli aggiornamenti. Per ulteriori informazioni, vedere la pagina relativa alla [risoluzione dei problemi relativi alle informazioni dettagliate basate sulla cartella di lavoro](../azure-monitor/insights/troubleshoot-workbooks.md) e alla [Panoramica delle cartelle di lavoro](../azure-monitor/platform/workbooks-overview.md)

## <a name="i-cant-interpret-the-data"></a>Non è possibile interpretare i dati

Per altre informazioni sui termini di dati, vedere il [Glossario monitoraggio di Azure per desktop virtuale di Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>I dati necessari non sono disponibili

Non è possibile trovare un punto dati per facilitare la diagnosi di un problema? Invia commenti e suggerimenti

- Per informazioni su come lasciare commenti, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto per desktop virtuale di Windows](troubleshoot-set-up-overview.md).
- È anche possibile lasciare commenti e suggerimenti per desktop virtuale Windows nell' [Hub di feedback per desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) o nel [Forum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues"></a>Problemi noti

Questi sono i problemi che attualmente conosciamo e che ci stiamo impegnando per correggere:

- Attualmente è possibile selezionare solo una sottoscrizione, un gruppo di risorse e un pool di host da monitorare alla volta. Per questo motivo, quando si usa la pagina report utente per comprendere l'esperienza di un utente, è necessario verificare che il pool host corretto sia stato usato dall'utente o che i dati non popolano gli oggetti visivi.

- Al momento non è possibile salvare le impostazioni preferite in monitoraggio di Azure, a meno che non si salvi un modello personalizzato della cartella di lavoro. Questo significa che gli amministratori IT dovranno immettere il nome della sottoscrizione, i nomi dei gruppi di risorse e le preferenze del pool host ogni volta che aprono il monitoraggio di Azure per desktop virtuale di Windows.

- Attualmente non è possibile esportare i dati da monitoraggio di Azure per desktop virtuale Windows in Excel.

- Tutti gli avvisi di monitoraggio con gravità 1 di Azure per tutti i prodotti all'interno della sottoscrizione selezionata verranno visualizzati nella pagina panoramica. Questo avviene per motivi di progettazione, in quanto gli avvisi di altri prodotti nella sottoscrizione possono avere un effetto sul desktop virtuale di Windows. A questo punto, la query è limitata agli avvisi di gravità 1, esclusi gli avvisi con priorità alta 0 dalla pagina panoramica.

- Alcuni messaggi di errore non vengono formulati in modo intuitivo e non tutti i messaggi di errore sono descritti nella documentazione di.

## <a name="next-steps"></a>Passaggi successivi

Se non si è certi di come interpretare i dati o si desidera ottenere altre informazioni sui termini comuni, consultare il [Glossario di monitoraggio di Azure per desktop virtuali Windows](azure-monitor-glossary.md). Per informazioni su come configurare e usare monitoraggio di Azure per desktop virtuale Windows, vedere [usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md).
---
title: Risolvere i problemi di valutazione e visualizzazione delle dipendenze in Azure MigrateTroubleshoot assessment and dependency visualization in Azure Migrate
description: Ottenere assistenza per la valutazione della risoluzione dei problemi e la visualizzazione delle dipendenze in Azure Migrate.Get help with troubleshooting assessment and dependency visualization in Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127669"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Risolvere i problemi relativi alla visualizzazione di valutazioni/dipendenze

Questo articolo consente di risolvere i problemi relativi alla valutazione e alla visualizzazione delle dipendenze con [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemi di preparazione alla valutazione

Correggere i problemi di preparazione alla valutazione come segue:Fix assessment readiness issues as follows:

**Problema** | **correzione**
--- | ---
Il tipo di avvio non è supportato | Azure non supporta le macchine virtuali con un tipo di avvio EFI. Si consiglia di convertire il tipo di avvio in BIOS prima di eseguire una migrazione. <br/><br/>È possibile usare migrazione server di Azure Migrate per gestire la migrazione di tali macchine virtuali. Il tipo di avvio della macchina virtuale verrà convertito in BIOS durante la migrazione.
Sistema operativo Windows supportato in modo condizionale | Il sistema operativo ha superato la data di fine del supporto e necessita di un contratto di supporto personalizzato (CSA) per il [supporto in Azure](https://aka.ms/WSosstatement). Prendere in considerazione l'aggiornamento prima di eseguire la migrazione ad Azure.Consider upgrading before you migrate to Azure.
Sistema operativo Windows non supportato | Azure supporta solo le [versioni selezionate del sistema operativo Windows.](https://aka.ms/WSosstatement) Valutare la possibilità di aggiornare il computer prima di eseguire la migrazione ad Azure.Consider upgrading the machine before you migrate to Azure.
Sistema operativo Linux con approvazione condizionale | Azure approva solo le [versioni selezionate del sistema operativo Linux.](../virtual-machines/linux/endorsed-distros.md) Valutare la possibilità di aggiornare il computer prima di eseguire la migrazione ad Azure.Consider upgrading the machine before you migrate to Azure.
Sistema operativo Linux non approvato | Il computer potrebbe avviarsi in Azure, ma Azure non fornisce alcun supporto del sistema operativo. Prendere in considerazione l'aggiornamento a una versione Linux approvata prima di eseguire la migrazione ad Azure.Consider upgrading to an [endorsed Linux version](../virtual-machines/linux/endorsed-distros.md) before you migrate to Azure.
Sistema operativo sconosciuto | Il sistema operativo della macchina virtuale è stato specificato come "Altro" in vCenter Server. Questo comportamento impedisce ad Azure Migrate di verificare la disponibilità di Azure della macchina virtuale. Assicurarsi che il sistema operativo sia [supportato](https://aka.ms/azureoslist) da Azure prima di eseguire la migrazione del computer.
Versione bit non supportata | Le macchine virtuali con sistemi operativi a 32 bit potrebbero essere avviate in Azure, ma è consigliabile eseguire l'aggiornamento a 64 bit prima di eseguire la migrazione ad Azure.VMs with a 32-bit operating systems might start in Azure, but we recommended that you upgrade to 64-bit before you migrate to Azure.
Richiede una sottoscrizione di Microsoft Visual Studio | Il computer esegue un sistema operativo client Windows, che è supportato solo tramite una sottoscrizione di Visual Studio.The machine is running a Windows client operating system, which is supported only through a Visual Studio subscription.
VM non trovata per le prestazioni di archiviazione richieste | Le prestazioni di archiviazione (operazioni di input/output al secondo [IOPS] e velocità effettiva) necessarie per la macchina superano il supporto della macchina VM di Azure.The storage performance (input/output operations per second [IOPS] and throughput) required for the machine exceeds Azure VM support. Prima di eseguire la migrazione, ridurre i requisiti di archiviazione per la macchina.
VM non trovata per le prestazioni di rete richieste | Le prestazioni di rete (ingresso/uscita) richieste per la macchina superano le prestazioni supportate dalla macchina virtuale di Azure. Ridurre i requisiti di rete per la macchina.
VM non trovata nel percorso specificato | Usare una località di destinazione diversa prima di eseguire migrazione.
Uno o più dischi inadatti | Uno o più dischi collegati alla macchina virtuale non soddisfano i requisiti di Azure.One or more disks attached to the VM don't meet Azure requirements. Un<br/><br/> Azure Migrate: la valutazione del server attualmente non supporta dischi Ultra SSD e valuta i dischi in base ai limiti dei dischi per i dischi gestiti premium (32 TB).<br/><br/> Per ogni disco collegato alla macchina virtuale, assicurarsi che la dimensione del disco sia < 64 TB (supportato da dischi Ultra SSD).<br/><br/> In caso contrario, ridurre le dimensioni del disco prima di eseguire la migrazione ad Azure oppure usare più dischi in Azure e eseguirne lo [striping insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati. Assicurarsi che le prestazioni (IOPS e velocità effettiva) necessarie per ogni disco siano supportate dai dischi delle [macchine virtuali gestite](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)di Azure.
Una o più schede di rete non idonee. | Prima di eseguire la migrazione, rimuovere le schede di rete non usate dalla macchina.
Il numero di dischi supera il limite | Rimuovere i dischi non usati dalla macchina prima di eseguire la migrazione.
Le dimensioni del disco hanno superato il limite | Azure Migrate: la valutazione del server attualmente non supporta i dischi Ultra SSD e valuta i dischi in base ai limiti premium dei dischi (32 TB).<br/><br/> Tuttavia, Azure supporta dischi con dimensioni fino a 64 TB (supportate da dischi SSD Ultra). Ridurre i dischi a meno di 64 TB prima della migrazione oppure usare più dischi in Azure e [selezionarli insieme](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) per ottenere limiti di archiviazione più elevati.
Il disco non è disponibile nella località specificata | Assicurarsi che il disco si trovi nella località di destinazione prima di eseguire la migrazione.
Il disco non è disponibile per la ridondanza specificata | Il disco deve usare il tipo di archiviazione di ridondanza definito nelle impostazioni di valutazione (per impostazione predefinita, LRS).
Impossibile determinare l'idoneità del disco a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
La macchina virtuale con core e memoria richiesti non è stata trovata | Impossibile trovare un tipo di macchina virtuale adatto. Prima di eseguire la migrazione, quindi, ridurre la memoria e il numero di core della macchina locale.
Impossibile determinare l'idoneità della macchina virtuale a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Impossibile determinare l'idoneità per uno o più dischi a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.
Impossibile determinare l'idoneità per una o più schede di rete a causa di un errore interno | Provare a creare una nuova valutazione per il gruppo.

## <a name="linux-vms-are-conditionally-ready"></a>Le macchine virtuali Linux sono "condizionalmente pronte"

La valutazione del server contrassegna le macchine virtuali Linux come "Condizionalmente pronte" a causa di un divario noto nella valutazione del server.

- Il divario impedisce di rilevare la versione secondaria del sistema operativo Linux installato nelle macchine virtuali locali.
- Ad esempio, per RHEL 6.10, attualmente Server Assessment rileva solo RHEL 6 come versione del sistema operativo.
-  Poiché Azure approva solo versioni specifiche di Linux, le macchine virtuali Linux sono attualmente contrassegnate come pronte in modo condizionale in Server Assessment.Because Azure endorses only specific versions of Linux, the Linux VMs are currently marked as conditionally ready in Server Assessment.
- È possibile determinare se il sistema operativo Linux in esecuzione nella macchina virtuale locale viene approvato in Azure esaminando il supporto di Azure Linux.You can determine whether the Linux OS running on the on-premises VM is endorsed in Azure by reviewing [Azure Linux support](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Dopo aver verificato la distribuzione approvata, è possibile ignorare questo avviso.

## <a name="azure-skus-bigger-than-on-premises"></a>SKU di Azure più grandi di quelli localiAzure SKUs bigger than on-premises

La valutazione del server di migrazione di Azure potrebbe consigliare SKU delle macchine virtuali di Azure con più core e memoria rispetto all'allocazione locale corrente in base al tipo di valutazione:Azure Migrate Server Assessment might recommend Azure VM SKUs with more cores and memory than current on-premises allocation based on the type of assessment:


- La raccomandazione SKU della macchina virtuale dipende dalle proprietà della valutazione.
- Ciò è influenzato dal tipo di valutazione eseguita in Server Assessment: *Performance-based*o *As on-premises*.
- Per le valutazioni basate sulle prestazioni, Server Assessment considera i dati di utilizzo delle macchine virtuali locali (CPU, memoria, disco e utilizzo della rete) per determinare lo SKU della macchina virtuale di destinazione corretto per le macchine virtuali locali. Aggiunge anche un fattore di comfort quando si determina un utilizzo efficace.
- Per il dimensionamento locale, i dati sulle prestazioni non vengono considerati e lo SKU di destinazione è consigliato in base all'allocazione locale.

Per mostrare in che modo questo può influenzare i consigli, prendiamo un esempio:

Abbiamo una macchina virtuale locale con quattro core e otto GB di memoria, con un utilizzo della CPU del 50% e il 50% di utilizzo della memoria e un fattore di comfort specificato pari a 1,3.

-  Se la valutazione è **As on-premise**, è consigliabile uno SKU della macchina virtuale di Azure con quattro core e 8 GB di memoria.
- Se la valutazione è basata sulle prestazioni, basata sull'utilizzo effettivo della CPU e della memoria (50% di 4 core , 1,3 , 2,6 core e 50% di memoria da 8 GB, 1,3 x 5,3 GB di memoria), lo SKU VM più economico di quattro core (numero di core supportato più vicino) e otto GB di memoria (più vicino supportato (dimensioni della memoria).
- [Ulteriori informazioni](concepts-assessment-calculation.md#types-of-assessments) sul dimensionamento della valutazione.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>SKU del disco di Azure più grandi di quelli localiAzure disk SKUs bigger than on-premises

Azure Migrate Server Assessment potrebbe consigliare un disco più grande in base al tipo di valutazione.
- Il dimensionamento del disco in Valutazione server dipende da due proprietà di valutazione: criteri di dimensionamento e tipo di archiviazione.
- Se i criteri di ridimensionamento sono **basati sulle prestazioni**e il tipo di archiviazione è impostato su **Automatic**, le operazioni di I/O al secondo e i valori di velocità effettiva del disco vengono considerati quando si identifica il tipo di disco di destinazione (HDD standard, SSD standard o Premium). È quindi consigliabile uno SKU del disco dal tipo di disco e si considerano i requisiti di dimensione del disco locale.
- Se i criteri di ridimensionamento sono **basati sulle prestazioni**e il tipo di archiviazione è **Premium**, è consigliabile uno SKU del disco Premium in Azure in base ai requisiti di IOPS, velocità effettiva e dimensioni del disco locale. La stessa logica viene utilizzata per eseguire il dimensionamento del disco quando il criterio di ridimensionamento è **As locale** e il tipo di archiviazione è **Standard HDD**, **Standard SSD**o **Premium**.

Ad esempio, se si dispone di un disco locale con 32 GB di memoria, ma le operazioni di I/O al secondo aggregate di lettura e scrittura per il disco sono 800 IOPS, Server Assessment consiglia un disco Premium (a causa dei requisiti di IOPS più elevati) e quindi consiglia uno SKU del disco in grado di iOPS e le dimensioni richieste. In questo esempio la corrispondenza più vicina sarebbe P15 (256 GB, 1.100 operazioni di I/O al secondo). Anche se la dimensione richiesta dal disco locale era 32 GB, Server Assessment consiglia un disco più grande a causa dell'elevato requisito di IOPS del disco locale.

## <a name="utilized-corememory-percentage-missing"></a>Percentuale di core/memoria utilizzata mancante

Server Assessment segnala "PercentageOfCoresUtilizedMissing" o "PercentageOfMemoryUtilizedMissing" quando l'appliance Azure Migrate non è in grado di raccogliere dati sulle prestazioni per le macchine virtuali locali pertinenti.

- Ciò può verificarsi se le macchine virtuali vengono disattivate durante la durata della valutazione. L'appliance non può raccogliere dati sulle prestazioni per una macchina virtuale quando è disattivata.
- Se mancano solo i contatori di memoria e si sta tentando di valutare le macchine virtuali Hyper-V, verificare se la memoria dinamica è abilitata in queste macchine virtuali. Esiste un problema noto solo per le macchine virtuali Hyper-V, in cui un'appliance di Azure Migrate non può raccogliere dati sull'utilizzo della memoria per le macchine virtuali per cui non è abilitata la memoria dinamica.
- Se uno dei contatori delle prestazioni non è presente, la valutazione del server di migrazione di Azure esegue il fallback ai core e alla memoria allocati e consiglia una dimensione della macchina virtuale corrispondente.
- Se mancano tutti i contatori delle prestazioni, verificare che siano soddisfatti i requisiti di accesso alla porta per la valutazione. Ulteriori informazioni sui requisiti di accesso alle porte per [VMware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access) [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) e la valutazione del server [fisico.](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access)

## <a name="is-the-operating-system-license-included"></a>La licenza del sistema operativo è inclusa?

Azure Migrate Server Assessment considera attualmente il costo della licenza del sistema operativo solo per i computer Windows.Azure Migrate Server Assessment currently considers the operating system license cost only for Windows machines. I costi di licenza per le macchine Linux non sono attualmente considerati.

## <a name="how-does-performance-based-sizing-work"></a>Come funziona il dimensionamento basato sulle prestazioni?

Server Assessment raccoglie continuamente i dati delle prestazioni dei computer locali e li usa per consigliare lo SKU della macchina virtuale e del disco in Azure. [Scopri come](concepts-assessment-calculation.md#calculate-sizing-performance-based) vengono raccolti i dati basati sulle prestazioni.


## <a name="dependency-visualization-in-azure-government"></a>Visualizzazione delle dipendenze in Azure per enti pubbliciDependency visualization in Azure Government

Azure Migrate dipende dalla mappa del servizio per la funzionalità di visualizzazione delle dipendenze. Poiché la mappa del servizio non è attualmente disponibile in Azure per enti pubblici, questa funzionalità non è disponibile in Azure per enti pubblici.

## <a name="dependencies-dont-show-after-agent-install"></a>Le dipendenze non vengono visualizzate dopo l'installazione dell'agente

Dopo aver installato gli agenti di visualizzazione delle dipendenze nelle macchine virtuali locali, Azure Migrate richiede in genere 15-30 minuti per visualizzare le dipendenze nel portale. Se hai aspettato più di 30 minuti, assicurati che Microsoft Monitoring Agent (MMA) possa connettersi all'area di lavoro di Log Analytics.

Per VM di Windows:
1. Nel Pannello di controllo avviare MMA.
2. Nelle **proprietà** > di Microsoft Monitoring Agent**Azure Log Analytics (OMS)** verificare che **lo stato** dell'area di lavoro sia verde.
3. Se lo stato non è verde, provare a rimuovere l'area di lavoro e ad aggiungerla nuovamente a MMA.

    ![Stato MMA](./media/troubleshoot-assessment/mma-properties.png)

Per le macchine virtuali Linux, assicurarsi che i comandi di installazione per MMA e l'agente di dipendenza siano stati eseguiti correttamente.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

- **MMS agent**: Esaminare i sistemi operativi [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) supportati.
- **Agente di dipendenza:** i sistemi operativi [Windows e Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) supportati.

## <a name="visualize-dependencies-for--hour"></a>Visualizzare le dipendenze per >'ora

Anche se Azure Migrate consente di tornare a una data specifica nell'ultimo mese, la durata massima per la quale è possibile visualizzare le dipendenze è un'ora.

Ad esempio, è possibile utilizzare la funzionalità di durata del tempo nel mapping delle dipendenze per visualizzare le dipendenze per ieri, ma è possibile visualizzarle solo per un periodo di un'ora.

Tuttavia, è possibile usare i log di Monitoraggio di Azure per eseguire query sui [dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) per una durata più lunga.

## <a name="visualized-dependencies-for--10-machines"></a>Dipendenze visualizzate per > 10 macchine

In Azure Migrate Server Assessment è possibile [visualizzare le dipendenze per i gruppi](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) con un massimo di 10 macchine virtuali. Per i gruppi più grandi, è consigliabile suddividere le macchine virtuali in gruppi più piccoli per visualizzare le dipendenze.

## <a name="machines-show-install-agent"></a>I computer mostrano "Agente di installazione"

Dopo la migrazione di macchine con visualizzazione delle dipendenze abilitata in Azure, i computer potrebbero visualizzare l'azione "Installa agente" anziché "Visualizza dipendenze" a causa del comportamento seguente:After migrating machines with dependency visualization enabled to Azure, machines might show "Install agent" action instead of "View dependencies" due to the following behavior:


- Dopo la migrazione ad Azure, le macchine locali vengono disattivate e le macchine virtuali equivalenti vengono attivate in Azure.After migration to Azure, on-premises machines are turned off and equivalent VMs are spun up in Azure. Queste macchine acquisiscono un indirizzo MAC diverso
- I computer potrebbero anche avere un indirizzo IP diverso, a seconda che l'indirizzo IP locale sia stato mantenuto o meno.
- Se gli indirizzi MAC e IP sono diversi da quelli locali, Azure Migrate non associa i computer locali ai dati di dipendenza della mappa del servizio. In questo caso, verrà visualizzata l'opzione per installare l'agente anziché visualizzare le dipendenze.
- Dopo una migrazione di prova in Azure, le macchine locali rimangono accese come previsto. I computer equivalenti svolti in Azure acquisiscono un indirizzo MAC diverso e potrebbero acquisire indirizzi IP diversi. A meno che non si blocchi il traffico di log di Monitoraggio di Azure in uscita da queste macchine, Azure Migrate non assocerà i computer locali a i dati di dipendenza della mappa del servizio e pertanto mostrerà l'opzione per installare gli agenti anziché visualizzare le dipendenze.


## <a name="capture-network-traffic"></a>Acquisire il traffico di rete

Raccogliere i registri del traffico di rete come segue:Collect network traffic logs as follows:

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Premere F12 per avviare Strumenti di sviluppo. Se necessario, deselezionare l'impostazione Cancella voci durante la **navigazione.**
3. Selezionare la scheda **Rete** e avviare l'acquisizione del traffico di rete:
   - In Chrome selezionare **Preserve log** (Mantieni log). La registrazione viene avviata automaticamente. Un cerchio rosso indica che è in corso l'cattura del traffico. Se il cerchio rosso non viene visualizzato, selezionare il cerchio nero per iniziare.
   - In Microsoft Edge e Internet Explorer, la registrazione dovrebbe iniziare automaticamente. In caso contrario, seleziona il pulsante di riproduzione verde.
4. Provare a riprodurre l'errore.
5. Dopo aver riscontrato l'errore durante la registrazione, arrestare la registrazione e salvare una copia dell'attività registrata:
   - In Chrome, fai clic con il pulsante destro del mouse e seleziona **Salva come HAR con contenuti.** Questa azione comprime ed esporta i registri come file con estensione har.
   - In Microsoft Edge o Internet Explorer selezionare l'opzione **Esporta traffico acquisito.** Questa azione comprime ed esporta il log.
6. Selezionare la scheda **Console** per verificare la presenza di eventuali avvisi o errori. Per salvare il log della console:
   - In Chrome fare clic con il pulsante destro del mouse in un punto qualsiasi del log della console. Selezionare **Salva con nome**, per esportare e comprimere il registro.
   - In Microsoft Edge o Internet Explorer fare clic con il pulsante destro del mouse sui relativi errori e scegliere **Copia tutto**.
7. Chiudere Strumenti di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

[Creare](how-to-create-assessment.md) o [personalizzare](how-to-modify-assessment.md) una valutazione.

---
title: Controllo istanze large di Azure HANA tramite portale di Azure | Microsoft Docs
description: Descrive il modo in cui è possibile identificare e interagire con le istanze large di Azure HANA tramite il portale
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099807"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controllo di istanze Large di HANA in Azure tramite il portale di Azure
Questo documento illustra il modo in cui vengono presentate le [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) in [portale di Azure](https://portal.azure.com) e quali attività possono essere eseguite tramite portale di Azure con le unità di istanze large di Hana distribuite per l'utente. La visibilità delle istanze large di HANA in portale di Azure viene fornita tramite un provider di risorse di Azure per istanze large di HANA, attualmente disponibile in anteprima pubblica

## <a name="register-hana-large-instance-resource-provider"></a>Registrare il provider di risorse per istanze large di HANA
In genere la sottoscrizione di Azure usata per le distribuzioni di istanze large di HANA è registrata per il provider di risorse per istanze large di HANA. Tuttavia, se non è possibile visualizzare le unità di istanze large di HANA, è necessario registrare il provider di risorse nella sottoscrizione di Azure. Esistono due modi per registrare il provider di risorse per istanze large di HANA

### <a name="register-through-cli-interface"></a>Eseguire la registrazione tramite l'interfaccia CLI
È necessario accedere alla sottoscrizione di Azure, usata per la distribuzione di istanze large di HANA tramite l'interfaccia della riga di comando di Azure. È possibile (ri-) registrare il provider di istanze large di HANA con questo comando:
    
    az provider register --namespace Microsoft.HanaOnAzure

Per altre informazioni, vedere l'articolo relativo ai [provider e ai tipi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Eseguire la registrazione tramite portale di Azure
È possibile (ri-) registrare il provider di risorse di istanze large di HANA tramite portale di Azure. È necessario elencare la sottoscrizione in portale di Azure e fare doppio clic sulla sottoscrizione, che è stata usata per distribuire le unità di istanze large di HANA. Nella pagina Panoramica della sottoscrizione selezionare "provider di risorse" come illustrato di seguito e digitare "HANA" nella finestra di ricerca. 

![Registrare HLI RP tramite portale di Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Nella schermata mostrata, il provider di risorse è già stato registrato. Se il provider di risorse non è ancora registrato, premere "Re-register" o "Register".

Per altre informazioni, vedere l'articolo relativo ai [provider e ai tipi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Visualizzazione di unità di istanze large di HANA nel portale di Azure
Quando si invia una richiesta di distribuzione di istanze large di HANA, viene richiesto di specificare la sottoscrizione di Azure da connettere anche alle istanze large di HANA. È consigliabile usare la stessa sottoscrizione usata per distribuire il livello dell'applicazione SAP che funziona nelle unità di istanze large di HANA.
Quando vengono distribuite le prime istanze large di HANA, viene creato un nuovo [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) nella sottoscrizione di Azure inviata nella richiesta di distribuzione per le istanze large di Hana.  Il nuovo gruppo di risorse elenca tutte le unità di istanze large di HANA distribuite nella sottoscrizione specifica.

Per trovare il nuovo gruppo di risorse di Azure, è possibile elencare il gruppo di risorse nella sottoscrizione navigando nel riquadro di spostamento a sinistra della portale di Azure

![Riquadro di spostamento in portale di Azure](./media/hana-li-portal/portal-resource-group.png)

Nell'elenco dei gruppi di risorse viene visualizzato l'elenco, potrebbe essere necessario filtrare la sottoscrizione usata per la distribuzione di istanze large di HANA

![Filtrare i gruppi di risorse in portale di Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Dopo aver filtrato la sottoscrizione corretta, è comunque possibile che sia presente un lungo elenco di gruppi di risorse. Cercarne uno con una post-correzione di **-tXXX** , dove "xxx" è costituito da tre cifre, ad esempio **-T050**. 

Quando è stato trovato il gruppo di risorse, elencarne i dettagli. L'elenco ricevuto potrebbe essere simile al seguente:

![Elenco HLI in portale di Azure](./media/hana-li-portal/portal-hli-units-list.png)

Tutte le unità elencate rappresentano una singola unità di istanze large di HANA distribuita nella sottoscrizione. In questo caso, si esaminano otto diverse unità di istanze large di HANA, che sono state distribuite nella sottoscrizione.

Se sono stati distribuiti diversi tenant di istanze large di HANA nella stessa sottoscrizione di Azure, si troveranno più gruppi di risorse di Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Esaminare gli attributi di una singola unità HLI
Nell'elenco delle unità di istanze large di HANA è possibile fare clic su una singola unità e ottenere i dettagli della singola unità di istanze large di HANA. 

Nella schermata panoramica, dopo aver fatto clic su' Mostra più', si sta ricevendo una presentazione dell'unità, simile alla seguente:

![Mostra Panoramica di un'unità HLI](./media/hana-li-portal/portal-show-overview.png)

Esaminando i diversi attributi mostrati, questi attributi hanno un aspetto poco diverso rispetto agli attributi delle macchine virtuali di Azure. Nell'intestazione del lato sinistro vengono visualizzati il gruppo di risorse, l'area di Azure, il nome della sottoscrizione e l'ID, oltre ad alcuni tag aggiunti. Per impostazione predefinita, alle unità di istanze large di HANA non è assegnato alcun tag. Sul lato destro dell'intestazione, il nome dell'unità viene elencato come assegnato al termine della distribuzione. Viene visualizzato il sistema operativo e l'indirizzo IP. Come per le macchine virtuali, viene visualizzato anche il tipo di unità di istanze large di HANA con il numero di thread CPU e di memoria. Altre informazioni sulle diverse unità di istanze large di HANA sono illustrate di seguito:

- [SKU disponibili per HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architettura di archiviazione SAP HANA (istanze large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

I dati aggiuntivi sul lato inferiore destro sono la revisione del timbro di istanze large di HANA. I valori possibili sono:

- Revisione 3
- Revisione 4

Revisione 4 è l'architettura più recente rilasciata in istanze large di HANA con miglioramenti significativi nella latenza di rete tra le macchine virtuali di Azure e le unità di istanze large di HANA distribuite in timbri o righe di Revisione
Un'altra informazione molto importante si trova nell'angolo inferiore destro della panoramica con il nome del gruppo di posizionamento di prossimità di Azure creato automaticamente per ogni unità di istanze large di HANA distribuita. Quando si distribuiscono le macchine virtuali di Azure che ospitano il livello applicazione SAP, è necessario fare riferimento a questo gruppo di posizionamento di prossimità. Usando il [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) associato all'unità di istanze large di Hana, è necessario assicurarsi che le VM di Azure vengano distribuite in prossimità dell'unità di istanze large di Hana. Il modo in cui è possibile usare i gruppi di posizionamento di prossimità per individuare il livello dell'applicazione SAP nello stesso data center di Azure in cui sono ospitate le unità di istanze large per la revisione 4 ospitate in [gruppi di posizionamento di Azure per la latenza di rete ottimale con le applicazioni SAP ](sap-proximity-placement-scenarios.md).

Un campo aggiuntivo nella colonna a destra dell'intestazione informa lo stato di alimentazione dell'unità di istanze large di HANA.

> [!NOTE]
> Lo stato di alimentazione descrive se l'unità hardware è accesa o spenta. Non fornisce informazioni sul sistema operativo in esecuzione. Quando si riavvia un'unità di istanze large di HANA, si verificherà un piccolo periodo di tempo in cui lo stato dell'unità cambia per **iniziare** a spostarsi nello stato **avviato**. Quando si trova nello stato **Started** significa che il sistema operativo è in fase di avvio o che il sistema operativo è stato avviato completamente. Di conseguenza, dopo il riavvio dell'unità, non è possibile prevedere di accedere immediatamente all'unità non appena lo stato passa a **avviato**.
> 

Se si preme "Vedi altro", vengono visualizzate informazioni aggiuntive. Un'altra informazione è la visualizzazione della revisione del timbro per le istanze large di HANA, in cui è stata distribuita l'unità. Vedere l'articolo [che cos'è SAP Hana in Azure (istanze large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) per le diverse revisioni dei timbri delle istanze large di Hana

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Controllare le attività di una singola unità di istanze large di HANA 
Oltre a fornire una panoramica delle unità di istanze large di HANA, è possibile controllare le attività dell'unità specifica. Un log attività potrebbe avere un aspetto simile al seguente:

![Riquadro di spostamento in portale di Azure](./media/hana-li-portal/portal-activity-list.png)

Una delle attività principali registrate è il riavvio di un'unità. I dati elencati includono lo stato dell'attività, il timestamp con cui l'attività è stata attivata, l'ID della sottoscrizione fuori dal quale l'attività è stata attivata e l'utente di Azure che ha attivato l'attività. 

Un'altra attività che viene registrata sono le modifiche apportate all'unità nei metadati di Azure. Oltre al riavvio avviato, è possibile visualizzare l'attività di **scrittura HANAInstances**. Questo tipo di attività non esegue alcuna modifica nell'unità di istanze large di HANA, ma documenta le modifiche apportate ai metadati dell'unità in Azure. Nel caso elencato, è stato aggiunto ed eliminato un tag (vedere la sezione successiva).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Aggiungere ed eliminare un tag di Azure a un'unità di istanze large di HANA
Un'altra possibilità consiste nell'aggiungere un [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a un'unità di istanze large di Hana. Il modo in cui i tag vengono assegnati non è diverso dall'assegnazione dei tag alle macchine virtuali. Come per le macchine virtuali, i tag sono presenti nei metadati di Azure e, per le istanze large di HANA, presentano le stesse restrizioni dei tag per le macchine virtuali.

L'eliminazione dei tag funziona allo stesso modo delle macchine virtuali. Entrambe le attività, l'applicazione e l'eliminazione di un tag verranno elencate nel log attività della specifica unità di istanze large di HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Controllare le proprietà di un'unità di istanze large di HANA
Le **Proprietà** della sezione includono informazioni importanti che si ottengono quando le istanze vengono passate all'utente. Si tratta di una sezione in cui è possibile ottenere tutte le informazioni che potrebbero essere necessarie nei casi di supporto o necessarie quando si configura la configurazione dello snapshot di archiviazione. Questa sezione è una raccolta di dati relativi all'istanza, alla connettività dell'istanza di ad Azure e al back-end di archiviazione. La parte superiore della sezione ha un aspetto simile al seguente:


![parte superiore delle proprietà di HLI in portale di Azure](./media/hana-li-portal/portal-properties-top.png)

I primi elementi di dati sono già stati visualizzati nella schermata panoramica. Tuttavia, una parte importante dei dati è l'ID del circuito ExpressRoute, ottenuto come le prime unità distribuite sono state passate. In alcuni casi di supporto, è possibile che vengano richiesti i dati. Una voce di dati importante viene visualizzata nella parte inferiore dello screenshot. I dati visualizzati sono l'indirizzo IP della testa di archiviazione NFS che consente di isolare l'archiviazione nel **tenant** nello stack di istanze large di Hana. Questo indirizzo IP è necessario anche quando si modifica il [file di configurazione per i backup degli snapshot di archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Scorrendo verso il basso nel riquadro delle proprietà si ottengono dati aggiuntivi, ad esempio un ID di risorsa univoco per l'unità di istanze large di HANA o l'ID sottoscrizione assegnato alla distribuzione.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Riavviare un'unità di istanze large di HANA tramite portale di Azure
Quando si avvia un riavvio del sistema operativo Linux, si sono verificate diverse situazioni in cui il sistema operativo non è riuscito a completare correttamente il riavvio. Per forzare un riavvio, è necessario aprire una richiesta di servizio per fare in modo che le operazioni di Microsoft eseguano il riavvio dell'unità di istanze large di HANA. La funzionalità di un riavvio dell'alimentazione di un'unità di istanze large di HANA è ora integrata nell'portale di Azure. Come nella sezione Panoramica dell'unità di istanze large di HANA, viene visualizzato il pulsante Riavvia nella parte superiore della sezione dati

![#1 passaggio di riavvio in portale di Azure](./media/hana-li-portal/portal-restart-first-step.png)

Quando si preme il pulsante Riavvia, viene chiesto se si desidera effettivamente riavviare l'unità. Quando si conferma premendo il pulsante "Sì", l'unità viene riavviata.

> [!NOTE]
> Durante il processo di riavvio, si verificherà un piccolo periodo di tempo in cui lo stato dell'unità cambia per **iniziare** a spostarsi nello stato **avviato**. Quando si trova nello stato **Started** significa che il sistema operativo è in fase di avvio o che il sistema operativo è stato avviato completamente. Di conseguenza, dopo il riavvio dell'unità, non è possibile prevedere di accedere immediatamente all'unità non appena lo stato passa a **avviato**.

> [!IMPORTANT]
> A seconda della quantità di memoria nell'unità di istanze large di HANA, un riavvio e un riavvio dell'hardware e del sistema operativo possono richiedere fino a un'ora


## <a name="open-a-support-request-for-hana-large-instances"></a>Aprire una richiesta di supporto per le istanze large di HANA
Dal portale di Azure visualizzare le unità di istanze large di HANA, è possibile creare richieste di supporto specifiche per un'unità di istanze large di HANA. Quando si segue la **nuova richiesta di supporto** 

![avviare il passaggio della richiesta di servizio #1 in portale di Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Per ottenere il servizio di SAP HANA in istanze Large elencato nella schermata successiva, potrebbe essere necessario selezionare "tutti i servizi" come illustrato di seguito.

![Selezionare tutti i servizi in portale di Azure](./media/hana-li-portal/portal-create-service-request.png)

Nell'elenco dei servizi è possibile trovare il servizio **SAP Hana istanza large**. Quando si sceglie il servizio, è possibile selezionare tipi di problemi specifici, come illustrato:


![Selezionare la classe di problema nella portale di Azure](./media/hana-li-portal/portal-select-problem-class.png)

In ognuno dei diversi tipi di problemi, viene offerta una selezione di sottotipi di problemi che è necessario selezionare per caratterizzare ulteriormente il problema. Dopo aver selezionato il sottotipo, è ora possibile denominare l'oggetto. Al termine del processo di selezione, è possibile passare al passaggio successivo della creazione. Nella sezione **Solutions** viene indicata la documentazione relativa alle istanze large di Hana, che potrebbero fornire un puntatore a una soluzione del problema. Se non è possibile trovare una soluzione per il problema nella documentazione suggerita, andare al passaggio successivo. Nel passaggio successivo verrà chiesto se il problema riguarda le macchine virtuali o le unità di istanze large di HANA. Queste informazioni consentono di indirizzare la richiesta di supporto agli specialisti corretti. 

![Dettagli del caso di supporto in portale di Azure](./media/hana-li-portal/portal-support-request-details.png)

Con la risposta alle domande e sono stati forniti dettagli aggiuntivi, è possibile procedere al passaggio successivo per esaminare la richiesta di supporto e inviarla.

## <a name="next-steps"></a>Passaggi successivi

- [Come monitorare SAP HANA (istanze large) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitoraggio e risoluzione dei problemi dal lato HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)


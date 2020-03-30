---
title: Controllo di istanze di grandi dimensioni HANA di Azure tramite il portale di Azure. Documenti Microsoft
description: Descrive il modo in cui è possibile identificare e interagire con le istanze di grandi dimensioni di Azure HANA tramite il portale
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099807"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controllo di istanze Large di HANA in Azure tramite il portale di Azure
Questo documento illustra il modo in cui le [istanze di grandi dimensioni HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vengono presentate nel portale di [Azure](https://portal.azure.com) e quali attività possono essere eseguite tramite il portale di Azure con le unità di istanza di grandi dimensioni HANA distribuite per l'utente. La visibilità delle istanze di grandi dimensioni HANA nel portale di Azure viene fornita tramite un provider di risorse di Azure per le istanze di grandi dimensioni HANA, attualmente in anteprima pubblica

## <a name="register-hana-large-instance-resource-provider"></a>Registrare il provider di risorse di istanza HANA di grandi dimensioniRegister HANA Large Instance Resource Provider
In genere la sottoscrizione di Azure usata per le distribuzioni di istanze di grandi dimensioni HANA è registrata per il provider di risorse di istanze di grandi dimensioni HANA. Tuttavia, se non è possibile visualizzare le unità di istanza di grandi dimensioni HANA distribuite, è necessario registrare il provider di risorse nella sottoscrizione di Azure.However, if you can't see you deployed HANA Large Instance units, you should register the Resource Provider in your Azure subscription. La registrazione del provider di risorse di istanza di grandi dimensioni HANA è in due modi

### <a name="register-through-cli-interface"></a>Registrati tramite interfaccia CLI
È necessario essere connessi alla sottoscrizione di Azure, usati per la distribuzione di istanze di grandi dimensioni HANA tramite l'interfaccia della riga di comando di Azure.You need to be logged into your Azure subscription, used for the HANA Large Instance deployment via the Azure CLI interface. È possibile (re)registrare il provider di istanze di grandi dimensioni HANA con questo comando:You can (re-)register the HANA Large Instance Provider with this command:
    
    az provider register --namespace Microsoft.HanaOnAzure

Per altre informazioni, vedere l'articolo Tipi e provider di risorse di [AzureFor](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli) more information, see the article Azure resource providers and types


### <a name="register-through-azure-portal"></a>Registrare tramite il portale di AzureRegister through Azure portal
È possibile (ri)registrare il provider di risorse di istanze di grandi dimensioni HANA tramite il portale di Azure.You can (re-)register the HANA Large Instance Resource Provider through Azure portal. È necessario elencare la sottoscrizione nel portale di Azure e fare doppio clic sulla sottoscrizione, usata per distribuire le unità di istanza HANA Large Instance. Uno si è nella pagina di panoramica della sottoscrizione, selezionare "Provider di risorse" come illustrato di seguito e digitare "HANA" nella finestra di ricerca. 

![Registrare HLI RP tramite il portale di AzureRegister HLI RP through Azure portal](./media/hana-li-portal/portal-register-hli-rp.png)

Nella schermata visualizzata, il provider di risorse è già stato registrato. Nel caso in cui il provider di risorse non sia ancora registrato, premere "re-register" o "register".

Per altre informazioni, vedere l'articolo Tipi e provider di risorse di [AzureFor](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell) more information, see the article Azure resource providers and types


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Visualizzazione di unità di istanza di grandi dimensioni HANA nel portale di AzureDisplay of HANA Large Instance units in the Azure portal
Quando si invia una richiesta di distribuzione di un'istanza di grandi dimensioni HANA, viene chiesto di specificare anche la sottoscrizione di Azure che si connette alle istanze DI grandi dimensioni HANA. È consigliabile usare la stessa sottoscrizione usata per distribuire il livello dell'applicazione SAP che funziona con le unità di istanza di grandi dimensioni HANA.
Quando le prime istanze HANA di grandi dimensioni vengono distribuite, viene creato un nuovo gruppo di risorse di [Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) nella sottoscrizione di Azure inviata nella richiesta di distribuzione per le istanze HANA di grandi dimensioni.  Il nuovo gruppo di risorse elencherà tutte le unità di istanza di grandi dimensioni HANA distribuite nella sottoscrizione specifica.

Per trovare il nuovo gruppo di risorse di Azure, elencare il gruppo di risorse nella sottoscrizione navigando nel riquadro di spostamento sinistro del portale di Azure

![Riquadro di spostamento nel portale di AzureNavigation pane in Azure portal](./media/hana-li-portal/portal-resource-group.png)

Nell'elenco dei gruppi di risorse, che si sta ottenendo elencati, potrebbe essere necessario filtrare in base alla sottoscrizione utilizzata per la distribuzione di istanze di grandi dimensioni HANA

![Filtrare i gruppi di risorse nel portale di AzureFilter resource groups in Azure portal](./media/hana-li-portal/portal-filtering-subscription.png)

Dopo aver filtrato la sottoscrizione corretta, è ancora possibile disporre di un lungo elenco di gruppi di risorse. Cercare uno con una post-correzione di **-Txxx** dove "xxx" sono tre cifre, ad esempio **-T050**. 

Quando è stato trovato il gruppo di risorse, elencarne i dettagli. L'elenco che hai ricevuto potrebbe essere simile al seguente:

![Elenco HLI nel portale di AzureHLI list in Azure portal](./media/hana-li-portal/portal-hli-units-list.png)

Tutte le unità elencate rappresentano una singola unità HANA Large Instance distribuita nella sottoscrizione. In questo caso, si esaminano otto diverse unità di istanza di grandi dimensioni HANA, distribuite nella sottoscrizione.

Se sono stati distribuiti più tenant di istanze di grandi dimensioni HANA nella stessa sottoscrizione di Azure, sono disponibili più gruppi di risorse di AzureIf you deployed several HANA Large Instance tenants under the same Azure subscription, you will find multiple Azure resource groups 


## <a name="look-at-attributes-of-single-hli-unit"></a>Osservare gli attributi di una singola unità HLI
Nell'elenco delle unità di istanza HANA Large, è possibile fare clic su una singola unità e ottenere i dettagli della singola unità di istanza HANA Large. 

Nella schermata di panoramica, dopo aver fatto clic su "Mostra altro", si riceve una presentazione dell'unità, che ha l'aspetto di:

![Mostra panoramica di un'unità HLI](./media/hana-li-portal/portal-show-overview.png)

Esaminando i diversi attributi visualizzati, tali attributi hanno un aspetto non diverso rispetto agli attributi della macchina virtuale di Azure.Looking at the different attributes shown, those attributes look no different than Azure VM attributes. Nell'intestazione sul lato sinistro vengono visualizzati il gruppo di risorse, l'area di Azure, il nome e l'ID della sottoscrizione, nonché alcuni tag aggiunti. Per impostazione predefinita, alle unità di istanza di grandi dimensioni HANA non è assegnato alcun tag. Sul lato destro dell'intestazione, il nome dell'unità viene elencato come assegnato al termine della distribuzione. Vengono visualizzati il sistema operativo e l'indirizzo IP. Come per le macchine virtuali viene visualizzato anche il tipo di unità di istanza HANA Large con il numero di thread della CPU e memoria. Ulteriori dettagli sulle diverse unità di istanza HANA Large sono riportate di seguito:

- [SKU disponibili per HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architettura di archiviazione di SAP HANA (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Dati aggiuntivi sul lato inferiore destro è la revisione del timbro HANA Large Instance. I valori possibili sono:

- Revisione 3
- Revisione 4

Revisione 4 è l'architettura più recente rilasciata di istanze di grandi dimensioni HANA con miglioramenti importanti nella latenza di rete tra macchine virtuali di Azure e unità di istanza di grandi dimensioni HANA distribuite in revisioni 4 o righe.
Un'altra informazione molto importante si trova nell'angolo inferiore destro della panoramica con il nome del gruppo di posizionamento di prossimità di Azure creato automaticamente per ogni unità di istanza HANA di grandi dimensioni distribuita. È necessario fare riferimento a questo gruppo di posizionamento di prossimità quando si distribuiscono le macchine virtuali di Azure che ospitano il livello dell'applicazione SAP. Usando il gruppo di posizionamento di [prossimità](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) di Azure associato all'unità Istanza di grandi dimensioni HANA, assicurarsi che le macchine virtuali di Azure vengano distribuite in prossimità dell'unità istanza di grandi dimensioni HANA. Il modo in cui è possibile usare i gruppi di posizionamento di prossimità per individuare il livello dell'applicazione SAP nello stesso data center di Azure come unità di istanza di grandi dimensioni HANA ospitate dalla revisione 4 è descritto in Gruppi di posizionamento di prossimità di Azure per una latenza di [rete ottimale con le applicazioni SAP.](sap-proximity-placement-scenarios.md)

Un campo aggiuntivo nella colonna destra dell'intestazione informa sullo stato di alimentazione dell'unità di istanza HANA Large.

> [!NOTE]
> Lo stato di alimentazione indica se l'unità hardware è accesa o spenta. Non fornisce informazioni sul sistema operativo è in funzione. Quando si riavvia un'unità di istanza di grandi dimensioni HANA, si verificherà un piccolo periodo di tempo in cui lo stato dell'unità diventa **Inizio** per spostarsi nello stato **Iniziato**. Essere nello stato di **Iniziato** significa che il sistema operativo si sta avviando o che il sistema operativo è stato avviato completamente. Di conseguenza, dopo un riavvio dell'unità, non è possibile prevedere di accedere immediatamente all'unità non appena lo stato passa a **Iniziato**.
> 

Se si preme "Vedi di più", vengono visualizzate ulteriori informazioni. Un'informazione aggiuntiva indica la revisione del timbro Istanza di grandi dimensioni HANA, l'unità è stata distribuita. Vedere l'articolo [Che cos'è SAP HANA in Azure (istanze di grandi dimensioni)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) per le diverse revisioni dei timbri di istanza di grandi dimensioni HANASee the article What is SAP HANA on Azure (Large Instances) for the different revisions of HANA Large Instance stamps

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Controllare le attività di una singola unità di istanza HANA Large 
Oltre a fornire una panoramica delle unità di istanza di grandi dimensioni HANA, è possibile controllare le attività dell'unità specifica. Un log attività potrebbe essere simile al seguente:An activity log could look like:

![Riquadro di spostamento nel portale di AzureNavigation pane in Azure portal](./media/hana-li-portal/portal-activity-list.png)

Una delle principali attività registrate sono i riavvii di un'unità. I dati elencati includono lo stato dell'attività, il timestamp in cui è stata attivata l'attività, l'ID sottoscrizione da cui è stata attivata l'attività e l'utente di Azure che ha attivato l'attività. 

Un'altra attività che viene registrata sono le modifiche all'unità nei metadati di Azure.Another activity that is getting recorded are changes to the unit in the Azure meta data. Oltre al riavvio avviato, è possibile visualizzare l'attività di **scrittura HANAInstances**. Questo tipo di attività non esegue alcuna modifica sull'unità di istanza di tipo HANA Large, ma documenta le modifiche ai metadati dell'unità in Azure.This type of activity performs no changes on the HANA Large Instance unit itself, but is documenting changes to the meta data of the unit in Azure. Nel caso elencato, abbiamo aggiunto ed eliminato un tag (vedi sezione successiva).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Aggiungere ed eliminare un tag di Azure in un'unità di istanza HANA Large Add and delete an Azure tag to a HANA Large Instance unit
Un'altra possibilità è quella di aggiungere un [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a un'unità di istanza di grandi dimensioni HANA. Il modo in cui i tag vengono assegnati non varia dall'assegnazione di tag alle macchine virtuali. Come per le macchine virtuali, i tag esistono nei metadati di Azure e, per le istanze di grandi dimensioni HANA, hanno le stesse restrizioni dei tag per le macchine virtuali.

L'eliminazione dei tag funziona allo stesso modo delle macchine virtuali. Entrambe le attività, l'applicazione e l'eliminazione di un tag verranno elencate nel registro attività della particolare unità di istanza HANA Large.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Controllare le proprietà di un'unità di istanza DI grandi dimensioni HANACheck properties of a HANA Large Instance unit
La sezione **Proprietà** include informazioni importanti che si ottengono quando le istanze vengono consegnate all'utente. Si tratta di una sezione in cui si ottengono tutte le informazioni che è possibile richiedere nei casi di supporto o che è necessario quando si imposta la configurazione dello snapshot di archiviazione. Di conseguenza, questa sezione è una raccolta di dati intorno all'istanza, la connettività dell'istanza ad Azure e il back-end di archiviazione. La parte superiore della sezione è simile a:


![parte superiore delle proprietà HLI nel portale di Azure](./media/hana-li-portal/portal-properties-top.png)

I primi elementi di dati, già visualizzati nella schermata di panoramica. Ma una parte importante dei dati è l'ID circuito ExpressRoute, ottenuto come prime unità distribuite sono state consegnate. In alcuni casi di supporto, è possibile che vengano richiesti tali dati. Una voce di dati importante viene visualizzata nella parte inferiore dello screenshot. I dati visualizzati sono l'indirizzo IP dell'intestazione di archiviazione NFS che isola lo spazio di archiviazione nel **tenant** nello stack di istanze di grandi dimensioni HANA. Questo indirizzo IP è necessario anche quando si modifica il file di configurazione per i backup snapshot di [archiviazione.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots) 

Mentre si scorre verso il basso nel riquadro delle proprietà, si ottengono dati aggiuntivi come un ID di risorsa univoco per l'unità di istanza HANA Large o l'ID sottoscrizione assegnato alla distribuzione.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Riavviare un'unità di istanza di grandi dimensioni HANA tramite il portale di AzureRestart a HANA Large Instance unit through Azure portal
Infase di un riavvio del sistema operativo Linux, ci sono state varie situazioni in cui il sistema operativo non è riuscito a completare correttamente il riavvio. Per forzare un riavvio, è necessario aprire una richiesta di servizio per fare in modo che le operazioni Microsoft eseguano un riavvio dell'unità di istanza DI grandi dimensioni HANA. La funzionalità di un riavvio dell'alimentazione di un'unità di istanza HANA large è ora integrata nel portale di Azure.The functionality of a power restart of a HANA Large Instance unit is now integrated into the Azure portal. Nella parte di panoramica dell'unità Istanza di grandi dimensioni HANA, viene visualizzato il pulsante per il riavvio nella parte superiore della sezione dei dati

![Passaggio di riavvio #1 nel portale di AzureRestart step #1 in Azure portal](./media/hana-li-portal/portal-restart-first-step.png)

Mentre si preme il pulsante di riavvio, viene chiesto se si desidera veramente riavviare l'unità. Come si conferma premendo il pulsante "Sì", l'unità si riavvierà.

> [!NOTE]
> Nel processo di riavvio, si verificherà un piccolo periodo di tempo in cui lo stato dell'unità cambia in **Avvio** per spostarsi nello stato **Iniziato**. Essere nello stato di **Iniziato** significa che il sistema operativo si sta avviando o che il sistema operativo è stato avviato completamente. Di conseguenza, dopo un riavvio dell'unità, non è possibile prevedere di accedere immediatamente all'unità non appena lo stato passa a **Iniziato**.

> [!IMPORTANT]
> A seconda della quantità di memoria nell'unità di istanza HANA Large Instance, il riavvio e il riavvio dell'hardware e del sistema operativo possono richiedere fino a un'ora


## <a name="open-a-support-request-for-hana-large-instances"></a>Aprire una richiesta di supporto per istanze HANA di grandi dimensioniOpen a support request for HANA large Instances
Dalla visualizzazione del portale di Azure delle unità di istanza di grandi dimensioni HANA, è possibile creare richieste di supporto specifiche anche per un'unità di istanza di grandi dimensioni HANA. Come si segue il link **Nuova richiesta** di supporto 

![avviare il passaggio della richiesta di servizio #1 nel portale di AzureInitiate service request step #1 in Azure portal](./media/hana-li-portal/portal-initiate-support-request.png)

Per ottenere il servizio di istanze di grandi dimensioni SAP HANA elencate nella schermata successiva, potrebbe essere necessario selezionare "Tutti i servizi" come mostrato di seguito

![Selezionare tutti i servizi nel portale di AzureSelect all services in Azure portal](./media/hana-li-portal/portal-create-service-request.png)

Nell'elenco dei servizi è possibile trovare il servizio **SAP HANA Large Instance**. Quando si sceglie tale servizio, è possibile selezionare tipi di problemi specifici, come illustrato di seguito:As you choose that service, you can select specific problem types as shown:


![Selezionare la classe di problemi nel portale di AzureSelect problem class in Azure portal](./media/hana-li-portal/portal-select-problem-class.png)

Sotto ciascuno dei diversi tipi di problema, ti viene offerta una selezione di sottotipi di problemi che devi selezionare per caratterizzare ulteriormente il problema. Dopo aver selezionato il sottotipo, è ora possibile assegnare un nome all'oggetto. Una volta completato il processo di selezione, è possibile passare al passaggio successivo della creazione. Nella sezione **Soluzioni** viene puntata la documentazione relativa alle istanze di grandi dimensioni HANA, che potrebbe fornire un puntatore a una soluzione del problema. Se non riesci a trovare una soluzione per il tuo problema nella documentazione suggerita, vai al passaggio successivo. Nel passaggio successivo verrà chiesto se il problema riguarda le macchine virtuali o le unità di istanza DI grandi dimensioni HANA. Queste informazioni aiutano a indirizzare la richiesta di supporto agli specialisti corretti. 

![Dettagli del caso di supporto nel portale di AzureDetails of support case in Azure portal](./media/hana-li-portal/portal-support-request-details.png)

Quando hai risposto alle domande e fornito ulteriori dettagli, puoi andare al passaggio successivo per esaminare la richiesta di supporto e inviarla.

## <a name="next-steps"></a>Passaggi successivi

- [Come monitorare SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitoraggio e risoluzione dei problemi dal lato HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)


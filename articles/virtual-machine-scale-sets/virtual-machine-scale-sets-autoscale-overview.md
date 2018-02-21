---
title: "Panoramica della scalabilità automatica con i set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Informazioni sui diversi metodi con cui è possibile scalare automaticamente un set di scalabilità di macchine virtuali in base alle prestazioni o a una pianificazione fissa"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 868523a3aca441a47218297be2ce9f9e46dd84a1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Panoramica della scalabilità automatica con i set di scalabilità di macchine virtuali di Azure
Un set di scalabilità di macchine virtuali di Azure può aumentare o diminuire automaticamente il numero di istanze di macchine virtuali che eseguono l'applicazione. Questo comportamento automatico ed elastico riduce il sovraccarico di gestione per monitorare e ottimizzare le prestazioni dell'applicazione. Creare regole che definiscono le prestazioni minime accettabili per un'esperienza positiva del cliente. Quando vengono soddisfatte le soglie definite, le regole di scalabilità automatica intervengono per regolare la capacità del set di scalabilità. È possibile anche pianificare eventi per aumentare o ridurre automaticamente la capacità del set di scalabilità a intervalli fissi. Questo articolo offre una panoramica delle metriche delle prestazioni disponibili e delle azioni che la scalabilità automatica può eseguire.


## <a name="benefits-of-autoscale"></a>Vantaggi della scalabilità automatica
Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nel set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità.

Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. È possibile controllare le metriche da monitorare, ad esempio per la CPU o la memoria, il tempo per cui il carico dell'applicazione deve soddisfare una determinata soglia e il numero di istanze di macchine virtuali da aggiungere al set di scalabilità.

Nelle ore serali o nel fine settimana è possibile che la richiesta delle applicazioni si riduca. Se il carico diminuisce in modo coerente nel tempo, è possibile configurare regole di scalabilità automatica per diminuire il numero di istanze di macchine virtuali nel set di scalabilità. Questa azione riduce i costi di esecuzione del set di scalabilità poiché si esegue solo il numero di istanze necessarie per soddisfare la richiesta corrente.


## <a name="use-host-based-metrics"></a>Usare le metriche basate su host
È possibile creare regole di scalabilità automatica basate sulle metriche host disponibili dalle istanze di macchine virtuali. Le metriche basate su host consentono maggiore visibilità delle prestazioni delle istanze di macchine virtuali in un set di scalabilità senza la necessità di installare o configurare ulteriori agenti e raccolte di dati. Le regole di scalabilità automatica che usano queste metriche possono aumentare o ridurre il numero di istanze di macchine virtuali in risposta all'utilizzo della CPU, alla richiesta di memoria o all'accesso al disco.

È possibile creare regole di scalabilità automatica che usano metriche basate su host con uno degli strumenti seguenti:

- [Azure portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Per creare le regole di scalabilità automatica che usano metriche delle prestazioni più dettagliate, è possibile [installare e configurare l'estensione Diagnostica di Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) sulle istanze di macchine virtuali o [configurare l'applicazione in modo che usi App Insights](#application-level-metrics-with-app-insights).

Le regole di scalabilità automatica che usano le metriche basate su host, le metriche delle macchine virtuali nel guest con l'estensione di diagnostica di Azure e App Insights possono usare le seguenti impostazioni di configurazione.

### <a name="metric-sources"></a>Origini delle metriche
Le regole di scalabilità automatica possono usare le metriche fornite da una delle seguenti origini:

| Origine della metrica        | Caso d'uso                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Set di scalabilità corrente    | Per le metriche basate su host che non richiedono l'installazione o la configurazione di altri agenti.                                  |
| Account di archiviazione      | L'estensione di diagnostica di Azure scrive le metriche delle prestazioni nell'archiviazione di Azure che quindi viene usata per attivare le regole di scalabilità automatica. |
| Coda del bus di servizio    | L'applicazione o altri componenti possono trasmettere i messaggi in una coda del bus di servizio di Azure per attivare le regole.                   |
| Application Insights | Un pacchetto di strumentazione installato nell'applicazione che trasmette le metriche direttamente dall'app.                         |


### <a name="autoscale-rule-criteria"></a>Criteri delle regole di scalabilità automatica
È possibile usare le metriche basate su host seguenti quando si creano regole di scalabilità automatica. Se si usa l'estensione di diagnostica di Azure o App Insights, si definiscono le metriche da monitorare e usare con le regole di scalabilità automatica.

| Nome metrica               |
|---------------------------|
| CPU percentuale            |
| Rete in ingresso                |
| Rete in uscita               |
| Byte letti da disco           |
| Byte scritti su disco          |
| Operazioni lettura disco/sec  |
| Operazioni scrittura disco/sec |
| Crediti CPU rimanenti     |
| Crediti CPU usati      |

Quando si creano regole di scalabilità automatica per monitorare una determinata metrica, le regole esaminano una delle seguenti azioni di aggregazione di metriche:

| Tipo di aggregazione |
|------------------|
| Media          |
| Minima          |
| Massima          |
| Totale            |
| Last (Ultimo)             |
| Conteggio            |

Le regole di scalabilità automatica vengono attivate quando le metriche vengono confrontate con la soglia definita usando uno dei seguenti operatori:

| Operatore                 |
|--------------------------|
| Maggiore di             |
| Maggiore o uguale a |
| Minore di                |
| Minore o uguale a    |
| Uguale a                 |
| Diverso da             |


### <a name="actions-when-rules-trigger"></a>Azioni eseguite all'attivazione delle regole
Quando viene attivata una regola di scalabilità automatica, il set di scalabilità può eseguire la scalabilità in uno dei modi seguenti:

| Operazione di scalabilità     | Caso d'uso                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumenta numero di   | Un numero fisso di istanze di macchine virtuali da creare. È utile nei set di scalabilità con un numero ridotto di macchine virtuali.                                           |
| Aumenta percentuale di | Aumento delle istanze di macchine virtuali basato su una percentuale. Adatto per set di scalabilità più grandi in cui un aumento predefinito può non migliorare notevolmente le prestazioni. |
| Aumenta numero a   | Creare il numero di istanze di macchine virtuali necessarie per raggiungere un limite massimo desiderato.                                                            |
| Riduci numero di   | Un numero fisso di istanze di macchine virtuali da rimuovere. È utile nei set di scalabilità con un numero ridotto di macchine virtuali.                                           |
| Riduci percentuale di | Diminuzione delle istanze di macchine virtuali basata su una percentuale. Adatto per set di scalabilità più grandi in cui un aumento predefinito può non ridurre notevolmente i consumi e i costi. |
| Riduci numero di   | Rimuovere il numero di istanze di macchine virtuali necessarie per raggiungere una quantità minima desiderata.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Metriche delle macchine virtuali nel guest con l'estensione di diagnostica di Azure
L'estensione di diagnostica di Azure è un agente eseguito all'interno di un'istanza di macchine virtuali. L'agente monitora e salva le metriche delle prestazioni nell'archiviazione di Azure. Queste metriche delle prestazioni contengono informazioni più dettagliate sullo stato della macchina virtuale, ad esempio *AverageReadTime* per i dischi o *PercentIdleTime* per la CPU. È possibile creare regole di scalabilità automatica in base a una conoscenza più dettagliata delle prestazioni delle macchine virtuali, non solo della percentuale di utilizzo della memoria o della CPU.

Per usare l'estensione Diagnostica di Azure, è necessario creare gli account di archiviazione di Azure per le istanze di macchine virtuali, installare l'agente di diagnostica di Azure, quindi configurare le macchine virtuali per tramettere contatori delle prestazioni specifici nell'account di archiviazione.

Per altre informazioni, vedere gli articoli su come abilitare l'estensione Diagnostica di Azure in una [VM Linux](../virtual-machines/linux/diagnostic-extension.md) o in una [VM Windows](../virtual-machines/windows/ps-extensions-diagnostics.md).


## <a name="application-level-metrics-with-app-insights"></a>Metriche a livello di applicazione con App Insights
Per ottenere maggiore visibilità delle prestazioni delle applicazioni, è possibile usare Application Insights. Si installa un pacchetto di strumentazione di piccole dimensioni nell'applicazione che monitora l'app e invia i dati di telemetria ad Azure. È possibile monitorare metriche come i tempi di risposta dell'applicazione, le prestazioni in fase di caricamento delle pagine e i conteggi di sessioni. Queste metriche consentono di creare regole di scalabilità automatica a un livello granulare e incorporato poiché le regole attivate si basano su informazioni dettagliate e di utilità pratica che possono incidere sull'esperienza dell'utente.

Per altre informazioni su Application Insights, vedere [Informazioni su Azure Application Insights](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Scalabilità automatica pianificata
È possibile anche creare regole di scalabilità automatica in base a pianificazioni. Queste regole consentono di scalare automaticamente il numero di istanze di macchine virtuali a intervalli fissi. Con le regole basate su prestazioni, le prestazioni dell'applicazione potrebbero essere compromesse prima che vengano attivate le regole di scalabilità automatica e che venga effettuato il provisioning delle nuove istanze di macchine virtuali. Se è possibile prevedere questa richiesta, viene eseguito il provisioning di ulteriori istanze di macchine virtuali e queste sono pronte per soddisfare la richiesta aggiuntiva di utilizzo da parte del cliente e dell'applicazione.

Di seguito sono riportati alcuni scenari di esempio che possono trarre vantaggio dall'utilizzo di regole di scalabilità automatica basate su pianificazione:

- Scalare automaticamente il numero di istanze di macchine virtuali all'inizio della giornata di lavoro quando aumentano le richieste dei clienti. Al termine della giornata lavorativa ridurre automaticamente il numero di istanze di macchine virtuali per ridurre al minimo i costi delle risorse nelle ore notturne quando l'utilizzo delle applicazioni è inferiore.
- Se un reparto usa un'applicazione in modo intensivo in specifici periodi del ciclo fiscale o mensile, ridimensionare automaticamente il numero di istanze di macchine virtuali per soddisfare queste richieste aggiuntive.
- Quando si verifica un evento di marketing, una promozione o in periodo di saldi, è possibile ridimensionare automaticamente il numero di istanze di macchine virtuali in anticipo rispetto all'esigenza del cliente. 


## <a name="next-steps"></a>Passaggi successivi
È possibile creare regole di scalabilità automatica che usano metriche basate su host con uno degli strumenti seguenti:

- [Azure portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Questa panoramica ha descritto in dettaglio come usare le regole di scalabilità automatica per scalare orizzontalmente e aumentare o diminuire il *numero* di istanze di macchine virtuali nel set di scalabilità. È possibile anche ridimensionare in verticale per aumentare o diminuire le *dimensioni* delle istanze di macchine virtuali. Per altre informazioni, vedere [Ridimensionamento automatico verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Per informazioni su come gestire le istanze di macchine virtuali, vedere [Gestire set di scalabilità di macchine virtuali con Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Per informazioni su come generare avvisi all'attivazione delle regole di scalabilità automatica, vedere [Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Monitoraggio di Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Vedere anche [Use audit logs to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) (Usare i log di controllo per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).
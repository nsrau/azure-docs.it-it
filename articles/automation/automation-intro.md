---
title: Panoramica di Automazione di Azure
description: Informazioni su come usare Automazione di Azure per automatizzare il ciclo di vita dell'infrastruttura e delle applicazioni.
services: automation
ms.subservice: process-automation
keywords: automazione di azure, DSC, powershell, configurazione dello stato, gestione aggiornamenti, rilevamento modifiche, DSC, inventario, runbook, python, grafico
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010240"
---
# <a name="an-introduction-to-azure-automation"></a>Introduzione ad Automazione di Azure

Questo articolo fornisce una breve panoramica di Automazione di Azure e risposte ad alcune domande comuni. Per altre informazioni sulle diverse funzionalità, visitare i collegamenti indicati in questa panoramica.

## <a name="about-azure-automation"></a>Informazioni su Automazione di Azure

Automazione di Azure offre un servizio di automazione e configurazione basato sul cloud che supporta la gestione coerente di ambienti Azure e non Azure. Include automazione dei processi, gestione della configurazione, gestione degli aggiornamenti, funzionalità condivise e funzionalità eterogenee. Automazione fornisce il controllo completo durante la distribuzione, l'attività e la rimozione delle autorizzazioni di carichi di lavoro e risorse.

![Funzionalità di Automazione](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automazione dei processi

L'automazione dei processi in Automazione di Azure consente di automatizzare tutte le attività di gestione cloud frequenti, dispendiose in termini di tempo e soggette a errori. Questo servizio consente di concentrarsi sulle attività che apportano valore aggiunto. Grazie alla riduzione degli errori e all'incremento dell'efficienza, Automazione di Azure favorisce anche la riduzione dei costi operativi. L'ambiente operativo di automazione dei processi è descritto in [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md).

L'automazione dei processo supporta l'integrazione dei servizi di Azure e di altri sistemi pubblici richiesti nella distribuzione, configurazione e gestione di processi end-to-end. Il servizio consente di creare [runbook](automation-runbook-types.md) in forma grafica in PowerShell o con Python. Usando un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) è possibile unificare la gestione con l'orchestrazione degli ambienti locali. I [webhook](automation-webhooks.md) consentono di soddisfare le richieste e assicurare la continuità del recapito e delle operazioni attivando l'automazione da ITSM, DevOps e sistemi di monitoraggio. 

## <a name="configuration-management"></a>Gestione della configurazione

La [configurazione dello stato desiderato](automation-dsc-overview.md) di Automazione di Azure è una soluzione basata sul cloud per PowerShell Desired State Configuration (DSC) che fornisce i servizi necessari per gli ambienti aziendali. Con questa funzionalità è possibile gestire le risorse DSC in Automazione di Azure e applicare le configurazioni alle macchine virtuali o fisiche da un server di pull DSC nel cloud di Azure. È possibile monitorare e aggiornare automaticamente le configurazioni tra macchine virtuali o fisiche in Windows o Linux, nel cloud o in locale. Il supporto dell'inventario consente di eseguire query sulle risorse nel guest per ottenere la visibilità delle applicazioni installate e di altri elementi di configurazione.
 
Il servizio di configurazione dello stato di Automazione di Azure offre funzionalità avanzate di creazione report e ricerca. È possibile usare queste funzionalità per individuare informazioni dettagliate sugli elementi configurati all'interno di un sistema operativo. Il servizio supporta il rilevamento delle modifiche tra servizi, daemon, software, registro di sistema e file nell'ambiente in uso per la diagnosi delle modifiche indesiderate e la generazione di avvisi. Una funzionalità importante correlata è la creazione di report di eventi principali, ad esempio gli eventi generati quando i nodi deviano dalle relative configurazioni assegnate. 

## <a name="update-management"></a>Gestione degli aggiornamenti

Automazione di Azure include la soluzione di [gestione degli aggiornamenti](automation-update-management.md) per i sistemi Windows e Linux in ambienti ibridi. Con questa soluzione è possibile ottenere la visibilità della conformità degli aggiornamenti in Azure e nell'ambiente locale e in altri ambienti cloud. La gestione degli aggiornamenti consente di creare distribuzioni pianificate per orchestrare l'installazione degli aggiornamenti all'interno di una finestra di manutenzione definita. Se un aggiornamento non deve essere installato in un computer, è possibile usare le funzionalità di gestione degli aggiornamenti per escluderlo da una distribuzione.

## <a name="shared-capabilities"></a>Funzionalità condivise

Automazione di Azure offre diverse funzionalità condivise, tra cui risorse condivise, controllo degli accessi in base al ruolo, pianificazione flessibile, integrazione del controllo del codice sorgente, controllo e assegnazione di tag.

### <a name="shared-resources"></a><a name="shared-resources"></a>Risorse condivise

Automazione di Azure è costituito da un set di risorse condivise che semplificano l'automazione e la configurazione degli ambienti su larga scala.

* **[Pianificazioni](automation-schedules.md)** : consentono di attivare le operazioni di Automazione a intervalli predefiniti.
* **[Moduli](automation-integration-modules.md)** : consentono di gestire Azure e altri sistemi. È possibile importare i moduli nell'account di Automazione per cmdlet e risorse DSC Microsoft, di terze parti, della community e personalizzati.
* **[Raccolta di moduli](automation-runbook-gallery.md)** : supporta l'integrazione nativa con PowerShell Gallery per visualizzare i runbook e importarli nell'account di Automazione. La raccolta consente di iniziare rapidamente usando l'integrazione e la creazione dei processi da PowerShell Gallery e Microsoft Script Center.
* **[Pacchetti Python 2](python-packages.md)** : supportano i runbook Python 2 per l'account di Automazione.
* **[Credenziali](automation-credentials.md)** : consentono di proteggere le informazioni riservate che possono essere usate da runbook e configurazioni in fase di esecuzione.
* **[Connessioni](automation-connections.md)** : consentono di archiviare le coppie nome-valore delle informazioni comuni per le connessioni ai sistemi. L'autore del modulo definisce le connessioni nei runbook e nelle configurazioni per essere usate in fase di esecuzione.
* **[Certificati](automation-certificates.md)** : definiscono le informazioni da usare per l'autenticazione e la protezione delle risorse distribuite quando si accede ai runbook o alle configurazioni DSC in fase di esecuzione. 
* **[Variabili](automation-variables.md)** : contengono contenuti che possono essere usati nei runbook e nelle configurazioni. È possibile modificare i valori delle variabili senza dover modificare i runbook o le configurazioni che vi fanno riferimento.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Automazione di Azure supporta il controllo degli accessi in base al ruolo per regolare l'accesso all'account di Automazione e alle relative risorse. Per altre informazioni sulla configurazione del controllo degli accessi in base al ruolo nell'account di Automazione, in runbook e processi, vedere [Controllo degli accessi in base al ruolo per Automazione di Azure](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integrazione del controllo del codice sorgente

Automazione di Azure consente l'[integrazione del controllo del codice sorgente](source-control-integration.md). Questa funzionalità promuove la configurazione come codice in cui è possibile archiviare runbook o configurazioni in un sistema di controllo del codice sorgente.

## <a name="heterogeneous-support-windows-and-linux"></a>Supporto eterogeneo (Windows e Linux)

Automazione è progettato per funzionare nell'ambiente cloud ibrido e anche nei sistemi Windows e Linux. Offre un modo coerente per automatizzare e configurare i carichi di lavoro distribuiti e il sistema operativo sul quale vengono eseguiti.

## <a name="common-scenarios-for-automation"></a>Scenari di Automazione comuni

Automazione di Azure supporta la gestione per tutto il ciclo di vita dell'infrastruttura e delle applicazioni. Gli scenari comuni includono:

* **Scrivere runbook**: creare runbook di PowerShell, del flusso di lavoro PowerShell, grafici, Python 2 e DSC in linguaggi comuni. 
* **Compilare e distribuire risorse**: distribuire macchine virtuali in un ambiente ibrido usando i runbook e i modelli di Azure Resource Manager. Eseguire l'integrazione negli strumenti di sviluppo come Jenkins e Azure DevOps.
* **Configurare le macchine virtuali**: valutare e configurare macchine Windows e Linux con configurazioni per l'infrastruttura e l'applicazione.
* **Condivisione delle conoscenze**: è possibile trasferire nel sistema informazioni sul modo in cui l'organizzazione distribuisce e gestisce i carichi di lavoro. 
* **Recuperare l'inventario**: ottenere un inventario completo delle risorse distribuite per targeting, creazione di report e conformità. 
* **Trovare modifiche**: identificare le modifiche che possono causare errori di configurazione e migliorare la conformità operativa.
* **Monitorare**: isolare le modifiche apportate al computer che causano problemi e correggere o inoltrare il problema ai sistemi di gestione.
* **Proteggere**: mettere in quarantena i computer se vengono generati avvisi di sicurezza. Definire i requisiti nel guest.
* **Governare**: configurare il controllo degli accessi in base al ruolo per i team. Recuperare le risorse inutilizzate.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Prezzo di Automazione

È possibile rivedere i prezzi associati ad Automazione di Azure nella pagina dei [prezzi](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un account di Automazione](automation-quickstart-create-account.md)


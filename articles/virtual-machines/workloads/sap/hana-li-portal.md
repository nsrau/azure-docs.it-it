---
title: Istanze Large di HANA Azure controllare tramite il portale di Azure | Microsoft Docs
description: Viene descritto il modo in cui è possibile identificare e interagire con le istanze Large di HANA Azure tramite portale
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009369"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controllo di istanze Large di HANA in Azure tramite il portale di Azure
Questo documento illustra il modo in cui [istanze Large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vengono presentati in [portale di Azure](https://portal.azure.com) e quali attività possono essere eseguite tramite portale di Azure con le unità di istanze Large di HANA che vengono distribuite automaticamente. Visibilità delle istanze Large di HANA nel portale di Azure viene fornita tramite un provider di risorse di Azure per istanze Large di HANA, che è attualmente in anteprima pubblica

## <a name="register-hana-large-instance-resource-provider"></a>Registra Provider di risorse in istanze Large di HANA
In genere la sottoscrizione di Azure che si usa per le distribuzioni di istanze Large di HANA è registrata per il Provider di risorse istanze grandi HANA. Tuttavia, se non è possibile visualizzare che le unità di istanze Large di HANA è stato distribuito, è necessario registrare il Provider di risorse nella sottoscrizione di Azure. Sono disponibili due modalità della registrazione del provider di risorsa istanza Large di HANA

### <a name="register-through-cli-interface"></a>Registrare tramite l'interfaccia della riga di comando
È necessario essere connessi alla sottoscrizione di Azure, usata per la distribuzione di istanze Large di HANA tramite l'interfaccia della riga di comando di Azure. È possibile (ri-) registrare il Provider di istanze Large di HANA con questo comando:
    
    az provider register --namespace Microsoft.HanaOnAzure

Per altre informazioni, vedere l'articolo [provider di risorse di Azure e tipi](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Registrazione tramite il portale di Azure
È possibile (ri) registrazione Provider di risorse di istanza grande HANA tramite il portale di Azure. È necessario elencare la sottoscrizione nel portale di Azure e fare doppio clic sulla sottoscrizione, che è stata usata per distribuire le unità di istanze Large di HANA. Un trovano nella pagina di panoramica della sottoscrizione, selezionare "Provider di risorse", come illustrato di seguito e digitare "HANA" nella finestra di ricerca. 

![Registrare HLI RP tramite il portale di Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Nella schermata illustrata, il provider di risorse è stato già registrato. Nel caso in cui il provider di risorse non è ancora registrato, premere "Re-register" o "register".

Per altre informazioni, vedere l'articolo [provider di risorse di Azure e tipi](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Visualizzazione di unità di istanze Large di HANA nel portale di Azure
Quando si invia una richiesta di distribuzione di istanze Large di HANA, viene chiesto di specificare la sottoscrizione di Azure che ci si connette a istanze Large di HANA nonché. È consigliabile, usare la stessa sottoscrizione in uso per distribuire il livello applicazione SAP che funziona per le unità di istanze Large di HANA.
Come la prima volta vengano distribuite istanze Large di HANA, una nuova [gruppo di risorse Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) viene creato nella sottoscrizione di Azure è stato inviato nella richiesta di distribuzione per le istanze Large di HANA.  Nuovo gruppo di risorse sono elencate tutte le unità di istanze Large di HANA che è stato distribuito nella sottoscrizione specifica.

Per trovare il nuovo gruppo di risorse di Azure, il gruppo di risorse nella sottoscrizione è elencare navigando tra il riquadro di spostamento a sinistra del portale di Azure

![Riquadro di spostamento nel portale di Azure](./media/hana-li-portal/portal-resource-group.png)

Nell'elenco dei gruppi di risorse, sono come entrare a far, potrebbe essere necessario filtrare la sottoscrizione che è stato usato per disporre di istanze Large di HANA distribuite

![Filtrare i gruppi di risorse nel portale di Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Dopo il filtro per la sottoscrizione corretta, si potrebbe essere comunque un lungo elenco di gruppi di risorse. Uno con una post-correzione di cercare **- Txxx** in cui "xxx" sono tre cifre, ad esempio **-T050**. 

Come osservato il gruppo di risorse, elencare i dettagli di esso. L'elenco che è stato ricevuto è simile:

![Elenco HLI nel portale di Azure](./media/hana-li-portal/portal-hli-units-list.png)

Tutte le unità elencate rappresentano una singola unità di istanze Large di HANA che è stata distribuita nella sottoscrizione. In questo caso, esaminiamo otto unità di istanze Large di HANA diversa, che sono state distribuite nella sottoscrizione.

Se è stato distribuito diversi tenant di istanze Large di HANA nella stessa sottoscrizione di Azure, si troverà più gruppi di risorse di Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Esaminare gli attributi della singola unità HLI
Nell'elenco delle unità di istanze Large di HANA, è possibile fare clic su una singola unità e ottenere i dettagli della singola unità di istanze Large di HANA. 

Nella schermata di panoramica, si riceve una presentazione dell'unità, che è simile a:

![Panoramica di presentazione di un'unità HLI](./media/hana-li-portal/portal-show-overview.png)

Esaminando i diversi attributi illustrati, tali attributi aspetto difficilmente diversi rispetto agli attributi di macchina virtuale di Azure. Nell'intestazione laterale a sinistra, Mostra il gruppo di risorse, area di Azure, nome della sottoscrizione e ID nonché alcuni tag che è stato aggiunto. Per impostazione predefinita, le unità di istanze Large di HANA non hanno un tag assegnato. Sul lato destro dell'intestazione, il nome dell'unità è elencato come assegnato durante la distribuzione è stata eseguita. Viene visualizzato il sistema operativo, nonché l'indirizzo IP. Come con le macchine virtuali digitare nell'unità di istanze Large di HANA con il numero di CPU thread e la memoria viene visualizzato anche. Altre informazioni su diverse unità di istanze Large di HANA, riportati di seguito:

- [SKU disponibili per HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architettura di archiviazione di SAP HANA (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Un campo aggiuntivo nella colonna a destra dell'intestazione comunica lo stato di alimentazione dell'unità istanza Large di HANA.

> [!NOTE]
> Lo stato di alimentazione descrive se l'unità di hardware è basato su Attiva o disattiva. Non offre informazioni relative al sistema operativo attivo e in esecuzione. Come si riavvia un'unità di istanze Large di HANA, si verificherà un tempo di piccole in cui lo stato dell'unità viene modificato per **Starting** per spostare lo stato delle **avviato**. In corso nello stato del **avviato** significa che il sistema operativo è corso l'avvio o che sia stato avviato il sistema operativo completamente. Di conseguenza, dopo il riavvio dell'unità, non è possibile aspettarsi di accedere immediatamente a unità, non appena lo stato passa a **avviato**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Attività di controllo di una singola unità di istanze Large di HANA 
Oltre a fornire una panoramica delle unità di istanze Large di HANA, è possibile controllare le attività dell'unità specifico. Un log attività è simile:

![Riquadro di spostamento nel portale di Azure](./media/hana-li-portal/portal-activity-list.png)

Si verifica una delle principali attività registrate riavvio di un'unità. I dati elencati includono lo stato dell'attività, il timestamp dell'attività è stata attivata, l'ID sottoscrizione di cui l'attività è stata attivata e l'utente di Azure che ha attivato l'attività. 

Un'altra attività che è registrata sono modifiche per l'unità nei metadati di Azure. Oltre il riavvio avviato, è possibile visualizzare l'attività di **scrivere HANAInstances**. Questo tipo di attività non esegue modifiche nell'unità di istanze Large di HANA se stesso, ma è la documentazione delle modifiche ai metadati dell'unità in Azure. Nel caso elencato, è aggiunto e viene eliminato un tag (vedere la sezione successiva).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Aggiungere ed eliminare un tag di Azure a un'unità di istanze Large di HANA
Hai un'altra possibilità consiste nell'aggiungere un [tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a un'unità di istanze Large di HANA. La modalità di recupero assegnati tag non è diversa dall'assegnazione di tag alle macchine virtuali. Come con i tag esistono nei metadati di Azure e, per istanze Large di HANA, le macchine virtuali hanno le stesse limitazioni come tag per le macchine virtuali.

L'eliminazione di tag funziona esattamente come con le macchine virtuali. Entrambe le attività, applicazione e l'eliminazione di un tag verranno elencate nel log attività dell'unità di istanze Large di HANA specifico.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Verificare le proprietà di un'unità di istanze Large di HANA
La sezione **proprietà** include informazioni importanti che si ottengono quando le istanze vengono presentate all'utente. Si tratta di una sezione in cui è ottenere tutte le informazioni che è possibile richiedere supporto case o per cui è necessario durante l'impostazione di configurazione degli snapshot di archiviazione. Di conseguenza questa sezione è una raccolta di dati lungo l'istanza, la connettività dell'istanza di Azure e del back-end di archiviazione. La parte superiore della sezione sarà simile a:


![nella parte superiore della proprietà HLI nel portale di Azure](./media/hana-li-portal/portal-properties-top.png)

I primo alcuni elementi dati, è stato illustrato nella schermata Panoramica già. Ma una parte importante di dati è l'ID del circuito ExpressRoute, che è stato ottenuto come le unità distribuite prima sono stati trasmesso. In alcuni casi di supporto, potrebbe ottenere richiesto per tali dati. Una voce di dati importanti viene visualizzata nella parte inferiore della schermata. I dati visualizzati sono l'indirizzo IP di inizio archiviazione NFS che isola lo spazio di archiviazione per i **tenant** nello stack di istanze Large di HANA. Questo indirizzo IP è necessario anche quando si modifica il [i backup di snapshot di file di configurazione per l'archiviazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Durante lo scorrimento verso il basso nel riquadro delle proprietà, si ottengono dati aggiuntivi, ad esempio un ID di risorsa univoco per l'unità di istanze Large di HANA o l'ID sottoscrizione che è stato assegnato alla distribuzione.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Riavviare un'unità di istanze Large di HANA tramite il portale di Azure
Preparazione al riavvio del sistema operativo Linux, non vi sono varie situazioni in cui il sistema operativo è stato possibile non completato un riavvio. Per forzare un riavvio, è necessario aprire una richiesta di servizio di Microsoft operations eseguire un riavvio dell'alimentazione dell'unità di istanze Large di HANA. La funzionalità di un riavvio di potenza di un'unità di istanze Large di HANA è ora integrata nel portale di Azure. Come trovano nella parte panoramica dell'unità di istanze Large di HANA, viene visualizzato il pulsante di riavvio nella parte superiore della sezione dati

![Riavviare passaggio #1 nel portale di Azure](./media/hana-li-portal/portal-restart-first-step.png)

Come si è premendo il pulsante di riavvio, viene chiesto se si vuole riavviare l'unità. Come confermare facendo clic sul pulsante "Yes", l'unità verrà riavviato.

> [!NOTE]
> Nel processo di riavvio, si verificherà un tempo di piccole in cui lo stato dell'unità viene modificato per **Starting** per spostare lo stato delle **avviato**. In corso nello stato del **avviato** significa che il sistema operativo è corso l'avvio o che sia stato avviato il sistema operativo completamente. Di conseguenza, dopo il riavvio dell'unità, non è possibile aspettarsi di accedere immediatamente a unità, non appena lo stato passa a **avviato**.

> [!IMPORTANT]
> Dipende dalla quantità di memoria espressa in unità di istanze Large di HANA, un riavvio e il riavvio di hardware e del sistema operativo può richiedere fino a un'ora


## <a name="open-a-support-request-for-hana-large-instances"></a>Aprire una richiesta di supporto per istanze large di HANA
Dalla visualizzazione del portale Azure di unità di istanze Large di HANA, è possibile creare le richieste di supporto in modo specifico per una grande unità di istanze HANA anche. Mentre si esegue il collegamento **nuova richiesta di supporto** 

![avviare il passaggio #1 della richiesta del servizio nel portale di Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Per ottenere il servizio di SAP HANA in istanze Large elencati nella schermata successiva, si potrebbe essere necessario selezionare ' tutti i servizi "come illustrato di seguito

![Selezionare tutti i servizi nel portale di Azure](./media/hana-li-portal/portal-create-service-request.png)

Nell'elenco dei servizi, è possibile trovare il servizio **SAP HANA in istanze Large**. Se si sceglie tale servizio, è possibile selezionare tipi di problemi specifici come illustrato:


![Classe di problema selezionare nel portale di Azure](./media/hana-li-portal/portal-select-problem-class.png)

In ognuno dei tipi di problema diverso, viene offerta una selezione di sottotipi di problema che è necessario selezionare questa opzione per caratterizzare ulteriormente il problema. Dopo aver selezionato il sottotipo, è ora possibile assegnare un nome soggetto. Dopo aver completato il processo di selezione, è possibile passare al passaggio successivo della creazione. Nel **soluzioni** sezione, si è punta alla documentazione per istanze Large di HANA, che potrebbe fornire un puntatore a una soluzione del problema. Se è possibile trovare una soluzione al problema nella documentazione di suggerito, andare al passaggio successivo. Nel passaggio successivo, verrà richiesto se il problema riguarda le macchine virtuali o con unità di istanze Large di HANA. Queste informazioni consentono di indirizzare la richiesta di supporto per gli specialisti corretti. 

![Dettagli del caso di supporto nel portale di Azure](./media/hana-li-portal/portal-support-request-details.png)

Durante le risposte alle domande e forniti altri dettagli, è possibile passare il passaggio successivo per rivedere la richiesta di supporto e l'invio è.

## <a name="next-steps"></a>Passaggi successivi

- [Come monitorare SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitoraggio e risoluzione dei problemi dal lato HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)


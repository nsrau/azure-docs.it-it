---
title: Soluzioni in Operations Management Suite (OMS) | Microsoft Docs
description: "Le soluzioni consentono di estendere la funzionalità di Operations Management Suite (OMS) offrendo scenari di gestione in pacchetto che i clienti possono aggiungere all'area di lavoro OMS.  Questo articolo fornisce informazioni dettagliate sulle soluzioni personalizzate create dai clienti e dai partner."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2443dd73fdf441721bd6f6f340da515d9f5a22a2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="working-with-management-solutions-in-operations-management-suite-oms-preview"></a>Utilizzo di soluzioni di gestione in Operations Management Suite (OMS) (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per le soluzioni di gestione in OMS attualmente disponibili in versione di anteprima.    
> 
> 

Le soluzioni di gestione estendono la funzionalità di Operations Management Suite (OMS) offrendo scenari di gestione in pacchetto che i clienti possono aggiungere al proprio ambiente.  Oltre alle [soluzioni fornite da Microsoft](../log-analytics/log-analytics-add-solutions.md), i partner e i clienti possono creare soluzioni di gestione da usare nel proprio ambiente o da rendere disponibili per i propri clienti attraverso la community.

## <a name="finding-and-installing-management-solutions"></a>Ricerca e installazione di soluzioni di gestione
Ci sono diversi metodi per trovare e installare soluzioni di gestione, come descritto nelle sezioni seguenti.

### <a name="azure-marketplace"></a>Azure Marketplace
Le soluzioni di gestione fornite da Microsoft e dai partner qualificati possono essere installate da Azure Marketplace nel portale di Azure.

1. Accedere al portale di Azure.
2. Nel riquadro sinistro selezionare **Altri servizi**.
3. Scorrere verso il basso fino a **Soluzioni** oppure digitare *soluzioni* nella finestra di dialogo **Filtra**.
4. Fare clic sul pulsante **+ Aggiungi**.
5. Cercare le soluzioni di interesse esaminando le soluzioni disponibili, facendo clic sul pulsante **Filtra** oppure digitando nella casella **Cerca tutti gli elementi**.
6. Fare clic su un elemento del Marketplace per visualizzare le informazioni dettagliate.
7. Fare clic su **Crea** per aprire il riquadro **Aggiungi soluzione**.
8. Verrà chiesto di immettere le informazioni necessarie, ad esempio l'[area di lavoro OMS e l'account di Automazione](#oms-workspace-and-automation-account), oltre ai valori per i parametri nella soluzione.
9. Fare clic su **Crea** per installare la soluzione.

### <a name="oms-portal"></a>Portale di OMS
Le soluzioni di gestione fornite da Microsoft possono essere installate dalla Raccolta soluzioni nel portale di OMS.

1. Accedere al portale di OMS.
2. Fare clic su sul riquadro **Raccolta soluzioni**.
3. Nella pagina Raccolta soluzioni di OMS, è possibile ottenere informazioni su ogni soluzione disponibile. Fare clic sul nome della soluzione che si desidera aggiungere a OMS.
4. Nella pagina relativa alla soluzione scelta vengono visualizzate informazioni dettagliate sulla soluzione visualizzata. Fare clic su **Aggiungi**.
5. Nella pagina di panoramica di OMS viene visualizzato un nuovo riquadro per la soluzione aggiunta, che è possibile iniziare a usare dopo che il servizio OMS ha elaborato i dati.

### <a name="azure-quickstart-templates"></a>Modelli di Guida introduttiva di Azure
I membri della community possono inviare le soluzioni di gestione come modelli di avvio rapido di Azure.  È possibile scaricare questi modelli per installarli in un secondo momento oppure esaminarli per capire come [creare soluzioni personalizzate](#creating-a-solution).

1. Seguire la procedura descritta in [Area di lavoro OMS e account di Automazione](#oms-workspace-and-automation-account) per collegare un'area di lavoro e un account.
2. Passare a [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).  
3. Cercare una soluzione adatta alle proprie esigenze.
4. Selezionare la soluzione nei risultati per visualizzarne i dettagli.
5. Fare clic sul pulsante **Distribuisci in Azure**.
6. Verrà chiesto di fornire informazioni come il gruppo di risorse e la posizione, oltre ai valori per i parametri nella soluzione.
7. Fare clic su **Acquista** per installare la soluzione.

### <a name="deploy-azure-resource-manager-template"></a>Distribuire un modello di Azure Resource Manager
Le soluzioni ottenute dalla community o [create personalmente](#creating-a-solution) vengono implementate come modello di Resource Manager ed è possibile usare uno dei metodi standard per la [distribuzione di un modello](../azure-resource-manager/resource-group-template-deploy-portal.md).  Si noti che prima di installare la soluzione è necessario creare e collegare l'[area di lavoro OMS e l'account di Automazione](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Area di lavoro OMS e account di Automazione
La maggior parte delle soluzioni di gestione richiede un'[area di lavoro OMS](../log-analytics/log-analytics-manage-access.md) per contenere le viste e un [account di Automazione](../automation/automation-security-overview.md#automation-account-overview) per contenere i runbook e le risorse correlate. L'area di lavoro e l'account devono soddisfare i requisiti indicati di seguito.

* Una soluzione può usare solo un'area di lavoro OMS e un account di Automazione.  
* L'area di lavoro OMS e l'account di Automazione usati da una soluzione devono essere collegati tra loro. Un'area di lavoro OMS può essere collegata a un solo account di Automazione e un account di Automazione può essere collegato a una sola area di lavoro OMS.
* Per essere collegati, l'area di lavoro OMS e l'account di Automazione devono trovarsi nello stesso gruppo di risorse e nella stessa area.  L'eccezione è rappresentata da un'area di lavoro OMS nell'area Stati Uniti orientali e un account di Automazione nell'area Stati Uniti orientali 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Creazione di un collegamento tra un'area di lavoro OMS e un account di Automazione
La modalità con cui si specificano l'area di lavoro OMS e l'account di Automazione dipende dal metodo di installazione per la soluzione.

* Quando si installa una soluzione Microsoft tramite il portale di OMS, questa viene installata nell'area di lavoro OMS corrente e non è necessario un account di Automazione.
* Quando si installa una soluzione tramite Azure Marketplace, vengono chiesti un'area di lavoro OMS e un account di Automazione e viene creato automaticamente il collegamento tra essi.  
* Per le soluzioni esterne ad Azure Marketplace, è necessario collegare l'area di lavoro OMS e l'account di Automazione prima di installare la soluzione.  A tale scopo, è possibile selezionare qualsiasi soluzione in Azure Marketplace e quindi selezionare l'area di lavoro OMS e l'account di Automazione.  Non è necessario installare effettivamente la soluzione, perché il collegamento verrà creato non appena si selezionano l'area di lavoro OMS e l'account di Automazione.  Una volta creato il collegamento, è possibile usare l'area di lavoro OMS e l'account di Automazione per qualsiasi soluzione. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Verifica del collegamento tra un'area di lavoro OMS e un account di Automazione
È possibile verificare il collegamento tra un'area di lavoro OMS e un account Automazione mediante la procedura seguente.

1. Selezionare l'account di Automazione nel portale di Azure.
2. Scorrere verso la parte inferiore del riquadro **Impostazioni**.
3. Se nel riquadro **Impostazioni** c'è una sezione denominata **Risorse di OMS**, significa che l'account è collegato a un'area di lavoro OMS.
4. Selezionare **Area di lavoro** per visualizzare i dettagli dell'area di lavoro OMS collegata a questo account di Automazione.

## <a name="listing-management-solutions"></a>Creazione di un elenco delle soluzioni di gestione
Usare la procedura seguente per visualizzare le soluzioni di gestione nelle aree di lavoro collegate alla sottoscrizione di Azure.

1. Accedere al portale di Azure.
2. Nel riquadro sinistro selezionare **Altri servizi**.
3. Scorrere verso il basso fino a **Soluzioni** oppure digitare *soluzioni* nella finestra di dialogo **Filtra**.
4. Verranno elencate le soluzioni installate in tutte le aree di lavoro.

Si noti che è possibile visualizzare solo le soluzioni Microsoft installate nell'area di lavoro corrente usando il portale di OMS.

## <a name="removing-a-management-solution"></a>Rimozione di una soluzione di gestione
Quando una soluzione di gestione viene rimossa, vengono rimosse anche tutte le risorse incluse nella soluzione.  

1. Individuare la soluzione nel portale di Azure usando la procedura descritta in [Creazione di un elenco delle soluzioni di gestione](#listing-solutions).
2. Selezionare la soluzione da rimuovere.
3. Fare clic sul pulsante **Elimina**.

## <a name="creating-a-management-solution"></a>Creazione di una soluzione di gestione
Istruzioni complete sulla creazione di soluzioni di gestione sono disponibili nell'articolo [Creazione di soluzioni in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Passaggi successivi
* Cercare i [modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates) per esempi di modelli di Resource Manager.
* Creare [soluzioni di gestione](operations-management-suite-solutions-creating.md) personalizzate.


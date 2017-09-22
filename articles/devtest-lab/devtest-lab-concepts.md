---
title: Concetti relativi a DevTest Labs | Microsoft Docs
description: Informazioni sui concetti di base di DevTest Labs e su come possa semplificare la creazione, la gestione e il monitoraggio delle macchine virtuali di Azure
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 7028cdacf7d597c238bf29bd26bbd44705d66224
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="devtest-labs-concepts"></a>Concetti di Lab di sviluppo e test
## <a name="overview"></a>Panoramica
L'elenco seguente contiene le definizioni e i concetti chiave di Lab di sviluppo e test:

## <a name="labs"></a>Lab
Un lab è l'infrastruttura che comprende un gruppo di risorse, ad esempio macchine virtuali, e ne consente una migliore gestione mediante la specifica di limiti e quote.

## <a name="virtual-machine"></a>Macchina virtuale
Una macchina virtuale di Azure è uno dei vari tipi di [risorse di calcolo scalabili e su richiesta](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) offerte da Azure. Le macchine virtuali di Azure offrono la flessibilità della virtualizzazione, consentendo di evitare l'acquisto e la gestione di hardware fisico. Per la gestione delle macchine virtuali è comunque necessario eseguire determinate attività, ad esempio la configurazione, l'applicazione di patch e l'installazione del software in cui le macchine virtuali stesse vengono eseguite.

L'articolo [Panoramica delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) offre informazioni sugli aspetti da tenere in considerazione prima di creare una macchina virtuale, oltre a indicazioni su come creare e gestire la macchina virtuale stessa.

## <a name="claimable-vm"></a>Macchina virtuale a disposizione degli utenti
Una macchina virtuale di Azure a disposizione degli utenti è una macchina virtuale che può essere usata da qualsiasi utente di laboratorio che dispone delle autorizzazioni appropriate. Un amministratore di laboratorio può preparare le macchine virtuali con le immagini e gli elementi di base specifici e salvarle in un pool condiviso. Un utente di laboratorio può quindi richiedere una macchina virtuale nel pool quando ha l'esigenza di usarne una con la configurazione specifica.

Una macchina virtuale a disposizione degli utenti non viene inizialmente assegnata ad alcun utente specifico, ma viene visualizzata nell'elenco di ogni utente in "Claimable virtual machines" (Macchine virtuali a disposizione degli utenti). Se una macchina virtuale viene richiesta da un utente, viene spostata nell'area "My virtual machines" (Le mie macchine virtuali) e non è più disponibile per altri utenti.

## <a name="environment"></a>Environment
In DevTest Labs il termine ambiente indica una raccolta di risorse di Azure in un lab. In [questo post di blog](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) viene discussa la creazione di ambienti con più macchine virtuali a partire da modelli di Azure Resource Manager.

## <a name="base-images"></a>Immagini di base
Le immagini di base sono immagini di macchine virtuali con tutti gli strumenti e le impostazioni preinstallati e configurati per creare rapidamente una macchina virtuale. È possibile eseguire il provisioning di una macchina virtuale scegliendo una base esistente e aggiungendo un elemento per installare l'agente di test. È quindi possibile salvare la macchina virtuale di cui è stato effettuato il provisioning come base in modo che la base possa essere utilizzata senza la necessità di reinstallare l'agente di test per ogni processo di provisioning della macchina virtuale.

## <a name="artifacts"></a>Elementi
Gli elementi vengono usati per distribuire e configurare l'applicazione dopo il provisioning di una macchina virtuale. Gli elementi possono essere:

* Strumenti che si vuole installare nella VM, come agenti, Fiddler, Visual Studio.
* Azioni che si desidera eseguire sulla macchina virtuale, ad esempio la clonazione di un archivio.
* Applicazioni che si vuole testare.

Gli elementi sono file JSON basati su [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) che contengono istruzioni per eseguire la distribuzione e applicare la configurazione.

## <a name="artifact-repositories"></a>Repository di elementi
I repository di elementi sono repository git in cui vengono archiviati gli elementi. È possibile aggiungere gli stessi archivi di elementi a più lab all'interno dell'organizzazione consentendone il riuso e la condivisione.

## <a name="formulas"></a>Formule
Le formule, oltre alle immagini di base, offrono un meccanismo per il provisioning rapido di VM. Una formula è un elenco di valori predefiniti di proprietà usati nei lab di sviluppo/test per creare macchine virtuali.
Con le formule, è possibile creare VM con lo stesso insieme di proprietà (come l'immagine base, la dimensione della VM, la rete virtuale e gli elementi), senza il bisogno di specificare tali proprietà ogni volta. Quando si crea una VM da una formula, i valori predefiniti possono essere modificati o usati così come sono.

## <a name="policies"></a>Criteri
I criteri consentono di controllare i costi nel lab. Ad esempio, è possibile creare un criterio per arrestare automaticamente le macchine virtuali in base a una pianificazione definita.

## <a name="caps"></a>Limiti
I limiti rappresentano un meccanismo per ridurre al minimo gli sprechi nel lab. Ad esempio, è possibile impostare un limite per limitare il numero di macchine virtuali che possono essere create per ogni utente o in un lab.

## <a name="security-levels"></a>Livelli di sicurezza
L'accesso sicuro è determinato dal controllo degli accessi in base al ruolo di Azure. Per comprendere il funzionamento dell'accesso, è utile conoscere le differenze tra un'autorizzazione, un ruolo e un ambito definiti dal Controllo degli accessi in base al ruolo.

* Autorizzazione: un'autorizzazione è un accesso definito a un'azione specifica, ad esempio un accesso in lettura a tutte le macchine virtuali.
* Ruolo: un ruolo è un set di autorizzazioni che possono essere raggruppate e assegnate a un utente. Ad esempio, il ruolo *proprietario della sottoscrizione* ha accesso a tutte le risorse all'interno di una sottoscrizione.
* Ambito: un ambito è un livello della gerarchia di una risorsa di Azure, ad esempio un gruppo di risorse, un singolo lab o un'intera sottoscrizione.

Nell'ambito di DevTest Labs, esistono due tipi di ruoli per definire le autorizzazioni utente: proprietario del lab e utente del lab.

* Proprietario del lab: un proprietario del lab ha accesso a tutte le risorse all'interno del lab. Un proprietario del lab può quindi modificare i criteri, leggere e scrivere nelle VM, modificare la rete virtuale e così via.
* Utente del lab: può visualizzare tutte le risorse del lab, ad esempio VM, criteri e reti virtuali, ma non può modificare i criteri o le VM create da altri utenti.

Per informazioni sulla creazione di ruoli personalizzati in DevTest Labs, vedere [Concedere le autorizzazioni utente per specifici criteri di lab](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Poiché gli ambiti sono gerarchici, quando un utente ha le autorizzazioni per un determinato ambito, gli vengono automaticamente concesse tali autorizzazioni per ogni ambito di livello inferiore incluso. Ad esempio, se un utente è assegnato al ruolo di proprietario della sottoscrizione, ha accesso a tutte le risorse in una sottoscrizione, che includono tutte le macchine virtuali, tutte le reti virtuali e tutti i lab. Il proprietario di una sottoscrizione eredita quindi automaticamente il ruolo di proprietario del lab, ma non il contrario. Il proprietario di un lab ha accesso a un lab, che è un ambito più basso del livello della sottoscrizione. Il proprietario di un lab quindi non potrà visualizzare le macchine virtuali, le reti virtuali o nessuna altra risorsa esterna al lab.

## <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure
Tutti i concetti illustrati in questo articolo possono essere configurati con i modelli di Azure Resource Manager, che consentono di definire l'infrastruttura e la configurazione della soluzione di Azure e distribuirle ripetutamente in uno stato coerente.

L'articolo [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) descrive la struttura di un modello di Azure Resource Manager e le proprietà disponibili nelle diverse sezioni di un modello.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
[Creare un lab in Azure DevTest Labs](devtest-lab-create-lab.md)


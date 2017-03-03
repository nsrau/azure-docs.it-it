---
title: "Distribuire un&quot;app nei set di scalabilità di macchine virtuali | Microsoft Docs"
description: "Distribuire un&quot;applicazione nei set di scalabilità delle macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: f13545d753690534e0e645af67efcf1b524837eb
ms.openlocfilehash: dad27b11b5f02ed41826b82882cc5089eb69cb04
ms.lasthandoff: 02/09/2017


---
# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Distribuire un'applicazione nei set di scalabilità delle macchine virtuali
Un'applicazione in esecuzione in un set di scalabilità di una VM viene in genere distribuita in uno dei tre modi seguenti:

* Installazione di un nuovo software in un'immagine di piattaforma in fase di distribuzione. Un'immagine di piattaforma in questo contesto è un'immagine di un sistema operativo da Azure Marketplace, ad esempio Ubuntu 16.04, Windows Server 2012 R2 e così via.

È possibile installare nuovi software in un'immagine di piattaforma usando un'[estensione di VM](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Un'estensione di VM è un software che viene eseguito durante la distribuzione di una VM. In fase di distribuzione è possibile eseguire qualsiasi codice tramite un'estensione dello script personalizzata. [Qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) viene illustrato un modello di Azure Resource Manager di esempio con due estensioni di VM: un'estensione di script personalizzata Linux per installare Apache e PHP e un'estensione di diagnostica per generare dati sulle prestazioni usati dalla funzionalità Scalabilità automatica.

Un vantaggio di questo approccio è che offre un livello di separazione tra il codice dell'applicazione e il sistema operativo e consente la gestione separata dell'applicazione. Ovviamente ciò comporta anche la presenza di più parti mobili e la distribuzione della VM potrebbe richiedere più tempo se lo script deve scaricare e configurare molti elementi .

**Se si trasmettono dati sensibili nel comando dell'estensione dello script personalizzata, come ad esempio una password, assicurarsi di specificare `commandToExecute` nell'attributo `protectedSettings` dell'estensione dello script personalizzata invece dell'attributo `settings`.**

* Creare un'immagine di VM personalizzata che includa sia il sistema operativo sia l'applicazione in un singolo disco rigido virtuale. In questo caso il set di scalabilità è costituito da un set di VM copiate da un'immagine creata dall'utente, che è necessario gestire. Questo approccio non richiede alcuna configurazione aggiuntiva in fase di distribuzione della VM. Tuttavia, nella versione `2016-03-30` dei set di scalabilità di VM (e versioni precedenti), i dischi del sistema operativo per le VM nel set di scalabilità sono limitati a un singolo account di archiviazione. Pertanto, è possibile avere un massimo di 40 VM in un set di scalabilità anziché le 100 VM per set con le immagini di piattaforma. Per altri dettagli vedere la [panoramica sulla progettazione di set di scalabilità](virtual-machine-scale-sets-design-overview.md).

    >[!NOTE]
    >La versione dell'API di set di scalabilità di macchine virtuali di Microsoft Azure `2016-04-30-preview` supporta l'uso di Azure Managed Disks per il disco del sistema operativo ed eventuali dischi dati aggiuntivi. Per altre informazioni, vedere [Panoramica del servizio Managed Disks](../storage/storage-managed-disks-overview.md) e [Usare dischi dati collegati](virtual-machine-scale-sets-attached-disks.md). 

* Distribuire un'immagine di piattaforma o personalizzata che sia sostanzialmente un host del contenitore e installare l'applicazione come uno o più contenitori da gestire con un agente di orchestrazione o uno strumento per la gestione della configurazione. L'aspetto positivo di questo approccio è che consente di eseguire l'astrazione dell'infrastruttura cloud dal livello dell'applicazione e di poterli gestire separatamente.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>Cosa accade in caso di aumento della capacità di un set di scalabilità di VM
Quando si aggiungono una o più VM a un set di scalabilità aumentando la capacità, manualmente o tramite scalabilità automatica, l'applicazione viene installata automaticamente. Se ad esempio il set di scalabilità ha estensioni definite, queste vengono eseguite ogni volta che viene creata una nuova VM. Se il set di scalabilità è basato su un'immagine personalizzata, qualsiasi nuova VM è una copia dell'immagine di origine personalizzata. Se le VM del set di scalabilità sono host del contenitore, potrebbe essere richiesto un codice di avvio per caricare i contenitori in un'estensione dello script personalizzata oppure un'estensione potrebbe installare un agente che esegue la registrazione con un agente di orchestrazione del cluster (ad esempio servizio contenitore di Azure).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Come si gestiscono gli aggiornamenti dell'applicazione nei set di scalabilità di VM
Per gli aggiornamenti dell'applicazione in set di scalabilità di VM sono disponibili tre approcci principali derivati dai tre metodi di distribuzione dell'applicazione precedenti:

* Aggiornamento con estensioni di VM. Tutte le estensioni di VM definite per un set di scalabilità di VM vengono eseguite ogni volta che viene distribuita una nuova VM, viene ricreata l'immagine di una VM esistente o viene aggiornata un'estensione di VM. Un approccio valido per aggiornare l'applicazione prevede l'aggiornamento diretto tramite estensioni, di cui si aggiorna semplicemente la definizione. Un modo semplice per adottare tale approccio prevede la modifica di fileUris in modo che punti al nuovo software.

* Approccio dell'immagine personalizzata non modificabile. Quando l'applicazione, o i relativi componenti, vengono inseriti in un'immagine di VM, è possibile concentrarsi sulla creazione di una pipeline affidabile per automatizzare compilazione, test e distribuzione delle immagini. È possibile progettare l'architettura per facilitare il passaggio rapido di un set di scalabilità temporaneo in produzione. Un buon esempio di questo approccio è rappresentato dall'[uso del driver Spinnaker di Azure](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Packer e Terraform supportano inoltre Azure Resource Manager; pertanto, è anche possibile definire le immagini "come codice" e compilarle in Azure, quindi usare il disco rigido virtuale nel set di scalabilità. Tuttavia, tale approccio diventerebbe problematico per immagini Marketplace, in cui gli script personalizzati/le estensioni acquistano importanza in quanto i bit non vengono modificati direttamente da Marketplace.

* Aggiornamento dei contenitori. Eseguire l'astrazione della gestione del ciclo di vita delle applicazioni a un livello superiore all'infrastruttura cloud, ad esempio incapsulando le applicazioni e i relativi componenti in contenitori che vengono gestiti tramite agenti di orchestrazione e di configurazione come Chef/Puppet.

Le VM dei set di scalabilità diventano una base stabile per i contenitori e richiedono solo protezione occasionale e aggiornamenti correlati al sistema operativo. Come accennato, il servizio contenitore di Azure è un buon esempio di questo approccio e della creazione di un servizio associato.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Come distribuire un aggiornamento del sistema operativo nei domini di aggiornamento
Si supponga di che voler aggiornare un'immagine del sistema operativo mantenendo in esecuzione il set di scalabilità di VM. Un possibile approccio prevede di aggiornare le immagini una VM alla volta. A tale scopo è possibile usare PowerShell o l'interfaccia della riga di comando di Azure. Sono disponibili comandi separati per aggiornare il modello del set di scalabilità di VM (la modalità di definizione della sua configurazione) e per eseguire chiamate di "aggiornamento manuale" su singole VM. Il documento di Azure [Aggiornare un set di scalabilità di macchine virtuali](./virtual-machine-scale-sets-upgrade-scale-set.md) include altre informazioni sulle opzioni disponibili per eseguire aggiornamenti del sistema operativo in un set di scalabilità di macchine virtuali.



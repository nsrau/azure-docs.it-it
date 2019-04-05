---
title: Immagini Red Hat Enterprise Linux in Azure | Microsoft Docs
description: Informazioni sulle immagini Red Hat Enterprise Linux in Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/18/2019
ms.author: borisb
ms.openlocfilehash: fb3c0e46324a22bdd95bf7d93c28e69c195927e8
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045423"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Immagini Red Hat Enterprise Linux in Azure
Questo articolo illustra le immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure Marketplace con i criteri per la denominazione e conservazione.

Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).

>[!Important]
> Le immagini RHEL attualmente disponibili in Azure Marketplace supportano i modelli di licenza BYOS (Bring-Your-Own-Subscription) o PAYG (Pay-As-You-Go, ovvero pagamento in base al consumo). Il [Vantaggio Azure Hybrid Use](../windows/hybrid-use-benefit-licensing.md) e il passaggio dinamico da BYOS a PAYG e viceversa non sono supportati. Il passaggio da una modalità di licenza all'altra richiede la ridistribuzione della macchina virtuale dall'immagine corrispondente.

>[!Note]
> Per qualsiasi problema correlato alle immagini RHEL nella raccolta di Azure Marketplace, inviare un ticket di supporto a Microsoft.

## <a name="images-available-in-the-ui"></a>Immagini disponibili nell'interfaccia utente
Quando si cerca "Red Hat" nel Marketplace o quando si crea una risorsa nell'interfaccia utente del portale di Azure, verranno visualizzati un subset di immagini RHEL disponibili e dei prodotti Red Hat correlati. È sempre possibile ottenere il set completo di immagini di VM disponibili usando l'interfaccia della riga di comando/PowerShell/API di Azure.

Per visualizzare il set completo di immagini RedHat disponibili in Azure, eseguire il comando seguente

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Convenzione di denominazione
Le immagini di VM in Azure sono organizzate per editore, offerta, SKU e versione. La combinazione di editore:offerta:SKU:versione è l'URN dell'immagine e identifica in modo univoco l'immagine da utilizzare.

Ad esempio, `RedHat:RHEL:7-RAW:7.6.2018103108` fa riferimento a un'immagine RHEL 7.6 con partizione raw compilata il 31 ottobre 2018.

Di seguito è illustrato un esempio di come creare una VM RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Il percorso del collegamento "latest"
L'API REST di Azure consente l'uso del percorso del collegamento "latest" per la versione in alternativa alla versione specifica. L'uso di "latest" eseguirà il provisioning dell'immagine più recente disponibile per l'editore, l'offerta e lo SKU specificati.

Ad esempio, `RedHat:RHEL:7-RAW:latest` fa riferimento all'immagine con partizione raw della famiglia RHEL 7 più recente disponibile.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> In generale, il confronto delle versioni per determinare la versione più recente segue le regole del [metodo CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Convenzione di denominazione attuale
Tutte le immagini RHEL attualmente pubblicate utilizzano il modello con pagamento in base al consumo e sono connesse all'[infrastruttura di aggiornamento per Red Hat (RHUI) in Azure](https://aka.ms/rhui-update). A causa di una limitazione di progettazione dell'infrastruttura RHUI, è stata adottata una nuova convenzione di denominazione per le immagini della famiglia RHEL 7. La denominazione della famiglia RHEL 6 non è stata attualmente modificata.

La limitazione sta nel fatto che quando un `yum update` non selettivo viene eseguito su una VM connessa all'infrastruttura RHUI, la versione RHEL viene aggiornata alla versione più recente nella famiglia attuale. Per altre informazioni, vedere [questo collegamento](https://aka.ms/rhui-update). Ciò potrebbe causare confusione quando un'immagine di RHEL 7.2 con provisioning diventa RHEL 7.6 dopo un aggiornamento. È comunque possibile effettuare il provisioning da un'immagine precedente, come illustrato negli esempi precedenti, specificando esplicitamente la versione richiesta. Se la versione richiesta non viene specificata durante il provisioning di una nuova immagine RHEL 7, allora verrà eseguito il provisioning dell'immagine più recente.

>[!NOTE]
> Nel set di immagini RHEL per SAP, la versione RHEL rimane fissa. Di conseguenza, la convenzione di denominazione include una versione particolare nello SKU.

>[!NOTE]
> Il set di immagini RHEL 6 non è stato spostato alla nuova convenzione di denominazione.

Le offerte seguenti sono SKU attualmente disponibili per l'uso generale:

Offerta| SKU | Partizionamento | Provisioning | Note
:----|:----|:-------------|:-------------|:-----
RHEL | 7-RAW | RAW | Agente Linux | Famiglia di immagini RHEL 7
| | 7-LVM | LVM | Agente Linux | Famiglia di immagini RHEL 7
| | 7-RAW-CI | RAW-CI | cloud-init | Famiglia di immagini RHEL 7
| | 6.7 | RAW | Agente Linux | Immagini RHEL 6.7, convenzione di denominazione precedente
| | 6.8 | RAW | Agente Linux | Come sopra per RHEL 6.8
| | 6.9 | RAW | Agente Linux | Come sopra per RHEL 6.9
| | 6.10 | RAW | Agente Linux | Come sopra per RHEL 6.10
| | 7,2 | RAW | Agente Linux | Come sopra per RHEL 7.2
| | 7.3 | RAW | Agente Linux | Come sopra per RHEL 7.3
| | 7.4 | RAW | Agente Linux | Come sopra per RHEL 7.4
| | 7.5 | RAW | Agente Linux | Come sopra per RHEL 7.5
RHEL-SAP | 7.4 | LVM | Agente Linux | RHEL 7.4 per SAP HANA e app aziendali
| | 7.5 | LVM | Agente Linux | RHEL 7.5 per SAP HANA e app aziendali
RHEL-SAP-HANA | 6.7 | RAW | Agente Linux | RHEL 6.7 per SAP HANA
| | 7,2 | LVM | Agente Linux | RHEL 7.2 per SAP HANA
| | 7.3 | LVM | Agente Linux | RHEL 7.3 per SAP HANA
RHEL-SAP-APPS | 6.8 | RAW | Agente Linux | RHEL 6.8 per Business Applications SAP
| | 7.3 | LVM | Agente Linux | RHEL 7.3 per Business Applications SAP

### <a name="old-naming-convention"></a>Convenzione di denominazione precedente
La famiglia di immagini RHEL 7 e la famiglia di immagini RHEL 6 utilizzavano versioni specifiche nei relativi SKU fino alla modifica della convenzione di denominazione descritta in precedenza.

Saranno presenti SKU numerici nell'elenco completo delle immagini. Microsoft e Red Hat creeranno nuovi SKU numerici quando viene resa disponibile una nuova versione secondaria.

### <a name="other-available-offers-and-skus"></a>Altre offerte e altri SKU disponibili
L'elenco completo delle offerte e degli SKU disponibili può includere immagini aggiuntive oltre a quelle elencate nella tabella precedente, ad esempio, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Queste offerte possono essere usate per fornire il supporto di soluzioni di marketplace specifiche oppure possono essere pubblicate per anteprime e a scopo di test. Possono essere modificate o rimosse in qualsiasi momento senza preavviso. Non utilizzarle a meno che la loro presenza non sia stata documentata pubblicamente da Microsoft o Red Hat.

## <a name="publishing-policy"></a>Criteri di pubblicazione
Microsoft e Red Hat aggiornano le immagini man mano che vengono rilasciate nuove versioni secondarie, in base alle esigenze per soddisfare CVE specifiche o per modifiche o aggiornamenti occasionali della configurazione. Ci impegniamo a fornire le immagini aggiornate appena possibile, entro tre giorni lavorativi dopo un rilascio o la disponibilità di una correzione CVE.

Aggiorniamo la versione secondaria corrente solo in una famiglia di immagini specificata. Con il rilascio di una versione secondaria più recente, interrompiamo l'aggiornamento della versione secondaria precedente. Ad esempio, con la versione di RHEL 7.6, le immagini RHEL 7.5 non verranno più aggiornate.

>[!NOTE]
> Le VM di Azure attive il cui provisioning è stato eseguito da immagini RHEL con pagamento in base al consumo sono connesse all'infrastruttura RHUI di Azure e possono ricevere aggiornamenti e correzioni non appena vengono rilasciati da Red Hat e replicati nell'infrastruttura RHUI di Azure (in genere in meno di 24 ore a seguito del rilascio ufficiale da parte di Red Hat). Queste VM non richiedono una nuova immagine pubblicata per ottenere gli aggiornamenti e i clienti hanno il controllo completo sul momento in cui avviare l'aggiornamento.

## <a name="image-retention-policy"></a>Criteri di conservazione delle immagini
I criteri attuali prevedono la conservazione di tutte le immagini pubblicate in precedenza. Ci riserviamo il diritto di rimuovere le immagini che potrebbero causare problemi di qulasiasi tipo. Ad esempio, le immagini con configurazioni non corrette a causa di aggiornamenti successivi della piattaforma o dei componenti potrebbero essere rimosse. Le immagini che possono essere rimosse seguiranno i criteri di Marketplace correnti per fornire notifiche fino a 30 giorni prima della rimozione dell'immagine.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'infrastruttura di aggiornamento per Azure Red Hat [in questo articolo](https://aka.ms/rhui-update).
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata) (Ciclo di vita di Red Hat Enterprise Linux).

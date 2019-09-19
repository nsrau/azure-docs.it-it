---
title: Risoluzione dei problemi relativi a errori di allocazione di una macchina virtuale di Azure nel modello di distribuzione classica| Microsoft Docs
description: Risolvere i problemi relativi a errori di allocazione quando si crea, riavvia o ridimensiona una macchina virtuale classica in Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d43176e04337c2faf7be0bea682428056bc4ab46
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059191"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Procedura per la risoluzione dei problemi relativi a scenari di errori di allocazione specifici nel modello di distribuzione classica

Di seguito sono illustrati gli scenari di allocazione comuni che causano una richiesta di allocazione da bloccare. Verrà esaminato ogni scenario più avanti in questo articolo.

- Ridimensionare una VM o aggiungere VM o istanze dei ruoli a un servizio cloud esistente
- Riavviare VM arrestate (deallocate) parzialmente
- Riavviare VM arrestate (deallocate) completamente
- Distribuzioni di gestione temporanea e di produzione (solo Platform-as-a-Service)
- Gruppo di affinità (prossimità di macchina virtuale o servizio)
- Rete virtuale basata su gruppi di affinità

Quando si riceve un errore di allocazione, verificare se uno degli scenari elencati è pertinente con questo errore. Usare l'errore di allocazione restituito dalla piattaforma Azure per identificare lo scenario corrispondente. Se la richiesta è bloccata, rimuovere alcuni dei vincoli di blocco per aprire la richiesta a più cluster, aumentando quindi la possibilità di eseguire l'allocazione correttamente.
In generale, se l'errore non indica che "la dimensione di macchina virtuale richiesta non è supportata", è sempre possibile ritentare in un secondo momento. Questo avviene perché nel cluster è stato liberato un numero di risorse sufficiente a soddisfare la richiesta. Se il problema è che la dimensione della VM richiesta non è supportata, provare con una dimensione diversa di VM. In caso contrario, l'unica opzione consiste nel rimuovere il vincolo di blocco.

Due scenari di errore comuni sono correlati ai gruppi di affinità. In passato, il gruppo di affinità veniva usato per fornire la prossimità alle istanze di macchine virtuali e servizi o per abilitare la creazione della rete virtuale. Con l'introduzione delle reti virtuali dell'area, i gruppi di affinità non sono più necessari per creare una rete virtuale. Con la riduzione della latenza di rete nell'infrastruttura di Azure, l'indicazione che riguarda l'uso dei gruppi di affinità per la prossimità di macchine virtuali o servizi è stata modificata.

Il diagramma seguente illustra la tassonomia degli scenari di allocazione (bloccata). 

![Tassonomia di allocazione bloccata](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Ridimensionare una VM o aggiungere VM o istanze dei ruoli a un servizio cloud esistente
**Error (Errore) (Error (Errore)e)**

Upgrade_VMSizeNotSupported o GeneralError

**Causa del blocco su un cluster**

La richiesta di ridimensionamento di una VM o di aggiunta di una VM o di un'istanza del ruolo a un servizio cloud esistente deve essere eseguita nel cluster originale che ospita il servizio cloud esistente. La creazione di un nuovo servizio cloud consente alla piattaforma Azure di trovare un altro cluster con risorse disponibili o che supporti le dimensioni della VM richieste.

**Soluzione alternativa**

Se l'errore è Upgrade_VMSizeNotSupported*, provare con dimensioni della VM diverse. Se l'uso di dimensioni della VM diverse non è possibile, ma è accettabile usare un indirizzo IP virtuale (indirizzo VIP) diverso, creare un nuovo servizio cloud per ospitare la nuova VM e aggiungere il nuovo servizio cloud alla rete virtuale dell'area in cui sono in esecuzione le VM esistenti. Se il servizio cloud esistente non usa una rete virtuale dell'area, è comunque possibile creare una nuova rete virtuale per il nuovo servizio cloud, quindi connettere la [rete virtuale esistente a quella nuova](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Altre informazioni sulle [reti virtuali a livello di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se l'errore è GeneralError, è probabile che il tipo di risorsa (ad esempio, le dimensioni specifiche della VM) sia supportato dal cluster, che al momento non dispone di risorse disponibili. Analogamente allo scenario riportato sopra, aggiungere la risorsa di calcolo desiderata tramite la creazione di un nuovo servizio cloud (notare che il nuovo servizio cloud deve usare un indirizzo VIP diverso) e usare una rete virtuale dell'area per connettere i servizi cloud.

## <a name="restart-partially-stopped-deallocated-vms"></a>Riavviare VM arrestate (deallocate) parzialmente
**Error (Errore) (Error (Errore)e)**

GeneralError*

**Causa del blocco su un cluster**

La deallocazione parziale significa che una o più macchine virtuali in un servizio cloud sono state arrestate (deallocate), ma non tutte. Quando si arresta (viene deallocata) una VM, vengono rilasciate le risorse associate. Il riavvio della VM arrestata (deallocata) è quindi una nuova richiesta di allocazione. Riavviare le VM in un servizio cloud parzialmente deallocato equivale ad aggiungere VM a un servizio cloud esistente. La richiesta di allocazione deve essere eseguita nel cluster originale che ospita il servizio cloud esistente. La creazione di un servizio cloud diverso consente alla piattaforma Azure di trovare un altro cluster con risorse disponibili o che supporti le dimensioni della VM richieste.

**Soluzione alternativa**

Se è accettabile usare un indirizzo VIP diverso, eliminare le VM arrestate (deallocate), mantenendo però i dischi associati, quindi riaggiungere le VM tramite un servizio cloud diverso. Usare una rete virtuale dell'area per connettere i servizi cloud:

* Se il servizio cloud esistente usa una rete virtuale dell'area, è sufficiente aggiungere il nuovo servizio cloud alla stessa rete virtuale.
* Se il servizio cloud esistente non usa una rete virtuale dell'area, creare una nuova rete virtuale per il nuovo servizio cloud, quindi connettere la [rete virtuale esistente a quella nuova](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Altre informazioni sulle [reti virtuali a livello di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Riavviare VM arrestate (deallocate) completamente
**Error (Errore) (Error (Errore)e)**

GeneralError*

**Causa del blocco su un cluster**

La deallocazione completa significa che sono state arrestate (deallocate) tutte le VM da un servizio cloud. Le richieste di allocazione per il riavvio di queste VM devono essere eseguite nel cluster originale che ospita il servizio cloud. La creazione di un nuovo servizio cloud consente alla piattaforma Azure di trovare un altro cluster con risorse disponibili o che supporti le dimensioni della VM richieste.

**Soluzione alternativa**

Se è accettabile usare un indirizzo VIP diverso, eliminare le VM arrestate (deallocate) originali, mantenendo però i dischi associati, quindi eliminare il servizio cloud corrispondente. Le risorse di calcolo associate sono già state rilasciate al momento dell'arresto (deallocazione) delle VM. Creare un nuovo servizio cloud per riaggiungere le VM.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Distribuzioni di gestione temporanea o di produzione (solo Platform-as-a-Service)
**Error (Errore) (Error (Errore)e)**

New_General* o New_VMSizeNotSupported\*

**Causa del blocco su un cluster**

Le distribuzione di gestione temporanea e di produzione di un servizio cloud sono ospitate nello stesso cluster. Quando si aggiunge la seconda distribuzione, la richiesta di allocazione corrispondente verrà eseguita nello stesso cluster che ospita la prima distribuzione.

**Soluzione alternativa**

Eliminare la prima distribuzione e il servizio cloud originale, quindi ridistribuire il servizio cloud. Questa azione potrebbe inserire la prima distribuzione in un cluster con risorse disponibili sufficienti per entrambe le distribuzioni o in un cluster che supporta le dimensioni della VM richieste.

## <a name="affinity-group-vmservice-proximity"></a>Gruppo di affinità (prossimità di VM o servizio)
**Error (Errore) (Error (Errore)e)**

New_General* o New_VMSizeNotSupported\*

**Causa del blocco su un cluster**

Qualsiasi risorsa di calcolo assegnata a un gruppo di affinità è associata a un cluster. Le nuove richieste di risorse di calcolo in quel gruppo di affinità vengono eseguite nello stesso cluster in cui sono ospitate le risorse esistenti. Questo vale indipendentemente dal fatto che le nuove risorse vengano create tramite un servizio cloud nuovo o esistente.

**Soluzione alternativa**

Se un gruppo di affinità non è necessario, non usare un gruppo di affinità o raggruppare le risorse di calcolo in più gruppi di affinità.

## <a name="affinity-group-based-virtual-network"></a>Rete virtuale basata su gruppi di affinità
**Error (Errore) (Error (Errore)e)**

New_General* o New_VMSizeNotSupported\*

**Causa del blocco su un cluster**

Prima dell'introduzione delle reti virtuali dell'area, era necessario associare una rete virtuale a un gruppo di affinità. Di conseguenza, le risorse di calcolo inserite in un gruppo di affinità sono associate agli stessi vincoli descritti nello scenario di allocazione: Sezione precedente del gruppo di affinità (prossimità di VM/servizi). Le risorse di calcolo sono legate a un cluster.

**Soluzione alternativa**

Se il gruppo di affinità non è necessario, creare una nuova rete virtuale dell'area per le nuove risorse aggiunte, quindi [connettere la rete virtuale esistente a quella nuova](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Altre informazioni sulle [reti virtuali a livello di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

In alternativa, è possibile [eseguire la migrazione della rete virtuale basata su gruppi di affinità alla rete virtuale dell'area](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), quindi aggiungere di nuovo le risorse desiderate.



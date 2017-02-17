---
title: "Set di disponibilità per macchine virtuali Windows in Azure | Documentazione Microsoft"
description: "Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione dei set di disponibilità nei servizi di infrastruttura di Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f9449f58-664b-4d5d-82f6-84c5d083047f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: 0d4a7f8d7f469c43c972a163651688796483f8fc


---
# <a name="azure-availability-sets-guidelines"></a>Linee guida per i set di disponibilità di Azure
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione dei passaggi di pianificazione necessari per i set di disponibilità per garantire che le applicazioni rimangano accessibili in caso di eventi pianificati o non pianificati.

## <a name="implementation-guidelines-for-availability-sets"></a>Linee guida di implementazione per i set di disponibilità
Decisioni:

* Quanti set di disponibilità sono necessari per i diversi ruoli e livelli nell’infrastruttura dell'applicazione?

Attività:

* Definire il numero di macchine virtuali in ogni livello dell'applicazione desiderata.
* Determinare se è necessario modificare il numero di domini di errore o aggiornamento da usare per l'applicazione.
* Definire i set di disponibilità richiesti tramite la convenzione di denominazione scelta e le macchine virtuali che vi risiederanno. Una macchina virtuale può risiedere soltanto in un set di disponibilità. 

## <a name="availability-sets"></a>Set di disponibilità
Azure consente di inserire le macchine virtuali in un gruppo logico, definito set di disponibilità. Quando si creano macchine virtuali all'interno di un set di disponibilità, la piattaforma Azure ne distribuirà il posizionamento nell'infrastruttura sottostante. Nel caso di un evento di manutenzione pianificato sulla piattaforma di Azure o di un errore dell'hardware o dell'infrastruttura sottostante, l'uso di set di disponibilità assicura che almeno una macchina virtuale rimanga in esecuzione.

La procedura consigliata prevede che le applicazioni non risiedano in una singola macchina virtuale. Un set di disponibilità contenente un'unica macchina virtuale non può offrire alcuna protezione da eventi pianificati o non pianificati nella piattaforma Azure. Il [Contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) richiede la presenza di due o più macchine virtuali nell'ambito di un set di disponibilità per consentire la distribuzione delle macchine virtuali nell'infrastruttura sottostante.

In Azure, l'infrastruttura sottostante è suddivisa in domini di errore e domini di aggiornamento. Tali domini sono definiti in base agli host che condividono un ciclo di aggiornamento comune o un'infrastruttura fisica simile, ad esempio quella di alimentazione e rete. Azure distribuirà automaticamente le macchine virtuali in un set di disponibilità nell'ambito dei domini per garantire la disponibilità e la tolleranza di errore. A seconda della dimensione dell'applicazione e del numero di macchine virtuali all'interno di un set di disponibilità, è possibile modificare il numero di domini che si desidera usare. Altre informazioni su [gestione della disponibilità e uso dei domini di aggiornamento e di errore](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando si progetta l'infrastruttura dell'applicazione, pianificare i livelli applicazione usati. Raggruppare nei set di disponibilità le VM che svolgono la stessa funzione, creando ad esempio un set di disponibilità per le VM front-end che eseguono IIS. Creare un set di disponibilità distinto per le VM back-end che eseguono SQL Server. L'obiettivo è garantire che ogni componente dell'applicazione sia protetto da un set di disponibilità e che almeno un'istanza rimanga sempre in esecuzione.

In ogni livello di applicazione è possibile usare servizi di bilanciamento del carico a supporto di un set di disponibilità, per garantire che il traffico venga sempre indirizzato a un'istanza in esecuzione. Senza bilanciamento del carico, le macchine virtuali possono restare in esecuzione durante gli eventi di manutenzione pianificata e non pianificata, ma gli utenti finali potrebbero non essere in grado di risolvere i problemi se la macchina virtuale primaria non è disponibile.

Progettare l'applicazione per la disponibilità elevata a livello di archiviazione. È consigliabile usare un account di archiviazione separato per ogni VM di un set di disponibilità. Mantenere tutti i dischi (del sistema operativo e dei dati) associati a una VM nello stesso account di archiviazione. Quando si aggiungono più dischi rigidi virtuali a un account di archiviazione, tenere presenti i [limiti](../storage/storage-scalability-targets.md) degli account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Jan17_HO5-->



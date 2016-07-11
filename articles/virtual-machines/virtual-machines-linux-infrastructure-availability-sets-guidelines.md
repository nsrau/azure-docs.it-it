<properties
	pageTitle="Linee guida sui set di disponibilità | Microsoft Azure"
	description="Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione dei set di disponibilità nei servizi di infrastruttura di Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="iainfou"/>

# Linee guida sui set di disponibilità

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Questo articolo è incentrato sulla comprensione dei passaggi di pianificazione necessari per i set di disponibilità per garantire che le applicazioni rimangano accessibili in caso di eventi pianificati o non pianificati.

## Linee guida di implementazione per i set di disponibilità

Decisioni:

- Quanti set di disponibilità sono necessari per i diversi ruoli e livelli nell’infrastruttura dell'applicazione?

Attività:

- Definire il numero di macchine virtuali in ogni livello dell'applicazione desiderata.
- Determinare se è necessario modificare il numero di domini di errore o aggiornamento da usare per l'applicazione.
- Definire i set di disponibilità richiesti tramite la convenzione di denominazione scelta e le macchine virtuali che vi risiederanno. Una macchina virtuale può risiedere soltanto in un set di disponibilità.

## Set di disponibilità

Azure consente di inserire le macchine virtuali in un gruppo logico, definito set di disponibilità. Quando si creano macchine virtuali all'interno di un set di disponibilità, la piattaforma Azure ne distribuirà il posizionamento nell'infrastruttura sottostante. Nel caso di un evento di manutenzione pianificato sulla piattaforma di Azure o di un errore dell'hardware o dell'infrastruttura sottostante, l'uso di set di disponibilità assicura che almeno una macchina virtuale rimanga in esecuzione.

La procedura consigliata prevede che le applicazioni non risiedano in una singola macchina virtuale. Un set di disponibilità contenente un'unica macchina virtuale non può offrire alcuna protezione da eventi pianificati o non pianificati nella piattaforma Azure. Il [Contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) richiede la presenza di due o più macchine virtuali nell'ambito di un set di disponibilità per consentire la distribuzione delle macchine virtuali nell'infrastruttura sottostante.

In Azure, l'infrastruttura sottostante è suddivisa in domini di errore e domini di aggiornamento. Tali domini sono definiti in base agli host che condividono un ciclo di aggiornamento comune o un'infrastruttura fisica simile, ad esempio quella di alimentazione e rete. Azure distribuirà automaticamente le macchine virtuali in un set di disponibilità nell'ambito dei domini per garantire la disponibilità e la tolleranza di errore. A seconda della dimensione dell'applicazione e del numero di macchine virtuali all'interno di un set di disponibilità, è possibile modificare il numero di domini che si desidera usare. Altre informazioni sulla [gestione della disponibilità e l'uso dei domini di aggiornamento e di errore](virtual-machines-linux-manage-availability.md).

Quando si progetta l'infrastruttura dell'applicazione, è necessario pianificare anche i livelli di applicazione che si desidera usare. Raggruppare nei set di disponibilità le macchine virtuali che svolgono la stessa funzione, creando ad esempio un set di disponibilità per le macchine virtuali front-end che eseguono Apache o nginx. Creare un set di disponibilità distinto per le macchine virtuali back-end che eseguono MongoDB o MySQL. L'obiettivo è garantire che ogni componente dell'applicazione sia protetto da un set di disponibilità e che almeno un'istanza rimanga sempre in esecuzione.

In ogni livello di applicazione è possibile usare servizi di bilanciamento del carico a supporto di un set di disponibilità, per garantire che il traffico venga sempre indirizzato a un'istanza in esecuzione. Senza bilanciamento del carico, le macchine virtuali possono restare in esecuzione durante gli eventi di manutenzione pianificata e non pianificata, ma gli utenti finali potrebbero non essere in grado di risolvere i problemi se la macchina virtuale primaria non è disponibile.


## Passaggi successivi
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0629_2016-->
<properties
	pageTitle="Progettare set di scalabilità di macchine virtuali per la scalabilità | Microsoft Azure"
	description="Informazioni su come progettare set di scalabilità di macchine virtuali per la scalabilità"
	keywords="macchina virtuale linux,set di scalabilità macchine virtuali" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="gatneil"/>

# Progettare set di scalabilità di VM per la scalabilità

Questo argomento descrive considerazioni di progettazione per i set di scalabilità di macchine virtuali. Per sapere che cosa sono i set di scalabilità di macchine virtuali, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).


## Archiviazione

Un set di scalabilità usa account di archiviazione per archiviare i dischi del sistema operativo delle VM all'interno del set stesso. È consigliabile un rapporto di 20 VM al massimo per account di archiviazione. È anche consigliabile usare le diverse lettere dell'alfabeto come caratteri iniziali dei nomi degli account di archiviazione. Questo consente di suddividere il carico tra diversi sistemi interni. Ad esempio, nel modello seguente è usata la funzione di modello ARM uniqueString per generare hash di prefisso anteposti a nomi di account di archiviazione: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## Provisioning eccessivo

A partire dalla versione dell'API 2016-03-30, l'impostazione predefinita dei set di scalabilità di VM corrisponde al provisioning eccessivo di VM. Ciò significa che il set di scalabilità avvierà un numero maggiore di VM rispetto a quello richiesto dall'utente, eliminando quindi le VM non necessarie. Questo consente di migliorare la percentuale di riuscita del provisioning, poiché se il provisioning ha esito negativo anche per una sola VM, Azure Resource Manager considera non riuscita l'intera distribuzione. Le VM aggiuntive non verranno addebitate all'utente e non verranno considerate per il raggiungimento dei limiti di quota.

Questa funzione consente di migliorare la percentuale di riuscita del provisioning, ma può causare il comportamento confuso delle applicazioni non progettate per gestire VM che scompaiono senza preavviso. Per disattivare la funzionalità di provisioning in eccesso, inserire la stringa "overprovision": false nel modello, se non è già presente.

Se si disattiva il provisioning eccessivo, è possibile usare senza problemi un numero maggiore di VM per account di archiviazione. Non è tuttavia consigliabile superare le 40 macchine virtuali.


## Limiti
Un set di scalabilità basato su un'immagine personalizzata creata dall'utente deve creare tutti i dischi rigidi virtuali del disco del sistema operativo all'interno di un unico account di archiviazione. Di conseguenza, il numero massimo consigliato di VM in un set di scalabilità basato su un'immagine personalizzata è 20. Se si disattiva il provisioning eccessivo, è possibile arrivare a 40.

Un set di scalabilità basato su un'immagine di piattaforma ha un limite di 100 VM, per le quali è consigliabile usare 5 account di archiviazione.

Per un numero di macchine virtuali superiore a tali limiti, è necessario distribuire più set di scalabilità. Per informazioni su come eseguire questa operazione, [vedere questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).

<!---HONumber=AcomDC_0615_2016-->
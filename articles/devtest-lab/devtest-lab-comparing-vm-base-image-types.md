<properties
	pageTitle="Confronto tra immagini personalizzate e formule nei lab di sviluppo/test | Microsoft Azure"
	description="Conoscere le differenze tra le immagini personalizzate e le formule come basi per le macchine virtuali consente di decidere quale soluzione è più adatta per il proprio ambiente."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/08/2016"
	ms.author="tarcher"/>

# Confronto tra immagini personalizzate e formule nei lab di sviluppo/test

## Panoramica
Sia le [immagini personalizzate](./devtest-lab-create-template.md) che le [formule](./devtest-lab-manage-formulas.md) possono essere usate come basi per [creare nuove macchine virtuali](./devtest-lab-add-vm-with-artifacts.md). La differenza principale tra le immagini personalizzate e le formule è che un'immagine personalizzata è semplicemente un'immagine basata su un disco rigido virtuale, mentre una formula è un'immagine basata su un disco rigido virtuale *con in più* impostazioni preconfigurate, ad esempio dimensioni della macchina virtuale, rete virtuale e subnet, elementi e così via. Le impostazioni preconfigurate vengono specificate con valori predefiniti che possono essere sostituiti al momento della creazione della macchina virtuale. Questo articolo illustra alcuni dei vantaggi e degli svantaggi relativi all'uso delle immagini personalizzate e delle formule.
 
## Vantaggi e svantaggi delle immagini personalizzate
Le immagini personalizzate offrono un modo statico e non modificabile per creare macchine virtuali da un ambiente di propria scelta.

**Vantaggi**
- Il provisioning della macchina virtuale da un'immagine personalizzata è veloce poiché non vengono apportate modifiche dopo che la macchina virtuale viene attivata dall'immagine. In altre parole, non vi sono impostazioni da applicare poiché l'immagine personalizzata è semplicemente un'immagine senza impostazioni. 
- Le macchine virtuali create da un'unica immagine personalizzata sono identiche.

**Svantaggi**
- Se è necessario aggiornare alcuni aspetti dell'immagine personalizzata, si dovrà ricreare l'immagine.  

## Vantaggi e svantaggi delle formule
Le formule offrono un modo dinamico per creare macchine virtuali in base a una configurazione o alle impostazioni desiderate.

**Vantaggi**
- È possibile acquisire in tempo reale le modifiche apportate nell'ambiente in tempo reale usando gli elementi. Ad esempio, per installare una macchina virtuale con i bit più recenti della pipeline di rilascio o integrare il codice più recente dal repository, è possibile specificare semplicemente un elemento che consenta di distribuire i bit più recenti o integri il codice più recente nella formula insieme a un'immagine di base di destinazione. Ogni volta che la formula viene usata per creare macchine virtuali, il codice o i bit più recenti vengono integrati o distribuiti alla macchina virtuale. 
- A differenza delle immagini personalizzate, le formule possono definire impostazioni predefinite come le dimensioni delle macchine virtuali e le impostazioni della rete virtuale. 
- Le impostazioni salvate in una formula vengono visualizzate come valori predefiniti, ma possono essere modificate quando viene creata la macchina virtuale. 

**Svantaggi**
- La creazione di una macchina virtuale da una formula può richiedere più tempo rispetto alla creazione da un'immagine personalizzata.

<!---HONumber=AcomDC_0518_2016-->
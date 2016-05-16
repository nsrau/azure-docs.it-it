<properties
	pageTitle="Creare più macchine virtuali | Microsoft Azure"
	description="Opzioni per la creazione di più macchine virtuali in Windows"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="guybo"/>

# Creazione di più macchine virtuali di Azure

Esistono molti scenari in cui è necessario creare un numero elevato di macchine virtuali simili, ad esempio gli scenari di high-performance computing (HPC), di analisi dei dati su larga scala, di server di livello intermedio o back-end scalabili e spesso senza stato (come i server Web) e di database distribuiti.

Questo articolo illustra le opzioni disponibili per creare più VM in Azure nei casi più complessi della semplice creazione manuale di una serie di VM. La creazione manuale, infatti, non è adeguatamente scalabile se è necessario creare un numero più consistente di VM.

Un modo per ottimizzare la creazione di molte VM simili consiste nell'usare il costrutto dei _cicli di risorse_ di Azure Resource Manager.

## Cicli di risorse

I cicli di risorse rappresentano un tipo di sintassi abbreviata presente nei modelli di Azure Resource Manager che consente di creare un set di risorse configurate in modo simile in un ciclo. È possibile usare cicli di risorse per creare, ad esempio, più account di archiviazione, interfacce di rete, macchine virtuali. Per altre informazioni sui cicli di risorse, fare riferimento a [Creare VM in set di disponibilità usando cicli di risorse](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## Problemi di scalabilità

Anche se i cicli di risorse rendono più semplice la creazione di infrastrutture cloud su larga scala e consentono modelli più concisi, alcuni problemi permangono. Ad esempio, quando si creano 100 macchine virtuali tramite un ciclo di risorse, al momento di definire le VM è necessario correlare le schede NIC delle VM agli account di archiviazione. È probabile che il numero di VM sia diverso dal numero di account di archiviazione e che quindi i loop di risorse abbiano dimensioni diverse. Si tratta di problemi risolvibili ma la loro complessità aumenta in modo significativo con il ridimensionamento.

Un altro problema si verifica quando è necessaria un'infrastruttura ridimensionabile in modo flessibile, ad esempio nel caso della scalabilità automatica, che consente di aumentare o diminuire automaticamente il numero di VM a seconda del carico di lavoro. Le VM non sono dotate di un meccanismo integrato per variare di numero (aumento e riduzione). Se si effettua una riduzione rimuovendo VM, assicurarsi che le VM siano distribuite in modo equilibrato nei domini di aggiornamento e di errore per garantire la disponibilità elevata costituisce un altro problema.

Infine, anche se i cicli di risorse hanno una sintassi abbreviata, quando se ne usa uno, l'infrastruttura sottostante riceve più chiamate per la creazione di risorse. Se più chiamate creano risorse simili, si crea un'opportunità implicita per Azure di migliorare la progettazione e di ottimizzare l'affidabilità e le prestazioni della distribuzione. A questo punto entrano in gioco i _set di scalabilità di macchine virtuali_.

## Set di scalabilità di macchine virtuali

I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure per distribuire e gestire un set di macchine virtuali identiche. Dato che tutte le macchine virtuali hanno la stessa configurazione, è facile ridurre e aumentare i set di scalabilità di VM. È sufficiente modificare il numero di VM del set, che può essere configurato per la scalabilità automatica in base alle esigenze del carico di lavoro.

Per le applicazioni che richiedono la scalabilità (aumento e riduzione di istanze e capacità) delle risorse di calcolo, le operazioni di ridimensionamento vengono bilanciate in modo implicito tra domini di errore e domini di aggiornamento.

Invece di correlare più risorse, ad esempio schede NIC e VM, un set di scalabilità è dotato delle proprietà relative alla rete, all'archiviazione, alle macchine virtuali e all'estensione, che è possibile configurare in modo centralizzato.

Per un'introduzione ai set di scalabilità di VM, fare riferimento alla [pagina del prodotto](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Per informazioni più dettagliate, passare alla [documentazione](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0504_2016-->
<properties
	pageTitle="Risoluzione dei problemi di scalabilità automatica con set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Informazioni sulla risoluzione dei problemi di scalabilità automatica con set di scalabilità di macchine virtuali. Comprendere i problemi tipici rilevati e come risolverli."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="guybo"/>
    
# Risoluzione dei problemi di scalabilità automatica con set di scalabilità di macchine virtuali

**Problema**: è stata creata un'infrastruttura per la scalabilità automatica in Azure Resource Manager tramite set di scalabilità di macchine virtuali, ad esempio distribuendo un modello simile al seguente: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. Le regole di scalabilità impostate funzionano ma, indipendentemente dal carico delle macchine virtuali, la scalabilità automatica non funziona.

## Passaggi per la risoluzione dei problemi

Alcuni aspetti da considerare:

- Quanti core sono presenti in ogni macchina virtuale e viene caricato ogni singolo core? Il modello di avvio rapido di Azure di esempio riportato sopra ha uno script do\_work.php, che carica un singolo core. Se si usa una macchina virtuale di dimensioni superiori a Standard\_A1 è necessario eseguire il carico più volte. Per verificare il numero di core delle macchine virtuali, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Quante VM sono presenti nel set di scalabilità di macchine virtuali e vengono eseguite operazioni su ogni VM?

    Un aumento del numero di istanze ha luogo unicamente quando l'uso medio della CPU tra **tutte** le macchine virtuali in un set di scalabilità supera il valore di soglia, in base al tempo definito all'interno delle regole di scalabilità automatica.

- Sono stati persi eventi di scalabilità?

    Cercare gli eventi di scalabilità nei registri di controllo nel portale di Azure. Potrebbero essersi verificati un aumento e poi una riduzione delle prestazioni non rilevati. È possibile filtrare per "Scalabilità".

	![Log di controllo][audit]

- Le soglie di aumento e riduzione del numero di istanze sono sufficientemente diverse?

    Si supponga di impostare una regola per aumentare il numero di istanze quando l'uso medio della CPU è superiore al 50% in 5 minuti e per ridurre il numero di istanze quando l'uso medio della CPU è inferiore al 50%. Questo può provocare un problema di instabilità quando l'uso della CPU si avvicina a tale soglia e le azioni di ridimensionamento devono aumentare e ridurre costantemente le dimensioni del set. Il servizio di scalabilità automatica prova quindi a risolvere l'instabilità e questo può risolversi in un mancato ridimensionamento. Assicurarsi che le soglie di aumento e riduzione del numero di istanze siano sufficientemente diverse per consentire le azioni di ridimensionamento.

- È stato scritto un modello JSON personalizzato?

    È facile commettere errori. È quindi consigliabile partire da un modello funzionante come quello riportato sopra e apportare piccole modifiche incrementali. Il modello deve correlare un account di archiviazione di estensione della diagnostica, il set di scalabilità e la risorsa Microsoft.Insights e fare riferimento in modo corretto al nome della metrica dei dati delle prestazioni, che presenta differenze tra Windows e Linux.

- È possibile eseguire manualmente l'aumento o la riduzione del numero di istanze?

    Provare a ridistribuire la risorsa del set di scalabilità di macchine virtuali con un'impostazione di "capacità" diversa, per modificare manualmente il numero di macchine virtuali. Un modello di esempio per questa operazione è disponibile all'indirizzo: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing. Potrebbe essere necessario modificare il modello per fare in modo che abbia le stesse dimensioni della macchina virtuale usate dal set di scalabilità. Se è possibile modificare manualmente il numero di macchine virtuali, il problema è limitato alla scalabilità automatica.

- Verificare lo stato delle risorse Microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights in [Esplora risorse di Azure](https://resources.azure.com/).

    Questo è uno strumento di risoluzione dei problemi indispensabile che mostra lo stato delle risorse di Azure Resource Manager. Fare clic sulla sottoscrizione ed esaminare il gruppo di risorse di cui si sta provando a risolvere i problemi. In Provider di risorse di calcolo esaminare il set di scalabilità di macchine virtuali la relativa visualizzazione delle istanze, che mostra lo stato di una distribuzione. Controllare anche la visualizzazione delle istanze delle macchine virtuali incluse nel set di scalabilità. Accedere quindi al provider di risorse Microsoft.Insights e verificare lo stato delle regole di scalabilità automatica.

- L'estensione della diagnostica funziona e genera dati sulle prestazioni?
 
    In Azure Resource Manager la scalabilità automatica funziona per mezzo di un'estensione di macchina virtuale detta estensione della diagnostica, diversa per Linux e per Windows. L'estensione invia i dati sulle prestazioni a un account di archiviazione specificato nel modello. I dati vengono quindi aggregati dal servizio Azure Insights.

    Se il servizio Insights non può leggere i dati delle macchine virtuali, invia un messaggio di posta elettronica all'indirizzo specificato durante la creazione dell'account Azure, ad esempio in caso di malfunzionamento delle macchine virtuali.

    È anche possibile esaminare i dati personalmente. Esaminare l'account di archiviazione di Azure con uno strumento di esplorazione cloud. Usare ad esempio [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2) per accedere e scegliere la sottoscrizione di Azure usata e il nome dell'account di archiviazione di diagnostica a cui si fa riferimento nella definizione dell'estensione della diagnostica nel modello di distribuzione.

	![Cloud Explorer][explorer]

    Di seguito è riportata una serie di tabelle in cui vengono archiviati i dati di ogni macchina virtuale. Prendendo ad esempio Linux e la relativa metrica della CPU, esaminare le righe più recenti. Visual Studio Cloud Explorer supporta un linguaggio di query che permette di eseguire una query di questo tipo: "Timestamp gt datetime'2016-02-02T21:20:00Z'" per assicurarsi di ottenere gli eventi più recenti (orario UTC). I dati visualizzati corrispondono alle regole di scalabilità impostate? Nell'esempio seguente, l'uso della CPU nel computer 20 è aumentato fino al 100% negli ultimi 5 minuti.

	![Tabelle di archiviazione][tables]

    Se i dati non sono presenti, il problema riguarda l'estensione della diagnostica in esecuzione nelle macchine virtuali. Se i dati sono presenti, il problema può riguardare le regole di scalabilità oppure il servizio Insights. Verificare lo [Stato di Azure](https://azure.microsoft.com/status/).

    Dopo aver seguito questa procedura, è possibile provare a visitare i forum di [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp) o di [Stack Overflow](http://stackoverflow.com/questions/tagged/azure) oppure rivolgersi al supporto tecnico. Sarà necessario condividere il modello e la visualizzazione dei dati sulle prestazioni.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

<!---HONumber=AcomDC_0427_2016-->
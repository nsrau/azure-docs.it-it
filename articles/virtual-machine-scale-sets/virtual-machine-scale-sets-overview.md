---
title: Panoramica dei set di scalabilità di macchine virtuali | Microsoft Docs
description: Informazioni sui set di scalabilità di macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: gbowerman
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: guybo

---
# Panoramica dei set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire un set di macchine virtuali identiche. Con tutte le macchine virtuali configurate allo stesso modo, i set di scalabilità di macchine virtuali sono progettati per supportare la vera scalabilità automatica (non è necessario il pre-provisioning delle macchine virtuali) e semplificare quindi la creazione di servizi su larga scala destinati a carichi di lavoro Big Compute, Big Data e nei contenitori.

Per le applicazioni che richiedono la scalabilità (aumento e riduzione di istanze e capacità) delle risorse di calcolo, le operazioni di ridimensionamento vengono bilanciate in modo implicito tra domini di errore e domini di aggiornamento. Per un'introduzione ai set di scalabilità di macchine virtuali, vedere questo [annuncio del blog di Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Per altre informazioni sui set di scalabilità di macchine virtuali, guardare i video seguenti:

* [Mark Russinovich illustra i set di scalabilità di Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Set di scalabilità di macchine virtuali con Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## Creazione e gestione dei set di scalabilità di macchine virtuali
È possibile creare un set di scalabilità di macchine virtuali nel [portale di Azure](https://portal.azure.com) selezionando *nuova* e digitando "scalabilità" nella barra di ricerca. Nei risultati verrà visualizzato "Set di scalabilità di macchine virtuali". In questa pagina è possibile compilare i campi necessari per personalizzare e distribuire il set di scalabilità.

I set di scalabilità di macchine virtuali possono anche essere definiti e distribuiti tramite modelli JSON e [API REST](https://msdn.microsoft.com/library/mt589023.aspx) esattamente come le singole macchine virtuali di Gestione risorse di Azure. Di conseguenza, è possibile usare qualsiasi metodo di distribuzione standard di Gestione risorse di Azure. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Un set di modelli di esempio per i set di scalabilità di macchine virtuali è disponibile [qui](https://github.com/Azure/azure-quickstart-templates), all'interno del repository GitHub dedicato ai modelli della guida introduttiva di Azure (cercare i modelli contenenti *vmss* nel titolo).

Nelle pagine dei dettagli per questi modelli verrà visualizzato un pulsante per il collegamento alla funzionalità di distribuzione del portale. Per distribuire il set di scalabilità di macchine virtuali, fare clic sul pulsante e quindi specificare tutti i parametri richiesti nel portale. In caso di dubbi sul fatto che una risorsa supporti le lettere maiuscole o una combinazione di maiuscole e minuscole, per sicurezza è consigliabile usare sempre valori di parametri con lettere minuscole. Per un'analisi video di un modello di set di scalabilità di macchine virtuali, vedere:

[Analisi del modello di set di scalabilità di macchine virtuali](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## Aumento e riduzione di risorse e capacità di un set di scalabilità di macchine virtuali
Per aumentare o ridurre il numero di macchine virtuali in un set di scalabilità di macchine virtuali, è sufficiente modificare la proprietà *capacity* e ridistribuire il modello. Questa semplificazione agevola la scrittura di un livello di scalabilità personalizzato per definire eventi di scalabilità personalizzati non supportati dalla funzionalità di scalabilità automatica di Azure.

Se si sta ridistribuendo un modello per modificare la capacità, è possibile definire un modello molto più piccolo che include solo lo SKU e la capacità aggiornata. [Qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) è riportato un esempio.

Per eseguire la procedura di creazione di un set di scalabilità che viene ridimensionato automaticamente, vedere [Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali](virtual-machine-scale-sets-windows-autoscale.md)

## Monitoraggio del set di scalabilità di macchine virtuali
Il [portale di Azure](https://portal.azure.com) include un elenco di set di scalabilità con le proprietà di base, nonché un elenco delle macchine virtuali nel set. Per altri dettagli è possibile usare [Esplora risorse di Azure](https://resources.azure.com) per visualizzare i set di scalabilità di macchine virtuali. I set di scalabilità di macchine virtuali sono una risorsa di Microsoft.Compute, di conseguenza da questo sito è possibile visualizzarli espandendo i collegamenti seguenti:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## Scenari dei set di scalabilità di macchine virtuali
Questa sezione mostra un elenco di alcuni scenari di set di scalabilità di macchine virtuali tipici. Anche alcuni servizi di Azure di livello più elevato (ad esempio Batch, Service Fabric, servizio contenitore di Azure) useranno questi scenari.

* **Connessione RDP/SSH a istanze del set di scalabilità di macchine virtuali**: un set di scalabilità di macchine virtuali viene creato in una rete virtuale e alle singole macchine virtuali nel set di scalabilità non vengono allocati indirizzi IP pubblici. Questo è positivo perché in genere non si vogliono sostenere le spese e il sovraccarico di gestione richiesti dall'allocazione di indirizzi IP pubblici distinti a tutte le risorse senza stato della griglia di calcolo ed è possibile connettersi facilmente a queste macchine virtuali da altre risorse della rete virtuale, incluse quelle con indirizzi IP pubblici, ad esempio i servizi di bilanciamento del carico o le macchine virtuali autonome.
* **Connettersi alle macchine virtuali usando regole NAT**: è possibile creare un indirizzo IP pubblico, assegnarlo a un servizio di bilanciamento del carico e definire le regole NAT in ingresso che eseguono il mapping di una porta nell'indirizzo IP a una porta di una macchina virtuale del set di scalabilità. ad esempio:
  
  | Sorgente | Porta di origine | Destination | Porta di destinazione |
  | --- | --- | --- | --- |
  |  IP pubblico |Porta 50000 |vmss\_0 |Porta 22 |
  |  IP pubblico |Porta 50001 |vmss\_1 |Porta 22 |
  |  IP pubblico |Porta 50002 |vmss\_2 |Porta 22 |
  
   Ecco un esempio di creazione di un set di scalabilità VM che usa le regole NAT per abilitare la connessione SSH a ogni VM in un set di scalabilità usando un solo IP pubblico: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Ecco un esempio della stessa operazione con RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Connettersi alle macchine virtuali usando una macchina virtuale jumpbox**: se si crea un set di scalabilità di macchine virtuali e una macchina virtuale autonoma nella stessa rete virtuale, la macchina virtuale autonoma e le macchine virtuali del set di scalabilità possono connettersi l'una all'altra usando i rispettivi indirizzi IP interni definiti dalla rete virtuale/subnet. Se si crea un indirizzo IP pubblico e lo si assegna alla macchina virtuale autonoma, è possibile connettersi tramite RDP o SSH alla macchine virtuale autonoma e quindi connettersi da tale macchina virtuale alle istanze del set di scalabilità di macchine virtuali. Si noterà quindi che un semplice set di scalabilità di macchine virtuali è intrinsecamente più sicuro di una semplice macchina virtuale autonoma con un indirizzo IP pubblico nella configurazione predefinita.
  
   [A titolo di esempio per questo approccio, questo modello crea un semplice cluster Mesos costituito da una macchina virtuale master autonoma che gestisce un cluster di macchine virtuali basato sul set di scalabilità di macchine virtuali.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)
* **Bilanciamento del carico round robin alle istanze del set di scalabilità di macchine virtuali**: per trasmettere dati a un cluster di elaborazione di macchine virtuali con un approccio "round robin", è possibile configurare un servizio di bilanciamento del carico di Azure con le relative regole di bilanciamento del carico. È anche possibile definire alcuni probe per verificare che l'applicazione sia in esecuzione effettuando il ping delle porte con un protocollo, un intervallo e un percorso di richiesta specificati. [Gateway applicazione](https://azure.microsoft.com/services/application-gateway/) di Azure supporta anche set di scalabilità, insieme a scenari di bilanciamento del carico più sofisticati.
  
   [Ecco un esempio che crea un set di scalabilità di macchine virtuali che eseguono il server Web IIS e usa un servizio di bilanciamento del carico per bilanciare il carico ricevuto da ogni macchina virtuale. Usa anche il protocollo HTTP per eseguire il ping di un URL specifico su ogni macchina virtuale. ](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) Esaminare il tipo di risorsa Microsoft.Network/loadBalancers e i profili networkProfile ed extensionProfile nell'oggetto virtualMachineScaleSet.
* **Distribuzione di un set di scalabilità di macchine virtuali come cluster di elaborazione in un gestore di cluster PaaS**: i set di scalabilità di macchine virtuali sono talvolta descritti come un ruolo di lavoro di prossima generazione. È una definizione valida che però rischia di confondere le funzionalità dei set di scalabilità con quelle del ruolo di lavoro PaaS v1. In un certo senso i set di scalabilità di macchine virtuali offrono un "ruolo di lavoro" o risorsa di lavoro effettiva, in quanto forniscono una risorsa di calcolo generalizzata indipendente dalla piattaforma o dal runtime, personalizzabile e integrata nel servizio IaaS di Gestione risorse di Azure.
  
   Un ruolo di lavoro PaaS v1, sebbene limitato in termini di supporto per la piattaforma o il runtime (solo immagini della piattaforma Windows), include anche servizi come lo scambio di indirizzi VIP, impostazioni di aggiornamento configurabili, impostazioni specifiche della distribuzione del runtime o delle app che non sono *ancora* disponibili nei set di scalabilità di macchine virtuali o che verranno forniti da altri servizi PaaS di livello più elevato come Service Fabric. Tenendo presente questo fatto, è possibile considerare i set di scalabilità di macchine virtuali come un'infrastruttura che supporta la tecnologia di piattaforma distribuita come servizio e quindi le soluzioni PaaS, come Service Fabric o i gestori di cluster come Mesos, possono basarsi sui set di scalabilità di macchine virtuali come un livello di calcolo scalabile.
  
   [A titolo di esempio per questo approccio, questo modello crea un semplice cluster Mesos costituito da una macchina virtuale master autonoma che gestisce un cluster di macchine virtuali basato sul set di scalabilità di macchine virtuali.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) Le versioni future del [servizio contenitore di Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) distribuiranno versioni più complesse/avanzate di questo scenario basato sui set di scalabilità di VM.

## Indicazioni sulla scalabilità e le prestazioni dei set di scalabilità di macchine virtuali
* Non creare più di 500 VM in più set di scalabilità di VM alla volta.
* Prevedere non più di 20 VM per ogni account di archiviazione, a meno che non si imposti la proprietà *overprovision* su "false", nel qual caso è possibile arrivare a 40.
* Diversificare il più possibile le prime lettere dei nomi degli account di archiviazione. I modelli di set di scalabilità VM di esempio nei [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates/) offrono esempi di come procedere.
* Se si usano macchine virtuali personalizzate, non pianificare più di 40 macchine virtuali per set di scalabilità in un singolo account di archiviazione. Sarà necessaria l'immagine copiata in precedenza nell'account di archiviazione prima di poter iniziare a distribuire i set di scalabilità di macchine virtuali. Per altre informazioni, vedere le domande frequenti.
* Non pianificare più di 4096 VM per rete virtuale.
* Il numero di VM che è possibile creare è limitato dalla quota di memoria centrale nell'area in cui si esegue la distribuzione. Potrebbe essere necessario contattare il supporto tecnico per richiedere l'aumento della quota di calcolo anche se l'attuale limite di core da usare con i servizi cloud o IaaS v1 è elevato. Per informazioni sulla quota, è possibile eseguire il comando dell'interfaccia della riga di comando di Azure `azure vm list-usage` e il comando di PowerShell `Get-AzureRmVMUsage`. Se si usa una versione di PowerShell precedente alla 1.0, usare `Get-AzureVMUsage`.

## Domande frequenti sui set di scalabilità di macchine virtuali
**D.** Quante macchine virtuali si possono includere in un set di scalabilità?

**R.** 100 se si usano immagini della piattaforma che è possibile distribuire in più account di archiviazione. Se si usano immagini personalizzate, fino a 40 (se la proprietà *overprovision* è impostata su "false", 20 per impostazione predefinita), perché attualmente le immagini personalizzate sono limitate a un solo account di archiviazione.

**Quali altri limiti di risorse esistono per i set di scalabilità di macchine virtuali?**

**R.** Non è possibile creare più di 500 VM in più set di scalabilità per area durante un periodo di 10 minuti. Si applicano i [limiti del servizio per la sottoscrizione di Azure/](../azure-subscription-service-limits.md) esistenti.

**D.** I dischi dati sono supportati nei set di scalabilità di macchine virtuali?

**R.** Non nella versione iniziale. Le opzioni per l'archiviazione dei dati sono:

* File di Azure (unità condivise SMB)
* Unità del sistema operativo
* Unità Temp (locale, non supportata dal servizio di archiviazione di Azure)
* Servizi dati di Azure (ad esempio tabelle di Azure, BLOB di Azure)
* Servizi dati esterni (ad esempio, database remoto)

**D.** Quali aree di Azure supportano i set di scalabilità di VM?

**R.** Tutte le aree che supportano Gestione risorse di Azure supportano i set di scalabilità di macchine virtuali.

**D.** Come si crea un set di scalabilità di VM con un'immagine personalizzata?

**R.** Lasciare vuota la proprietà vhdContainers, ad esempio:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
            },
            "osType": "Windows"
        }
    },


**D.** Se si riduce la capacità del set di scalabilità di macchine virtuali da 20 a 15, quali macchine virtuali verranno rimosse?

**R.** Le macchine virtuali vengono rimossi dal set di scalabilità in modo uniforme tra domini di aggiornamento e domini di errore per ottimizzare la disponibilità. Le VM con ID più alto sono rimosse per prime.

**D.** Cosa accade se poi si aumenta la capacità da 15 a 18?

**R.** Se si aumenta la capacità a 18, verranno create 3 nuove VM. Ogni volta l'ID istanza di VM verrà incrementato dal valore più elevato precedente (ad esempio 20, 21, 22). Le VM vengono bilanciate tra domini di aggiornamento e domini di errore.

**D.** Quando si usano più estensioni in un set di scalabilità di macchine virtuali, è possibile imporre una sequenza di esecuzione?

**R.** Non direttamente, ma, per l'estensione customScript, lo script potrebbe attendere il completamento di un'altra estensione, [ad esempio monitorando il log dell'estensione](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh). Sono disponibili informazioni aggiuntive sulla sequenziazione di estensione in questo post di blog: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Sequenziazione dell'estensione in set di disponibilità di macchine virtuali di Azure).

**D.** I set di scalabilità di macchine virtuali si integrano con i set di disponibilità di Azure?

**R.** Sì. Un set di scalabilità di VM è un set di disponibilità implicito impostato con 5 domini di errore e 5 domini di aggiornamento. Non è necessario configurare nulla in virtualMachineProfile. Nelle versioni future i set di scalabilità di macchine virtuali si estenderanno probabilmente su più tenant, ma per ora un set di scalabilità è un set di disponibilità singolo.

<!---HONumber=AcomDC_0914_2016-->
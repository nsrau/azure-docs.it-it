<properties
   pageTitle="Architettura di Gestione risorse di Azure | Microsoft Azure"
   description="Informazioni sull'architettura di Gestione risorse e sulle relazioni tra i provider di risorse di calcolo, rete e archiviazione."
   services="virtual-machines"
   documentationCenter=""
   authors="davidmu1"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="davidmu"/>

# Architettura di Gestione risorse di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.



In questo articolo viene fornita una panoramica delle architetture di Gestione servizi e Gestione risorse per la creazione di applicazioni e carichi di lavoro basati su infrastruttura.

## Architettura per Gestione servizi

Prima di affrontare l'architettura di gestione risorse di Azure e i vari provider di risorse, è opportuno esaminare l'architettura esistente per Gestione servizi di Azure. In Gestione servizi di Azure, le risorse di calcolo, di archiviazione o di rete per l'hosting delle macchine virtuali sono fornite da:

- Un servizio cloud obbligatorio che funge da contenitore per l'hosting di macchine virtuali (calcolo). Le macchine virtuali sono fornite automaticamente con una scheda di rete (NIC) e un indirizzo IP assegnati da Azure. Il servizio cloud contiene inoltre un'istanza del servizio di bilanciamento del carico esterno, un indirizzo IP pubblico ed endpoint predefiniti per consentire il traffico di desktop remoto e di PowerShell remoto per le macchine virtuali basate su Windows e il traffico Secure Shell (SSH) per le macchine virtuali basate su Linux.
- Un account di archiviazione obbligatorio per l'archiviazione dei dischi rigidi virtuali per una macchina virtuale, inclusi il sistema operativo, i dati temporanei e altri dischi dati (archiviazione).
- Una rete virtuale facoltativa che funge da contenitore aggiuntivo, in cui è possibile creare una struttura di subnet e definire la subnet in cui si trova la macchina virtuale (rete).

Questi sono i componenti e le relative relazioni per Gestione servizi di Azure.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Architettura per Gestione risorse

Per Gestione risorse di Azure, i provider di risorse supportano le singole risorse per la creazione di macchine virtuali funzionanti nella configurazione necessaria. Per le macchine virtuali, sono disponibili tre provider di risorse principali:

- Provider di risorse di calcolo (CRP): supporta le istanze di macchine virtuali e i set di disponibilità facoltativi.
- Provider di risorse di archiviazione (SRP): supporta account di archiviazione obbligatori per l'archiviazione dei dischi rigidi virtuali per le macchine virtuali, inclusi il sistema operativo e altri dischi dati.
- Provider di risorse di rete (NRP): supporta le schede di rete, gli indirizzi IP di macchine virtuali e le subnet all'interno di reti virtuali obbligatori, nonché i servizi di bilanciamento del carico, gli indirizzi IP del servizio di bilanciamento del carico e i gruppi di sicurezza di rete facoltativi.

Esistono inoltre relazioni tra le risorse all'interno dei provider di risorse:

- Una macchina virtuale dipende da un account di archiviazione specifico definito nel provider SRP per l'archiviazione dei relativi dischi nell'archiviazione BLOB (obbligatorio).
- Una macchina virtuale fa riferimento a una scheda di interfaccia di rete (NIC) specifica definita nel provider NRP (obbligatorio) e un set di disponibilità definito nel CRP (facoltativo).
- Una NIC fa riferimento all'indirizzo IP assegnato della macchina virtuale (obbligatorio), alla subnet della rete virtuale per la macchina virtuale (obbligatorio) e a un gruppo di sicurezza di rete (facoltativo).
- Una subnet all'interno di una rete virtuale fa riferimento a un gruppo di sicurezza di rete (facoltativo).
- Un'istanza del servizio di bilanciamento del carico fa riferimento al pool back-end di indirizzi IP che includono la NIC di una macchina virtuale (facoltativo) e fa riferimento a un indirizzo IP pubblico o privato del servizio di bilanciamento del carico (facoltativo).

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch2.png)

La componentizzazione delle risorse offre una maggiore flessibilità durante la configurazione dell'infrastruttura per un carico di lavoro IT ospitato in Azure. I modelli di Gestione risorse di Azure sfruttano questa flessibilità per creare il set di risorse dipendenti necessarie per una configurazione specifica. Durante l'esecuzione di un modello, Gestione risorse garantisce che le risorse per una configurazione vengano create nell'ordine corretto per mantenere le dipendenze e i riferimenti. Ad esempio, Gestione risorse non creerà la NIC per una macchina virtuale finché non ha creato la rete virtuale con una subnet e un indirizzo IP (un gruppo di sicurezza di rete è facoltativo).

Un gruppo di risorse è un contenitore logico per le risorse correlate per un'applicazione, che può essere costituita da più macchine virtuali, NIC, indirizzi IP, servizi di bilanciamento del carico, subnet e gruppi di sicurezza di rete. Ad esempio, è possibile gestire tutte le risorse dell'applicazione come una singola unità di gestione. Le risorse possono essere create, aggiornate ed eliminate insieme. Ecco un'applicazione di esempio distribuita in un singolo gruppo di risorse.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

L'applicazione è costituita da:

- Due macchine virtuali che utilizzano lo stesso account di archiviazione, sono nello stesso set di disponibilità e nella stessa subnet di una rete virtuale.
- Una singola NIC e un indirizzo IP di macchina virtuale per ogni macchina virtuale.
- Un servizio di bilanciamento del carico esterno che distribuisce il traffico Internet alle NIC delle due macchine virtuali.

Tutte le risorse dell'applicazione vengono gestite tramite il singolo gruppo di risorse che le contiene.

È inoltre possibile visualizzare la componentizzazione e le relazioni dipendenti tra le risorse quando si crea una macchina virtuale basata su Gestione risorse tramite Azure PowerShell o l'interfaccia delle riga di comando di Azure. Prima di poter eseguire il comando che crea la macchina virtuale, è necessario creare un gruppo di risorse, un account di archiviazione, una rete virtuale con una subnet e una NIC con un indirizzo IP. Per altre informazioni, vedere [Creare e preconfigurare una macchina virtuale Windows con Gestione risorse e Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)

## Passaggi successivi

[Distribuire e gestire le macchine virtuali utilizzando modelli di Gestione risorse di Azure e l’interfaccia della riga di comando di Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Distribuire e gestire macchine virtuali di Azure usando modelli di Gestione risorse e PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Azure](resource-group-overview.md)

<!---HONumber=AcomDC_1203_2015-->
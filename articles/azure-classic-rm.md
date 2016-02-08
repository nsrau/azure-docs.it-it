<properties
   pageTitle="Modelli di distribuzione Gestione risorse e Gestione servizi (Classico) | Azure"
   description="Comprendere le differenze tra i modelli di distribuzione Gestione risorse e Classico."
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"/>

# Modelli di distribuzione di Azure

La piattaforma Azure è in fase di transizione. Per gli utenti che non hanno familiarità con Azure o che lo usano da anni è importante comprendere alcune delle principali modifiche che verranno apportate alla piattaforma durante la transizione.

Tutte le risorse di Azure supportano uno o entrambi i modelli di distribuzione seguenti:

- **Gestione risorse**: si tratta del modello di distribuzione più recente per le risorse Azure. La maggior parte delle risorse più recenti supporta già questo modello di distribuzione, che in futuro sarà supportato da tutte le risorse.   
 
- **Classico:** questo modello è attualmente supportato dalla maggior parte delle risorse di Azure esistenti. Le nuove risorse aggiunte in Azure non supportano questo modello.

La documentazione per ciascuna risorsa di Azure descrive nel dettaglio con quali modelli del servizio può essere creata.

## Perché è importante? 

È importante per i motivi seguenti:

- Le funzionalità della piattaforma Azure in uso sono diverse tra questi due modelli. Ad esempio, le risorse create usando il modello di distribuzione Gestione risorse (o semplicemente Gestione risorse) possono essere create con i [modelli di Gestione risorse di Azure](resource-group-overview.md/#template-deployment), mentre non è possibile farlo con le risorse create con il modello di distribuzione Classico.
- Le funzionalità o i comportamenti delle singole risorse di Azure possono differire tra i due modelli oppure esistere solo in un modello o nell'altro. Ad esempio, il bilanciamento del carico del traffico tra macchine virtuali create con il modello di distribuzione Classico è *implicito* perché le macchine virtuali sono membri di un servizio cloud di Azure e il carico viene bilanciato automaticamente tra macchine virtuali all'interno di un servizio cloud. Le macchine virtuali create usando Gestione risorse non sono membri di un servizio cloud ed è necessario creare *in modo esplicito* una risorsa separata del Servizio di bilanciamento del carico di Azure per bilanciare il carico tra più macchine virtuali.  
- Le modalità di creazione, configurazione e gestione delle risorse di Azure differiscono tra questi due modelli.
- Le risorse create usando un modello di distribuzione non possono necessariamente interagire con le risorse create usando un modello di distribuzione differente. Ad esempio, le macchine virtuali di Azure create usando un unico modello di distribuzione possono essere connesse solo alle reti virtuali di Azure create usando lo stesso modello di distribuzione.    

Alla base di ciascun modello di distribuzione è prevista un'API per ogni risorsa. Sono disponibili l'[API di Gestione risorse](https://msdn.microsoft.com/library/azure/dn948464.aspx) per il modello di distribuzione di Gestione risorse e l'[API di gestione del servizio](https://msdn.microsoft.com/library/azure/ee460799.aspx) per il modello di distribuzione Classico. Gli sviluppatori possono scrivere codice per l'interazione *diretta* con queste API.

I professionisti IT, tuttavia, in genere interagiscono con queste API *indirettamente* usando un portale grafico in un Web browser, i cmdlet di Azure PowerShell in un computer Windows oppure l'interfaccia della riga di comando di Azure (CLI) in un computer Windows, OS X o Linux. Tutti e tre i metodi indiretti usati dal professionista IT interagiscono direttamente con le API. Ciò significa che quando una nuova funzionalità viene introdotta nella piattaforma o nelle risorse di Azure è sempre direttamente disponibile usando prima l'API, mentre i metodi indiretti ottengono il supporto per le nuove risorse e funzionalità dopo che l'API è stata resa disponibile.

Le sezioni seguenti illustrano come configurare le risorse di Azure usando i vari modelli di distribuzione usando i tre metodi indiretti.

## Portali
Azure offre due portali:

- **[Portale di Azure](https://manage.windowsazure.com):** se si usa Azure da tempo si conosce già questo portale, che viene usato per creare e configurare le risorse più obsolete di Azure, che supportano il modello di distribuzione classico. Questo portale non permette invece di creare o configurare le risorse che supportano unicamente Gestione risorse. 
- **[Portale di anteprima di Azure](https://azure.microsoft.com/overview/preview-portal/):** se si usa una risorsa di Azure più recente è probabile che questo portale sia già stato usato. Questo portale consente di creare e configurare alcune risorse di Azure. In futuro sarà possibile creare e configurare tutte le risorse di Azure in questo portale. Per alcune risorse che supportano entrambi i modelli di distribuzione, è possibile usare questo portale per creare e configurare una risorsa con uno dei due modelli di distribuzione. 

Alcune risorse e funzionalità possono essere create e configurate solo in un portale o nell'altro. Non è ancora possibile creare o configurare alcune risorse o funzionalità in entrambi i portali, ma solo con PowerShell, l'interfaccia della riga di comando o entrambi. La documentazione per ciascuna risorsa di Azure descrive nel dettaglio con quale metodo può essere creata.

## PowerShell
Con [PowerShell](powershell-install-configure.md) è possibile usare una riga di comando o creare script per creare e configurare le risorse di Azure da un computer Windows. Le singole risorse di Azure contengono [cmdlet di Gestione risorse](https://msdn.microsoft.com/library/azure/mt125356.aspx), [cmdlet di Gestione dei servizi](https://msdn.microsoft.com/library/azure/dn708504.aspx) o entrambi. Alcune risorse e funzionalità possono essere create e/o configurate solo usando PowerShell oppure l'interfaccia della riga di comando. A seconda della risorsa, quando si usano i cmdlet PowerShell di Gestione risorse potrebbero essere disponibili due opzioni per la creazione e la configurazione delle risorse di Azure:

- **Solo cmdlet PowerShell:** è possibile creare e configurare singolarmente ciascuna risorsa di Azure usando i cmdlet per ogni risorsa. È possibile eseguire questa operazione dalla riga di comando o includendo più comandi in uno script di PowerShell archiviabile e la cui versione può essere controllata.

- **Cmdlet PowerShell con un modello di Gestione risorse di Azure:** è possibile usare PowerShell per creare risorse di Azure con un modello di Gestione risorse di Azure. È possibile salvare i modelli e controllarne la versione. Per altre informazioni leggere l'articolo [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md). Esistono anche diversi [modelli di Guida introduttiva](https://azure.microsoft.com/documentation/templates/) per soluzioni comuni che è possibile scaricare e modificare.

## CLI
È possibile creare e configurare le risorse di Azure da computer Windows, OS X o Linux usando l'interfaccia della riga di comando. Per installare l'interfaccia della riga di comando nel sistema operativo prescelto, leggere l'articolo [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md). Come PowerShell, sono disponibili diversi comandi che devono essere usati a seconda che si creino risorse con i modelli di distribuzione [Gestione risorse](xplat-cli-azure-resource-manager.md) o [Classico (Gestione dei servizi)](virtual-machines-command-line-tools.md).

## Passaggi successivi

- Per altre informazioni, vedere [Gestione risorse](/resource-group-overview.md).
- Informazioni su come [progettare i modelli](/best-practices-resource-manager-design-templates.md).
- Usare le [procedure consigliate](/best-practices-resource-manager-examples.md)

<!---HONumber=AcomDC_0128_2016-->
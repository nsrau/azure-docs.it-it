<properties
	pageTitle="Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure"
	description="Questo articolo illustra la gestione delle funzionalità del servizio di migrazione supportate dalla piattaforma in Azure Resource Manager con l'interfaccia della riga di comando di Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure

Questi passaggi mostrano come usare i comandi dell'interfaccia della riga di comando di Azure per eseguire la migrazione di risorse IaaS dal modello classico al modello di Resource Manager. L'articolo richiede l'[Interfaccia della riga di comando di Azure](../xplat-cli-install.md) connessa (`azure login`).

## Passaggio 1: Preparazione alla migrazione

Ecco alcune procedure consigliate per valutare la migrazione delle risorse IaaS dal modello classico al modello di Resource Manager

- L'elenco delle configurazioni o funzionalità non supportate è disponibile [qui](virtual-machines-windows-migration-classic-resource-manager.md). Se sono disponibili macchine virtuali che usano configurazioni o funzionalità non supportate, è consigliabile attendere l'annuncio del supporto di tali configurazioni o funzionalità. In alternativa, è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione se ciò soddisfa le esigenze.
-	Se si hanno script automatizzati che consentono di distribuire attualmente l'infrastruttura e le applicazioni. Provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.
- Poiché il servizio è in anteprima pubblica, assicurarsi che l'ambiente di test per la migrazione sia isolato dall'ambiente di produzione. Non combinare account di archiviazione, reti virtuali o altre risorse tra gli ambienti di test e di produzione.

## Passaggio 2: Impostare la sottoscrizione e iscriversi per l'anteprima pubblica della migrazione

Per gli scenari di migrazione è necessario configurare l'ambiente per il modello classico e di Resource Manager. [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e [selezionare la sottoscrizione](../xplat-cli-connect.md).

Selezionare la sottoscrizione di Azure usando il comando seguente

	azure account set "azure-subscription-name"

Effettuare l'iscrizione per l'anteprima pubblica con il comando seguente. Si noti che in alcuni casi si verifica il timeout del comando, tuttavia, la registrazione verrà completata. Seguire il passaggio successivo per controllare lo stato della registrazione.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Attendere 5 minuti che la registrazione venga completata. È possibile controllare lo stato dell'approvazione con il comando seguente. Prima di procedere, assicurarsi che RegistrationState sia 'Registrato'.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Passare ora dall'interfaccia della riga di comando alla modalità ASM

	azure config mode asm

## Passaggio 3: Comandi per eseguire la migrazione delle risorse IaaS

>[AZURE.NOTE] Si noti che tutte le operazioni illustrate di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma proverà di nuovo a eseguire l'azione.

### Migrazione di macchine virtuali in un servizio Cloud, non in una rete virtuale

Ottenere l'elenco dei servizi cloud con il comando seguente e selezionare il servizio cloud di cui si vuole eseguire la migrazione. Si noti che se le macchine virtuali nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web/di lavoro, verrà visualizzato un messaggio di errore.

	azure service list

Eseguire il comando seguente per ottenere il nome della distribuzione per il servizio cloud dall'output dettagliato. Nella maggior parte dei casi, il nome della distribuzione corrisponde al nome del servizio cloud.

	azure service show servicename -vv

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

Se si vuole eseguire la migrazione delle macchine virtuali a una rete virtuale creata dalla piattaforma, usare il comando seguente.

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Se si vuole eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Resource Manager, usare il comando seguente.

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

Dopo aver completato l'operazione di preparazione, è possibile osservare l'output dettagliato per ottenere lo stato di migrazione della VM e assicurarsi che lo stato delle VM sia 'Preparato'.

	azure vm show vmName -vv

Verificare le risorse preparate nella configurazione tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	azure service deployment abort-migration serviceName deploymentName

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	azure service deployment commit-migration serviceName deploymentName

### Migrazione di macchine virtuali in una rete virtuale

Selezionare la rete virtuale per cui si vuole eseguire la migrazione. Si noti che se la rete virtuale contiene ruoli Web/di lavoro o macchine virtuali con configurazioni non supportate, verrà visualizzato un messaggio di errore di convalida.

Ottenere tutte le reti virtuali nella sottoscrizione con il comando seguente.

	azure network vnet list

Preparare la rete virtuale preferita per la migrazione con il comando seguente.

	azure network vnet prepare-migration virtualnetworkname

Verificare le macchine virtuali preparate nella configurazione tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	azure network vnet abort-migration virtualnetworkname

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	azure network vnet commit-migration virtualnetworkname

## Riferimenti

- [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0511_2016-->
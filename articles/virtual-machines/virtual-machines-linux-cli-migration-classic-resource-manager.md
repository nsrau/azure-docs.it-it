<properties
	pageTitle="Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure | Microsoft Azure"
	description="Questo articolo illustra la migrazione supportata dalla piattaforma di risorse dal modello classico al modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="cynthn"/>

# Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure

Questi passaggi mostrano come usare i comandi dell'interfaccia della riga di comando di Azure per eseguire la migrazione dalle risorse IaaS (infrastruttura distribuita come servizio) dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager. L'articolo richiede l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

>[AZURE.NOTE] Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma proverà di nuovo a eseguire l'azione.

## Passaggio 1: Preparare la migrazione

Ecco alcune procedure consigliate per valutare la migrazione delle risorse IaaS dal modello classico al modello di Resource Manager:

- Leggere [l'elenco di configurazioni e funzionalità non supportate](virtual-machines-windows-migration-classic-resource-manager.md). Se sono disponibili macchine virtuali che usano configurazioni o funzionalità non supportate, è consigliabile attendere l'annuncio del supporto di tali configurazioni o funzionalità. In alternativa, è possibile rimuovere tale funzionalità o uscire da tale configurazione per abilitare la migrazione se ciò soddisfa le esigenze.
-	Se si hanno script automatizzati che consentono di distribuire subito l'infrastruttura e le applicazioni, provare a creare una configurazione di test simile usando questi script per la migrazione. In alternativa, è anche possibile configurare ambienti di esempio tramite il portale di Azure.

## Passaggio 2: Impostare la sottoscrizione e registrare il provider

Per gli scenari di migrazione è necessario configurare l'ambiente per il modello classico e di Resource Manager. [Installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e [selezionare la sottoscrizione](../xplat-cli-connect.md).

Accedere al proprio account.
	
	azure login

Selezionare la sottoscrizione di Azure usando il comando seguente.

	azure account set "<azure-subscription-name>"

>[AZURE.NOTE] La registrazione è un passaggio da eseguire un'unica volta, tuttavia deve essere eseguita prima di tentare la migrazione. Senza la registrazione verrà visualizzato il seguente messaggio di errore

>	*BadRequest : Subscription is not registered for migration.* 

Registrarsi con il provider di risorse di migrazione utilizzando il comando seguente. Si noti che in alcuni casi si verifica il timeout del comando. Tuttavia, la registrazione verrà completata.

	azure provider register Microsoft.ClassicInfrastructureMigrate

Attendere cinque minuti che la registrazione venga completata. È possibile controllare lo stato dell'approvazione con il comando seguente. Assicurarsi che RegistrationState sia `Registered` prima di procedere.

	azure provider show Microsoft.ClassicInfrastructureMigrate

Passare ora dall'interfaccia della riga di comando alla modalità `asm`.

	azure config mode asm


## Passaggio 3: Opzione 1 - Eseguire la migrazione delle macchine virtuali a un servizio cloud 

Ottenere l'elenco dei servizi cloud con il comando seguente e selezionare il servizio cloud di cui si vuole eseguire la migrazione. Si noti che se le VM nel servizio cloud si trovano in una rete virtuale o hanno ruoli Web/di lavoro, verrà visualizzato un messaggio di errore.

	azure service list

Eseguire il comando seguente per ottenere il nome della distribuzione per il servizio cloud dall'output dettagliato. Nella maggior parte dei casi, il nome della distribuzione corrisponde al nome del servizio cloud.

	azure service show <serviceName> -vv

Preparare le macchine virtuali nel servizio cloud per la migrazione. È possibile scegliere tra due opzioni.

Se si vuole eseguire la migrazione delle VM a una rete virtuale creata dalla piattaforma, usare il comando seguente.

	azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se si vuole eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Resource Manager, usare il comando seguente.

	azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Dopo aver completato l'operazione di preparazione, è possibile osservare l'output dettagliato per ottenere lo stato di migrazione delle VM, verificando che sia `Prepared`.

	azure vm show <vmName> -vv

Controllare la configurazione per le risorse preparate tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	azure service deployment abort-migration <serviceName> <deploymentName>

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	azure service deployment commit-migration <serviceName> <deploymentName>


	
## Passaggio 3: Opzione 2 - Eseguire la migrazione delle macchine virtuali a una rete virtuale

Selezionare la rete virtuale per cui si vuole eseguire la migrazione. Si noti che se la rete virtuale contiene ruoli Web/di lavoro o VM con configurazioni non supportate, verrà visualizzato un messaggio di errore di convalida.

Ottenere tutte le reti virtuali nella sottoscrizione con il comando seguente.

	azure network vnet list
	
Verrà visualizzato un risultato simile al seguente:

![Schermata della riga di comando con evidenziato l'intero nome della rete virtuale.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Nell'esempio precedente, il valore **virtualNetworkName** è l'intero nome **"Gruppo classicubuntu16 classicubuntu16"**.

Preparare la rete virtuale scelta per la migrazione con il comando seguente.

	azure network vnet prepare-migration <virtualNetworkName>

Controllare la configurazione per le macchine virtuali preparate tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	azure network vnet abort-migration <virtualNetworkName>

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	azure network vnet commit-migration <virtualNetworkName>

## Passaggio 4: Eseguire la migrazione di un account di archiviazione

Dopo aver completato la migrazione delle macchine virtuali, si consiglia di migrare l'account di archiviazione.

Preparare l'account di archiviazione per la migrazione con il comando seguente.

	azure storage account prepare-migration <storageAccountName>

Controllare la configurazione per l'account di archiviazione preparato tramite l'interfaccia della riga di comando o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente.

	azure storage account abort-migration <storageAccountName>

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente.

	azure storage account commit-migration <storageAccountName>

## Passaggi successivi

- [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello classico al modello di Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0720_2016-->
<properties
   pageTitle="Gestione delle macchine virtuali di Azure utilizzando l’interfaccia della riga di comando | Microsoft Azure"
   description="Descrive come gestire le macchine virtuali di Azure tramite la CLI di Azure per Mac, Linux e Windows."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Gestire le macchine virtuali tramite la CLI di Azure per Mac, Linux e Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la gestione di una risorsa creata con il modello di distribuzione classica. È inoltre possibile gestire una risorsa creata con il [modello di distribuzione di Gestione risorse](virtual-machines-how-to-automate-azure-resource-manager.md).

Molte attività che è possibile eseguire ogni giorno per gestire le macchine virtuali possono essere automatizzate da tramite la CLI di Azure. In questo articolo offre esempi di comandi per le attività più semplici e collegamenti ad articoli in cui visualizzare i comandi per attività più complesse.

>[AZURE.NOTE]Se non ancora stato installato e configurato Azure PowerShell, è possibile ottenere istruzioni [qui](../xplat-cli-install.md). Se si desidera una Guida introduttiva per le stesse attività in PowerShell, vedere [gestire le macchine virtuali con Azure PowerShell](virtual-machines-manage-vms-powershell.md).

## Come utilizzare i comandi di esempio
È necessario sostituire una parte del testo nei comandi con il testo appropriato per l'ambiente. Il < and > simboli indicano è necessario sostituire il testo. Quando si sostituisce il testo, rimuovere i simboli ma mantenere le virgolette.

> [AZURE.NOTE]Se si desidera archiviare e modificare l'output dei comandi della console a livello di programmazione, è possibile utilizzare un formato JSON come strumento di analisi, tra cui **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** o librerie di linguaggi valido per l'attività.

## Visualizzare informazioni su una macchina virtuale

Si tratta di un'attività di base che si utilizzerà spesso. È possibile utilizzarla per ottenere informazioni su una macchina virtuale, eseguire le attività in una macchina virtuale o recuperare l'output da archiviare in una variabile.

Per ottenere informazioni sulla macchina virtuale, eseguire questo comando, sostituendo tutto ciò che è racchiuso tra virgolette, inclusi i caratteri < and >:

     azure vm show -g <group name> -n <virtual machine name>

Per archiviare l'output in una variabile $vm come un documento JSON, eseguire:

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)

In alternativa, è possibile reindirizzare stdout in un file.

## Accedere a una macchina virtuale basata su Linux

In genere macchine Linux sono connesse tramite SSH. Per altre informazioni, vedere la pagina relativa all'[Uso di SSH con Linux in Azure](virtual-machines-linux-use-ssh-key.md). Panoramica di Gestione risorse di Microsoft Azure
## Arrestare una macchina virtuale

Eseguire questo comando:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]Utilizzare questo parametro per mantenere l'IP virtuale (VIP) del servizio cloud, qualora fosse l'ultima macchina virtuale inclusa nel servizio cloud specifico. <br><br> Se si utilizza il parametro StayProvisioned, sarà ancora configurato per la macchina virtuale.

## Avviare una macchina virtuale

Panoramica su Gestione risorse di Azure. Eseguire questo comando: azure vm start <group name> <virtual machine name>

## Collegamento di un disco dati

È inoltre necessario decidere se collegare un nuovo disco o uno che contiene già dati. Per un nuovo disco, il comando permette di creare il file con estensione VHD e contemporaneamente di collegarlo.

Per collegare un nuovo disco, eseguire questo comando:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Per collegare un disco dati esistente, eseguire questo comando:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## Creare una macchina virtuale Linux

Per creare una nuova macchina virtuale basata su Linux, sarà necessario disporre di diversi valori d' parte, tra cui nome di un gruppo di risorse, un percorso, un nome di immagine, un nome di macchina virtuale e un account di archiviazione per memorizzare l'immagine VHD di backup. Dopo aver creato le informazioni che si desidera utilizzare, Azure CLI può creare una sessione interattiva in modo da richiedere tali valori digitando:

    azure vm create

Naturalmente, se si dispone già di tali valori è possibile trovare le opzioni appropriate per passarli direttamente digitando `azure help vm create`.

## Passaggi successivi

Per ulteriori esempi di utilizzo dell'interfaccia della riga di comando di Azure in modalità **arm**, vedere il documento relativo all'[utilizzo dell'interfaccia della riga di comando di Microsoft Azure per Mac, Linux e Windows con Gestione risorse di Microsoft Azure.](../xplat-cli-resource-manager.md). Per ulteriori informazioni sulle risorse di Azure e i relativi concetti, vedere [Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md).

<!---HONumber=Sept15_HO4-->
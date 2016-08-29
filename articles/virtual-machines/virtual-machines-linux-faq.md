<properties
	pageTitle="Domande frequenti per le VM Linux | Microsoft Azure"
	description="Fornisce le risposte ad alcune delle domande comuni sulle macchine virtuali Linux create con un modello di Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>  

# Domande frequenti sulle macchine virtuali Linux 


Questo articolo analizza alcune delle domande più comuni sulle macchine virtuali Linux create in Azure mediante il modello di distribuzione Resource Manager. Per la versione di Windows di questo argomento, vedere [Domande frequenti sulle macchine virtuali Windows](virtual-machines-windows-faq.md)

## Cosa è possibile eseguire in una VM di Azure?

Tutti i sottoscrittori possono eseguire software del server in una macchina virtuale Azure. Per altre informazioni, vedere [Distribuzioni di Linux supportate da Azure](virtual-machines-linux-endorsed-distros.md)


## Quanta memoria è possibile utilizzare con una macchina virtuale?

Ogni disco dati può essere fino a 1 TB. Il numero di dischi dati che è possibile utilizzare dipende dalla dimensione della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).

Un account di archiviazione Azure fornisce memoria per il disco del sistema operativo e per qualsiasi disco dati. Ogni disco è un file con estensione vhd archiviato come BLOB di pagine. Per informazioni sui prezzi, vedere [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/).



## Come si accede alla macchina virtuale?

È necessario stabilire una connessione remota per accedere alla macchina virtuale tramite Secure Shell (SSH). Vedere le istruzioni su come connettersi [da Windows](virtual-machines-linux-ssh-from-windows.md) o [da Linux e Mac](virtual-machines-linux-ssh-from-linux.md). Per impostazione predefinita, la SSH consente un massimo di 10 connessioni simultanee. È possibile aumentare questo numero modificando il file di configurazione.


Se si verificano problemi, vedere l'argomento [Risolvere i problemi relativi alle connessioni Secure Shell (SSH)](virtual-machines-linux-troubleshoot-ssh-connection.md).

## È possibile usare il disco temporaneo (/dev/sdb1) per archiviare i dati?

Non usare il disco temporaneo (/dev/sdb1) per archiviare i dati. Si tratta solo di memorie temporanee e si rischierebbe di perdere dati che non possono essere recuperati.

## È possibile copiare o clonare una VM di Azure esistente?

Sì. Per le istruzioni, vedere [How to create a copy of a Linux virtual machine in the Resource Manager deployment model](virtual-machines-linux-copy-vm.md) (Creare una copia di una macchina virtuale Linux nel modello di distribuzione di Resource Manager).

## Perché non si vedono le aree del Canada centrale e del Canada orientale tramite Azure Resource Manager?

Le due nuove aree del Canada centrale e del Canada orientale non vengono registrate automaticamente per la creazione della macchina virtuale per le sottoscrizioni di Azure esistenti. La registrazione viene eseguita automaticamente quando si distribuisce una macchina virtuale tramite il portale di Azure in qualsiasi altra area di Azure Resource Manager. Dopo aver distribuito una macchina virtuale in qualsiasi altra area di Azure le nuove aree dovrebbero essere disponibili per le macchine virtuali successive.

## È possibile aggiungere un NIC alla VM dopo la sua creazione?

No. L'aggiunta di una scheda di rete può essere effettuata solo al momento della creazione.

<!---HONumber=AcomDC_0817_2016-->
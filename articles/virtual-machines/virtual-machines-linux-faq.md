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

Stabilire una connessione remota per accedere alla macchina virtuale tramite Secure Shell (SSH). Vedere le istruzioni su come connettersi [da Windows](virtual-machines-linux-ssh-from-windows.md) o [da Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md). Per impostazione predefinita, la SSH consente un massimo di 10 connessioni simultanee. È possibile aumentare questo numero modificando il file di configurazione.


Se si verificano problemi, vedere l'argomento [Risolvere i problemi relativi alle connessioni Secure Shell (SSH)](virtual-machines-linux-troubleshoot-ssh-connection.md).


## È possibile usare il disco temporaneo (/dev/sdb1) per archiviare i dati?

Non usare il disco temporaneo (/dev/sdb1) per archiviare i dati. Serve solo per l'archiviazione temporanea. I dati non ripristinabili rischiano di andare persi.


## È possibile copiare o clonare una VM di Azure esistente?

Sì. Per le istruzioni, vedere [How to create a copy of a Linux virtual machine in the Resource Manager deployment model](virtual-machines-linux-copy-vm.md) (Creare una copia di una macchina virtuale Linux nel modello di distribuzione di Resource Manager).


## Perché non si vedono le aree del Canada centrale e del Canada orientale tramite Azure Resource Manager?

Le due nuove aree del Canada centrale e del Canada orientale non vengono registrate automaticamente per la creazione della macchina virtuale per le sottoscrizioni di Azure esistenti. La registrazione viene eseguita automaticamente quando si distribuisce una macchina virtuale tramite il portale di Azure in qualsiasi altra area di Azure Resource Manager. Dopo aver distribuito una macchina virtuale in qualsiasi altra area di Azure le nuove aree dovrebbero essere disponibili per le macchine virtuali successive.


## È possibile aggiungere un NIC alla VM dopo la sua creazione?

No. L'aggiunta di una scheda di rete può essere effettuata solo al momento della creazione.


## Esistono requisiti relativi al nome del computer?

Sì. Il nome del computer non può contenere più di 64 caratteri. Vedere [Linee guida sulle convenzioni di denominazione dell'infrastruttura](virtual-machines-linux-infrastructure-naming-guidelines.md) per ulteriori informazioni sulla denominazione delle risorse.


## Quali requisiti devono avere i nomi utente durante la creazione di una VM?

I nomi utente devono contenere da 1 a 64 caratteri.

I nomi utente seguenti non sono consentiti:

<table>
	<tr>
		<td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
	</tr>
	<tr>
		<td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
	</tr>
	<tr>
		<td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
	</tr>
	<tr>
		<td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
	</tr>
	<tr>
		<td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
	</tr>
	<tr>
		<td style="text-align:center">john </td><td style="text-align:center"> proprietario </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
	</tr>
	<tr>
		<td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
	</tr>
	<tr>
		<td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
	</tr>
</table>


## Quali requisiti devono avere le password durante la creazione di una VM?

Le password devono contenere da 6 a 72 caratteri e soddisfare 3 dei seguenti 4 requisiti di complessità:

- Devono contenere caratteri minuscoli
- Devono contenere caratteri maiuscoli
- Devono contenere almeno un numero
- Devono contenere un carattere speciale (REGEX.CONFRONTA [\\W\_])

Le password seguenti non sono consentite:

<table>
	<tr>
		<td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$$word</td>
	</tr>
	<tr>
		<td style="text-align:center">pass@word1</td><td style="text-align:center">Password!</td><td style="text-align:center">Password1</td><td style="text-align:center">Password22</td><td style="text-align:center">iloveyou!</td>
	</tr>
</table>

<!---HONumber=AcomDC_0831_2016-->
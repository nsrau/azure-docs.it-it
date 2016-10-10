<properties
	pageTitle="Domande frequenti per le VM Windows | Microsoft Azure"
	description="Offre le risposte ad alcune delle domande comuni sulle macchine virtuali Windows create con un modello di Gestione risorse."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Domande frequenti sulle Macchine virtuali Windows 


Questo articolo analizza alcune delle domande più comuni sulle macchine virtuali Windows create in Azure mediante il modello di distribuzione di Gestione risorse. Per la versione di Linux di questo argomento, vedere [Domande frequenti sulle Macchine virtuali Linux](virtual-machines-linux-faq.md)

## Cosa è possibile eseguire in una VM di Azure?

Tutti i sottoscrittori possono eseguire software del server in una macchina virtuale Azure. Per informazioni sui criteri di supporto per l'esecuzione del software server Microsoft in Azure, vedere [Supporto di software server Microsoft per le macchine virtuali Microsoft Azure](https://support.microsoft.com/kb/2721672)

Alcune versioni di Windows 7 e Windows 8.1 sono disponibili agli abbonati MSDN Azure con benefici e agli abbonati MSDN sviluppo e test con pagamento in base al consumo, per attività di sviluppo e test. Per ulteriori informazioni, incluse le istruzioni e limitazioni, vedere [Immagini Client Windows per gli abbonati MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).


## Quanta memoria è possibile utilizzare con una macchina virtuale?

Ogni disco dati può essere fino a 1 TB. Il numero di dischi dati che è possibile utilizzare dipende dalla dimensione della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-windows-sizes.md).

Un account di archiviazione Azure fornisce memoria per il disco del sistema operativo e per qualsiasi disco dati. Ogni disco è un file con estensione vhd archiviato come BLOB di pagine. Per informazioni sui prezzi, vedere [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/).


## Come si accede alla macchina virtuale?

Stabilire una connessione remota mediante la Connessione desktop remoto (RDP) per una VM Windows. Per istruzioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](virtual-machines-windows-connect-logon.md). Sono supportate al massimo due connessioni simultanee, a meno che il server non sia configurato come host sessione Servizi Desktop remoto.


In caso di problemi con il Desktop remoto, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Se si ha familiarità con Hyper-V, è possibile che si stia cercando uno strumento simile a VMConnect. Azure non offre uno strumento simile, poiché l'accesso da console a una macchina virtuale non è supportato.

## È possibile utilizzare il disco temporaneo (l'unità D: per impostazione predefinita) per archiviare i dati?

Non usare il disco temporaneo per archiviare i dati. Si tratta solo di memorie temporanee, pertanto si rischierebbe di perdere dati che non possono essere recuperati. La perdita di dati può verificarsi quando si sposta la macchina virtuale in un host diverso. Il ridimensionamento di una macchina virtuale, l'aggiornamento dell'host o un errore hardware nell'host sono alcuni dei motivi che potrebbero portare a spostare una macchina virtuale.

Se si dispone di un'applicazione che deve utilizzare la lettera di unità D:, è possibile riassegnare le lettere di unità in modo che il disco temporaneo ne usi una diversa da D:. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md).

## Come modificare la lettera di unità del disco temporaneo?

È possibile modificare la lettera di unità spostando il file di paging e riassegnando le lettere di unità, ma è necessario assicurarsi di che eseguire i passaggi in un ordine specifico. Per le istruzioni, vedere [Modificare la lettera di unità di un disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md).

## È possibile aggiungere una VM esistente a un set di disponibilità?

No. Se si desidera che la VM faccia parte di un set di disponibilità, è necessario creare la VM all'interno del set. Attualmente non è possibile aggiungere una VM a un set di disponibilità dopo la sua creazione.

## È possibile caricare una macchina virtuale in Azure?

Sì. Per istruzioni, vedere [Caricare l'immagine di una VM di Windows in Microsoft Azure](virtual-machines-windows-upload-image.md)

## È possibile ridimensionare il disco del sistema operativo?

Sì. Per istruzioni, vedere [Come espandere l'unità del sistema operativo di una macchina virtuale in un gruppo di risorse di Azure](virtual-machines-windows-expand-os-disk.md).

## È possibile copiare o clonare una VM di Azure esistente?

Sì. Per istruzioni, vedere [Creare una copia di una macchina virtuale Windows nel modello di distribuzione di Gestione risorse](virtual-machines-windows-specialized-image.md).

## Perché non si vedono le aree del Canada centrale e del Canada orientale tramite Gestione risorse di Azure?

Le due nuove aree del Canada centrale e del Canada orientale non vengono registrate automaticamente per la creazione della macchina virtuale per le sottoscrizioni di Azure esistenti. La registrazione viene eseguita automaticamente quando si distribuisce una macchina virtuale tramite il portale di Azure in qualsiasi altra area di Azure Resource Manager. Dopo aver distribuito una macchina virtuale in qualsiasi altra area di Azure le nuove aree dovrebbero essere disponibili per le macchine virtuali successive.

## Le VM di Linux sono supportate da Azure?

Sì. Per creare rapidamente una VM Linux di prova, vedere [Creare una VM Linux in Azure usando il portale](virtual-machines-linux-quick-create-portal.md).

## È possibile aggiungere un NIC alla VM dopo la sua creazione?

No. L'aggiunta di una scheda di rete può essere effettuata solo al momento della creazione.

## Esistono requisiti relativi al nome del computer?

Sì. Il nome del computer non può contenere più di 15 caratteri. Vedere [Linee guida sulle convenzioni di denominazione dell'infrastruttura](virtual-machines-windows-infrastructure-naming-guidelines.md) per ulteriori informazioni sulla denominazione delle risorse.

## Quali requisiti devono avere i nomi utente durante la creazione di una VM?

I nomi utente possono contenere un massimo di 20 caratteri e non possono terminare con un punto (".").

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

Le password devono contenere da 8 a 123 caratteri e soddisfare 3 dei seguenti 4 requisiti di complessità:

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

<!---HONumber=AcomDC_0928_2016-->

<properties
		pageTitle="Monitoraggio di una macchina virtuale Linux con un'estensione VM | Microsoft Azure"
		description="Informazioni su come utilizzare l'estensione di diagnostica Linux per monitorare le prestazioni e i dati di diagnostica di una macchina virtuale Linux in Azure."
		services="virtual-machines-linux"
		documentationCenter=""
  		authors="NingKuang"
		manager="timlt"
		editor=""
  		tags="azure-service-management"/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="12/15/2015"
		ms.author="Ning"/>


# Uso dell'estensione di diagnostica Linux per monitorare le prestazioni e i dati di diagnostica di una macchina virtuale Linux

## Introduzione

L'estensione di diagnostica Linux consente all'utente di monitorare le macchine virtuali Linux eseguite in Microsoft Azure, con le funzionalità seguenti:

- Raccoglie e carica i dati relativi alla prestazioni del sistema delle macchine virtuali Linux, alla diagnostica e ai syslog nella tabella di archiviazione dell'utente.
- Consente all'utente di personalizzare la metrica dei dati che verrà raccolta e caricata.
- Consente all'utente di caricare nella tabella di archiviazione designata i file di log specificati.

Per la versione 2.0, i dati includono:

- Tutte le registrazioni Rsyslog Linux, inclusi i log di sistema, sicurezza e applicazioni.
- Tutti i dati di sistema specificati in questo [documento](https://scx.codeplex.com/wikipage?title=xplatproviders").
- I file di log specificati dall'utente.

Si noti che questa estensione funziona sia con i modelli classici che con i modelli di distribuzione Resource Manager.


## Come abilitare l'estensione
L'estensione può essere abilitata tramite il [portale di Azure](https://ms.portal.azure.com/#), Azure PowerShell o gli script dell'interfaccia della riga di comando di Azure.

Per visualizzare e configurare i dati di sistema e prestazioni direttamente dal portale di Azure, seguire questi [passaggi](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "URL del blog di Windows"/).


Questo articolo è incentrato sull'abilitazione e la configurazione dell'estensione tramite i comandi dell'interfaccia della riga di comando di Azure. In questo modo è possibile leggere e visualizzare i dati direttamente dalla tabella di archiviazione.


## Prerequisiti
- Agente Linux di Microsoft Azure 2.0.6 o versioni successive. Si noti che la maggior parte delle immagini della raccolta Linux di macchine virtuali di Azure include la versione 2.0.6 o successive. È possibile eseguire **WAAgent -version** per verificare la versione installata nella macchina virtuale. Se la macchina virtuale esegue una versione precedente alla 2.0.6, è possibile seguire queste [istruzioni](https://github.com/Azure/WALinuxAgent "istruzioni") per aggiornarla.
- [Interfaccia della riga di comando di Azure](./xplat-cli-install.md). Seguire [queste linee guida](./xplat-cli-install.md) per configurare l'ambiente dell'interfaccia della riga di comando di Azure nella macchina virtuale. Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile utilizzare il comando **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) per accedere ai relativi comandi. Ad esempio, **set estensioni macchina virtuale di azure --guida** per informazioni dettagliate sull’utilizzo, **accesso azure** per accedere ad Azure, eseguire **elenco macchine virtuali di azure ** per elencare tutte le macchine virtuali presenti in Azure.
- Un account di archiviazione per archiviare i dati. Saranno necessari un nome di account di archiviazione e un tasto di scelta creati in precedenza per caricare i dati nella risorsa di archiviazione.


## Usare l'interfaccia della riga di comando di Azure per abilitare l'estensione della diagnostica Linux

###  Scenario 1. Abilitare l'estensione con il set di dati predefinito
Per la versione 2.0 e versioni successive, i dati predefiniti che verranno raccolti includono:

- Tutte le informazioni Rsyslog (inclusi i log di sistema, sicurezza e applicazioni).  
- Un set di dati di sistema di base. Il set di dati completo è descritto in questo [documento](https://scx.codeplex.com/wikipage?title=xplatproviders). Per abilitare dati aggiuntivi, proseguire con i passaggi 2 e 3 dello scenario.

Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto seguente:

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account"
	}

Passaggio 2. Eseguire **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Scenario 2. Personalizzare la metrica di monitoraggio delle prestazioni  
In questa sezione viene descritto come personalizzare la tabella dati delle prestazioni e della diagnostica.

Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto visualizzato nell'esempio seguente. Specificare i dati specifici che si desidera raccogliere.

Per tutti i provider e le variabili supportati, fare riferimento a questo [documento](https://scx.codeplex.com/wikipage?title=xplatproviders). È possibile disporre di più query e archiviarle in più tabelle aggiungendo altre query nello script.

Per impostazione predefinita i dati Rsyslog verranno sempre raccolti.

	{
     	"storageAccountName":"storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"perfCfg":[
           	{"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
           	}
          ]
	}


Passaggio 2. Eseguire **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Scenario 3. Caricamento dei propri file di log
In questa sezione viene descritto come raccogliere e caricare file di log specifici all'account di archiviazione. È necessario specificare il percorso del file di log e il nome della tabella in cui archiviare il log. È possibile disporre di più file di log aggiungendo più voci di file/tabella nello script.

Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto seguente:

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"fileCfg":[
           	{"file":"/var/log/mysql.err",
             "table":"mysqlerr"
           	}
          ]
	}


Passaggio 2. Eseguire **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Scenario 4. Disabilitare l'estensione di monitoraggio Linux
Passaggio 1. Creare un file denominato PrivateConfig.json con il contenuto seguente:

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account",
     	“perfCfg”:[],
     	“enableSyslog”:”False”
	}


Passaggio 2. Eseguire **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


## Esaminare i dati
I dati delle prestazioni e della diagnostica vengono archiviati in una tabella di archiviazione di Azure. Vedere [questo articolo](storage-ruby-how-to-use-table-storage.md) per informazioni su come accedere ai dati nella tabella di archiviazione utilizzando gli script dell’interfaccia della riga di comando di Azure.

È anche possibile usare gli strumenti dell'interfaccia utente seguenti per accedere ai dati:

1.	Utilizzare Esplora server di Visual Studio. Passare all'account di archiviazione. Dopo circa 5 minuti dall'esecuzione della macchina virtuale si dovrebbero vedere le quattro tabelle predefinite: "LinuxCpu", "LinuxDisk", "LinuxMemory" e "Linuxsyslog". Fare doppio clic sul nome della tabella per visualizzare i dati.
2.	Usare [Esplora archivi Azure](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer") per accedere ai dati.

![immagine](./media/virtual-machines-linux-classic-diagnostic-extension/no1.png)

Se è stato abilitato fileCfg o perfCfg, specificati negli scenari 2 e 3, sarà anche possibile visualizzare dati non predefiniti con gli strumenti indicati precedentemente.



## Problemi noti
- Per la versione 2.0, le informazioni Rsyslog e il file di log specificato dal cliente sono accessibili solo tramite scripting.

<!---HONumber=AcomDC_0323_2016-->
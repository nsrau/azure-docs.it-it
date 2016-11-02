<properties
	pageTitle="La connessione SSH a una VM Linux è stata rifiutata, ha esito negativo o genera errori | Microsoft Azure"
	description="Risolvere i problemi e correggere gli errori SSH, ad esempio una connessione SSH non riuscita o rifiutata per una macchina virtuale di Azure che esegue Linux."
	keywords="connessione SSH rifiutata, errore SSH, SSH Azure, connessione SSH non riuscita"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure che ha esito negativo, è stata rifiutata o genera errori

Sono vari i motivi per cui potrebbero verificarsi errori Secure Shell (SSH), la connessione SSH non riesce o viene rifiutata durante il tentativo di connessione a una macchina virtuale di Azure basata su Linux. Questo articolo consente di individuare i problemi e correggerli.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](http://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](http://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](http://azure.microsoft.com/support/faq/).

## VM create con il modello di distribuzione di Resource Manager

Per reimpostare le credenziali o il disco SSHD, è possibile utilizzare i comandi dell'interfaccia della riga di comando di Azure direttamente o tramite l'[estensione VMAccessForLinux di Azure](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). Dopo ogni passaggio della procedura di risoluzione dei problemi, ritentare di connettersi alla VM.

### Prerequisiti dell'interfaccia della riga di comando di Azure

Se necessario, [installare l'interfaccia della riga di comando di Azure e connettersi alla sottoscrizione di Azure](../xplat-cli-install.md). Effettuare l'accesso usando il comando `azure login` e controllare di essere in modalità Resource Manager (`azure config mode arm`).

Verificare di aver installato l'[agente Linux di Microsoft Azure](virtual-machines-linux-agent-user-guide.md) 2.0.5 o versione successiva.

### Reimpostare un disco SSHD
La configurazione del disco SSHD in sé può essere errata oppure il servizio ha rilevato un errore. È possibile reimpostare il disco SSHD per controllare la validità della configurazione SSH.

#### Interfaccia della riga di comando di Azure
```bash
azure vm reset-access -g <resource group> -n <vm name> -r
```

#### Estensione dell'accesso alle VM
Estensioni dell'accesso lette in un file json che definisce l'azione da eseguire, ad esempio la reimpostazione SSH, la reimpostazione di una chiave SSH, l'aggiunta di un nuovo utente e così via. Per prima cosa, creare un file denominato PrivateConf.json con il contenuto seguente:

```bash
{  
	"reset_ssh":"True"
}
```

Eseguire quindi manualmente l'estensione `VMAccessForLinux` per reimpostare la connessione del disco SSHD:

```bash
azure vm extension set <resource group> <vm name> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Reimpostare le credenziali SSH di un utente
Se il disco SSHD funziona correttamente, è possibile reimpostare la password di un determinato utente.

#### Interfaccia della riga di comando di Azure
```bash
azure vm reset-access -g <resource group> <vm name> -u <username> -p <new password>
```

Se si utilizza l'autenticazione con chiave SSH, è possibile reimpostare la chiave SSH per un determinato utente:

```bash
azure vm reset-access -g <resource group> -n <vm name> -u <username> -M <~/.ssh/azure_id_rsa.pub>
```

#### Estensione dell'accesso alle VM
Creare un file denominato PrivateConf.json con il contenuto seguente:

```bash
{
	"username":"Username", "password":"NewPassword"
}
```

In alternativa, per reimpostare la chiave SSH relativa a un determinato utente, creare un file denominato PrivateConf.json con il contenuto seguente:

```bash
{
	"username":"Username", "ssh_key":"ContentsOfNewSSHKey"
}
```

Al termine di uno dei passaggi precedenti, eseguire manualmente l'estensione `VMAccessForLinux` per reimpostare le credenziali SSH dell'utente:

```
azure vm extension set <resource group> <vmname> VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
```

### Ridistribuire una VM
È possibile ridistribuire una VM in un altro nodo all'interno di Azure, correggendo eventuali problemi di rete sottostanti. Per ridistribuire una VM tramite il portale di Azure, selezionare **Sfoglia** > **Macchine virtuali** > *la propria macchina virtuale Linux* > **Ridistribuisci**. Per informazioni su come eseguire questa operazione, vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md). Al momento non è possibile ridistribuire una VM tramite la l'interfaccia della riga di comando di Azure.

> [AZURE.NOTE] Si noti che al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.


## VM create con il modello di distribuzione classica

Per risolvere gli errori di connessione SSH più comuni nelle VM create con il modello di distribuzione classica, provare a eseguire questi passaggi. Dopo ogni passaggio, tentare la riconnessione alla VM.

- Reimpostare l'accesso remoto dal [portale di Azure](https://portal.azure.com). Nel portale di Azure selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Linux* > **Reimposta accesso...**.

- Riavviare la VM. Nel [portale di Azure](https://portal.azure.com) selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Linux* > **Riavvia**.

	-OPPURE-

	Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Macchine virtuali** > **Istanze** > **Riavvia**.

- Ridistribuire la VM su un nuovo nodo di Azure. Per informazioni su come eseguire questa operazione, vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Si noti che al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.

- Seguire le istruzioni in[Come reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md) per:
	- Reimpostare la password o la chiave SSH.
	- Creare un nuovo account utente _sudo_.
	- Reimpostare la configurazione SSH.

- Controllare l'integrità delle risorse della VM per eventuali problemi di piattaforma.<br> Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Linux* > **Impostazioni** > **Controlla integrità**.


## Risorse aggiuntive

- Se non si riesce ancora a eseguire la configurazione SSH sulla VM dopo aver eseguito la relativa procedura, è possibile seguire i [passaggi dettagliati per la risoluzione dei problemi](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) per esaminare ulteriori configurazioni di rete e le operazioni per risolvere il problema.

- Per ulteriori informazioni sulla risoluzione dei problemi di accesso dell'applicazione, vedere [Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-linux-troubleshoot-app-connection.md).

- Per ulteriori informazioni sulla risoluzione dei problemi di macchine virtuali create con il modello di distribuzione classica, vedere l'articolo su come [reimpostare una password o SSH per macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md).

<!---HONumber=AcomDC_0803_2016-->
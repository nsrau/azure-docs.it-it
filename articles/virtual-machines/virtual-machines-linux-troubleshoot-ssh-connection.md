<properties
	pageTitle="Risoluzione dei problemi di connessione SSH a una macchina virtuale di Azure | Microsoft Azure"
	description="Risolvere i problemi e correggere gli errori SSH, ad esempio una connessione SSH non riuscita o rifiutata per una macchina virtuale di Azure che esegue Linux."
	keywords="connessione SSH rifiutata,errore SSH,SSH Azure,connessione SSH non riuscita"
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
	ms.topic="support-article"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Risolvere i problemi relativi alle connessioni Secure Shell a una macchina virtuale di Azure basata su Linux

Sono vari i motivi per cui potrebbero verificarsi errori Secure Shell (SSH) durante il tentativo di connessione a una macchina virtuale di Azure basata su Linux. Questo articolo consente di individuare i problemi e correggerli.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](http://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](http://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](http://azure.microsoft.com/support/faq/).


## Correggere errori SSH comuni

Questa sezione elenca le correzioni rapide dei comuni problemi di connessione SSH.

### Risolvere i problemi delle macchine virtuali create con il modello di distribuzione classica

Per risolvere gli errori di connessione SSH più comuni nelle macchine virtuali create con il modello di distribuzione classica, provare a eseguire questi passaggi. Dopo ogni passaggio, provare a rieseguire la connessione alla macchina virtuale.

- Reimpostare l'accesso remoto dal [portale di Azure](https://portal.azure.com). Nel portale di Azure selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Linux* > **Reimposta accesso...**.

- Riavviare la macchina virtuale. Nel [portale di Azure](https://portal.azure.com) selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Linux* > **Riavvia**.

	-OPPURE-

	Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Macchine virtuali** > **Istanze** > **Riavvia**.

- Ridistribuire la macchina virtuale su un nuovo nodo di Azure. Per informazioni su come eseguire questa operazione, vedere [Ridistribuzione della macchina virtuale su un nuovo nodo di Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Si noti che al termine di questa operazione i dati temporanei del disco andranno persi e gli indirizzi IP dinamici associati alla macchina virtuale saranno aggiornati.

- Seguire le istruzioni in[Come reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md) per:
	- Reimpostare la password o la chiave SSH.
	- Creare un nuovo account utente _sudo_.
	- Reimpostare la configurazione SSH.

- Controllare l'integrità delle risorse della VM per eventuali problemi di piattaforma.<br> Selezionare **Sfoglia** > **Macchine virtuali (classico)** > *macchina virtuale Linux* > **Impostazioni** > **Controlla integrità**.


### Risolvere i problemi delle macchine virtuali create con il modello di distribuzione Resource Manager

Per risolvere gli errori di connessione SSH più comuni nelle macchine virtuali create con il modello di distribuzione Resource Manager, provare a eseguire questi passaggi.

#### Reimpostare la connessione SSH
Tramite l'interfaccia della riga di comando di Azure verificare che sia installato l'[agente Linux di Microsoft Azure ](virtual-machines-linux-agent-user-guide.md) versione 2.0.5 o successiva.

Se necessario, [installare l'interfaccia della riga di comando di Azure e connettersi alla sottoscrizione di Azure](../xplat-cli-install.md). Accedere con il comando `azure login`. Verificare di essere in modalità Resource Manager: ```
	azure config mode arm
	```

Reimpostare la connessione SSH con uno dei due metodi seguenti;

**Metodo 1:** usare il comando `vm reset-access` come illustrato nell’esempio seguente. L'estensione `VMAccessForLinux` verrà installata nella macchina virtuale.

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```



**Metodo 2:** creare un file denominato PrivateConf.json con il contenuto seguente:

	```
	{  
		"reset_ssh":"True"
	}
	```

Eseguire quindi manualmente l'estensione `VMAccessForLinux` per reimpostare la connessione SSH:

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

#### Reimpostare le credenziali SSH

**Metodo 1:** eseguire il comando `vm reset-access` per impostare una delle credenziali SSH.

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

Per altre informazioni su questo comando, digitare `azure vm reset-access -h` nella riga di comando.

**Metodo 2:** creare un file denominato PrivateConf.json con il contenuto seguente:

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

Eseguire quindi l'estensione Linux usando il nome file appena descritto (Privateconf):

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

Se si vuole tentare un altro approccio di risoluzione dei problemi, è possibile seguire passaggi simili a quelli riportati in [Come reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md). Ricordare di modificare le istruzioni dell'interfaccia della riga di comando di Azure per la modalità Resource Manager.


## Risolvere gli errori SSH in modo più dettagliato

Se il client SSH non riesce ancora a raggiungere il servizio SSH nella macchina virtuale, i motivi possono essere molti. Il diagramma seguente illustrati i componenti coinvolti.

![Diagramma che mostra i componenti del servizio SSH](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot1.png)

I passaggi seguenti saranno utili per isolare l'origine dell'errore e individuare le soluzioni.

### Operazioni preliminari

Prima di tutto controllare lo stato della macchina virtuale nel portale.

Nel [portale di Azure classico](https://manage.windowsazure.com), per le macchine virtuali create con il modello di distribuzione classica:

1. Selezionare **Macchine virtuali** > *Nome macchina virtuale*.
2. Selezionare il **dashboard** della macchina virtuale per controllarne lo stato.
3. Selezionare **Monitoraggio** per visualizzare le recenti attività di elaborazione e archiviazione e le risorse di rete.
4. Selezionare **Endpoint** per verificare che sia presente un endpoint per il traffico SSH.

Nel [portale di Azure](https://portal.azure.com):

1. Per le macchine virtuali create con il modello di distribuzione classica selezionare **Sfoglia** > **Macchine virtuali (classico)** > *Nome macchina virtuale*.

	-OPPURE-

	Per le macchine virtuali create con il modello Resource Manager selezionare **Sfoglia** > **Macchine virtuali** > *Nome VM*.

	Nel riquadro di stato relativo alla macchina virtuale deve essere visualizzata l'opzione **In esecuzione**. Scorrere verso il basso per visualizzare le recenti attività di elaborazione e archiviazione e le risorse di rete.

2. Selezionare **Impostazioni** per esaminare gli endpoint, gli indirizzi IP e altre impostazioni.

	Per identificare gli endpoint nelle macchine virtuali create con Resource Manager, verificare che sia stato definito un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Verificare anche che al gruppo di sicurezza di rete siano state applicate le regole e che nella subnet sia fatto riferimento ad esse.

Per verificare la connettività di rete, controllare gli endpoint configurati e determinare se è possibile raggiungere la macchina virtuale tramite un altro protocollo, ad esempio HTTP o un altro servizio.

Dopo questi passaggi, tentare nuovamente la connessione SSH.


### Individuare l'origine del problema

L'impossibilità per il client SSH nel computer in uso di raggiungere il servizio SSH nella macchina virtuale di Azure può essere dovuta problemi o errori di configurazione nelle origini seguenti:

- Computer client SSH
- Dispositivo periferico dell'organizzazione
- Endpoint del servizio cloud ed elenco di controllo di accesso (ACL)
- Gruppi di sicurezza di rete
- Macchina virtuale di Azure basata su Linux

#### Origine 1: computer client SSH

Per escludere il computer in uso come origine dell'errore, verificare che sia in grado di stabilire connessioni SSH a un altro computer locale basato su Linux.

![Diagramma che evidenzia i componenti del computer client SSH](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se ciò non è possibile, effettuare i seguenti controlli nel computer:

- Un'impostazione locale del firewall che blocca il traffico SSH in ingresso o in uscita (TCP 22)
- Software proxy client installato localmente che impedisce le connessioni SSH
- Software di monitoraggio della rete installato localmente che impedisce le connessioni SSH
- Altri tipi di software di sicurezza che eseguono il monitoraggio del traffico o consentono/non consentono tipi di traffico specifici

Se una delle seguenti condizioni è applicabile, disabilitare temporaneamente il software e tentare una connessione SSH a un computer locale per scoprire il motivo per cui la connessione è bloccata nel computer in uso. Contattare quindi l'amministratore di rete per correggere le impostazioni del software in modo da consentire le connessioni SSH.

Se si usa l'autenticazione del certificato, verificare la presenza delle seguenti autorizzazioni nella cartella .ssh della home directory:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (o qualsiasi altro file in cui siano archiviate le chiavi private)
- Chmod 644 ~/.ssh/known\_hosts (contiene gli host con i quali è stabilita una connessione SSH)

#### Origine 2: dispositivo periferico dell'organizzazione

Per escludere il dispositivo periferico dell'organizzazione come origine dell'errore, verificare che un computer connesso direttamente a Internet sia in grado di stabilire connessioni SSH alla macchina virtuale di Azure. Se si accede alla macchina virtuale tramite una connessione Azure ExpressRoute o VPN da sito a sito, passare al paragrafo [Origine 4: gruppi di sicurezza di rete](#nsg).

![Diagramma che evidenzia il dispositivo periferico dell'organizzazione](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se non si dispone di un computer connesso direttamente a Internet, è possibile creare facilmente e usare una nuova macchina virtuale di Azure nel suo proprio gruppo di risorse o servizio cloud. Per altre informazioni, vedere [Creare una macchina virtuale con Linux in Azure](virtual-machines-linux-quick-create-cli.md). Dopo aver completato i test, eliminare il gruppo di risorse o la macchina virtuale e il servizio cloud.

Se è possibile creare una connessione SSH con un computer connesso direttamente a Internet, controllare se nel dispositivo periferico dell'organizzazione si verificano le condizioni seguenti:

- Un firewall interno blocca il traffico SSH con Internet
- Un server proxy impedisce le connessioni SSH
- Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sui dispositivi presenti nella rete perimetrale impedisce le connessioni SSH

Rivolgersi all'amministratore di rete per correggere le impostazioni dei dispositivi periferici dell'organizzazione in modo da consentire traffico SSH con Internet.

#### Origine 3: endpoint del servizio cloud e ACL

> [AZURE.NOTE] Questa origine si applica solo alle macchine virtuali create con il modello di distribuzione classica. Per le macchine virtuali create con Resource Manager, passare a [Origine 4: gruppi di sicurezza di rete](#nsg).

Per escludere l'endpoint di servizio cloud e l'elenco di controllo di accesso (ACL) come possibile origine dell'errore, verificare che un'altra macchina virtuale di Azure che si trova nella stessa rete virtuale sia in grado di stabilire connessioni SSH alla macchina virtuale di Azure.

![Diagramma che evidenzia l'endpoint del servizio cloud e l'elenco di controllo di accesso](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se non si dispone di un'altra macchina virtuale nella stessa rete virtuale, è possibile crearne facilmente una nuova. Per altre informazioni, vedere [Creare una VM Linux in Azure tramite l'interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md). Dopo aver completato i test, eliminare la macchina virtuale aggiuntiva.

Se è possibile creare una connessione SSH con una macchina virtuale presente nella stessa rete virtuale, verificare quanto segue:

- **La configurazione dell'endpoint per il traffico SSH nella macchina virtuale di destinazione.** La porta TCP privata dell'endpoint dovrebbe corrispondere alla porta TCP su cui è in ascolto il servizio SSH nella macchina virtuale (per impostazione predefinita è la porta 22). Per le macchine virtuali create con il modello di distribuzione Resource Manager, verificare il numero di porta TCP SSH nel portale di Azure selezionando **Sfoglia** > **Macchine virtuali (v2)** > *Nome macchina virtuale* > **Impostazioni** > **Endpoint**.

- **L'ACL relativa all'endpoint per il traffico SSH sulla macchina virtuale di destinazione.** Gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico SSH in ingresso nell'endpoint. Verificare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per altre informazioni, vedere [Informazioni sugli elenchi di controllo di accesso (ACL) di rete](../virtual-network/virtual-networks-acl.md).

Per escludere l'endpoint come origine del problema, rimuovere l'endpoint corrente, creare un nuovo endpoint e specificare il nome SSH (porta TCP 22 per il numero di porta pubblica e privata). Per ulteriori informazioni, vedere [Configurare endpoint in una macchina virtuale in Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
#### Origine 4: gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono di avere un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure. Controllare le regole del gruppo di sicurezza di rete per garantire che sia consentito il traffico SSH da Internet. Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

#### Origine 5: macchina virtuale di Azure basata su Linux

L'ultima origine di possibili problemi corrisponde alla stessa macchina virtuale di Azure.

![Diagramma che evidenzia la macchina virtuale di Azure basata su Linux](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se non è stato ancora fatto, seguire le istruzioni [per reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md).

Provare nuovamente la connessione dal computer. Se ancora non riesce, alcuni dei problemi possibili sono:

- Il servizio SSH non è in esecuzione sulla macchina virtuale di destinazione.
- Il servizio SSH non è in ascolto sulla porta TCP 22. Per verificare questo aspetto, installare un client telnet nel computer locale ed eseguire "telnet *cloudServiceName*.cloudapp.net 22". In questo modo è possibile determinare se la macchina virtuale consente comunicazioni in ingresso e in uscita per l'endpoint SSH.
- Il firewall locale nella macchina virtuale di destinazione include regole che impediscono il traffico SSH in ingresso o in uscita.
- Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sulla macchina virtuale di Azure impedisce le connessioni SSH.


## Risorse aggiuntive

- Per altre informazioni sulla risoluzione dei problemi di macchine virtuali create con il modello di distribuzione classica, vedere l’articolo relativo a [come reimpostare una password o SSH per macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md).

- Per altre informazioni sulla risoluzione dei problemi di macchine virtuali create con il modello di distribuzione Resource Manager, vedere:
	- [Risolvere i problemi relativi a connessioni Desktop remoto di Windows a una macchina virtuale Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)
	-	[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0518_2016-->
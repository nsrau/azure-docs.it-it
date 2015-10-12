<properties
	pageTitle="Impossibile connettersi a una macchina virtuale di Azure tramite SSH | Microsoft Azure"
	description="Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure che esegue Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="dkshir"/>

# Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo è dedicato alla risoluzione dei problemi delle connessioni SSH su una macchina virtuale creata con il modello di distribuzione di gestione risorse o il modello di distribuzione classica.


Possono essere varie le cause degli errori SSH per una macchina virtuale di Azure basata su Linux. Questo articolo consente di individuarle e correggere gli errori.

> [AZURE.NOTE]Questo articolo si applica solo a macchine virtuali di Azure che eseguono Linux. Per la risoluzione dei problemi di connessioni a macchine virtuali di Azure che eseguono Windows, vedere [questo articolo](virtual-machines-troubleshoot-remote-desktop-connections.md).

## Contattare il supporto tecnico di Azure

Se è necessaria ulteriore assistenza in qualsiasi punto in questo articolo, è possibile contattare gli esperti di Azure su [MSDN Azure e i forum di overflow dello stack](http://azure.microsoft.com/support/forums/).

In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Passaggi di base

Provare a eseguire questi passaggi per risolvere gli errori di connessione SSH più comuni:

1. Reimpostare l'accesso remoto dal [portale di Azure](https://portal.azure.com). Fare clic su **Esplora tutto** > **Macchine virtuali (classiche)** > macchina virtuale Windows > **Reimposta accesso remoto**.

	![Reimpostare l'accesso remoto](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. Riavviare la macchina virtuale. Dal [portale di anteprima di Azure](https://portal.azure.com) fare clic su **Esplora tutto** > **Macchine virtuali (classiche)** > macchina virtuale Windows > **Riavvia**. Dal [portale di gestione di Azure](https://manage.windowsazure.com) aprire **Macchine virtuali** > **Istanze** e fare clic su **Riavvia**.

3. [Ridimensionare la macchina virtuale](https://msdn.microsoft.com/library/dn168976.aspx).

4. Seguire le istruzioni in [Come reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) nella macchina virtuale per:

	- Reimpostare la password o la chiave SSH.
	- Creare un nuovo account utente sudo.
	- Reimpostare la configurazione SSH.

## Risoluzione dettagliata dei problemi

Se il client SSH non riesce ancora a raggiungere il servizio SSH sulla macchina virtuale, questo può essere dovuto a vari motivi. Di seguito sono riportati i componenti coinvolti.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Le informazioni nelle sezioni seguenti saranno utili per isolare l'origine dell'errore e individuare le soluzioni.

### Passaggi prima della risoluzione dei problemi

Prima di tutto controllare lo stato della macchina virtuale nel portale di Azure.

Nel [portale di gestione di Azure](https://manage.windowsazure.com):

1. Fare clic su **Macchine virtuali** > *Nome macchina virtuale*.
2. Fare clic sul **dashboard** della macchina virtuale per controllarne lo stato.
3. Fare clic su **Monitoraggio** per visualizzare le recenti attività di elaborazione e archiviazione e le risorse di rete.
4. Fare clic su **Endpoint** per verificare che sia presente un endpoint per il traffico SSH.

Nel [portale di anteprima di Azure](https://portal.azure.com):

1. Fare clic su **Sfoglia** > **Macchine virtuali** > *Nome macchina virtuale*. Per una macchina virtuale creata in Gestione risorse di Azure, fare clic su **Sfoglia** > **Macchine virtuali (v2)** > *Nome macchina virtuale*. Nel riquadro di stato relativo alla macchina virtuale deve essere visualizzata l'opzione **In esecuzione**. Scorrere verso il basso per visualizzare le recenti attività di elaborazione e archiviazione e le risorse di rete.
2. Fare clic su **Impostazioni** per esaminare gli endpoint, gli indirizzi IP e altre impostazioni.

Per verificare la connettività di rete, controllare gli endpoint configurati e determinare se è possibile raggiungere la macchina virtuale tramite un altro protocollo, ad esempio HTTP o un altro servizio.

Dopo questi passaggi, tentare nuovamente la connessione SSH.


### Passaggi per la risoluzione dei problemi

L'impossibilità per il client SSH nel computer in uso di raggiungere il servizio SSH nella macchina virtuale di Azure può essere dovuta alle seguenti origini di problemi o errori di configurazione possibili:

- Computer client SSH
- Dispositivo periferico dell'organizzazione
- Endpoint del servizio cloud ed elenco di controllo di accesso (ACL)
- Gruppi di sicurezza di rete
- Macchina virtuale di Azure basata su Linux

#### Origine 1: computer client SSH

Per escludere il computer in uso come origine dell'errore, verificare che sia in grado di stabilire connessioni SSH a un altro computer locale basato su Linux.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Se ciò non è possibile, effettuare i seguenti controlli nel computer:

- Un'impostazione locale del firewall che blocca il traffico SSH in ingresso o in uscita (TCP 22)
- Software proxy client installato localmente che impedisce le connessioni SSH
- Software di monitoraggio della rete installato localmente che impedisce le connessioni SSH
- Altri tipi di software di sicurezza che eseguono il monitoraggio del traffico o consentono/non consentono tipi di traffico specifici

In tutti questi casi, disabilitare temporaneamente il software e tentare una connessione SSH a un computer locale per determinare la causa. Quindi, contattare l'amministratore di rete per correggere le impostazioni del software in modo da consentire le connessioni SSH.

Se si usa l'autenticazione del certificato, verificare la presenza delle seguenti autorizzazioni nella cartella .ssh della home directory:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (o qualsiasi altro file in cui siano archiviate le chiavi private)
- Chmod 644 ~/.ssh/known\_hosts (contiene gli host con i quali è stabilita una connessione SSH)

#### Origine 2: dispositivo periferico dell'organizzazione

Per eliminare il dispositivo periferico dell'organizzazione come origine dell'errore, verificare che un computer connesso direttamente a Internet sia in grado di stabilire connessioni SSH alla macchina virtuale di Azure. Se si accede alla macchina virtuale tramite una connessione ExpressRoute o VPN da sito a sito, passare al paragrafo [Origine 4: gruppi di sicurezza di rete](#nsg).

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Se non si dispone di un computer connesso direttamente a Internet, è possibile creare facilmente e usare una nuova macchina virtuale di Azure nel suo proprio gruppo di risorse o servizio cloud. Per altre informazioni, vedere [Creare una macchina virtuale con Linux in Azure](virtual-machines-linux-tutorial.md). Dopo aver completato i test, eliminare il gruppo di risorse o la macchina virtuale e il servizio cloud.

Se è possibile creare una connessione SSH con un computer collegato direttamente a Internet, controllare se nel dispositivo periferico dell'organizzazione si verificano le seguenti condizioni:

- Un firewall interno blocca il traffico SSH con Internet
- Il server proxy impedisce le connessioni SSH
- Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sui dispositivi presenti nella rete perimetrale impedisce le connessioni SSH

Rivolgersi all'amministratore di rete per correggere le impostazioni dei dispositivi periferici dell'organizzazione in modo da consentire traffico SSH con Internet.

#### Origine 3: endpoint del servizio cloud e ACL

Per eliminare l'endpoint del servizio cloud e l'elenco di controllo di accesso (ACL) come possibile origine dell'errore, per le macchine virtuali create con il [modello di distribuzione classica](../resource-manager-deployment-model.md), verificare che un'altra macchina virtuale di Azure che si trova nella stessa rete virtuale sia in grado di stabilire connessioni SSH alla macchina virtuale di Azure.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Per le macchine virtuali create in Gestione risorse, passare a [Origine 4: gruppi di sicurezza di rete](#nsg).

Se non si dispone di un'altra macchina virtuale nella stessa rete virtuale, è possibile crearne facilmente una nuova. Per altre informazioni, vedere [Creare una macchina virtuale con Linux in Azure](virtual-machines-linux-tutorial.md). Dopo aver completato i test, eliminare la macchina virtuale aggiuntiva.

Se è possibile creare una connessione SSH con una macchina virtuale presente nella stessa rete virtuale, verificare quanto segue:

- La configurazione dell'endpoint per il traffico SSH nella macchina virtuale di destinazione. La porta TCP privata dell'endpoint dovrebbe corrispondere alla porta TCP su cui è in ascolto il servizio SSH nella macchina virtuale (per impostazione predefinita è la porta 22). Per le macchine virtuali create con il modello di distribuzione di Gestione risorse usando i modelli, verificare il numero di porta TCP SSH nel portale di anteprima di Azure facendo clic su **Sfoglia** > **Macchine virtuali (v2)** > *Nome macchina virtuale* > **Impostazioni** > **Endpoint**.
- L'ACL relativa all'endpoint per il traffico SSH sulla macchina virtuale di destinazione. Gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico SSH in ingresso nell'endpoint. Controllare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per altre informazioni, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)](../virtual-network/virtual-networks-acl.md).

Per eliminare l'endpoint come origine del problema, rimuovere l'endpoint corrente e creare un nuovo endpoint, specificando il nome **SSH** (porta TCP 22 per il numero di porta pubblico e privato). Per altre informazioni, vedere [Configurare endpoint in una macchina virtuale in Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Origine 4: gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono di avere un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure. Controllare le regole del gruppo di sicurezza di rete per garantire che sia consentito il traffico SSH da Internet. Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](../traffic-manager/virtual-networks-nsg.md).

#### Origine 5: macchina virtuale di Azure basata su Linux

L'ultima origine di possibili problemi corrisponde alla stessa macchina virtuale di Azure.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Se non è stato ancora fatto, seguire le istruzioni illustrate nell'articolo [Come reimpostare una password o SSH per le macchine virtuali Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) nella macchina virtuale.

Provare nuovamente la connessione dal computer. Se ancora non riesce, alcuni dei problemi possibili sono:

- Il servizio SSH non è in esecuzione sulla macchina virtuale di destinazione.
- Il servizio SSH non è in ascolto sulla porta TCP 22. Per verificare questo aspetto, installare un client telnet nel computer locale ed eseguire "telnet *cloudServiceName*.cloudapp.net 22". In questo modo sarà possibile scoprire se la macchina virtuale consente comunicazioni in ingresso e in uscita per l'endpoint SSH.
- Il firewall locale nella macchina virtuale di destinazione include regole che impediscono il traffico SSH in ingresso o in uscita.
- Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sulla macchina virtuale di Azure impedisce le connessioni SSH.


## Risorse aggiuntive

[Come reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Risolvere i problemi relativi a connessioni Desktop remoto di Windows a una macchina virtuale Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO1-->
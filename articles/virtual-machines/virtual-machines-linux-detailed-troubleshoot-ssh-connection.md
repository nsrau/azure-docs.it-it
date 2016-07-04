<properties
	pageTitle="Procedura dettagliata di risoluzione dei problemi SSH per una VM di Azure | Microsoft Azure"
	description="Procedura dettagliata di risoluzione dei problemi di connessione SSH a una macchina virtuale di Azure"
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
	ms.date="06/16/2016"
	ms.author="iainfou"/>

# Procedura dettagliata di risoluzione dei problemi SSH

Se il client SSH non riesce a raggiungere il servizio SSH nella VM, i motivi possono essere molti. Se sono state eseguite le operazioni più generali di [risoluzione dei problemi SSH](virtual-machines-linux-troubleshoot-ssh-connection.md), sarà necessario seguire una procedura dettagliata per la risoluzione del problema di connessione. In questo articolo sono descritti i passaggi dettagliati per individuare e risolvere gli eventuali errori della connessione SSH.

## Operazioni preliminari

Il diagramma seguente illustrati i componenti coinvolti.

![Diagramma che mostra i componenti del servizio SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

I passaggi seguenti saranno utili per isolare l'origine dell'errore e individuare le soluzioni.

Prima di tutto controllare lo stato della VM nel portale.

Nel [portale di Azure classico](https://manage.windowsazure.com) per le VM create con il modello di distribuzione classica:

1. Selezionare **Macchine virtuali** > *Nome macchina virtuale*.
2. Selezionare il **dashboard** della VM per controllarne lo stato.
3. Selezionare **Monitoraggio** per visualizzare le recenti attività di calcolo e archiviazione e le risorse di rete.
4. Selezionare **Endpoint** per verificare che sia presente un endpoint per il traffico SSH.

Nel [portale di Azure](https://portal.azure.com):

1. Per le VM create con il modello di distribuzione classica selezionare **Sfoglia** > **Macchine virtuali (classico)** > *Nome macchina virtuale*.

	-OPPURE-

	Per le VM create con il modello Resource Manager, selezionare **Sfoglia** > **Macchine virtuali** > *Nome VM*.

	Nel riquadro di stato relativo alla VM deve essere visualizzata l'opzione **In esecuzione**. Scorrere verso il basso per visualizzare le recenti attività di elaborazione e archiviazione e le risorse di rete.

2. Selezionare **Impostazioni** per esaminare gli endpoint, gli indirizzi IP e altre impostazioni.

	Per identificare gli endpoint nelle VM create con Resource Manager, verificare che sia stato definito un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Verificare anche che al gruppo di sicurezza di rete siano state applicate le regole e che nella subnet sia fatto riferimento ad esse.

Per verificare la connettività di rete, controllare gli endpoint configurati e determinare se è possibile raggiungere la macchina virtuale tramite un altro protocollo, ad esempio HTTP o un altro servizio.

Dopo questi passaggi, tentare nuovamente la connessione SSH.


## Individuare l'origine del problema

L'impossibilità per il client SSH nel computer in uso di raggiungere il servizio SSH nella VM di Azure può essere dovuta problemi o errori di configurazione nelle origini seguenti:

- Computer client SSH
- Dispositivo periferico dell'organizzazione
- Endpoint del servizio cloud ed elenco di controllo di accesso (ACL)
- Gruppi di sicurezza di rete
- VM di Azure basata su Linux

## Origine 1: computer client SSH

Per escludere il computer in uso come origine dell'errore, verificare che sia in grado di stabilire connessioni SSH a un altro computer locale basato su Linux.

![Diagramma che evidenzia i componenti del computer client SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

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

## Origine 2: dispositivo periferico dell'organizzazione

Per escludere il dispositivo periferico dell'organizzazione come origine dell'errore, verificare che un computer connesso direttamente a Internet sia in grado di stabilire connessioni SSH alla macchina virtuale di Azure. Se si accede alla VM tramite una connessione Azure ExpressRoute o VPN da sito a sito, passare al paragrafo [Origine 4: gruppi di sicurezza di rete](#nsg).

![Diagramma che evidenzia il dispositivo periferico dell'organizzazione](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se non si dispone di un computer connesso direttamente a Internet, è possibile creare facilmente e usare una nuova VM di Azure nel suo proprio gruppo di risorse o servizio cloud. Per altre informazioni, vedere [Creare una macchina virtuale con Linux in Azure](virtual-machines-linux-quick-create-cli.md). Dopo aver completato i test, eliminare il gruppo di risorse o la VM e il servizio cloud.

Se è possibile creare una connessione SSH con un computer connesso direttamente a Internet, controllare se nel dispositivo periferico dell'organizzazione si verificano le condizioni seguenti:

- Un firewall interno blocca il traffico SSH con Internet
- Un server proxy impedisce le connessioni SSH
- Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sui dispositivi presenti nella rete perimetrale impedisce le connessioni SSH

Rivolgersi all'amministratore di rete per correggere le impostazioni dei dispositivi periferici dell'organizzazione in modo da consentire traffico SSH con Internet.

## Origine 3: endpoint del servizio cloud e ACL

> [AZURE.NOTE] Questa origine si applica solo alle VM create con il modello di distribuzione classica. Per le VM create con Resource Manager, passare a [Origine 4: gruppi di sicurezza di rete](#nsg).

Per escludere l'endpoint di servizio cloud e l'elenco di controllo di accesso (ACL) come possibile origine dell'errore, verificare che un'altra macchina virtuale di Azure che si trova nella stessa rete virtuale sia in grado di stabilire connessioni SSH alla macchina virtuale di Azure.

![Diagramma che evidenzia l'endpoint del servizio cloud e l'elenco di controllo di accesso](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se non si dispone di un'altra macchina virtuale nella stessa rete virtuale, è possibile crearne facilmente una nuova. Per altre informazioni, vedere [Creare una VM Linux in Azure tramite l'interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md). Dopo aver completato i test, eliminare la macchina virtuale aggiuntiva.

Se è possibile creare una connessione SSH con una macchina virtuale presente nella stessa rete virtuale, verificare quanto segue:

- **La configurazione dell'endpoint per il traffico SSH nella macchina virtuale di destinazione.** La porta TCP privata dell'endpoint dovrebbe corrispondere alla porta TCP su cui è in ascolto il servizio SSH nella macchina virtuale (per impostazione predefinita è la porta 22). Per le VM create con il modello di distribuzione Resource Manager, verificare il numero di porta TCP SSH nel portale di Azure selezionando **Sfoglia** > **Macchine virtuali (v2)** > *Nome macchina virtuale* > **Impostazioni** > **Endpoint**.

- **L'ACL relativa all'endpoint per il traffico SSH sulla macchina virtuale di destinazione.** Gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico SSH in ingresso nell'endpoint. Verificare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per altre informazioni, vedere [Informazioni sugli elenchi di controllo di accesso (ACL) di rete](../virtual-network/virtual-networks-acl.md).

Per escludere l'endpoint come origine del problema, rimuovere l'endpoint corrente, creare un nuovo endpoint e specificare il nome SSH (porta TCP 22 per il numero di porta pubblica e privata). Per ulteriori informazioni, vedere [Configurare endpoint in una macchina virtuale in Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## Origine 4: gruppi di sicurezza di rete

I gruppi di sicurezza di rete consentono di avere un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure. Controllare le regole del gruppo di sicurezza di rete per garantire che sia consentito il traffico SSH da Internet. Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

## Origine 5: macchina virtuale di Azure basata su Linux

L'ultima origine di possibili problemi corrisponde alla stessa macchina virtuale di Azure.

![Diagramma che evidenzia la macchina virtuale di Azure basata su Linux](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se non è stato ancora fatto, seguire le istruzioni [per reimpostare una password o SSH per le macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md).

Provare nuovamente la connessione dal computer. Se ancora non riesce, alcuni dei problemi possibili sono:

- Il servizio SSH non è in esecuzione sulla macchina virtuale di destinazione.
- Il servizio SSH non è in ascolto sulla porta TCP 22. Per verificare questo aspetto, installare un client telnet nel computer locale ed eseguire "telnet *cloudServiceName*.cloudapp.net 22". In questo modo è possibile determinare se la macchina virtuale consente comunicazioni in ingresso e in uscita per l'endpoint SSH.
- Il firewall locale nella macchina virtuale di destinazione include regole che impediscono il traffico SSH in ingresso o in uscita.
- Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sulla macchina virtuale di Azure impedisce le connessioni SSH.


## Risorse aggiuntive
Per ulteriori informazioni sulla risoluzione dei problemi di accesso dell'applicazione, vedere [Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](virtual-machines-linux-troubleshoot-app-connection.md).

<!---HONumber=AcomDC_0622_2016-->
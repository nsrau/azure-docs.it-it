---
title: Procedura dettagliata di risoluzione dei problemi SSH per una VM di Azure | Microsoft Docs
description: Procedura dettagliata di risoluzione dei problemi di connessione SSH a una macchina virtuale di Azure
keywords: connessione SSH rifiutata,errore SSH,SSH Azure,connessione SSH non riuscita
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 66fc8bac46decacdd2214475e94980c447045935
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Procedura dettagliata di risoluzione dei problemi di connessione SSH a una macchina virtuale Linux in Azure
Se il client SSH non riesce a raggiungere il servizio SSH nella VM, i motivi possono essere molti. Se è stata eseguita la [procedura generica di risoluzione dei problemi SSH](troubleshoot-ssh-connection.md), per risolvere il problema di connessione è necessario ricorrere una procedura dettagliata. Questo articolo descrive i passaggi dettagliati per individuare e risolvere gli eventuali errori della connessione SSH.

## <a name="take-preliminary-steps"></a>Operazioni preliminari
Il diagramma seguente illustrati i componenti coinvolti.

![Diagramma che mostra i componenti del servizio SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

I passaggi seguenti sono utili per isolare l'origine dell'errore e individuare le soluzioni.

1. Controllare lo stato della VM nel portale.
   Nel [portale di Azure](https://portal.azure.com) selezionare **Macchine virtuali** > *Nome macchina virtuale*.

   Nel riquadro di stato relativo alla VM deve essere visualizzata l'opzione **In esecuzione**. Scorrere verso il basso per visualizzare le recenti attività di elaborazione e archiviazione e le risorse di rete.

2. Selezionare **Impostazioni** per esaminare gli endpoint, gli indirizzi IP, i gruppi di sicurezza di rete e altre impostazioni.

   La macchina virtuale deve disporre di un endpoint definito per il traffico SSH che è possibile visualizzare in **Endpoint** o in **[Gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)**. Gli endpoint nelle VM create tramite Resource Manager vengono archiviati in un gruppo di sicurezza di rete. Verificare che le regole siano state applicate al gruppo di sicurezza di rete e che vi si faccia riferimento nella subnet.

Per verificare la connettività di rete, controllare gli endpoint configurati e determinare se è possibile connettersi alla macchina virtuale tramite un altro protocollo, ad esempio HTTP, o un altro servizio.

Dopo questi passaggi, tentare nuovamente la connessione SSH.

## <a name="find-the-source-of-the-issue"></a>Individuare l'origine del problema
L'impossibilità per il client SSH nel computer in uso di connettersi al servizio SSH nella macchina virtuale di Azure può essere dovuta a problemi o errori di configurazione nelle aree seguenti:

* [Computer client SSH](#source-1-ssh-client-computer)
* [Dispositivo periferico dell'organizzazione](#source-2-organization-edge-device)
* [Endpoint del servizio cloud ed elenco di controllo di accesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Gruppi di sicurezza di rete](#source-4-network-security-groups)
* [VM di Azure basata su Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Origine 1: computer client SSH
Per escludere il computer in uso come origine dell'errore, verificare che sia in grado di stabilire connessioni SSH a un altro computer locale basato su Linux.

![Diagramma che evidenzia i componenti del computer client SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se non è possibile stabilire una connessione, eseguire i controlli seguenti sul computer:

* Un'impostazione locale del firewall che blocca il traffico SSH in ingresso o in uscita (TCP 22)
* Software proxy client installato localmente che impedisce le connessioni SSH
* Software di monitoraggio della rete installato localmente che impedisce le connessioni SSH
* Altri tipi di software di sicurezza che eseguono il monitoraggio del traffico o consentono/non consentono tipi di traffico specifici

Se una delle seguenti condizioni è applicabile, disabilitare temporaneamente il software e tentare una connessione SSH a un computer locale per scoprire il motivo per cui la connessione è bloccata nel computer in uso. Contattare quindi l'amministratore di rete per correggere le impostazioni del software in modo da consentire le connessioni SSH.

Se si usa l'autenticazione del certificato, verificare la presenza delle seguenti autorizzazioni nella cartella .ssh della home directory:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (o qualsiasi altro file in cui siano archiviate le chiavi private)
* Chmod 644 ~/.ssh/known_hosts (contiene gli host con i quali è stabilita una connessione SSH)

## <a name="source-2-organization-edge-device"></a>Origine 2: dispositivo periferico dell'organizzazione
Per escludere il dispositivo periferico dell'organizzazione come origine dell'errore, verificare che un computer connesso direttamente a Internet sia in grado di stabilire connessioni SSH alla macchina virtuale di Azure. Se si accede alla VM tramite una connessione Azure ExpressRoute o VPN da sito a sito, passare al paragrafo [Origine 4: gruppi di sicurezza di rete](#nsg).

![Diagramma che evidenzia il dispositivo periferico dell'organizzazione](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se non si ha un computer connesso direttamente a Internet, creare e usare una nuova macchina virtuale di Azure nel gruppo di risorse o nel servizio cloud della macchina virtuale stessa. Per altre informazioni, vedere [Creare una macchina virtuale che esegue Linux in Azure](quick-create-cli.md). Dopo aver completato i test, eliminare il gruppo di risorse o la VM e il servizio cloud.

Se è possibile creare una connessione SSH con un computer connesso direttamente a Internet, controllare se nel dispositivo periferico dell'organizzazione si verificano le condizioni seguenti:

* Un firewall interno blocca il traffico SSH con Internet
* Un server proxy impedisce le connessioni SSH
* Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sui dispositivi presenti nella rete perimetrale impedisce le connessioni SSH

Rivolgersi all'amministratore di rete per correggere le impostazioni dei dispositivi periferici dell'organizzazione in modo da consentire traffico SSH con Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origine 3: endpoint del servizio cloud e ACL
> [!NOTE]
> Questa origine si applica solo alle VM create con il modello di distribuzione classica. Per le VM create con Resource Manager, passare a [Origine 4: gruppi di sicurezza di rete](#nsg).

Per escludere l'endpoint servizio cloud e l'elenco di controllo di accesso (ACL) come possibili origini dell'errore, verificare che un'altra macchina virtuale di Azure che si trova nella stessa rete virtuale sia in grado di connettersi tramite SSH.

![Diagramma che evidenzia l'endpoint del servizio cloud e l'elenco di controllo di accesso](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se non si dispone di un'altra macchina virtuale nella stessa rete virtuale, è possibile crearne facilmente una nuova. Per altre informazioni, vedere [Creare una VM Linux in Azure usando l'interfaccia della riga di comando](quick-create-cli.md). Dopo aver completato i test, eliminare la macchina virtuale aggiuntiva.

Se è possibile creare una connessione SSH con una macchina virtuale presente nella stessa rete virtuale, verificare quanto segue:

* **La configurazione dell'endpoint per il traffico SSH sulla macchina virtuale di destinazione.** La porta TCP privata dell'endpoint dovrebbe corrispondere alla porta TCP su cui è in ascolto il servizio SSH nella macchina virtuale (per impostazione predefinita è la porta 22). Verificare il numero di porta TCP SSH nel portale di Azure selezionando **Macchine virtuali** > *Nome macchina virtuale* > **Impostazioni** > **Endpoint**.
* **L'ACL relativa all'endpoint per il traffico SSH sulla macchina virtuale di destinazione.** Gli ACL consentono di specificare il traffico in ingresso da Internet consentito o negato in base al relativo indirizzo IP di origine. ACL configurati in modo errato possono impedire il traffico SSH in ingresso nell'endpoint. Verificare gli ACL per assicurarsi che il traffico in ingresso dagli indirizzi IP pubblici del proxy o da altri server periferici sia consentito. Per altre informazioni, vedere [Informazioni sugli elenchi di controllo di accesso (ACL) di rete](../../virtual-network/virtual-networks-acl.md).

Per escludere l'endpoint come origine del problema, rimuovere l'endpoint corrente, creare un nuovo endpoint e specificare il nome SSH (porta TCP 22 per il numero di porta pubblica e privata). Per altre informazioni, vedere [Configurare endpoint in una macchina virtuale in Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Origine 4: gruppi di sicurezza di rete
I gruppi di sicurezza di rete consentono di avere un controllo più granulare del traffico in entrata e in uscita consentito. È possibile creare regole che si estendono alle subnet e ai servizi cloud in una rete virtuale di Azure. Controllare le regole del gruppo di sicurezza di rete per garantire che sia consentito il traffico SSH da Internet.
Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md).

È anche possibile usare la funzione di verifica IP per convalidare la configurazione del gruppo di sicurezza di rete. Per altre informazioni, vedere [Panoramica del monitoraggio della rete in Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Origine 5: macchina virtuale di Azure basata su Linux
L'ultima origine di possibili problemi corrisponde alla stessa macchina virtuale di Azure.

![Diagramma che evidenzia la macchina virtuale di Azure basata su Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se non lo si è ancora fatto, seguire le istruzioni [per reimpostare la password per le macchine virtuali basate su Linux](reset-password.md).

Provare nuovamente la connessione dal computer. Se ancora non riesce, alcuni dei problemi possibili sono:

* Il servizio SSH non è in esecuzione sulla macchina virtuale di destinazione.
* Il servizio SSH non è in ascolto sulla porta TCP 22. Per verificare questo aspetto, installare un client telnet nel computer locale ed eseguire "telnet *cloudServiceName*.cloudapp.net 22". In questo modo è possibile determinare se la macchina virtuale consente comunicazioni in ingresso e in uscita per l'endpoint SSH.
* Il firewall locale nella macchina virtuale di destinazione include regole che impediscono il traffico SSH in ingresso o in uscita.
* Un software per il rilevamento di intrusioni o il monitoraggio della rete in esecuzione sulla macchina virtuale di Azure impedisce le connessioni SSH.

## <a name="additional-resources"></a>Risorse aggiuntive
Per ulteriori informazioni sulla risoluzione dei problemi di accesso dell'applicazione, vedere [Risoluzione dei problemi di accesso a un'applicazione in esecuzione su una macchina virtuale di Azure](troubleshoot-app-connection.md)

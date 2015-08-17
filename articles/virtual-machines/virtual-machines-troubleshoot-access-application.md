<properties
	pageTitle="Risoluzione dei problemi di accesso a un'applicazione in esecuzione in una macchina virtuale di Azure"
	description="Se è impossibile accedere a un'applicazione in esecuzione su una macchina virtuale di Azure, è possibile utilizzare questi passaggi per isolare l'origine del problema."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="dkshir"/>

# Risoluzione dei problemi di accesso a un'applicazione in esecuzione in una macchina virtuale di Azure

Se è impossibile accedere a un'applicazione in esecuzione in una macchina virtuale di Azure, in questo articolo viene descritto un approccio metodico per isolare l'origine del problema e risolverlo.

> [AZURE.NOTE]Per supporto alla connessione a una macchina virtuale di Azure, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md) o [Risolvere i problemi di connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](virtual-machines-troubleshoot-ssh-connections.md).

Esistono quattro aree principali per risolvere i problemi di accesso di un'applicazione che è in esecuzione in una macchina virtuale di Azure.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access1.png)

1.	L’applicazione in esecuzione nella macchina virtuale di Azure.
2.	La macchina virtuale di Azure.
3.	Gli endpoint di Azure per il servizio cloud che contiene la macchina virtuale (per le macchine virtuali create nella gestione dei servizi), regole NAT in ingresso (per le macchine virtuali create in Gestione risorse) e i gruppi di sicurezza di rete.
4.	Dispositivo periferico di Internet

Per i computer client che accedono all'applicazione tramite una connessione site-to-site VPN o ExpressRoute, le principali aree che possono causare problemi sono l'applicazione e la macchina virtuale di Azure. Per determinare l'origine del problema e la sua risoluzione, attenersi alla seguente procedura.

## Passaggio 1: È possibile accedere all'applicazione dalla macchina virtuale di destinazione?

Tentare di accedere all'applicazione con il programma client appropriato dalla macchina virtuale in cui è in esecuzione l'applicazione, utilizzare il nome host locale, l'indirizzo IP locale o l'indirizzo di loopback (127.0.0.1).

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access2.png)

Ad esempio, se l'applicazione è un server web, eseguire un browser nella macchina virtuale e tentare di accedere a una pagina web ospitata nella macchina virtuale.

Se è possibile accedere all'applicazione, passare al [Passaggio 2](#step2).

Se non è possibile accedere all'applicazione, verificare quanto segue:

- Che l’applicazione sia in esecuzione nella macchina virtuale.
- Che l'applicazione sia in ascolto sulle porte TCP e UDP previste.

Nelle macchine virtuali basate su Windows o Linux, utilizzare il comando **netstat - a** per visualizzare le porte di ascolto attive. Esaminare l'output per le porte previste sulle quali l’applicazione dovrebbe essere in ascolto. Riavviare l'applicazione oppure configurarla per utilizzare le porte previste in base alle esigenze.

## <a id="step2"></a>Passaggio 2: È possibile accedere all'applicazione da un'altra macchina virtuale nella stessa rete virtuale?

Tentare di accedere all'applicazione da un'altra macchina virtuale nella stessa rete virtuale come macchina virtuale in cui è in esecuzione l'applicazione utilizzando il nome host della macchina virtuale o l’indirizzo IP pubblico, privato o provider assegnato da Azure. Per le macchine virtuali create nella gestione dei servizi, non utilizzare l'indirizzo IP pubblico del servizio cloud.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access3.png)

Ad esempio, se l'applicazione è un server web, tentare di accedere a una pagina web da un browser presente in un’altra macchina virtuale nella stessa rete virtuale.

Se è possibile accedere all'applicazione, passare al [Passaggio 3](#step3).

Se non è possibile accedere all'applicazione, verificare quanto segue:

- Che il firewall host nella macchina virtuale di destinazione consenta il traffico della richiesta in ingresso e della risposta in uscita.
- Che il software per il rilevamento di intrusioni o per il monitoraggio della rete in esecuzione sulla macchina virtuale consenta il traffico.
- Che i Gruppi di sicurezza di rete consentano il traffico.
- Che un componente separato in esecuzione nella rete virtuale nel percorso tra la macchina virtuale di test e la macchina virtuale, ad esempio un servizio di bilanciamento del carico o un firewall, consenta il traffico.

In una macchina virtuale basata su Windows, utilizzare Windows Firewall con sicurezza avanzata per determinare se le regole del firewall escludono il traffico in entrata e in uscita dell'applicazione.

## <a id="step3"></a>Passaggio 3: È possibile accedere all'applicazione da un computer all'esterno della rete virtuale, ma non connesso alla stessa rete del computer?

Se si tenta di accedere all'applicazione da un computer all'esterno della rete virtuale come macchina virtuale in cui è in esecuzione l'applicazione, ma che non è presente nella stessa rete del computer client originale.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access4.png)

Ad esempio, se l'applicazione è un server web, tentare di accedere a una pagina web da un browser in esecuzione su un computer che non è presente nella rete virtuale.

Se non è possibile accedere all'applicazione, verificare quanto segue:

- Per le macchine virtuali create nella gestione dei servizi, che la configurazione dell'endpoint per la macchina virtuale consenta il traffico in entrata, in particolare il protocollo (TCP o UDP) e i numeri di porta pubblica e privata. Per altre informazioni, vedere la pagina [Come configurare gli endpoint a una macchina virtuale](virtual-machines-set-up-endpoints.md).
- Per le macchine virtuali create in gestione dei servizi, che gli elenchi di controllo di accesso (ACL) nell’endpoint non impediscano il traffico in entrata da internet. Per altre informazioni, vedere la pagina [Come configurare gli endpoint a una macchina virtuale](virtual-machines-set-up-endpoints.md).
- Per le macchine virtuali create in Gestione risorse, che la configurazione della regola NAT in entrata per la macchina virtuale consenta il traffico in entrata, in particolare il protocollo (TCP o UDP) e i numeri di porta pubblica e privata. 
- Che i Gruppi di sicurezza di rete consentano il traffico della richiesta in ingresso e della risposta in uscita. Per altre informazioni, vedere [Che cos’è un Gruppo di sicurezza di rete (NSG)?](virtual-networks-nsg.md).

Se la macchina virtuale o un endpoint è un membro di un set con carico bilanciato:

- Verificare che il protocollo di probe (TCP o UDP) e il numero di porta siano corretti.
- Se il protocollo e porta di probe è diverso rispetto al set con carico bilanciato protocollo e porta:
	- Verificare che l'applicazione sia in ascolto sul protocollo (TCP o UDP) e numero di porta di probe (utilizzare **netstat – a** sulla macchina virtuale di destinazione).
	- Che il firewall host nella macchina virtuale di destinazione consenta il traffico della richiesta del probe in ingresso e della risposta del probe in uscita.

Se è possibile accedere all'applicazione, verificare che il dispositivo periferico di Internet consenta:

- Il traffico della richiesta dell’applicazione in uscita dal computer client per la macchina virtuale di Azure.
- Il traffico di risposta dell’applicazione in ingresso dalla macchina virtuale di Azure.

## Passaggi successivi

Se si sono seguiti i passaggi da 1 a 3 in questo articolo e si desidera ulteriore assistenza per risolvere il problema, è possibile:

- Ottenere assistenza dagli esperti di Azure in tutto il mondo. Presentare il problema nei forum MSDN Azure o Stack Overflow. Per altre informazioni vedere [Forum di Microsoft Azure](http://azure.microsoft.com/support/forums/).
- Archiviare un incidente del supporto tecnico di Azure. Aprire il [sito del supporto tecnico di Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottieni supporto** in **Supporto tecnico e alla fatturazione**.

## Risorse aggiuntive

[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Risolvere i problemi di connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](virtual-machines-troubleshoot-ssh-connections.md)

<!---HONumber=August15_HO6-->
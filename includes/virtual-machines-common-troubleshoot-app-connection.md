


Se è impossibile accedere a un'applicazione in esecuzione in una macchina virtuale di Azure, in questo articolo viene descritto un approccio metodico per isolare l'origine del problema e risolverlo.

> [AZURE.NOTE]  Per supporto alla connessione a una macchina virtuale di Azure, vedere [Risolvere i problemi di connessione a Desktop remoto a una macchina virtuale di Azure basata su Windows](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) o [Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Andare al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.


Esistono quattro aree principali per risolvere i problemi di accesso di un'applicazione che è in esecuzione in una macchina virtuale di Azure.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1.	L’applicazione in esecuzione nella macchina virtuale di Azure.
2.	La macchina virtuale di Azure.
3.	Gli endpoint di Azure per il servizio cloud che contiene la macchina virtuale per le macchine nel modello di distribuzione classica, le regole NAT in ingresso per le macchine virtuali nel modello di distribuzione di Resource Manager e i gruppi di sicurezza di rete.
4.	Il dispositivo periferico di Internet.

Per i computer client che accedono all'applicazione tramite una connessione site-to-site VPN o ExpressRoute, le principali aree che possono causare problemi sono l'applicazione e la macchina virtuale di Azure. Per determinare l'origine del problema e la sua risoluzione, attenersi alla seguente procedura.

## Passaggio 1: È possibile accedere all'applicazione dalla macchina virtuale di destinazione?

Provare ad accedere all'applicazione con il programma client appropriato dalla macchina virtuale in cui è in esecuzione. Usare il nome host locale, l'indirizzo IP locale o l'indirizzo di loopback (127.0.0.1).

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Ad esempio, se l'applicazione è un server Web, eseguire un browser nella macchina virtuale e provare ad accedere a una pagina Web ospitata nella macchina virtuale.

Se è possibile accedere all'applicazione, passare al [Passaggio 2](#step2).

Se non è possibile accedere all'applicazione, verificare quanto segue:

- Che l’applicazione sia in esecuzione nella macchina virtuale.
- Che l'applicazione sia in ascolto sulle porte TCP e UDP previste.

Nelle macchine virtuali basate su Windows o Linux, utilizzare il comando **netstat - a** per visualizzare le porte di ascolto attive. Esaminare l'output per le porte previste sulle quali l’applicazione dovrebbe essere in ascolto. Riavviare l'applicazione oppure configurarla per utilizzare le porte previste in base alle esigenze.

## <a id="step2"></a>Passaggio 2: È possibile accedere all'applicazione da un'altra macchina virtuale nella stessa rete virtuale?

Provare ad accedere all'applicazione da una macchina virtuale diversa, ma nella stessa rete virtuale, usando il nome host della macchina virtuale o l'indirizzo IP pubblico, privato o del provider assegnato da Azure. Per le macchine virtuali create con il modello di distribuzione classica, non usare l'indirizzo IP pubblico del servizio cloud.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Ad esempio, se l'applicazione è un server Web, provare ad accedere a una pagina Web da un browser presente in un'altra macchina virtuale nella stessa rete virtuale.

Se è possibile accedere all'applicazione, passare al [Passaggio 3](#step3).

Se non è possibile accedere all'applicazione, verificare quanto segue:

- Il firewall host nella macchina virtuale di destinazione consente il traffico delle richieste in ingresso e delle risposte in uscita.
- Il software per il rilevamento intrusione o il monitoraggio di rete in esecuzione nella macchina virtuale di destinazione consente il traffico.
- I Gruppi di sicurezza di rete consentono il traffico.
- Un componente separato in esecuzione nella macchina virtuale nel percorso tra la macchina virtuale di test e la macchina virtuale, ad esempio un servizio di bilanciamento del carico o un firewall, consente il traffico.

In una macchina virtuale basata su Windows, utilizzare Windows Firewall con sicurezza avanzata per determinare se le regole del firewall escludono il traffico in entrata e in uscita dell'applicazione.

## <a id="step3"></a>Passaggio 3: È possibile accedere all'applicazione da un computer all'esterno della rete virtuale, ma non connesso alla stessa rete del computer?

Provare ad accedere all'applicazione da un computer all'esterno della rete virtuale come macchina virtuale in cui è in esecuzione l'applicazione, ma che non si trova nella stessa rete del computer client originale.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Ad esempio, se l'applicazione è un server web, tentare di accedere a una pagina web da un browser in esecuzione su un computer che non è presente nella rete virtuale.

Se non è possibile accedere all'applicazione, verificare quanto segue:

- Per le macchine virtuali create con il modello di distribuzione classica, verificare che la configurazione dell'endpoint per la macchina virtuale consenta il traffico in ingresso, in particolare il protocollo (TCP o UDP) e i numeri di porta pubblica e privata. Per altre informazioni, vedere la pagina [Come configurare gli endpoint a una macchina virtuale](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).
- Per le macchine virtuali create con il modello di distribuzione classica, verificare che gli elenchi di controllo di accesso (ACL) nell'endpoint non impediscano il traffico in ingresso da Internet. Per altre informazioni, vedere la pagina [Come configurare gli endpoint a una macchina virtuale](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).
- Per le macchine virtuali create con il modello di distribuzione di Resource Manager, verificare che la configurazione della regola NAT in ingresso per la macchina virtuale consenta il traffico in ingresso, in particolare il protocollo (TCP o UDP) e i numeri di porta pubblica e privata.
- Che i Gruppi di sicurezza di rete consentano il traffico della richiesta in ingresso e della risposta in uscita. Per altre informazioni, vedere [Che cos’è un Gruppo di sicurezza di rete (NSG)?](../articles/virtual-network/virtual-networks-nsg.md).

Se la macchina virtuale o un endpoint è un membro di un set con carico bilanciato:

- Verificare che il protocollo di probe (TCP o UDP) e il numero di porta siano corretti.
- Se il protocollo e porta di probe è diverso rispetto al set con carico bilanciato protocollo e porta:
	- Verificare che l'applicazione sia in ascolto sul protocollo probe (TCP o UDP) e il numero di porta, usare **netstat –a** nella macchina virtuale di destinazione.
	- Verificare che il firewall host nella macchina virtuale di destinazione consenta il traffico delle richieste probe in ingresso e delle risposte probe in uscita.

Se è possibile accedere all'applicazione, verificare che il dispositivo periferico di Internet consenta:

- Il traffico della richiesta dell’applicazione in uscita dal computer client per la macchina virtuale di Azure.
- Il traffico di risposta dell’applicazione in ingresso dalla macchina virtuale di Azure.

## Risoluzione dei problemi di connettività dell’Endpoint

Se si verificano problemi durante la connessione a un Endpoint come ad esempio l’Endpoint di Desktop remoto, è possibile provare la seguente procedura di risoluzione dei problemi generali:

- Riavviare la macchina virtuale
- Ricreare l’endpoint
- Connettersi da una posizione diversa
- Ridimensionare la macchina virtuale
- Ricreare la macchina virtuale

Per ulteriori informazioni, vedere [Risoluzione dei problemi di connettività dell’Endpoint (errori RDP/SSH/HTTP, ecc.)](https://social.msdn.microsoft.com/Forums/azure/it-IT/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).



## Risorse aggiuntive

[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md)

[Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md)

<!---HONumber=AcomDC_0413_2016-->
Ci sono varie ragioni alla base dell'impossibilità di avviare o di connettersi a un'applicazione in esecuzione su una macchina virtuale (VM) di Azure. I motivi includono un'applicazione non in esecuzione o in attesa sulle porte previste, la porta di ascolto bloccata o regole di rete che non consentono la trasmissione corretta del traffico all'applicazione. In questo articolo viene descritto un approccio metodico per rilevare e correggere il problema.

Se si sono verificati problemi durante la connessione alla VM tramite RDP o SSH, consultare prima uno dei seguenti articoli:

 - [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md)
 - [Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md)

> [AZURE.NOTE] Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../articles/resource-manager-deployment-model.md). In questo articolo viene illustrato l'utilizzo di entrambi i modelli, ma Microsoft indica che le distribuzioni più nuove utilizzano il modello di gestione delle.

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiedi supporto**.

## Risoluzione rapida dei problemi di connettività dell'Endpoint

Nel caso di problemi di connessione a un'applicazione, provare con i seguenti passaggi generali per la risoluzione dei problemi. Dopo ogni passaggio, provare a connettersi nuovamente all'applicazione:

- Riavviare la macchina virtuale
- Ricreare l'endpoint/le regole del firewall/le regole del gruppo di sicurezza di rete
	- [Modello Classico: Gestire gli endpoint dei servizi cloud](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
	- [Modello di Resource Manager: Gestire gruppi di sicurezza di rete](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
- Connettersi da un percorso diverso, ad esempio da una diversa rete virtuale di Azure
- Ridistribuire la macchina virtuale
	- [Ridistribuire una VM Windows](../articles/virtual-machines/virtual-machines-windows-redeploy-to-new-node.md)
	- [Ridistribuire una VM Linux](../articles/virtual-machines/virtual-machines-linux-redeploy-to-new-node.md)
- Ricreare la macchina virtuale

Per ulteriori informazioni, vedere [Risoluzione dei problemi di connettività dell’Endpoint (errori RDP/SSH/HTTP, ecc.)](https://social.msdn.microsoft.com/Forums/azure/it-IT/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## Panoramica dettagliata della risoluzione dei problemi

Esistono quattro aree principali per risolvere i problemi di accesso di un'applicazione che è in esecuzione in una macchina virtuale di Azure.

![risolvere il problema che impedisce l'avvio dell'applicazione](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1.	L’applicazione in esecuzione nella macchina virtuale di Azure.
	- L'applicazione viene eseguita correttamente?
2.	La macchina virtuale di Azure.
	- La macchina virtuale viene eseguita correttamente e soddisfa le richieste?
3.	Endpoint di rete di Azure.
	- Endpoint di servizio cloud per le macchine virtuali nel modello di distribuzione classica.
	- Gruppi di sicurezza di rete e regole NAT in ingresso per le macchine virtuali nel modello di distribuzione Resource Manager.
	- Il traffico riesce a fluire dagli utenti alla macchina virtuale/applicazione sulle porte previste?
4.	Il dispositivo periferico di Internet.
	- Le regole del firewall applicate impediscono al traffico di fluire in modo corretto?

Per i computer client che accedono all'applicazione tramite una connessione site-to-site VPN o ExpressRoute, le principali aree che possono causare problemi sono l'applicazione e la macchina virtuale di Azure. Per determinare l'origine del problema e la sua risoluzione, attenersi alla seguente procedura.

## Passaggio 1: accedere all'applicazione dalla VM di destinazione

Provare ad accedere all'applicazione con il programma client appropriato dalla macchina virtuale in cui è in esecuzione. Usare il nome host locale, l'indirizzo IP locale o l'indirizzo di loopback (127.0.0.1).

![avviare l'applicazione direttamente dalla macchina virtuale](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Ad esempio, se l'applicazione è un server Web, aprire un browser nella VM e provare ad accedere a una pagina Web ospitata nella VM.

Se è possibile accedere all'applicazione, passare al [Passaggio 2](#step2).

Se non è possibile accedere all'applicazione, verificare le seguenti impostazioni:

- Che l’applicazione sia in esecuzione nella macchina virtuale.
- Che l'applicazione sia in ascolto sulle porte TCP e UDP previste.

Nelle macchine virtuali basate su Windows o Linux, utilizzare il comando **netstat - a** per visualizzare le porte di ascolto attive. Esaminare l'output per le porte previste sulle quali l’applicazione dovrebbe essere in ascolto. Riavviare l'applicazione oppure configurarla per usare le porte previste in base alle esigenze e provare di nuovo ad accedere all'applicazione in locale.

## <a id="step2"></a>Passaggio 2: accedere all'applicazione da un'altra VM della stessa rete virtuale

Provare ad accedere all'applicazione da una macchina virtuale diversa, ma nella stessa rete virtuale, usando il nome host della macchina virtuale o l'indirizzo IP pubblico, privato o del provider assegnato da Azure. Per le macchine virtuali create con il modello di distribuzione classica, non usare l'indirizzo IP pubblico del servizio cloud.

![applicazione avviata da una macchina virtuale diversa](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Ad esempio, se l'applicazione è un server Web, provare ad accedere a una pagina Web da un browser presente in un'altra macchina virtuale nella stessa rete virtuale.

Se è possibile accedere all'applicazione, passare al [Passaggio 3](#step3).

Se non è possibile accedere all'applicazione, verificare le seguenti impostazioni:

- Il firewall host nella macchina virtuale di destinazione consente il traffico delle richieste in ingresso e delle risposte in uscita.
- Il software per il rilevamento intrusione o il monitoraggio di rete in esecuzione nella macchina virtuale di destinazione consente il traffico.
- Gli endpoint dei servizi cloud o i gruppi di sicurezza di rete consentono il traffico.
	- [Modello Classico: Gestire gli endpoint dei servizi cloud](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
	- [Modello di Resource Manager: Gestire gruppi di sicurezza di rete](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
- Un componente separato in esecuzione nella macchina virtuale nel percorso tra la macchina virtuale di test e la macchina virtuale, ad esempio un servizio di bilanciamento del carico o un firewall, consente il traffico.

In una macchina virtuale basata su Windows, utilizzare Windows Firewall con sicurezza avanzata per determinare se le regole del firewall escludono il traffico in entrata e in uscita dell'applicazione.

## <a id="step3"></a>Passaggio 3: accedere all'applicazione dall'esterno della rete virtuale

Provare ad accedere all'applicazione da un computer all'esterno della rete virtuale come VM in cui è in esecuzione l'applicazione. Usare una rete diversa come computer client originale.

![avviare l'applicazione da un computer all'esterno della rete virtuale](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)  

Ad esempio, se l'applicazione è un server web, tentare di accedere a una pagina web da un browser in esecuzione su un computer che non è presente nella rete virtuale.

Se non è possibile accedere all'applicazione, verificare le seguenti impostazioni:

- VM create con il modello di distribuzione classica:
	- La configurazione dell'endpoint per la VM consente il traffico in ingresso, in particolare il protocollo (TCP o UDP) e i numeri di porta pubblica e privata.
	- Gli elenchi di controllo di accesso (ACL) nell'endpoint non impediscono il traffico in ingresso da Internet.
	- Per altre informazioni, vedere la pagina [Come configurare gli endpoint a una macchina virtuale](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

- Per le VM create con il modello di distribuzione di Resource Manager:
	- La configurazione della regola NAT in ingresso consente il traffico in ingresso, in particolare il protocollo (TCP o UDP) e i numeri di porta pubblica e privata.
	- Che i Gruppi di sicurezza di rete consentano il traffico della richiesta in ingresso e della risposta in uscita.
	- Per altre informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../articles/virtual-network/virtual-networks-nsg.md).

Se la macchina virtuale o un endpoint è un membro di un set con carico bilanciato:

- Verificare che il protocollo di probe (TCP o UDP) e il numero di porta siano corretti.
- Se il protocollo e porta di probe è diverso rispetto al set con carico bilanciato protocollo e porta:
	- Verificare che l'applicazione sia in ascolto sul protocollo probe (TCP o UDP) e il numero di porta. Usare **netstat –a** nella VM di destinazione.
	- Verificare che il firewall host nella macchina virtuale di destinazione consenta il traffico delle richieste probe in ingresso e delle risposte probe in uscita.

Se è possibile accedere all'applicazione, verificare che il dispositivo periferico di Internet consenta:

- Il traffico della richiesta dell’applicazione in uscita dal computer client per la macchina virtuale di Azure.
- Il traffico di risposta dell’applicazione in ingresso dalla macchina virtuale di Azure.

## Risorse aggiuntive

[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](../articles/virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md)

[Risolvere i problemi relativi alle connessioni Secure Shell (SSH) a una macchina virtuale di Azure basata su Linux](../articles/virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md)

<!---HONumber=AcomDC_0928_2016-->
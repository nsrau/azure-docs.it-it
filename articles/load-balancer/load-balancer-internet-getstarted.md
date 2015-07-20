
<properties
   pageTitle="Introduzione al bilanciamento del carico Internet | Microsoft Azure"
   description="Configurare il primo servizio di bilanciamento del carico Internet per le macchine virtuali o i servizi cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# Introduzione alla configurazione del bilanciamento del carico Internet

I servizi di bilanciamento del carico in Microsoft Azure funzionano con tutti i tipi di tenant (IaaS o PaaS) e tutte le versioni dei sistemi operativi (Windows o qualsiasi sistema operativo basato su Linux).


## Configurazione del bilanciamento del carico Internet per le macchine virtuali

Per bilanciare il carico del traffico di rete Internet tra le macchine virtuali di un servizio cloud, è necessario creare un set con carico bilanciato. Questa procedura presuppone che le macchine virtuali siano già state create e che si trovino tutte nello stesso servizio cloud.

Attenersi alla procedura seguente per configurare un set di bilanciamento del carico per le macchine virtuali:

1. Nel portale di gestione di Azure fare clic su Macchine virtuali e quindi sul nome di una macchina virtuale nel set con carico bilanciato.
2.	Fare clic su Endpoint, quindi su Aggiungi.

4.	Nella pagina Aggiungi un endpoint a una macchina virtuale fare clic sulla freccia destra.

4.	Nella pagina Specificare i dettagli dell'endpoint eseguire le operazioni seguenti:
	- In Nome digitare un nome per l'endpoint o selezionarne uno dall'elenco di endpoint predefiniti per i protocolli comuni.
	-  In Protocollo selezionare il protocollo richiesto dal tipo di endpoint, ossia TCP o UDP.
 	-  In Porta pubblica e Porta privata digitare il numero di porta da utilizzare per la macchina virtuale. È possibile usare le regole relative a porte private e firewall sulla macchina virtuale per reindirizzare il traffico in modo appropriato per l'applicazione. La porta privata può essere uguale alla porta pubblica. Per un endpoint per il traffico Web (HTTP), è ad esempio possibile assegnare la porta 80 sia come porta pubblica che privata.

5.	Selezionare Crea un set con carico bilanciato e quindi fare clic sulla freccia destra.

6.	Nella pagina Configura il set con carico bilanciato immettere un nome per il set con carico bilanciato e quindi assegnare i valori per il comportamento del probe del servizio di bilanciamento del carico di Azure. Il servizio di bilanciamento del carico usa i probe per determinare se le macchine virtuali del set con carico bilanciato sono disponibili a ricevere traffico in ingresso.

7.	Fare clic sul segno di spunta per creare l'endpoint con carico bilanciato. Nella colonna Nome del set con carico bilanciato della pagina Endpoint relativa alla macchina virtuale verrà visualizzata l'indicazione Sì.

8.	Nel portale di gestione fare clic su Macchine virtuali, quindi sul nome di una macchina virtuale aggiuntiva nel set con carico bilanciato, fare clic su Endpoint e infine su Aggiungi.

9.	Nella pagina Aggiungi un endpoint a una macchina virtuale fare clic su Aggiungi un endpoint a un set con carico bilanciato esistente, selezionare il nome del set con carico bilanciato e quindi fare clic sulla freccia destra.

10.	Nella pagina Specificare i dettagli dell'endpoint digitare un nome per l'endpoint e quindi fare clic sul segno di spunta. Per le macchine virtuali aggiuntive nel set con carico bilanciato, ripetere i passaggi da 8 a 10.

È anche possibile configurare gli endpoint con i cmdlet di Windows PowerShell seguenti:

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## Configurazione del bilanciamento del carico Internet per i servizi cloud


I servizi cloud vengono configurati automaticamente con il bilanciamento del carico e possono essere personalizzati tramite il modello del servizio

È possibile usare Azure SDK per .NET 2.5 per aggiornare il servizio cloud. Le impostazioni degli endpoint per i servizi cloud vengono effettuate nel file di [definizione del servizio](https://msdn.microsoft.com/library/azure/gg557553.aspx), con estensione csdef.

L'esempio seguente mostra la configurazione di un file servicedefinition.csdef per una distribuzione cloud:

Analizzando il frammento di codice per il file con estensione csdef generato da una distribuzione cloud, è possibile vedere l'endpoint esterno configurato per usare le porte HTTP sulle porte 10000, 10001 e 10002.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### Controllo dello stato di integrità del bilanciamento del carico per i servizi cloud


Di seguito è riportato un esempio di probe di integrità:

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

Il bilanciamento del carico combina le informazioni dell'endpoint e le informazioni del probe per creare un URL nel formato http://{DIP della VM}:80/Probe.aspx, che verrà usato per eseguire una query per l'integrità del servizio.

Il servizio rileverà l'accesso periodico da parte dello stesso IP. Si tratta della richiesta del probe di integrità proveniente dall'host del nodo in cui è in esecuzione la macchina virtuale. Il servizio deve rispondere con un codice di stato HTTP 200 per indicare l'integrità al bilanciamento del carico. Qualsiasi altro codice di stato HTTP (ad esempio 503) esclude direttamente la macchina virtuale dalla rotazione.

La definizione del probe ne controlla anche la frequenza. Nel caso precedente, il bilanciamento del carico controlla l'endpoint tramite probe ogni 5 secondi. Se non viene ricevuta alcuna risposta positiva per 10 secondi (due intervalli di probe), si presuppone che il probe abbia avuto esito negativo e la macchina virtuale viene esclusa dalla rotazione. Analogamente, se il servizio è escluso dalla rotazione e viene ricevuta una risposta positiva, il servizio viene immediatamente inserito di nuovo nella rotazione. Se il servizio passa continuamente da uno stato integro a uno non integro, il bilanciamento del carico può decidere di ritardare la reintroduzione del servizio nella rotazione fino a quando non risulta integro per un determinato numero di probe.

Per altre informazioni, fare riferimento allo schema di definizione del servizio per il [probe di integrità](https://msdn.microsoft.com/library/azure/jj151530.aspx).

## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-internal-getstarted.md)

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->
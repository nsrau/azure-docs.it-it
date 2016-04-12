<properties 
   pageTitle="Introduzione alla creazione del servizio di bilanciamento del carico per Internet nel modello di distribuzione classica per i servizi cloud | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico Internet nel modello di distribuzione classica per i servizi cloud"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Introduzione alla creazione del servizio di bilanciamento del carico Internet per i servizi cloud

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-cli.md).

I servizi cloud vengono configurati automaticamente con il servizio di bilanciamento del carico e possono essere personalizzati tramite il modello del servizio.

## Creare un bilanciamento del carico tramite il file di definizione del servizio
 
È possibile usare Azure SDK per .NET 2.5 per aggiornare il servizio cloud. Le impostazioni degli endpoint per i servizi cloud vengono effettuate nel file di [definizione del servizio](https://msdn.microsoft.com/library/azure/gg557553.aspx), con estensione .csdef.

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




## Controllo dello stato di integrità del servizio di bilanciamento del carico per i servizi cloud


Di seguito è riportato un esempio di probe di integrità:

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

Il servizio di bilanciamento del carico combina le informazioni dell'endpoint e le informazioni del probe per creare un URL nel formato http://{DIP della VM}:80/Probe.aspx, che verrà usato per eseguire una query per l'integrità del servizio.

Il servizio rileva probe periodiche dallo stesso indirizzo IP. Si tratta della richiesta del probe di integrità proveniente dall'host del nodo in cui è in esecuzione la macchina virtuale. Il servizio deve rispondere con un codice di stato HTTP 200 per indicare l'integrità al bilanciamento del carico. Qualsiasi altro codice di stato HTTP (ad esempio 503) esclude direttamente la macchina virtuale dalla rotazione.

La definizione del probe ne controlla anche la frequenza. Nel caso precedente, il bilanciamento del carico controlla l'endpoint tramite probe ogni 5 secondi. Se non viene ricevuta alcuna risposta positiva per 10 secondi (due intervalli di probe), si presuppone che il probe abbia avuto esito negativo e la macchina virtuale viene esclusa dalla rotazione. Analogamente, se il servizio è escluso dalla rotazione e viene ricevuta una risposta positiva, il servizio viene immediatamente inserito di nuovo nella rotazione. Se il servizio passa continuamente da uno stato integro a uno non integro, il servizio di bilanciamento del carico può decidere di ritardare la reintroduzione del servizio nella rotazione fino a quando non risulta integro per un determinato numero di probe.

Per altre informazioni, fare riferimento allo schema di definizione del servizio per il [probe di integrità](https://msdn.microsoft.com/library/azure/jj151530.aspx).

## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0323_2016-->
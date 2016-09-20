<properties
   pageTitle="Abilitare l'accesso pubblico a un'app del servizio contenitore di Azure | Microsoft Azure"
   description="Come abilitare l'accesso pubblico a un servizio contenitore di Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="adegeo"/>  

# Abilitare l'accesso pubblico a un'applicazione del servizio contenitore di Azure

Qualsiasi contenitore DC/OS nel [pool di agenti pubblico](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) del servizio contenitore di Azure viene esposto automaticamente a Internet. Per impostazione predefinita, le porte **80**, **443**, **8080** sono aperte e qualsiasi contenitore (pubblico) in ascolto su queste porte è accessibile. Questo articolo descrive come aprire altre porte per le applicazioni nel servizio contenitore di Azure.

## Aprire una porta (portale) 

Prima di tutto è necessario aprire la porta desiderata.

1. Accedere al portale.
2. Trovare il gruppo di risorse in cui è stato distribuito il servizio contenitore di Azure.
3. Selezionare il servizio di bilanciamento del carico dell'agente, che ha un nome simile a **XXXX-agent-lb-XXXX**.

    ![Servizio di bilanciamento del carico del servizio contenitore di Azure](media/container-service-dcos-agents/agent-load-balancer.png)  

4. Fare clic su **Probe** e quindi su **Aggiungi**.

    ![Probe del servizio di bilanciamento del carico del servizio contenitore di Azure](media/container-service-dcos-agents/add-probe.png)  

5. Compilare il form dei probe e fare clic su **OK**.

    | Campo | Descrizione |
    | ----- | ----------- |
    | Nome | Nome descrittivo del probe. |
    | Port | Porta del contenitore da testare. |
    | Path | (In modalità HTTP) Percorso relativo del sito Web su cui eseguire probe. HTTPS non è supportato. |
    | Interval | Intervallo di tempo tra i tentativi del probe, in secondi. |
    | Soglia non integra | Numero di tentativi consecutivi del probe prima che il contenitore sia considerato non integro. | 
    

6. Tornare alle proprietà del servizio di bilanciamento del carico dell'agente, fare clic su **Regole di bilanciamento del carico** e quindi su **Aggiungi**.

    ![Regole del servizio di bilanciamento del carico del servizio contenitore di Azure](media/container-service-dcos-agents/add-balancer-rule.png)  

7. Compilare il modulo del servizio di bilanciamento del carico e fare clic su **OK**.

    | Campo | Descrizione |
    | ----- | ----------- |
    | Nome | Nome descrittivo del servizio di bilanciamento del carico. |
    | Port | Porta pubblica in ingresso. |
    | Porta back-end | Porta pubblica interna del contenitore a cui instradare il traffico. |
    | Pool back-end | I contenitori in questo pool saranno la destinazione per questo servizio di bilanciamento del carico. |
    | Probe | Probe usato per determinare se una destinazione nel **Pool back-end** è integra. |
    | Persistenza della sessione | Determina la modalità di gestione del traffico da un client per la durata della sessione.<br><br>**Nessuno**: le richieste successive provenienti dallo stesso client possono essere gestite da qualsiasi contenitore.<br>**IP client**: le richieste successive provenienti dallo stesso indirizzo IP client IP vengono gestite dallo stesso contenitore.<br>**IP e protocollo client**: le richieste successive provenienti dalla stessa combinazione di indirizzo IP e protocollo client vengono gestite dallo stesso contenitore. |
    | Timeout di inattività | (Solo TCP) Tempo necessario, in minuti, per mantenere aperto un client TCP/HTTP aprire senza basarsi sui messaggi *keep-alive*. |

## Aggiungere una regola di sicurezza (portale)

Successivamente, è necessario aggiungere una regola di sicurezza che instradi il traffico dalla porta aperta tramite il firewall.

1. Accedere al portale.
2. Trovare il gruppo di risorse in cui è stato distribuito il servizio contenitore di Azure.
3. Selezionare il gruppo di sicurezza di rete dell'agente **pubblico**, che ha un nome simile a **XXXX-agent-public-nsg-XXXX**.

    ![Gruppo di sicurezza di rete del servizio contenitore di Azure](media/container-service-dcos-agents/agent-nsg.png)  

4. Selezionare **Regole di sicurezza in ingresso** e quindi fare clic su **Aggiungi**.

    ![Regole del gruppo di sicurezza di rete del servizio contenitore di Azure](media/container-service-dcos-agents/add-firewall-rule.png)  

5. Compilare la regola del firewall per consentire la porta pubblica e fare clic su **OK**.

    | Campo | Descrizione |
    | ----- | ----------- |
    | Nome | Nome descrittivo della regola del firewall. |
    | Priorità | Classificazione di priorità per la regola. Più è basso il numero, maggiore sarà la priorità. |
    | Sorgente | Consente di limitare l'intervallo di indirizzi IP in ingresso che la regola dovrà consentire o negare. Usare **Qualsiasi** per non specificare una restrizione. |
    | Service | Selezionare un set di servizi predefiniti a cui è destinata questa regola di sicurezza. In caso contrario, usare **Personalizzato** per crearne di personalizzati. |
    | Protocol | Consente di limitare il traffico in base a **TCP** o **UDP**. Usare **Qualsiasi** per non specificare una restrizione. |
    | Intervallo di porte | Quando **Servizio** è **Personalizzato**, specifica l'intervallo di porte interessato da questa regola. È possibile usare una singola porta, ad esempio **80**, o un intervallo come **1024 1500**. |
    | Azione | Consentire o negare il traffico che soddisfa i criteri. |

## Passaggi successivi

Informazioni sulle differenze tra [agenti DC/OS pubblici e privati](container-service-dcos-agents.md).

Sono disponibili altre informazioni sulla [gestione dei contenitori DC/OS](container-service-mesos-marathon-ui.md).

<!---HONumber=AcomDC_0907_2016-->
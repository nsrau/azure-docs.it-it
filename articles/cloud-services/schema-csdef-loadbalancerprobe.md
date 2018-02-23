---
title: Schema LoadBalancerProbe di definizione di Servizi cloud di Azure | Microsoft Docs
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 31c974c5a4b9dc9cff882ff42b73ee023fc4ad9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Schema LoadBalancerProbe di definizione di Servizi cloud di Azure
Il probe di bilanciamento del carico è un probe di integrità definito dal cliente per gli endpoint UDP e gli endpoint nelle istanze del ruolo. `LoadBalancerProbe` non è un elemento autonomo. Viene combinato con il ruolo Web o il ruolo di lavoro in un file di definizione del servizio. `LoadBalancerProbe` può essere usato da più di un ruolo.

L'estensione predefinita per il file di definizione del servizio è csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funzione di un probe di bilanciamento del carico
Azure Load Balancer è responsabile del routing del traffico in ingresso alle istanze del ruolo. Il servizio di bilanciamento del carico determina quali istanze possono ricevere il traffico controllando regolarmente ogni istanza per determinarne l'integrità. Il servizio di bilanciamento del carico controlla ogni istanza più volte al minuto. Sono disponibili due opzioni diverse per fornire l'integrità di un'istanza al servizio di bilanciamento del carico: il probe di bilanciamento del carico predefinito o un probe di bilanciamento del carico personalizzato, che viene implementato definendo LoadBalancerProbe nel file con estensione csdef.

Il probe di bilanciamento del carico predefinito utilizza l'agente guest nella macchina virtuale, che rimane in ascolto e invia una risposta HTTP 200 OK solo quando l'istanza è nello stato Pronto, ad esempio quando l'istanza non è negli stati Occupato, Riciclo in corso, Arresto e così via. Se l'agente guest non risponde con un messaggio HTTP 200 OK, Azure Load Balancer contrassegna l'istanza che non risponde e arresta l'invio di traffico a tale istanza. Azure Load Balancer continua a effettuare il ping dell'istanza e, se l'agente guest risponde con HTTP 200, Azure Load Balancer invia di nuovo il traffico a tale istanza. Quando si usa un ruolo Web, il codice del sito Web in genere viene eseguito in w3wp.exe, che non viene monitorato dall'infrastruttura di Azure o dall'agente guest e quindi gli errori in w3wp.exe, ad esempio le risposte HTTP 500, non vengono segnalati all'agente guest e il servizio di bilanciamento del carico non sa di dover escludere l'istanza dalla rotazione.

Il probe personalizzato del servizio di bilanciamento del carico esegue l'override del probe dell'agente guest predefinito e consente di creare una logica personalizzata per determinare l'integrità dell'istanza del ruolo. Il servizio di bilanciamento del carico controlla regolarmente l'endpoint (per impostazione predefinita, ogni 15 secondi) e l'istanza viene considerata nella rotazione se risponde con TCP ACK o HTTP 200 entro il periodo di timeout (per impostazione predefinita, 31 secondi). Può essere utile per implementare la propria logica per rimuovere le istanze dalla rotazione del servizio di bilanciamento del carico, ad esempio restituendo uno stato diverso da 200 se l'istanza usa più del 90% della CPU. Per i ruoli Web che usano w3wp.exe si ottiene anche il monitoraggio automatico del sito Web, perché gli errori nel codice del sito Web restituiscono uno stato diverso da 200 al probe del servizio di bilanciamento del carico. Se non si definisce LoadBalancerProbe nel file con estensione csdef, viene usato il comportamento predefinito del servizio di bilanciamento del carico (come descritto prima).

Se si usa un probe personalizzato di bilanciamento del carico, è necessario assicurarsi che la logica prenda in considerazione il metodo RoleEnvironment.OnStop. Quando si usa il probe predefinito di bilanciamento del carico, l'istanza viene esclusa dalla rotazione prima della chiamata a OnStop, ma un probe personalizzato di bilanciamento del carico può continuare a restituire 200 OK durante l'evento OnStop. Se si usa l'evento OnStop per pulire la cache, arrestare il servizio o apportare altre modifiche che possono influire sul comportamento di runtime del servizio, è necessario assicurarsi che la logica del probe personalizzato di bilanciamento del carico rimuova l'istanza dalla rotazione.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Schema di definizione del servizio di base per un probe di bilanciamento del carico
 Il formato di base di un file di definizione del servizio contenente un probe di bilanciamento del carico è il seguente.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementi dello schema
L'elemento `LoadBalancerProbes` del file di definizione del servizio include gli elementi seguenti:

- [Elemento LoadBalancerProbes](#LoadBalancerProbes)
- [Elemento LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> Elemento LoadBalancerProbes
L'elemento `LoadBalancerProbes` descrive la raccolta di probe di bilanciamento del carico. Questo è l'elemento padre dell'[elemento LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> Elemento LoadBalancerProbe
L'elemento `LoadBalancerProbe` definisce il probe di integrità per un modello. È possibile definire più probe di bilanciamento del carico. 

La tabella seguente descrive gli attributi dell'elemento `LoadBalancerProbe`:

|Attributo|type|DESCRIZIONE|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Richiesto. Nome del probe di bilanciamento del carico. Il nome deve essere univoco.|
| `protocol`          | `string` | Richiesto. Specifica il protocollo dell'endpoint. I possibili valori sono `http` o `tcp`. Se viene specificato `tcp`, è necessario ricevere una risposta ACK per completare il probe. Se viene specificato `http`, è necessario ricevere una risposta 200 OK dall'URI specificato per completare il probe.|
| `path`              | `string` | URI usato per richiedere lo stato di integrità alla macchina virtuale. `path` è obbligatorio se `protocol` impostato su `http`. In caso contrario, non è consentito.<br /><br /> Non esistono valori predefiniti.|
| `port`              | `integer` | facoltativo. Porta per la comunicazione del probe. È facoltativo per qualsiasi endpoint, perché verrà usata la stessa porta per il probe. È anche possibile configurare una porta diversa per il probe. L'intervallo di valori possibili è compresa tra 1 e 65535 inclusi.<br /><br /> Il valore predefinito viene impostato dall'endpoint.|
| `intervalInSeconds` | `integer` | facoltativo. Intervallo, in secondi, per la frequenza con cui controllare lo stato di integrità dell'endpoint. L'intervallo è in genere leggermente inferiore alla metà del periodo di timeout allocato (in secondi) il che consente due probe completi prima di escludere l'istanza dalla rotazione.<br /><br /> Il valore predefinito è 15, il valore minimo è 5.|
| `timeoutInSeconds`  | `integer` | facoltativo. Periodo di timeout, in secondi, applicato al probe, entro il quale nessuna risposta interromperà l'invio di altro traffico all'endpoint. Questo valore consente agli endpoint di essere esclusi dalla rotazione in tempi più rapidi o più brevi di quelli abitualmente usati in Azure (valori predefiniti).<br /><br /> Il valore predefinito è 31, il valore minimo è 11.|

## <a name="see-also"></a>Vedere anche
[Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Schema di definizione di Servizi cloud - Versione classica)
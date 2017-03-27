---
title: Tipi di endpoint di Gestione traffico | Documentazione Microsoft
description: "Questo articolo illustra tipi diversi di endpoint che è possibile usare con Gestione traffico di Azure"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: c27b6ed05faa5d9c408e6812d4ecbb8e0e2bbbab
ms.lasthandoff: 03/17/2017

---

# <a name="traffic-manager-endpoints"></a>Endpoint di Gestione traffico
Gestione traffico di Microsoft Azure consente di controllare la distribuzione del traffico di rete a distribuzioni di applicazioni in esecuzione in diversi data center. In Gestione traffico ogni distribuzione di applicazioni viene configurata come "endpoint". Quando Gestione traffico riceve una richiesta DNS, sceglie un endpoint disponibile da restituire nella risposta DNS. Gestione traffico basa la scelta sullo stato dell'endpoint corrente e sul metodo di routing del traffico. Per altre informazioni, vedere [Modalità di funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).

Gli endpoint supportati da Gestione traffico sono di tre tipi:

* **Endpoint di Azure** , usati per i servizi ospitati in Azure.
* **Endpoint esterni** , usati per i servizi ospitati all'esterno di Azure, in locale o da un provider di hosting diverso.
* **Endpoint annidati** , usati per combinare i profili di Gestione traffico e creare schemi di routing del traffico più flessibili, per supportare le esigenze di distribuzioni più grandi e complesse.

Non ci sono limitazioni al modo in cui è possibile combinare tipi di endpoint diversi in un unico profilo di Gestione traffico. Ogni profilo può contenere qualsiasi combinazione di tipi di endpoint.

Le sezioni seguenti descrivono i singoli tipi di endpoint in modo più approfondito.

## <a name="azure-endpoints"></a>Endpoint di Azure

In Gestione traffico gli endpoint di Azure vengono usati per i servizi basati su Azure. Sono supportati i tipi di risorse di Azure seguenti:

* Macchine virtuali IaaS "classiche" e servizi cloud PaaS.
* App Web
* Risorse PublicIPAddress, che possono essere collegate alle macchine virtuali direttamente o tramite Azure Load Balancer. È necessario che al valore publicIpAddress sia assegnato un nome DNS, da usare in un profilo di Gestione traffico.

Le risorse PublicIPAddress sono risorse di Azure Resource Manager. Tali risorse non sono presenti nel modello di distribuzione classica. Sono quindi supportate unicamente nelle esperienze di Gestione traffico di tipo Azure Resource Manager. Gli altri tipi di endpoint sono supportati mediante Resource Manager e il modello di distribuzione classica.

Quando si usano gli endpoint di Azure, Gestione trafficorileva l'arresto o l'avvio di una macchina virtuale IaaS "classica", di un servizio cloud o di un'app Web. Questo stato si riflette nello stato dell'endpoint. Per altri dettagli, vedere [Monitoraggio e failover degli endpoint di Gestione traffico](traffic-manager-monitoring.md#endpoint-and-profile-status). Quando il servizio sottostante viene arrestato, Gestione traffico non esegue controlli di integrità dell'endpoint e non indirizza il traffico all'endpoint stesso. Per l'istanza arrestata non si verifica alcun evento di fatturazione di Gestione traffico. Quando il servizio viene riavviato, la fatturazione riprende e l'endpoint è di nuovo idoneo a ricevere il traffico. Questo rilevamento non è applicabile agli endpoint PublicIpAddress.

## <a name="external-endpoints"></a>Endpoint esterni

Gli endpoint esterni vengono usati per i servizi esterni a Azure. Può trattarsi, ad esempio, di un servizio ospitato in locale o da un provider diverso. Gli endpoint esterni possono essere usati singolarmente o in combinazione con endpoint di Azure nello stesso profilo di Gestione traffico. La combinazione di endpoint di Azure con endpoint esterni consente un'ampia gamma di scenari:

* In un modello di failover attivo-attivo o attivo-passivo, uso di Azure per offrire maggiore ridondanza per un'applicazione locale esistente.
* Per ridurre la latenza dell'applicazione per utenti attivi in diversi paesi, possibilità di estendere un'applicazione locale esistente ad aree geografiche aggiuntive in Azure. Per altre informazioni, vedere [Metodo di routing del traffico Prestazioni](traffic-manager-routing-methods.md#performance-traffic-routing-method).
* Uso di Azure per offrire capacità aggiuntiva a un'applicazione locale esistente, in modo continuo o come soluzione "burst nel cloud" per gestire un picco di domanda.

In alcuni casi può essere utile usare endpoint esterni per fare riferimento a servizi di Azure. Per alcuni esempi, vedere la sezione [Domande frequenti](#faq). In questo caso, i controlli di integrità vengono fatturati in base alla tariffa degli endpoint di Azure anziché in base alla tariffa degli endpoint esterni. Tuttavia, a differenza di quanto si verifica per gli endpoint di Azure, se si arresta o si elimina il servizio sottostante, la fatturazione per i controlli di integrità prosegue fino a quando non si disabilita o elimina l'endpoint in Gestione traffico.

## <a name="nested-endpoints"></a>Endpoint annidati

Gli endpoint annidati combinano diversi profili di Gestione traffico per creare schemi di routing del traffico più flessibili e supportare le esigenze di distribuzioni più grandi e complesse. Quando si usano gli endpoint annidati, un profilo "figlio" viene aggiunto come endpoint a un profilo "padre". Entrambi i profili padre e figlio possono contenere altri endpoint di qualsiasi tipo, inclusi altri profili annidati. Per altre informazioni, vedere [nested Traffic Manager profiles](traffic-manager-nested-profiles.md)(Profili nidificati di Gestione traffico).

## <a name="web-apps-as-endpoints"></a>App Web come endpoint

Per la configurazione di app Web come endpoint in Gestione traffico si rendono necessarie alcune considerazioni aggiuntive:

1. Solo le app Web a partire dallo SKU "standard" sono idonee all'uso con Gestione traffico. I tentativi di aggiungere app Web dello SKU di una versione precedente hanno esito negativo. Se si esegue il downgrade dello SKU di un'app Web esistente, Gestione traffico smette di inviare traffico a tale app Web.
2. Quando un endpoint riceve una richiesta HTTP, usa l'intestazione "host" della richiesta per determinare quale app Web usare per gestirla. L'intestazione host contiene il nome DNS usato per avviare la richiesta, ad esempio "contosoapp.azurewebsites.net". Per usare un nome DNS diverso con l'app Web, tale nome DNS deve essere registrato come nome di dominio personalizzato per l'app. Quando si aggiunge un endpoint di app Web come endpoint di Azure, il nome DNS del profilo di Gestione traffico viene registrato automaticamente per l'app. Questa registrazione viene rimossa automaticamente quando l'endpoint viene eliminato.
3. Ogni profilo di Gestione traffico può avere al massimo un endpoint di app Web da ogni area di Azure. Per ovviare a questa limitazione, è possibile configurare un'app Web come endpoint esterno. Per altre informazioni, vedere la sezione [Domande frequenti](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Abilitazione e disabilitazione di endpoint

La disabilitazione di un endpoint in Gestione traffico risulta utile per rimuovere temporaneamente il traffico da un endpoint in modalità di manutenzione o in corso di ridistribuzione. Quando l'endpoint è di nuovo operativo, è possibile abilitarlo nuovamente.

Per abilitare e disabilitare gli endpoint è possibile usare il portale di Gestione traffico, PowerShell, l'interfaccia della riga di comando o l'API REST, tutti supportati sia in Resource Manager che nel modello di distribuzione classica.

> [!NOTE]
> La disabilitazione di un endpoint di Azure non ha nulla a che vedere con il relativo stato di distribuzione in Azure. Un servizio di Azure, ad esempio una macchina virtuale o un'app Web, rimane operativo e in grado di ricevere il traffico anche se è disabilitato in Gestione traffico. È possibile indirizzare il traffico direttamente all'istanza del servizio, senza usare il nome DNS del profilo di Gestione traffico. Per altre informazioni, vedere [Modalità di funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).

L'idoneità corrente di ogni endpoint a ricevere il traffico dipende dai fattori seguenti:

* Stato del profilo (abilitato/disabilitato)
* Stato dell'endpoint (abilitato/disabilitato)
* Risultati dei controlli di integrità per l'endpoint

Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Dal momento che Gestione traffico lavora a livello di DNS, non è in grado di influenzare le connessioni esistenti verso qualsiasi endpoint. Quando un endpoint non è disponibile, Gestione traffico indirizza le nuove connessioni a un altro endpoint disponibile. L'host dietro all'endpoint disabilitato o non integro, tuttavia, può continuare a ricevere il traffico tramite le connessioni esistenti fino a quando le sessioni in questione non vengono terminate. Per consentire lo smaltimento del traffico dalle connessioni esistenti, le applicazioni devono limitare la durata delle sessioni.

Se tutti gli endpoint di un profilo sono disabilitati o se il profilo stesso è disabilitato, Gestione traffico invia una risposta "NXDOMAIN" a una nuova query DNS.


## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).
* Informazioni sul [monitoraggio degli endpoint e sul failover automatico](traffic-manager-monitoring.md)di Gestione traffico.
* Informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md)di Gestione traffico.



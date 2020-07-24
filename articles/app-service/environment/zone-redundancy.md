---
title: Supporto della zona di disponibilità per gli ambienti del servizio app
description: Informazioni su come distribuire gli ambienti del servizio app in modo che le app siano con ridondanza della zona.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1b32ae55030cc24c8892b204ff7330269993a483
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098562"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Supporto della zona di disponibilità per gli ambienti del servizio app

Gli ambienti del servizio app (ASE) possono essere distribuiti in zone di disponibilità (AZ).  I clienti possono distribuire un servizio di bilanciamento del carico interno (ILB) gli ambienti in una specifica AZ all'interno di un'area di Azure. Se si aggiunge l'ambiente del servizio app ILB a una specifica AZ, le risorse usate da un ambiente del servizio app ILB verranno aggiunte al valore AZ specificato o distribuite in una modalità con ridondanza della zona.  

Un ambiente del servizio app ILB distribuito in modo esplicito in AZ viene considerato una risorsa di zona perché l'ambiente del servizio app ILB è aggiunto a una zona specifica. Le seguenti dipendenze dell'ambiente del servizio app ILB verranno aggiunte alla zona specificata:

- Indirizzo IP del servizio di bilanciamento del carico interno dell'ambiente del servizio app
- risorse di calcolo usate dall'ambiente del servizio app per gestire ed eseguire applicazioni Web

L'archiviazione file remota per le applicazioni Web distribuite in un ambiente del servizio app ILB di zona usa l'archiviazione con ridondanza della zona (ZRS).

A meno che non vengano seguiti i passaggi descritti in questo articolo, ILB gli ambienti non vengono distribuiti automaticamente in modo di zona. Non è possibile aggiungere un ambiente del servizio app esterno con un indirizzo IP pubblico a una zona di disponibilità specifica. 

È possibile creare il gli ambienti di zona ILB in una delle aree seguenti:

- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti orientali 2 (EUAP)
- Francia centrale 
- Giappone orientale
- Europa settentrionale
- Europa occidentale
- Asia sud-orientale
- Regno Unito meridionale
- West US 2

Le applicazioni distribuite in un ambiente del servizio app ILB di zona continueranno a essere eseguite e gestiranno il traffico sull'ambiente ASE anche se altre zone nella stessa area subiscono un'interruzione.  È possibile che i comportamenti non runtime, tra cui; il ridimensionamento del piano di servizio dell'applicazione, la creazione di applicazioni, la configurazione dell'applicazione e la pubblicazione dell'applicazione possono comunque essere interessati da un'interruzione in altre zone di disponibilità. La distribuzione con aggiunta di zone di un ambiente del servizio app ILB di zona garantisce solo tempi di inseguimento per le applicazioni già distribuite.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Come distribuire un ambiente del servizio app in una zona di disponibilità ##

È necessario creare il gli ambienti di zona ILB con i modelli ARM. Una volta creato un ambiente del servizio app ILB di zona tramite un modello ARM, è possibile visualizzarlo e interagire con esso tramite il portale di Azure e l'interfaccia della riga di comando.  Un modello ARM è necessario solo per la creazione iniziale di un ambiente del servizio app ILB di zona.

L'unica modifica necessaria in un modello ARM per specificare un ambiente del servizio app ILB è la nuova proprietà ***Zones*** . La proprietà ***Zones*** deve essere impostata sul valore "1", "2" o "3", a seconda della zona di disponibilità logica a cui deve essere aggiunto l'ambiente del servizio app ILB.

Il frammento di codice del modello ARM di esempio seguente illustra la nuova proprietà ***Zones*** che specifica che l'ambiente del servizio app ILB deve essere aggiunto alla zona 2.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Per rendere ridondante la zona delle app, è necessario distribuire due ILB gli ambienti di zona. I due gli ambienti di zona ILB devono trovarsi in zone di disponibilità separate. Sarà quindi necessario distribuire le app in ogni gli ambienti di ILB. Una volta create le app, è necessario configurare una soluzione di bilanciamento del carico. La soluzione consigliata consiste nel distribuire un [gateway applicazione con ridondanza della zona](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) upstream del gli ambienti di zona ILB. 

## <a name="in-region-data-residency"></a>Residenza dei dati in area ##

ILB gli ambienti distribuiti in una zona di disponibilità archivia solo i dati dei clienti all'interno dell'area in cui è stato distribuito l'ambiente del servizio app ILB di zona. Sia il contenuto del file di sito Web che le impostazioni fornite dal cliente e i segreti archiviati nel servizio app rimangono all'interno dell'area geografica in cui viene distribuito l'ambiente del servizio app ILB.

I clienti garantiscono la residenza dei dati in una singola area seguendo i passaggi descritti in precedenza nella sezione "come distribuire un ambiente del servizio app in una zona di disponibilità". Configurando un ambiente del servizio app in base a questi passaggi, una ambiente del servizio app distribuita in una zona di disponibilità soddisfa i requisiti di residenza dei dati dell'area, inclusi quelli specificati nella [Centro protezione di Azure](https://azuredatacentermap.azurewebsites.net/).

I clienti possono verificare che un ambiente del servizio app sia configurato correttamente per archiviare i dati in una singola area attenendosi alla procedura seguente: 

1. Usando [Esplora inventario risorse](https://resources.azure.com), passare alla risorsa ARM per il ambiente del servizio app.  Gli ambienti sono elencate in *providers/Microsoft. Web/hostingEnvironments*.
2. Se una proprietà *zone* è presente nella vista della sintassi JSON di ARM e contiene una matrice JSON a valore singolo con valore "1", "2" o "3", l'ambiente del servizio app è zonally distribuito e i dati dei clienti rimangono nella stessa area.
2. Se una proprietà *zone* non esiste o se la proprietà non dispone di un valore di zona valido come specificato in precedenza, l'ambiente del servizio app non viene zonally distribuito e i dati del cliente non vengono archiviati in modo esclusivo nella stessa area.



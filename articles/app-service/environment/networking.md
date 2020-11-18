---
title: Rete ambiente del servizio app
description: Dettagli rete ambiente del servizio app
author: ccompy
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 61059c3e0f9737df6ace338f4252a338ea1f200c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663843"
---
# <a name="app-service-environment-networking"></a>Rete ambiente del servizio app

> [!NOTE]
> Questo articolo riguarda il ambiente del servizio app V3 (anteprima)
> 

Il ambiente del servizio app (ASE) è una distribuzione a tenant singolo del servizio app Azure che ospita app Web, app per le API e app per le funzioni. Quando si installa un ambiente del servizio app, si sceglie la rete virtuale di Azure (VNet) in cui si vuole distribuirla. Tutte le applicazioni per il traffico in ingresso e in uscita si troveranno all'interno del VNet specificato.  

ASEv3 utilizza due subnet.  Una subnet viene usata per l'endpoint privato che gestisce il traffico in ingresso. Potrebbe trattarsi di una subnet già esistente o di una nuova.  La subnet in ingresso può essere usata per altri scopi rispetto all'endpoint privato. La subnet in uscita può essere usata solo per il traffico in uscita dall'ambiente del servizio app. Nient'altro può andare nella subnet in uscita dell'ambiente del servizio app.

## <a name="addresses"></a>Indirizzi 
L'ambiente del servizio app dispone degli indirizzi seguenti in fase di creazione:

| Tipo di indirizzo | description |
|--------------|-------------|
| Indirizzo in ingresso | L'indirizzo in ingresso è l'indirizzo dell'endpoint privato usato dall'ambiente del servizio app. |
| Subnet in uscita | La subnet in uscita è anche la subnet dell'ambiente del servizio app. Durante l'anteprima questa subnet viene usata solo per il traffico in uscita. |
| Indirizzo in uscita di Windows | Le app di Windows in questo ambiente del servizio app utilizzeranno questo indirizzo, per impostazione predefinita, quando si effettuano chiamate in uscita a Internet. |
| Indirizzo in uscita Linux | Per impostazione predefinita, le app Linux in questo ambiente del servizio app utilizzeranno questo indirizzo per eseguire chiamate in uscita a Internet. |

Se si elimina l'endpoint privato usato dall'ambiente del servizio app, non è possibile raggiungere le app nell'ambiente del servizio app. Non eliminare la zona privata di DNS di Azure associata all'ambiente del servizio app.  

L'ambiente del servizio app usa gli indirizzi nella subnet in uscita per supportare l'infrastruttura usata dall'ambiente del servizio app. Quando si ridimensionano i piani di servizio app nell'ambiente del servizio app, si useranno più indirizzi. Le app nell'ambiente del servizio app non hanno indirizzi dedicati nella subnet in uscita. Gli indirizzi usati da un'app nella subnet in uscita da un'app cambieranno nel tempo.

## <a name="ports"></a>Porte

L'ambiente del servizio app riceve il traffico dell'applicazione sulle porte 80 e 443.  Non sono previsti requisiti per le porte in ingresso o in uscita per l'ambiente del servizio app. 

## <a name="extra-configurations"></a>Configurazioni aggiuntive

A differenza di ASEv2, con ASEv3 è possibile impostare i gruppi di sicurezza di rete (gruppi) e le tabelle di route (UDR) in base alle esigenze senza restrizioni. Se si vuole forzare il tunneling di tutto il traffico in uscita dall'ambiente del servizio app a un dispositivo NVA. È possibile mettere i dispositivi WAF davanti a tutto il traffico in ingresso all'ambiente del servizio app. 

## <a name="dns"></a>DNS

Le app nell'ambiente del servizio app utilizzeranno il DNS con cui è configurata la VNet. Se si vuole che alcune app usino un server DNS diverso, è possibile impostarlo manualmente in base alle singole app con le impostazioni dell'app WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER. L'impostazione app WEBSITE_DNS_ALT_SERVER configura il server DNS secondario. Il server DNS secondario viene usato solo quando non è presente alcuna risposta dal server DNS primario. 

## <a name="preview-limitation"></a>Limitazione anteprima

Alcune funzionalità di rete non sono disponibili con ASEv3.  Gli elementi che non sono disponibili in ASEv3 includono:

• FTP • debug remoto • distribuzione del servizio di bilanciamento del carico esterno • possibilità di accedere a un registro contenitori privato per le distribuzioni di contenitori • possibilità di effettuare chiamate a reti virtuali con peering globale • possibilità di eseguire il backup o il ripristino con un endpoint del servizio o un endpoint privato protetto account di archiviazione • possibilità di fare riferimento alle impostazioni dell'insieme di credenziali delle app per l'endpoint di servizio o per l'endpoint privato proteggere gli account dell'insieme di credenziali delle credenziali • possibilità di usare BYOS per un endpoint di servizio o un endpoint privato protetto account di archiviazione • uso del flusso Network Watcher o NSG nel traffico in uscita
    
    


---
title: Panoramica di ambiente del servizio app
description: Panoramica sul ambiente del servizio app
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fbc498fcd654d16936c2548528e2600be68a2ad9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663842"
---
# <a name="app-service-environment-overview"></a>Panoramica di ambiente del servizio app 

> [!NOTE]
> Questo articolo riguarda il ambiente del servizio app V3 (anteprima)
> 

L'ambiente del servizio app di Azure è una funzionalità del servizio app di Azure che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del servizio app di Azure su vasta scala. Questa funzionalità può ospitare gli elementi seguenti:

- App Web di Windows
- App Web Linux
- Contenitori Docker
- Funzioni

Gli ambienti del servizio app sono adatti ai carichi di lavoro dell'applicazione che richiedono:

- Scalabilità elevata.
- Isolamento e accesso alla rete protetto.
- Uso intensivo della memoria.
- Richieste elevate al secondo (RPS). È possibile creare più gli ambienti in una singola area di Azure o in più aree di Azure. Questa flessibilità rende gli ambienti ideale per le applicazioni senza stato con scalabilità orizzontale con un requisito di RPS elevato.

Le applicazioni host dell'ambiente del servizio app da un solo cliente e lo fanno in uno dei relativi reti virtuali. I clienti hanno il controllo con granularità fine del traffico di rete in ingresso e in uscita dell'applicazione. Le applicazioni possono stabilire connessioni protette ad alta velocità tramite VPN a risorse aziendali locali.

ASEv3 è dotato di un piano tariffario, isolato V2.
Gli ambienti del servizio app V3 forniscono un ambiente per proteggere le app in una subnet della rete e forniscono una distribuzione privata del servizio app Azure.
Per ottenere la scalabilità orizzontale è possibile usare più ambienti del servizio app. Le app in esecuzione in ambienti del servizio app possono avere l'accesso controllato da dispositivi upstream, quali Web application firewall. Per altre informazioni, vedere Web application firewall (WAF).

## <a name="usage-scenarios"></a>Scenari di utilizzo

Il ambiente del servizio app presenta molti casi d'uso, tra cui:

- Applicazioni line-of-business interne
- Applicazioni che necessitano di più di 30 istanze ASP
- Sistema a tenant singolo per soddisfare i requisiti di conformità o sicurezza interni
- Hosting di applicazioni con isolamento rete
- Applicazioni multilivello

Sono disponibili diverse funzionalità di rete che consentono alle app nel servizio app multi-tenant di raggiungere risorse isolate dalla rete o di diventare isolate in rete. Queste funzionalità sono abilitate a livello di applicazione.  Con un ambiente del servizio app non è disponibile alcuna configurazione aggiuntiva per le app in VNet. Le app vengono distribuite in un ambiente di isolamento rete già presente in una VNet. Oltre all'ambiente del servizio app che ospita app isolate dalla rete, è anche un sistema a tenant singolo. Nessun altro cliente utilizza l'ambiente del servizio app. Se è necessaria una storia di isolamento completa, è anche possibile distribuire l'ambiente del servizio app su hardware dedicato. Tra l'hosting di applicazioni con isolamento rete, il tenant singolo e la capacità 

## <a name="dedicated-environment"></a>Ambiente dedicato
Un ambiente del servizio app è dedicato esclusivamente a una singola sottoscrizione e può ospitare 200 istanze del piano di servizio app. Può comprendere da 100 istanze contenute in un singolo piano di servizio app a 100 piani di servizio app da un'istanza singola o qualsiasi combinazione intermedia.

Un ambiente del servizio app è composto da front-end e ruoli di lavoro. I front-end sono responsabili della terminazione HTTP/HTTPS e del bilanciamento del carico automatico delle richieste di app all'interno di un ambiente del servizio app. I front-end vengono aggiunti automaticamente con l'aumentare dei piani di servizio app nell'ambiente del servizio app.

I ruoli di lavoro ospitano app di clienti e sono disponibili in tre dimensioni fisse:

- Due vCPU/8 GB di RAM
- Quattro vCPU/16 GB di RAM
- 8 vCPU/32 GB di RAM

I clienti non devono gestire i front-end e i ruoli di lavoro. L'infrastruttura è automaticamente. Man mano che i piani di servizio app vengono aggiunti o ridimensionati in un ambiente del servizio app, l'infrastruttura necessaria viene aggiunta o rimossa in base alle esigenze.

Sono previsti addebiti per le istanze del piano di servizio app isolated V2. Se non si dispone di piani di servizio app nell'ambiente del servizio app, viene addebitato come se fosse disponibile un piano di servizio app con un'istanza dei due Core Worker.

## <a name="virtual-network-support"></a>Supporto della rete virtuale
La funzionalità Ambiente del servizio app è una distribuzione del Sevizio app di Azure direttamente in una rete virtuale di Azure Resource Manager del cliente. Un ambiente del servizio app esiste sempre in una subnet di una rete virtuale. È possibile usare le funzionalità di sicurezza delle reti virtuali per controllare le comunicazioni di rete in ingresso e in uscita per le app.

I gruppi di sicurezza di rete permettono di limitare le comunicazioni di rete in ingresso alla subnet in cui risiede un ambiente del servizio app. È possibile usare i gruppi di sicurezza di rete per eseguire le app protette da dispositivi e servizi upstream, quali Web application firewall e provider di servizi SaaS di rete.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. Se si distribuisce un ambiente del servizio app in una rete virtuale che ha una connessione VPN alla rete locale, le app dell'ambiente del servizio app possono accedere alle risorse locali. Questo vale indipendentemente dal fatto che la VPN sia di tipo da sito a sito o Azure ExpressRoute.

## <a name="preview"></a>Anteprima
Il ambiente del servizio app V3 è in anteprima pubblica.  Alcune funzionalità vengono aggiunte durante l'avanzamento dell'anteprima. Le limitazioni correnti di ASEv3 includono:

- Impossibilità di ridimensionare un piano di servizio app oltre cinque istanze
- Impossibilità di ottenere un contenitore da un registro privato
- Impossibilità per le funzionalità del servizio app attualmente non supportate di passare attraverso il cliente VNet
- Nessun modello di distribuzione esterno con un endpoint accessibile da Internet
- Nessun supporto della riga di comando (AZ CLI e PowerShell)
- Nessuna funzionalità di aggiornamento da ASEv2 a ASEv3
- Nessun supporto FTP
- Nessun supporto per alcune funzionalità del servizio app che passano attraverso il VNet del cliente. Backup/ripristino, Key Vault riferimenti nelle impostazioni dell'app, usando un registro contenitori privato e la registrazione diagnostica nell'archiviazione non funzionerà con gli endpoint di servizio o gli endpoint privati
    
### <a name="asev3-preview-architecture"></a>Architettura di anteprima ASEv3
In ASEv3 Preview, l'ambiente del servizio app userà endpoint privati per supportare il traffico in ingresso. L'endpoint privato verrà sostituito con i bilanciamenti del carico per GA. Durante la fase di anteprima, l'ambiente del servizio app non avrà il supporto incorporato per un endpoint accessibile da Internet. È possibile aggiungere un gateway applicazione per tale scopo. L'ambiente del servizio app necessita di risorse in due subnet.  Il traffico in ingresso viene propagato attraverso un endpoint privato. L'endpoint privato può essere inserito in qualsiasi subnet, purché disponga di un indirizzo disponibile utilizzabile dagli endpoint privati.  La subnet in uscita deve essere vuota e delegata a Microsoft. Web/hostingEnvironments. Se usato dall'ambiente del servizio app, la subnet in uscita non può essere usata per altri scopi.

Con ASEv3 non sono presenti requisiti di rete in ingresso o in uscita nella subnet dell'ambiente del servizio app. È possibile controllare il traffico con i gruppi di sicurezza di rete e le tabelle di routing e influirà solo sul traffico dell'applicazione. Non eliminare l'endpoint privato associato all'ambiente del servizio app perché questa operazione non può essere annullata. L'endpoint privato usato per l'ambiente del servizio app viene usato per tutte le app nell'ambiente del servizio app. 

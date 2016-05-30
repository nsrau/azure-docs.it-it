<properties
   pageTitle="Scalabilità di Funzioni di Azure | Microsoft Azure"
   description="Comprendere le modalità di scalabilità di Funzioni di Azure per soddisfare le esigenze dei carichi di lavoro basati su eventi."
   services="functions"
   documentationCenter="na"
   authors="eduardolaureano"
   manager="erikre"
   editor=""
   tags=""
   keywords="Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="edlaure"/>
  
# Scalabilità di Funzioni di Azure
     
## Introduzione

Uno dei vantaggi di Funzioni di Azure è che le risorse vengono usate solo in base alle necessità per l'esecuzione del codice. Ciò significa che non è necessario pagare per VM inattive o riservare capacità per eventuali necessità future. Al contrario, la piattaforma alloca potenza di calcolo quando il codice è in esecuzione, aumentandola se necessario per gestire il carico e riducendola nuovamente quando il codice non è in esecuzione.

Il meccanismo che consente questa nuova funzionalità è il piano di servizio Dynamic. Questo nuovo piano di servizio offre un contenitore dinamico per il codice in grado di aumentare su richiesta, mentre vengono addebitati solo la quantità di memoria effettivamente usata dal codice e il tempo necessario per l'esecuzione, misurato in secondi di Gigabyte.

In questo articolo viene illustrata una panoramica del funzionamento del piano di servizio Dynamic e della scalabilità su richiesta della piattaforma per l'esecuzione di codice.

Se non si ha familiarità con Funzioni di Azure, vedere [Panoramica di Funzioni di Azure](functions-overview.md) per comprenderne meglio le funzioni.

## Configurazione dell'app di funzione

Esistono due impostazioni principali correlate alla scalabilità:

* Il [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) o il piano di servizio Dynamic 
* La dimensione della memoria per l'ambiente di esecuzione 

Il costo di una funzione varia a seconda del tipo di piano di servizio selezionato. Con i piani di servizio Dynamic, il costo è in funzione del tempo di esecuzione, delle dimensioni della memoria e del numero di esecuzioni. I costi si accumulano solo quando il codice viene effettivamente eseguito.

I piani di servizio normali consentono di ospitare le funzioni in VM esistenti che possono essere usate anche per eseguire altro codice. Oltre al pagamento mensile per queste VM, non vi sono altri costi aggiuntivi per l'esecuzione delle funzioni sulle macchine stesse.

## Scelta di un piano di servizio

Quando si creano app di funzione, è possibile scegliere di eseguirle all'interno del nuovo piano di servizio Dynamic o di un normale [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Nel piano di servizio app le funzioni vengono eseguite su una macchina virtuale dedicata, allo stesso modo delle app Web (per SKU Basic, Standard o Premium). Questa macchina virtuale dedicata viene allocata alle applicazioni e/o alle funzioni ed è disponibile indipendentemente dall'avvenuta esecuzione di un qualsiasi codice. È una buona opzione se vi sono VM esistenti che eseguono altro codice ma non sono sfruttate completamente o se si prevede di eseguire funzioni in modo continuativo o quasi. L'uso di una VM separa il costo dell'esecuzione e quello delle dimensioni della memoria, consentendo di limitare il costo di molte funzioni di lunga esecuzione al costo di una o più VM in cui vengono eseguite.

[AZURE.INCLUDE [Piano di servizio Dynamic](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0518_2016-->
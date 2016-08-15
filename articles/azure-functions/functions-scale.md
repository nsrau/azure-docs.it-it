<properties
   pageTitle="Scalabilità di Funzioni di Azure | Microsoft Azure"
   description="Comprendere le modalità di scalabilità di Funzioni di Azure per soddisfare le esigenze dei carichi di lavoro basati su eventi."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
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
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# Scalabilità di Funzioni di Azure

## Introduzione

Uno dei vantaggi di Funzioni di Azure è che le risorse di calcolo vengono usate solo quando necessario. Ciò significa che non è necessario pagare per macchine virtuali inattive o riservare capacità per eventuali necessità future. Al contrario, la piattaforma alloca potenza di calcolo quando il codice è in esecuzione, aumentandola se necessario per gestire il carico e riducendola quando il codice non è in esecuzione.

Il meccanismo che consente questa nuova funzionalità è il piano di servizio Dynamic.

Questo articolo offre informazioni generali sul funzionamento del piano di servizio Dynamic e sulla scalabilità della piattaforma su richiesta per l'esecuzione di codice.

Se non si ha familiarità con Funzioni di Azure, vedere l'articolo [Panoramica di Funzioni di Azure](functions-overview.md) per comprenderne meglio le funzioni.

## Configurare Funzioni di Azure

Esistono due impostazioni principali correlate alla scalabilità:

* Il [piano di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) o il piano di servizio Dynamic
* Le dimensioni della memoria per l'ambiente di esecuzione

Il costo di una funzione varia a seconda del piano di servizio selezionato. Con i piani di servizio Dynamic, il costo è in funzione del tempo di esecuzione, delle dimensioni della memoria e del numero di esecuzioni. I costi maturano solo quando il codice è effettivamente in esecuzione.

Un piano di servizio app ospita le funzioni in VM esistenti che possono essere usate anche per eseguire altro codice. Oltre al pagamento mensile per queste VM, non vengono addebitati altri costi per l'esecuzione delle funzioni sulle macchine stesse.

## Scegliere un piano di servizio

Quando si creano funzioni è possibile scegliere di eseguirle nel piano di servizio Dynamic o in un [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Nel piano di servizio app, le funzioni vengono eseguite su una macchina virtuale dedicata, allo stesso modo delle app Web per SKU Basic, Standard o Premium. Questa macchina virtuale dedicata viene allocata alle app e alle funzioni ed è sempre disponibile indipendentemente dall'effettiva esecuzione del codice. È una buona opzione in caso di VM esistenti sottoutilizzate che eseguono già altro codice o se si prevede di eseguire funzioni in modo continuativo o quasi. Una macchina virtuale separa il costo per tempo di esecuzione e dimensioni della memoria. È quindi possibile limitare il costo di molte funzioni a esecuzione prolungata al costo di una o più macchine virtuali sulle quali vengono eseguite.

[AZURE.INCLUDE [Piano di servizio Dynamic](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0803_2016-->
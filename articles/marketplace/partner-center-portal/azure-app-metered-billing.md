---
title: Fatturazione a consumo per le applicazioni gestite tramite il servizio di misurazione del Marketplace | Azure Marketplace
description: Questa documentazione è una guida per gli ISV che pubblicano applicazioni Azure con modelli di fatturazione flessibili.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 180015a6bab576c02dcfaaea589628e40c6adde4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736175"
---
# <a name="managed-application-metered-billing"></a>Fatturazione a consumo delle applicazioni gestite 

Con il servizio di misurazione del Marketplace, è possibile creare piani per le applicazioni gestite per applicazione Azure offerte addebitate in base alle unità non standard. Prima di pubblicare questa offerta, è possibile definire le dimensioni di fatturazione, ad esempio larghezza di banda, ticket o messaggi di posta elettronica elaborati. I clienti pagano quindi in base al consumo di queste dimensioni.  Il sistema informa Microsoft tramite l'API del servizio di misurazione del Marketplace degli eventi fatturabili non appena si verificano.

## <a name="prerequisites-for-metered-billing"></a>Prerequisiti per la fatturazione a consumo

Per consentire a un piano di applicazione gestita di utilizzare la fatturazione a consumo, è necessario:

* Soddisfa tutti i requisiti dell'offerta, come descritto in [creare un'offerta di applicazione Azure](create-new-azure-apps-offer.md).
* Configura i **prezzi** per i clienti che addebitano i costi mensili per il servizio. Il prezzo può essere zero se non si desidera addebitare una tariffa fissa e si basano invece esclusivamente sulla fatturazione a consumo.
* Imposta le **dimensioni di fatturazione** per gli eventi di misurazione per i quali il cliente pagherà al massimo della tariffa fissa.
* Integrarsi con le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) per informare Microsoft degli eventi fatturabili.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Come si integra la fatturazione a consumo con i prezzi

Quando si tratta di definire l'offerta insieme ai relativi modelli di prezzo, è importante comprendere la gerarchia delle offerte.

* Ogni offerta applicazione Azure può disporre di un modello di soluzione o di piani di applicazioni gestite.
* La fatturazione a consumo è implementata solo con i piani di applicazioni gestite.
* A ogni piano di applicazione gestita è associato un modello di determinazione dei prezzi. 
* Il modello di determinazione dei prezzi prevede una tariffa mensile ricorrente, che può essere impostata su $0.
* Oltre alla tariffa ricorrente, il piano può includere anche le dimensioni facoltative usate per addebitare ai clienti l'utilizzo non incluso nella tariffa fissa. Ogni dimensione rappresenta un'unità fatturabile che il servizio comunicherà a Microsoft tramite l' [API del servizio di misurazione del Marketplace](marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Offerta di esempio

Ad esempio, Contoso è un server di pubblicazione con un servizio dell'applicazione gestita denominato contoso Analytics (CoA). CoA consente ai clienti di analizzare grandi quantità di dati per la creazione di report e il data warehouse. Contoso è registrato come editore nel centro per i partner per il programma commerciale Marketplace per pubblicare offerte per i clienti di Azure. Al CoA sono associati due piani, descritti di seguito:

* Piano di base
    * Analizza 100 GB e genera 100 report per $0/mese
    * Oltre i 100 GB, paga $10 per ogni 1 GB
    * Oltre i report 100, pagare $1 per ogni report
* Piano Premium
    * Analizza 1000 GB e genera 1000 report per $350 al mese
    * Oltre i 1000 GB, paga $100 per ogni 1 TB
    * Oltre i report 1000, pagare $0,5 per ogni report

Un cliente di Azure che sottoscrive il servizio CoA può analizzare e generare report al mese in base al piano selezionato. Contoso misura l'utilizzo fino alla quantità inclusa senza inviare eventi di utilizzo a Microsoft. Quando i clienti utilizzano più della quantità inclusa, non è necessario modificare i piani o eseguire operazioni diverse. Contoso misura l'eccedenza oltre la quantità inclusa e inizia a creare eventi di utilizzo a Microsoft per un utilizzo aggiuntivo con l' [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md). Microsoft addebiterà a sua volta il cliente per l'utilizzo aggiuntivo specificato dall'editore.

## <a name="billing-dimensions"></a>Dimensioni di fatturazione

Le dimensioni di fatturazione vengono usate per comunicare con il cliente su come verranno fatturate per l'uso del software.  Queste dimensioni vengono usate anche per comunicare gli eventi di utilizzo a Microsoft. Sono definiti come segue:

* **Identificatore della dimensione**: identificatore non modificabile a cui si fa riferimento durante la creazione di eventi di utilizzo.
* **Nome dimensione**: il nome visualizzato associato alla dimensione, ad esempio "SMS inviati".
* **Unità di misura**: la descrizione dell'unità di fatturazione, ad esempio "per messaggio di testo" o "per ogni 100 di messaggi di posta elettronica".
* **Prezzo per unità**: prezzo di un'unità della dimensione.
* **Quantità inclusa per il periodo mensile**: quantità di dimensione inclusa al mese per i clienti che pagano la tariffa mensile ricorrente. deve essere un numero intero.

Le dimensioni di fatturazione vengono condivise tra tutti i piani per un'offerta. Alcuni attributi si applicano alla dimensione in tutti i piani e altri attributi sono specifici del piano.

Gli attributi, che definiscono la dimensione stessa, vengono condivisi tra tutti i piani per un'offerta. Prima di pubblicare l'offerta, una modifica apportata a questi attributi dal contesto di qualsiasi piano influirà sulla definizione della dimensione in tutti i piani. Dopo la pubblicazione dell'offerta, questi attributi non saranno più modificabili. Gli attributi sono:

* Identificatore
* Nome
* Unità di misura

Gli altri attributi di una dimensione sono specifici di ogni piano e possono avere valori diversi dal piano al piano.  Prima di pubblicare il piano, è possibile modificare questi valori e solo questo piano sarà interessato. Una volta pubblicato il piano, questi attributi non saranno più modificabili. Gli attributi sono:

* Prezzo per unità
* Quantità inclusa per i clienti mensili 
* Quantità inclusa per i clienti annuali 

Anche le dimensioni hanno due concetti speciali, "Enabled" e "infinite":

* **Enabled** indica che questo piano fa parte di questa dimensione.  È consigliabile lasciare questa opzione non selezionata se si sta creando un nuovo piano che non invia eventi di utilizzo basati su questa dimensione. Inoltre, tutte le nuove dimensioni aggiunte dopo la prima pubblicazione di un piano vengono visualizzate come "non abilitate" nel piano già pubblicato.  Una dimensione disabilitata non viene visualizzata in alcun elenco di dimensioni per un piano visualizzato dai clienti.
* **Infinito**, rappresentato dal simbolo infinito "∞", indica che questo piano fa parte di questa dimensione, senza utilizzo a consumo per questa dimensione. Se si desidera indicare ai clienti che la funzionalità rappresentata da questa dimensione è inclusa nel piano, ma senza limiti di utilizzo.  Una dimensione con utilizzo infinito viene visualizzata in elenchi di dimensioni per un piano visualizzato dai clienti.  Questo piano non comporterà mai alcun addebito.

>[!Note] 
>Gli scenari seguenti sono supportati in modo esplicito:  <br> -È possibile aggiungere una nuova dimensione a un nuovo piano.  La nuova dimensione non verrà abilitata per i piani già pubblicati. <br> -È possibile pubblicare un piano con una tariffa mensile fissa e senza dimensioni, quindi aggiungere un nuovo piano e configurare una nuova dimensione per quel piano. La nuova dimensione non verrà abilitata per i piani già pubblicati.

## <a name="constraints"></a>Vincoli

### <a name="locking-behavior"></a>Comportamento di blocco

Una dimensione usata con il servizio di misurazione del Marketplace rappresenta una conoscenza del modo in cui un cliente pagherà per il servizio.  Tutti i dettagli di una dimensione non sono più modificabili dopo la pubblicazione di un'offerta.  Prima di pubblicare l'offerta, è importante che le dimensioni siano completamente definite.

Una volta pubblicata un'offerta con una dimensione, non è più possibile modificare i dettagli a livello di offerta per tale dimensione:

* Identificatore
* Nome
* Unità di misura

Una volta pubblicato un piano, non è più possibile modificare i dettagli a livello di piano:

* Prezzo per unità
* Quantità inclusa per il periodo mensile
* Indica se la dimensione è abilitata per il piano

>[!Note]
>La fatturazione a consumo con il servizio di misurazione del Marketplace non è ancora supportata nel cloud di Azure per enti pubblici.

### <a name="upper-limits"></a>Limiti superiori

Il numero massimo di dimensioni che possono essere configurate per una singola offerta è 18 dimensioni univoche.

## <a name="get-support"></a>Ottenere supporto

Se si verifica uno dei casi seguenti, è possibile aprire un ticket di supporto.

* Problemi tecnici relativi all'API del servizio di misurazione del Marketplace.
* Un problema che deve essere inoltrato a causa di un errore o di un bug da parte dell'utente (ad esempio, evento di utilizzo errato).
* Eventuali altri problemi relativi alla fatturazione a consumo.

Per inviare il ticket di supporto, attenersi alla procedura seguente:

1. Andare alla [pagina del supporto](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). I primi menu a discesa vengono compilati automaticamente. Per il supporto del Marketplace, identificare la famiglia di prodotti come **servizi cloud e online**, il prodotto come **editore del Marketplace**. Non modificare le selezioni del menu a discesa pre-popolato.
2. In "selezionare la versione del prodotto" selezionare **gestione offerta dinamica**.
3. In "selezionare una categoria che descriva meglio il problema" scegliere **offerta applicazioni Azure**.
4. In "selezionare un problema che descrive meglio il problema" selezionare **fatturazione a consumo**.
5. Selezionando il pulsante **Avanti** , si verrà indirizzati alla pagina dei **Dettagli del problema** , in cui è possibile immettere ulteriori dettagli sul problema.

Per altre opzioni di supporto per gli editori, vedere [supporto per il programma Commercial Marketplace nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) .

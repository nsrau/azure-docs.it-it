---
title: Fatturazione a consumo con il servizio di misurazione del Marketplace | Azure Marketplace
description: Questa documentazione è una guida per gli ISV che pubblicano offerte SaaS con modelli di fatturazione flessibili.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 3fa485c9fb2835b8270cb35fc75b57251476005f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141784"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Fatturazione a consumo con il servizio di misurazione del Marketplace

Con il servizio di misurazione del Marketplace, è possibile creare offerte SaaS (software-as-a-Service) nel programma Marketplace commerciale addebitate in base alle unità non standard.  Prima di pubblicare questa offerta, è possibile definire le dimensioni di fatturazione, ad esempio larghezza di banda, ticket o messaggi di posta elettronica elaborati.  I clienti pagano quindi in base al consumo di queste dimensioni, con il sistema che informa Microsoft attraverso l'API del servizio di misurazione del Marketplace degli eventi fatturabili non appena si verificano.  

## <a name="prerequisites-for-metered-billing"></a>Prerequisiti per la fatturazione a consumo

Per poter usare la fatturazione a consumo, un'offerta SaaS deve:

* Soddisfa tutti i requisiti dell'offerta per un' [offerta di vendita tramite Microsoft](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) , come descritto in [creare un'offerta SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Si integra con le [API di evasione Saas](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) per consentire ai clienti di effettuare il provisioning e connettersi all'offerta.  
* Essere configurato per il modello **tariffario tariffa flat** per il caricamento dei clienti per il servizio.  Le dimensioni sono un'estensione facoltativa del modello tariffario flat. 
* Integrarsi con le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) per informare Microsoft degli eventi fatturabili.

>[!Note]
>Il servizio di misurazione del Marketplace è disponibile solo per il modello di fatturazione a tariffa fissa e non si applica al modello di fatturazione per utente.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Come si integra la fatturazione a consumo con i prezzi

Quando si tratta di definire l'offerta insieme ai relativi modelli di prezzo, è importante comprendere la gerarchia delle offerte.

* Ogni offerta SaaS è configurata in modo da essere venduta tramite Microsoft o meno.  Questa impostazione non può essere modificata dopo la pubblicazione di un'offerta.
* Ogni offerta SaaS, configurata per la vendita tramite Microsoft, può includere uno o più piani. Un utente sottoscrive l'offerta SaaS, ma viene acquistata tramite Microsoft entro il contesto di un piano.
* A ogni piano è associato un modello di determinazione dei prezzi: **Tariffa** fissa o **per utente**. Tutti i piani in un'offerta devono essere associati allo stesso modello di determinazione prezzi. Ad esempio, non può essere disponibile un'offerta in cui uno dei piani è un modello tariffario a tariffa fissa e un altro modello di determinazione prezzi per utente.
* All'interno di ogni piano configurato per un modello di fatturazione a tariffa fissa, è inclusa almeno una tariffa ricorrente (che può essere $0):
    * Tariffa **mensile** ricorrente: tariffa mensile flat prepagata in caso di ricorrenza mensile quando l'utente acquista il piano.
    * Tariffa **annuale** ricorrente: tariffa fissa annuale prepagata in caso di ricorrenza annuale quando l'utente acquista il piano.
* Oltre alle tariffe ricorrenti, il piano può includere anche le dimensioni facoltative usate per addebitare ai clienti l'utilizzo non incluso nella tariffa fissa.   Ogni dimensione rappresenta un'unità fatturabile che il servizio comunicherà a Microsoft tramite l' [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Offerta di esempio

Ad esempio, Contoso è un server di pubblicazione con un servizio SaaS denominato contoso Notification Services (CNS). Lo SNC consente ai clienti di inviare notifiche tramite posta elettronica o SMS. Contoso è registrato come editore nel centro per i partner per il programma commerciale Marketplace per pubblicare offerte per i clienti di Azure.  Ci sono due piani associati a CNS, descritti di seguito:

* Piano di base
    * Invia messaggi di posta elettronica 10000 e 1000 testi per $0/mese
    * Oltre i messaggi di posta elettronica 10000, pagare $1 per ogni 100 e-mail
    * Oltre i 1000 testi, pagare $0,02 per ogni testo
* Piano Premium
    * Invia messaggi di posta elettronica 50000 e 10000 testi per $350 al mese
    * Oltre i messaggi di posta elettronica 50000, pagare $0,5 per ogni 100 e-mail
    * Oltre i 10000 testi, pagare $0,01 per ogni testo

Un cliente di Azure che sottoscrive il servizio CNS potrà inviare la quantità inclusa di testo e messaggi di posta elettronica al mese in base al piano selezionato.  Quando i clienti utilizzano più della quantità inclusa, non è necessario modificare i piani o eseguire operazioni diverse.  Contoso misura l'eccedenza oltre la quantità inclusa e inizia a creare eventi di utilizzo a Microsoft per un utilizzo aggiuntivo con l' [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md).  Microsoft addebiterà a sua volta il cliente per l'utilizzo aggiuntivo specificato dall'editore.

## <a name="billing-dimensions"></a>Dimensioni di fatturazione

Le dimensioni di fatturazione vengono utilizzate per comunicare con il cliente in merito al modo in cui verranno fatturate per l'utilizzo del software e anche per comunicare gli eventi di utilizzo a Microsoft. Sono definiti come segue:

* **Identificatore della dimensione**: identificatore non modificabile a cui si fa riferimento durante la creazione di eventi di utilizzo.
* **Nome dimensione**: il nome visualizzato associato alla dimensione, ad esempio "SMS inviati".
* **Unità di misura**: la descrizione dell'unità di fatturazione, ad esempio "per messaggio di testo" o "per ogni 100 di messaggi di posta elettronica".
* **Prezzo per unità**: prezzo di un'unità della dimensione.  
* **Quantità inclusa per il periodo mensile**: quantità di dimensione inclusa al mese per i clienti che pagano la tariffa mensile ricorrente. deve essere un numero intero.
* **Quantità inclusa per il periodo annuale**: quantità di dimensione inclusa al mese per i clienti che pagano la tariffa annuale ricorrente. deve essere un numero intero.

Le dimensioni di fatturazione vengono condivise tra tutti i piani per un'offerta.  Alcuni attributi si applicano alla dimensione in tutti i piani e altri attributi sono specifici del piano.

Gli attributi che definiscono la dimensione sono condivisi tra tutti i piani per un'offerta.  Prima di pubblicare l'offerta, una modifica apportata a questi attributi dal contesto di qualsiasi piano influirà sulla definizione della dimensione in tutti i piani.  Dopo la pubblicazione dell'offerta, questi attributi non saranno più modificabili.  Questi attributi sono:

* Identificatore
* Name
* Unità di misura

Gli altri attributi di una dimensione sono specifici di ogni piano e possono avere valori diversi dal piano al piano.  Prima di pubblicare il piano è possibile modificare questi valori e solo questo piano sarà interessato.  Una volta pubblicato il piano, questi attributi non saranno più modificabili.  Questi attributi sono:

* Prezzo per unità
* Quantità inclusa per i clienti mensili 
* Quantità inclusa per i clienti annuali 

Anche le dimensioni hanno due concetti speciali, "Enabled" e "infinite":

* **Enabled** indica che questo piano fa parte di questa dimensione.  Se si crea un nuovo piano che non invia eventi di utilizzo in base a questa dimensione, è possibile lasciare l'opzione non selezionata.  Inoltre, tutte le nuove dimensioni aggiunte dopo la prima pubblicazione di un piano vengono visualizzate come "non abilitate" nel piano già pubblicato.  Una dimensione disabilitata non viene visualizzata in alcun elenco di dimensioni per un piano visualizzato dai clienti.
* **Infinito**, rappresentato dal simbolo di infinito "∞", indica che questo piano fa parte di questa dimensione, ma non di misurazione dell'utilizzo rispetto a questa dimensione.  Se si desidera indicare ai clienti che la funzionalità rappresentata da questa dimensione è inclusa nel piano, ma senza limiti di utilizzo.  Una dimensione con utilizzo infinito verrà visualizzata in elenchi di dimensioni per un piano visualizzato dai clienti, con l'indicazione che non verrà mai addebitato alcun costo per questo piano.

>[!Note] 
>Gli scenari seguenti sono supportati in modo esplicito: <br> -È possibile aggiungere una nuova dimensione a un nuovo piano.  La nuova dimensione non verrà abilitata per i piani già pubblicati. <br> -È possibile pubblicare un piano **tariffario flat** senza dimensioni, quindi aggiungere un nuovo piano e configurare una nuova dimensione per il piano. La nuova dimensione non verrà abilitata per i piani già pubblicati.

## <a name="constraints"></a>Vincoli

### <a name="trial-behavior"></a>Comportamento della versione di valutazione

La fatturazione a consumo con il servizio di misurazione del Marketplace non è compatibile con l'offerta di una versione di valutazione gratuita.  Non è possibile configurare un piano per usare la fatturazione a consumo e una versione di valutazione gratuita.

### <a name="locking-behavior"></a>Comportamento di blocco

Poiché una dimensione utilizzata con il servizio di misurazione del Marketplace rappresenta una conoscenza del modo in cui un cliente pagherà il servizio, tutti i dettagli relativi a una dimensione non saranno più modificabili dopo la pubblicazione.  È importante che le dimensioni siano definite completamente per un piano prima della pubblicazione.
  
Una volta pubblicata un'offerta con una dimensione, non è più possibile modificare i dettagli a livello di offerta per tale dimensione:

* Identificatore
* NOME
* Unità di misura

Una volta pubblicato un piano, non è più possibile modificare i dettagli a livello di piano:

* Prezzo per unità
* Quantità inclusa per il periodo mensile
* Quantità inclusa per il termine annuale
* Indica se la dimensione è abilitata per il piano

### <a name="upper-limits"></a>Limiti superiori

Il numero massimo di dimensioni che possono essere configurate per una singola offerta è 18 dimensioni univoche.

## <a name="get-support"></a>Supporto

Se si dispone di uno dei seguenti elementi, è possibile aprire un ticket di supporto.

* Problemi tecnici relativi all'API del servizio di misurazione del Marketplace.
* Un problema che deve essere inoltrato a causa di un errore o di un bug da parte dell'utente (ad esempio, evento di utilizzo errato).
* Eventuali altri problemi relativi alla fatturazione a consumo. 

Per inviare il ticket di supporto, attenersi alla procedura seguente:

1. Andare alla [pagina del supporto](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). I primi menu a discesa vengono compilati automaticamente. Per il supporto del Marketplace, identificare la famiglia di prodotti come **servizi cloud e online**, il prodotto come **editore del Marketplace**.  Non modificare le selezioni del menu a discesa pre-popolato.
2. In "selezionare la versione del prodotto" selezionare **gestione offerta dinamica**.
3. In "selezionare una categoria che descriva meglio il problema" scegliere **app Saas**.
4. In "selezionare un problema che descrive meglio il problema" selezionare **fatturazione a consumo**.
5. Selezionando il pulsante **Avanti** , si verrà indirizzati alla pagina dei **Dettagli del problema** , in cui è possibile immettere ulteriori dettagli sul problema.

Per altre opzioni di supporto dell'editore, vedere [supporto per il programma Commercial Marketplace nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) .

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) .

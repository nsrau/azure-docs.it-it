---
title: Fatturazione a consumo con il servizio di misurazione del Marketplace | Azure Marketplace
description: Questa documentazione è una guida per gli ISV che pubblicano offerte SaaS con modelli di fatturazione flessibili.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d6c46575ed497c5067b3ffc5c745e79f814bc212
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87304378"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Fatturazione a consumo con il servizio di misurazione del Marketplace

Con il servizio di misurazione del Marketplace è possibile creare offerte SaaS (software-as-a-Service) che vengono addebitate in base alle unità non standard.  Prima di pubblicare questa offerta, è possibile definire le dimensioni di fatturazione, ad esempio larghezza di banda, ticket o messaggi di posta elettronica elaborati.  I clienti pagano quindi in base al consumo di queste dimensioni, con il sistema che informa Microsoft attraverso l'API del servizio di misurazione del Marketplace degli eventi fatturabili non appena si verificano.  

## <a name="prerequisites-for-metered-billing"></a>Prerequisiti per la fatturazione a consumo

Per un'offerta SaaS per l'utilizzo della fatturazione a consumo, è necessario innanzitutto:

* Soddisfa tutti i requisiti dell'offerta per un' [offerta di vendita tramite Microsoft](./create-new-saas-offer.md#sell-through-microsoft) , come descritto in [creare un'offerta SaaS](./create-new-saas-offer.md).
* Si integra con le [API di evasione Saas](./pc-saas-fulfillment-api-v2.md) per consentire ai clienti di effettuare il provisioning e connettersi all'offerta.  
* Essere configurato per il modello **tariffario flat rate** quando si addebitano i clienti per il servizio.  Le dimensioni sono un'estensione facoltativa del modello tariffario flat. 
* Integrarsi con le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) per informare Microsoft degli eventi fatturabili.

L'offerta SaaS può quindi integrarsi con le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) per informare Microsoft degli eventi fatturabili.

>[!Note]
>Il servizio di misurazione del Marketplace è disponibile solo per il modello di fatturazione a tariffa fissa e non si applica al modello di fatturazione per utente.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Come si integra la fatturazione a consumo con i prezzi

Conoscere la gerarchia dell'offerta è importante, quando si tratta di definire l'offerta insieme ai relativi modelli tariffari.

* Ogni offerta SaaS è configurata in modo da essere venduta tramite Microsoft o meno.  Dopo la pubblicazione di un'offerta, questa opzione non può essere modificata.
* Ogni offerta SaaS, configurata per la vendita tramite Microsoft, può includere uno o più piani.  Un utente sottoscrive l'offerta SaaS, ma viene acquistata tramite Microsoft entro il contesto di un piano.
* A ogni piano è associato un modello di determinazione dei prezzi: **Tariffa** fissa o **per utente**. Tutti i piani in un'offerta devono essere associati allo stesso modello di determinazione prezzi. Non è ad esempio possibile che esista un'offerta con piani per un modello tariffario forfettario e un altro modello di determinazione prezzi per utente.
* All'interno di ogni piano configurato per un modello di fatturazione a tariffa fissa, è inclusa almeno una tariffa ricorrente (che può essere $0):
    * Tariffa **mensile** ricorrente: tariffa mensile flat prepagata in caso di ricorrenza mensile quando l'utente effettua l'acquisto del piano.
    * Tariffa **annuale** ricorrente: tariffa fissa annuale prepagata in caso di ricorrenza annuale quando l'utente acquista il piano.
* Oltre alle tariffe ricorrenti, un piano tariffario flat può includere anche dimensioni personalizzate facoltative usate per addebitare i clienti per l'utilizzo in eccedenza non incluso nella tariffa fissa.  Ogni dimensione rappresenta un'unità fatturabile che il servizio comunicherà a Microsoft tramite l' [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Offerta di esempio

Ad esempio, Contoso è un server di pubblicazione con un servizio SaaS denominato contoso Notification Services (CNS). Lo SNC consente ai clienti di inviare notifiche tramite posta elettronica o SMS. Contoso è registrato come editore nel partner Center per il programma commerciale Marketplace per pubblicare offerte SaaS per i clienti di Azure.  Ci sono due piani associati a CNS, descritti di seguito:

* Piano Basic
    * Invia messaggi di posta elettronica 10000 e 1000 testi per $0/mese (tariffa mensile flat)
    * Oltre i messaggi di posta elettronica 10000, pagare $1 per ogni 100 e-mail
    * Oltre i 1000 testi, pagare $0,02 per ogni testo

    [![Prezzi piano Basic](./media/saas-basic-pricing.png "Fare clic per la visualizzazione ingrandita")](./media/saas-basic-pricing.png)

* Piano Premium
    * Invia messaggi di posta elettronica 50000 e 10000 testi per $350/mese o 5 minuti e 1 milione di testi per $3500 all'anno
    * Oltre i messaggi di posta elettronica 50000, pagare $0,5 per ogni 100 e-mail
    * Oltre i 10000 testi, pagare $0,01 per ogni testo

    [![Prezzi del piano Premium](./media/saas-premium-pricing.png "Fare clic per la visualizzazione ingrandita")](./media/saas-premium-pricing.png)

* Piano Enterprise
    * Invia un numero illimitato di messaggi di posta elettronica e 50000 testi per $400 al mese
    * Oltre i 50000 testi pagati $0,005 per ogni txt

    [![Prezzi del piano Enterprise](./media/saas-enterprise-pricing.png "Fare clic per la visualizzazione ingrandita")](./media/saas-enterprise-pricing.png)

In base al piano selezionato, una sottoscrizione di acquisto di un cliente di Azure per l'offerta SaaS del SNC sarà in grado di inviare la quantità inclusa di testo e messaggi di posta elettronica per ogni periodo di abbonamento (mese o anno come visualizzato nei dettagli della sottoscrizione-startDate ed endDate).  Contoso conta l'utilizzo fino alla quantità inclusa in base senza inviare alcun evento di utilizzo a Microsoft. Quando i clienti utilizzano più della quantità inclusa, non è necessario modificare i piani o eseguire operazioni diverse.  Contoso misura l'eccedenza oltre la quantità inclusa e inizia a creare eventi di utilizzo a Microsoft per l'addebito dell'utilizzo in eccedenza tramite l' [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md).  Microsoft addebiterà a sua volta il cliente per l'utilizzo in eccedenza come specificato dall'editore nelle dimensioni personalizzate. La fatturazione in eccedenza viene effettuata al successivo ciclo di fatturazione (ogni mese, ma può essere trimestrale o anticipata per alcuni clienti).  Per un piano tariffario mensile flat, viene effettuata la fatturazione in eccedenza per ogni mese in cui si è verificata l'eccedenza.  Per un piano tariffario forfettario annuale, una volta consumata la quantità inclusa in base all'anno, tutto l'utilizzo aggiuntivo emesso dal contatore personalizzato verrà fatturato come eccedenza durante ogni ciclo di fatturazione (ogni mese) fino alla fine del periodo di validità dell'anno della sottoscrizione.

## <a name="billing-dimensions"></a>Dimensioni di fatturazione

Ogni dimensione di fatturazione definisce un'unità personalizzata in base alla quale gli ISV possono generare eventi di utilizzo.  Le dimensioni di fatturazione vengono usate anche per comunicare con il cliente su come verranno fatturate per l'uso del software.  Sono definiti come segue:

* **ID**: l'identificatore di dimensione non modificabile a cui si fa riferimento durante la creazione di eventi di utilizzo.
* **Nome visualizzato**: il nome visualizzato associato alla dimensione, ad esempio "SMS inviati".
* **Unità di misura**: la descrizione dell'unità di fatturazione, ad esempio "per messaggio di testo" o "per ogni 100 di messaggi di posta elettronica".
* **Prezzo per unità in USD**: prezzo di un'unità della dimensione.  Può essere 0. 
* **Quantità mensile inclusa in base**: quantità di dimensione inclusa al mese per i clienti che pagano la tariffa mensile ricorrente, deve essere un numero intero. Può essere 0 o Unlimited.
* **Quantità annua inclusa in base**: la quantità di dimensione inclusa per ogni anno per i clienti che pagano la tariffa annuale ricorrente, deve essere un numero intero. Può essere 0 o illimitato.

Le dimensioni di fatturazione vengono condivise tra tutti i piani per un'offerta.  Alcuni attributi si applicano alla dimensione in tutti i piani e altri attributi sono specifici del piano.

Gli attributi, che definiscono la dimensione stessa, vengono condivisi tra tutti i piani per un'offerta.  Prima di pubblicare l'offerta, una modifica apportata a questi attributi dal contesto di qualsiasi piano influirà sulla definizione della dimensione in tutti i piani.  Dopo la pubblicazione dell'offerta, questi attributi non saranno più modificabili.  Gli attributi sono:

* ID
* Nome visualizzato
* Unità di misura

Gli altri attributi di una dimensione sono specifici di ogni piano e possono avere valori diversi dal piano al piano.  Prima di pubblicare il piano, è possibile modificare questi valori e solo questo piano sarà interessato.  Una volta pubblicato il piano, questi attributi non saranno più modificabili.  Gli attributi sono:

* Prezzo per unità in USD
* Quantità mensile inclusa in base  
* Quantità annuale inclusa in baseIncluded  

Anche le dimensioni hanno due concetti speciali, "Enabled" e "infinite":

* **Enabled** indica che questo piano fa parte di questa dimensione.  Se si sta creando un nuovo piano che non invia eventi di utilizzo basati su questa dimensione, è consigliabile lasciare deselezionata questa opzione.  Inoltre, tutte le nuove dimensioni aggiunte dopo la prima pubblicazione di un piano vengono visualizzate come "non abilitate" nel piano già pubblicato.  Una dimensione disabilitata non viene visualizzata in alcun elenco di dimensioni per un piano visualizzato dai clienti.
* **Infinito** rappresentato dal simbolo di infinito "∞", indica che questo piano fa parte di questa dimensione, ma non genera l'utilizzo rispetto a questa dimensione.  Se si desidera indicare ai clienti che la funzionalità rappresentata da questa dimensione è inclusa nel piano, ma senza limiti di utilizzo.  Una dimensione con utilizzo infinito verrà visualizzata in elenchi di dimensioni per un piano visualizzato dai clienti, con l'indicazione che non verrà mai addebitato alcun costo per questo piano.

>[!Note] 
>Gli scenari seguenti sono supportati in modo esplicito: <br> -È possibile aggiungere una nuova dimensione a un nuovo piano.  La nuova dimensione non verrà abilitata per i piani già pubblicati. <br> -È possibile pubblicare un piano **tariffario flat** senza dimensioni, quindi aggiungere un nuovo piano e configurare una nuova dimensione per il piano. La nuova dimensione non verrà abilitata per i piani già pubblicati.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Impostazione del prezzo della dimensione per unità per mercato supportato

Analogamente ai prezzi forfettari, è possibile impostare prezzi per le dimensioni di fatturazione per ogni paese o area geografica supportata. Il server di pubblicazione deve usare la funzionalità di importazione ed esportazione dei dati dei prezzi nel centro per i partner.

1. Definire innanzitutto le dimensioni desiderate e contrassegnare i mercati supportati. 
1. Esportare quindi i dati nel file.
1. Aggiungere i prezzi corretti per paese/area geografica e importare il file nel centro per i partner.

L'interfaccia utente del contatore cambierà per indicare che i prezzi della dimensione possono essere visualizzati solo nel file.

[![Dimensioni del servizio di misurazione del Marketplace](./media/metering-service-dimentions.png "Fare clic per la visualizzazione ingrandita")](./media/metering-service-dimentions.png)


### <a name="private-plan"></a>Piano privato

Analogamente ai piani tariffari flat, un piano con dimensioni può essere impostato come piano privato, accessibile solo dai destinatari definiti del piano.

## <a name="constraints"></a>Vincoli

### <a name="trial-behavior"></a>Comportamento della versione di valutazione

La fatturazione a consumo con il servizio di misurazione del Marketplace non è compatibile con l'offerta di una versione di valutazione gratuita.  Non è possibile configurare un piano per usare la fatturazione a consumo e una versione di valutazione gratuita.

### <a name="locking-behavior"></a>Comportamento di blocco

Poiché una dimensione utilizzata con il servizio di misurazione del Marketplace rappresenta una conoscenza del modo in cui un cliente pagherà il servizio, tutti i dettagli relativi a una dimensione non saranno più modificabili dopo la pubblicazione.  È importante che le dimensioni siano definite completamente per un piano prima della pubblicazione.

Una volta pubblicata un'offerta con una dimensione, non è più possibile modificare i dettagli a livello di offerta per tale dimensione:

* ID
* Nome visualizzato
* Unità di misura

Una volta pubblicato un piano, non è più possibile modificare i dettagli a livello di piano:

* Prezzo per unità in USD
* Quantità mensile inclusa in base
* Quantità annuale inclusa nella base
* Indica se la dimensione è abilitata o meno per il piano

### <a name="upper-limits"></a>Limiti superiori

Il numero massimo di dimensioni che possono essere configurate per una singola offerta è 18 dimensioni univoche.

## <a name="get-support"></a>Ottenere supporto

Se si verifica uno dei problemi seguenti, è possibile aprire un ticket di supporto.

* Problemi tecnici relativi all'API del servizio di misurazione del Marketplace.
* Un problema che deve essere inoltrato a causa di un errore o di un bug da parte dell'utente (ad esempio, evento di utilizzo errato).
* Eventuali altri problemi relativi alla fatturazione a consumo.

Seguire le istruzioni in [supporto per il programma Commercial Marketplace nel centro per i partner](./support.md) per comprendere le opzioni di supporto dell'editore e aprire il ticket di supporto con Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere le [API del servizio di misurazione del Marketplace](./marketplace-metering-service-apis.md) .

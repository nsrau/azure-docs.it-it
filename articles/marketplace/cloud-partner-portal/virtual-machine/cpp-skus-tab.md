---
title: Scheda SKU macchina virtuale nell'portale Cloud Partner per Azure Marketplace
description: Descrive la scheda SKU usata nell'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 101f928e296d0b48b4e077676393772829aa76ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808976"
---
# <a name="virtual-machine-skus-tab"></a>Scheda SKU della macchina virtuale

La scheda **SKU** della pagina **Nuova offerta** consente di creare uno o più SKU e di associarli alla nuova offerta.  Diversi SKU possono differenziare una soluzione in base a set di funzionalità, tipi di immagine di macchina virtuale, velocità effettiva o scalabilità, modelli di fatturazione o altre caratteristiche.


## <a name="create-a-sku"></a>Creare uno SKU

Inizialmente a una nuova offerta non è associato alcun SKU e di conseguenza è necessario crearne uno nuovo facendo clic su **New SKU** (Nuovo SKU).

![Pulsante New SKU (Nuovo SKU) nella scheda Nuova offerta per macchine virtuali](./media/publishvm_005.png)

<br/>

Viene visualizzata la finestra di dialogo **New SKU** (Nuovo SKU).  Immettere l'identificatore per il nuovo SKU e quindi fare clic su **OK**. (Vedere di seguito per le convenzioni di denominazione degli identificatori).  Nella scheda **SKU** verranno ora visualizzati i campi disponibili per la modifica.    Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Scheda SKU del modulo nuova offerta per le macchine virtuali](./media/publishvm_006.png)

La tabella seguente descrive lo scopo, il contenuto e la formattazione di questi campi.  I campi obbligatori sono indicati da un asterisco (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Campo**       |     **Descrizione**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU Settings* (Impostazioni SKU)   |    |
| **ID SKU\***       | Identificatore dello SKU.  Questo nome non può superare i 50 caratteri, costituiti da caratteri alfanumerici minuscoli o trattini (-), e non può terminare con un trattino.  Il nome non può essere modificato dopo che l'offerta è stata pubblicata.  |
|  *SKU Details* (Dettagli SKU)   |  |
| **\* titolo**        | Nome descrittivo per l'offerta da visualizzare nel marketplace. La lunghezza massima consentita è di 50 caratteri. |
| **Riepilogo\***      | Breve descrizione dell'offerta da visualizzare nel marketplace. La lunghezza massima consentita è di 100 caratteri. |
| **Descrizione\***  | Testo della descrizione con una spiegazione più dettagliata dell'offerta.  <!-- TD: max len/guidance? 3k characters -->  |
| **Nascondi questo SKU\*** | Indica se lo SKU deve essere visibile nel marketplace ai clienti.  È consigliabile nascondere lo SKU se si vuole che sia disponibile solo tramite modelli di soluzione e non per l'acquisto singolo.  Nascondere lo SKU può essere utile anche per il test iniziale o per le offerte temporanee o stagionali. |
| **\* disponibilità cloud** | Determina in quali cloud lo SKU deve essere disponibile.  Il valore predefinito è la versione pubblica di Azure.  Microsoft Azure per enti pubblici è una soluzione cloud della community di enti pubblici con accesso controllato per gli enti pubblici federali, statali, locali o di popolazioni native degli Stati Uniti e per i relativi partner certificati.  Per altre informazioni sulle soluzioni cloud per enti pubblici, vedere [Welcome to Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (Panoramica su Azure per enti pubblici). |
| **Si tratta di uno SKU privato?\*** | Indica se lo SKU è pubblico o privato. Il valore predefinito è **No** (pubblico).  Per altre informazioni, vedere [SKU privati](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **\* di disponibilità paese/area geografica** | Determina in quali paesi o aree geografiche del mondo lo SKU sarà disponibile per l'acquisto. Selezionare almeno un paese/area geografica. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Prezzi*   |  |
| **Modello di licenza\***| Modello di fatturazione standard da usare.  Se si seleziona **Usage-based monthly billed SKU** (SKU fatturato mensilmente in base all'uso), viene visualizzato un menu a discesa che consente di specificare i dettagli di prezzo per core e di indicare se si desidera offrire un periodo di valutazione gratuita.  Questa sezione consente anche di esportare e importare la pianificazione dei prezzi in Excel. Per altre informazioni, vedere [Opzioni di fatturazione in Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Immagini VM*   |  |
| **\* della famiglia di sistemi operativi** | Indica se la macchina virtuale della soluzione è basata su Windows o Linux. |
| **Select Operating System Type** (Selezionare il tipo di sistema operativo) | Fornitore specifico o versione del sistema operativo specificato. |
| **Nome descrittivo del sistema operativo\*** | Nome del sistema operativo da visualizzare ai clienti.  |
| **Dimensioni di VM consigliate\*** | Consente di selezionare fino a sei dimensioni di macchina virtuale consigliate da un elenco standard.  Questo elenco viene passato al portale di Azure e a Microsoft Marketplace.  Le prime dimensioni della macchina virtuale in questo elenco valide (per la sottoscrizione, area, zona e così via) del cliente sono impostate come predefinite per quel potenziale cliente.  L'utente può modificare questa dimensione in qualsiasi compatibile con l'immagine della soluzione. | 
| **Open Ports** (Porte aperte)| Porte da aprire e protocollo per supportare lo SKU.  Queste configurazioni devono corrispondere alla rete virtuale configurata per la rete della macchina virtuale della soluzione e diventano valide durante la distribuzione della macchina virtuale. Dopo la pubblicazione di uno SKU, tuttavia, le impostazioni della porta possono essere modificate. Per altre informazioni, vedere [Come aprire le porte per una macchina virtuale con il portale di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>I mapping di rete predefiniti seguenti vengono aggiunti a tutte le macchine virtuali. &emsp; Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Disk Version** (Versione disco)  | Macchina virtuale della soluzione associata, specificata dal numero di versione e dall'URL del disco. La versione del disco deve essere nel formato [versione semantica](https://semver.org/) `<major>.<minor>.<patch>`.  L'URL è l'URI di firma di accesso condiviso creato per il disco rigido virtuale del sistema operativo.  È possibile aggiungere fino a otto versioni del disco per ogni SKU, ma solo il numero di versione del disco più alto per uno SKU viene visualizzato in Azure Marketplace. Le altre versioni sono visibili solo tramite API.  <!--TD: Add more specific link to API --> <br/> Il menu a discesa **New data disk** (Nuovo disco dati) consente di associare fino a 15 dischi dati alla macchina virtuale.  Dopo aver pubblicato uno SKU con una determinata versione di macchina virtuale e i dischi dati associati, questa configurazione non può essere modificata.  Eventuali versioni della macchina virtuale aggiunte allo SKU devono supportare lo stesso numero di dischi dati. <br/> Se non è stata creata alcuna immagine di macchina virtuale basata su Azure, è possibile aggiungere aggiornamenti al campo in un secondo momento.  Per informazioni sulla creazione della risorsa di macchina virtuale associata, vedere la sezione [Create VM technical assets](./cpp-create-technical-assets.md) (Creare risorse tecniche di macchina virtuale).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Fare clic su **Salva** per salvare le voci immesse. Nella scheda successiva è possibile specificare se l'offerta supporta [Test Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Altre considerazioni sui prezzi

Il modello di prezzi descritto è una descrizione di base,  è soggetto a modifiche e può essere interessato da normative fiscali locali oppure a livello di area o da politiche di prezzo Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Nuove dimensioni core aggiunte il 7/2/2019

Gli editori di VM hanno ricevuto una notifica il 2 luglio 2019 dell'aggiunta di nuovi prezzi per le nuove dimensioni di macchine virtuali di Azure (in base al numero di core).  I nuovi prezzi sono per le dimensioni di base 10, 44, 48, 60, 120, 208 e 416.  Per le macchine virtuali esistenti, i nuovi prezzi per queste dimensioni di core sono stati calcolati automaticamente in base ai prezzi correnti.  Gli editori hanno fino al 1 ° agosto 2019 per esaminare i prezzi aggiuntivi e apportare le modifiche desiderate.  Dopo questa data, se non è già stata ripubblicata dal server di pubblicazione, i prezzi calcolati automaticamente per queste nuove dimensioni di core diverranno effettivi.


### <a name="simplified-currency-pricing"></a>Prezzi di valuta semplificata

A partire dal 1° settembre 2018, nel portale verrà aggiunta una nuova sezione denominata **Simplified Currency Pricing** (Prezzi in valuta semplificati). Microsoft sta semplificando il business di Azure Marketplace, consentendo prezzi più prevedibili e raccolte di clienti internazionali. Questa semplificazione si otterrà riducendo il numero delle valute in cui emettere le fatture ai clienti.  Per altre informazioni, vedere [Update an existing VM offer on Azure Marketplace](./cpp-update-existing-offer.md) (Aggiornare un'offerta di macchina virtuale esistente in Azure).


### <a name="additional-information-on-taxes-and-prices"></a>Altre informazioni su prezzi e imposte

* Microsoft classifica alcuni paesi o aree geografiche come *paesi rimittenti fiscali*.  In questi paesi/aree geografiche Microsoft raccoglie le imposte dai clienti, quindi paga (i remi) le imposte al governo.  In altri paesi/aree geografiche, i partner sono in genere responsabili della raccolta di imposte dai clienti e del pagamento delle imposte al governo. Se si sceglie di vendere negli ultimi paesi o aree geografiche, è necessario avere la possibilità di calcolare e pagare le imposte locali.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* I prezzi non sono modificabili dopo la pubblicazione dell'offerta. È comunque possibile aggiungere o rimuovere aree supportate. 
* Microsoft addebita al cliente le spese per l'uso della macchina virtuale di Azure standard, oltre alle spese di SKU pianificate.
* I prezzi sono impostati per tutte le aree in valuta locale ai tassi di cambio disponibili al momento dell'impostazione dei prezzi.  <!-- TD: Meaning? - Offer created, published, other? -->
* Per impostare individualmente il prezzo di ogni area, esporta il foglio di calcolo dei prezzi, applica i prezzi personalizzati e quindi importa. 


## <a name="next-steps"></a>Passaggi successivi

Facoltativamente, è possibile specificare le informazioni sul [test drive](./cpp-test-drive-tab.md) se si supporta questa funzionalità; in caso contrario, fornire i dati del [Marketplace](./cpp-marketplace-tab.md) per l'offerta.

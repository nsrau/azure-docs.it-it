---
title: Creare un'offerta per le macchine virtuali | Microsoft Docs
description: Pubblicare una macchina virtuale in Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d77dad52e75253de02fd079d791861356c4c5e1f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247076"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Pubblicare una macchina virtuale in Azure Marketplace

Questo articolo illustra la procedura da seguire per pubblicare un'offerta per una macchina virtuale in Azure Marketplace.

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti tecnici e non tecnici elencati di seguito si applicano alla pubblicazione di una macchina virtuale in Azure Marketplace

### <a name="technical"></a>Tecnici

-   [Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Creare e caricare un disco rigido virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Creare e testare una VM Linux da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Creare e caricare un disco rigido virtuale Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Creare e testare una VM Windows da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Come risolvere problemi comuni incontrati durante la creazione del disco rigido virtuale](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

-   [Raccomandazioni sulla sicurezza per le immagini di Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>Non tecnici (requisiti aziendali)

 -   La società (o la sua affiliata) deve avere sede in uno dei paesi di origine della vendita supportati da Azure Marketplace

-   Il prodotto deve essere concesso in licenza in un modo compatibile con i modelli di fatturazione supportati da Azure Marketplace

-   Il partner è responsabile di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale. Il supporto può essere gratuito, a pagamento o tramite il supporto della community.

-   Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.

-   È necessario offrire contenuti che soddisfino i criteri affinché l'offerta sia presentata in Azure Marketplace e nel portale di gestione di Azure.

-   Occorre accettare le condizioni delle Politiche di partecipazione a Microsoft Azure Marketplace e del Contratto per gli editori.

-   È necessario accettare le [Condizioni per l'utilizzo del sito Web di Microsoft Azure](https://azure.microsoft.com/support/legal/website-terms-of-use/), l'[Informativa sulla privacy di Microsoft](https://www.microsoft.com/privacystatement/default.aspx) e il [Contratto del Programma Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="before-you-begin"></a>Prima di iniziare

Una volta soddisfatti tutti i prerequisiti, è possibile iniziare a creare l'offerta per il modello di soluzione. Prima di iniziare, rivedere le informazioni sulle offerte e le SKU riportate di seguito.

**Offerta**

Un'offerta dell'applicazione Azure corrisponde a una classe di offerta di prodotti da parte di un editore. Per rendere disponibile su Azure Marketplace un nuovo tipo di soluzione/applicazione, l'approccio migliore consiste nel creare una nuova offerta. Un'offerta è una raccolta di SKU. Ogni offerta viene visualizzata come entità a sé stante in Azure Marketplace.

**SKU**

Uno SKU è la più piccola unità acquistabile di un'offerta. All'interno della stessa classe di prodotti (offerta), le SKU consentono di distinguere tra diverse funzionalità supportate, Ad esempio, l'offerta può essere gestita o non gestita e sono supportati modelli di fatturazione diversi.

Aggiungere più SKU negli scenari seguenti:
- Si vogliono supportare modelli di fatturazione diversi, come Bring Your Own License (BYOL) o Pagamento in base al consumo (PAYG).
- Ogni SKU supporta un set di funzionalità diverso e ogni set di funzionalità ha un prezzo diverso.

Una SKU viene visualizzata sotto l'offerta padre in Azure Marketplace e come entità acquistabile a sé stante nel portale di Azure.

## <a name="to-create-a-new-offer"></a>Per creare un nuova offerta

1.  Accedere al [portale Cloud Partner](http://cloudpartner.azure.com/).

2.  Nella barra di spostamento a sinistra selezionare **+ Nuova offerta** e quindi **Macchine virtuali**.

    ![Nuova offerta di macchine virtuali](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  In **Nuova offerta** selezionare **Editor**.

![Nuova offerta - Editor](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  In **Editor** specificare le informazioni nelle viste seguenti:
    - Impostazioni dell'offerta
    - SKU
    - Marketplace
    - Supporto. Ogni vista contiene un set di campi da compilare. I campi obbligatori sono contrassegnati con un asterisco rosso (\*)

## <a name="to-configure-offer-settings"></a>Per configurare le Impostazioni dell'offerta

1. In Impostazioni dell'offerta configurare i campi **Identità dell'offerta** seguenti.

    **ID offerta**

     Identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere al massimo 50 caratteri. Ad esempio, se l'editore **contoso** pubblica un'offerta con ID **sample-vm**, in Azure Marketplace l'offerta viene visualizzata come **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
    >[!Note]
    >Questo campo viene bloccato dopo la pubblicazione dell'offerta.

    **ID editore**

    Un elenco a discesa per il profilo di pubblicazione. Scegliere il profilo con cui si vuole pubblicare l'offerta. 
    >[!Note]
    >Questo campo è bloccato dopo la pubblicazione dell'offerta.

    **Nome**

    Il nome visualizzato dell'offerta. Questo nome viene visualizzato su Azure Marketplace e nel portale di Azure. Può contenere massimo 50 caratteri. Seguire le istruzioni seguenti al momento di scegliere il nome dell'offerta:
    -  Includere un nome di marchio riconoscibile per il prodotto. 
    - Non includere il nome della società, a meno che non corrisponda al nome con cui l'offerta viene commercializzata.
    - Se l'offerta viene commercializzata sul proprio sito Web, assicurarsi che il nome sia identico al nome del sito Web.

2. Scegliere **Salva** per completare l'impostazione dell'offerta.

## <a name="to-create-a-sku"></a>Per creare una SKU

1. Selezionare **SKU**. 
2. Selezionare **Add a SKU** (Aggiungi SKU) per immettere un ID SKU. L'ID SKU è l'identificatore univoco della SKU in un'offerta. Questo ID è visibile negli URL dei prodotti, nei modelli di Azure Resource Manager e nei report di fatturazione. L'ID SKU:
    - Può essere composto da massimo 50 caratteri.
    - Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-).
    - L'ID non può terminare con un trattino

    ![Aggiungere uno SKU](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>Configurare i dettagli della SKU

Dopo aver aggiunto una SKU, questa compare nell'elenco delle SKU in visualizzazione SKU. Per visualizzare i dettagli della SKU, selezionarne il nome. È possibile usare la visualizzazione Dettagli per aggiungere informazioni nei campi seguenti.

### <a name="hide-this-sku"></a>Hide this SKU (Nascondi questo SKU)

Usare questa impostazione per gestire la visibilità della SKU. Se l'opzione è disattivata, la SKU è visibile ai clienti in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/). È consigliabile nascondere lo SKU se si desidera che sia disponibile solo tramite modelli di soluzione e non per l'acquisto singolo.

### <a name="cloud-availability"></a>Cloud Availability (Disponibilità cloud)

Questo campo consente di definire la disponibilità dello SKU nei diversi cloud di Azure.

**Azure pubblico**

Questa SKU può essere distribuita ai clienti in tutte le aree di Azure pubbliche dotate di integrazione con il Marketplace.

**Cloud di Azure per enti pubblici**

Questa SKU può essere distribuita nel cloud di Azure per enti pubblici. Prima della pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), è consigliabile testare la propria soluzione e verificare che funzioni come previsto. Per lo staging e il test, [richiedere un account di prova](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>Microsoft Azure per enti pubblici è un Government-Community Cloud con accesso controllato per i clienti degli enti federali, statali e locali degli Stati Uniti E dei partner che forniscono servizi a tali enti.

### <a name="countryregion-availability"></a>Country/Region availability (Disponibilità paese/area geografica)

Questo campo identifica le aree in cui la SKU sarà disponibile per l'acquisto. I paesi in cui rendere disponibile lo SKU vanno considerati con attenzione. Alcuni paesi sono classificati come "Microsoft Tax Remitted Country" (Paese a rimessa imposte Microsoft).

-   In questi paesi è Microsoft a riscuotere le imposte dai clienti e a versare (rimettere) le imposte al governo.

-   In altri paesi sono i partner a essere responsabili della riscossione delle imposte dai clienti e del versamento delle imposte al governo. Se si sceglie di vendere in questi ultimi, è necessario avere la capacità di calcolare e versare le imposte in tali paesi.

![Selezione disponibilità per paese/area geografica](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>Prezzi

Attualmente sono supportati due modelli di prezzi.

#### <a name="bring-your-own-license-byol"></a>Bring-Your-Own-License (BYOL)

Il partner gestisce le licenze del software in esecuzione nella macchina virtuale. Microsoft addebiterà solo i costi di infrastruttura.

#### <a name="usage-based-monthly-billed-sku"></a>SKU fatturato mensilmente in base all'utilizzo

I clienti ricevono un addebito su base oraria secondo le tariffe stabilite dagli editori in base alle dimensioni della macchina virtuale. In caso di **fatturazione oraria** degli SKU, il prezzo totale sarà la somma del costo del software addebitato dall'editore più il costo dell'infrastruttura addebitato da Microsoft. Questo costo totale sarà visualizzato al cliente come prezzo orario e mensile quando prende in considerazione l'acquisto. La fatturazione in questo caso sarà su base mensile.

All'interno del modello basata sull'utilizzo, è necessario specificare altre impostazioni.

**Versione di prova gratuita**

È possibile specificare se si desidera fornire una versione di valutazione gratuita per i clienti.
In questo caso al cliente non viene addebitato il costo del software per i primi 30/90 giorni (a seconda della selezione) dopo la distribuzione della macchina virtuale. Trascorso il periodo di prova, i clienti ricevono un addebito su base oraria secondo le tariffe stabilite dagli editori nel modello orario.

**Prezzo per core**

È possibile impostare il prezzo per ogni core dello SKU. A tale scopo, è sufficiente immettere un prezzo di base per un singolo core e automaticamente verranno calcolati i prezzi per gli altri core. Immettere i prezzi in USD nel portale: i prezzi per le altre aree verranno calcolati automaticamente. È possibile verificare i prezzi nelle altre aree tramite la funzionalità di **esportazione dei dati dei prezzi**

![Prezzo per core](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**Prezzi singoli**

Se si desidera definire il prezzo di ogni core separatamente, è possibile impostare singolarmente il prezzo di ogni set di core.

![Prezzi singoli](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**Esportazione/importazione dei prezzi**

È possibile esportare i prezzi configurati tramite il portale per apportare modifiche tramite l'interfaccia di Excel. Ciò consente anche di verificare i prezzi per ogni area e i prezzi nelle valute locali.
Facendo clic sull'opzione di **esportazione dei prezzi**, verrà scaricato un file di Excel prepopolato con i dettagli dei prezzi. Sarà quindi possibile modificarli all'interno di Excel e quindi usare l'opzione di **importazione dei prezzi** per importare le modifiche apportate.
I prezzi importati si rifletteranno anche nel portale.

All'interno dell'Excel, i prezzi per le diverse aree sono elencati nella valuta locale. Il tasso di cambio che utilizziamo viene aggiornato quotidianamente.

![Esportazione/importazione dei prezzi con esempi di tasso di cambio](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   I prezzi non possono essere modificati dopo la pubblicazione dell'offerta. È comunque possibile aggiungere o rimuovere aree geografiche supportate.
>-   Il prezzo viene addebitato all'utente in aggiunta ai  [prezzi delle macchine virtuali di \'Azure](https://aka.ms/vmpricingdetails).
>-   I prezzi sono impostati per tutte le aree in valuta locale, usando i tassi di cambio disponibili al momento dell'impostazione dei prezzi.
>-   Per impostare o visualizzare il prezzo di ogni area geografica singolarmente, esportare il foglio di calcolo dei prezzi e importarlo con i prezzi personalizzati.

## <a name="vm-images"></a>VM Images (Immagini di VM)

La sezione successiva da completare sarà quella delle immagini di VM. Prima di passare a questa sezione, è necessario che il disco rigido virtuale che si desidera pubblicare sia pronto. Ecco di seguito alcuni collegamenti utili per la creazione del disco rigido virtuale:

-   [Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Creazione e caricamento di un disco rigido virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Creare e testare una VM Linux da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Creazione e caricamento di un disco rigido virtuale Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Creare e testare una VM Windows da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Come risolvere problemi comuni incontrati durante la creazione del disco rigido virtuale](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting)

Quando il disco rigido virtuale è pronto, è possibile avviare la compilazione di questa sezione.
Ecco i dettagli relativi ad alcuni campi.

### <a name="recommended-vm-sizes"></a>Recommended VM Sizes (Dimensioni VM consigliate)

Selezionare fino a sei dimensioni della macchina virtuale. Questi consigli vengono visualizzati dal cliente in Azure Marketplace e nel pannello Piano tariffario del portale di Azure quando sceglie di acquistare e distribuire l'immagine. **Si tratta esclusivamente di consigli. Il cliente può selezionare qualsiasi dimensione di macchina virtuale appropriata ai dischi specificati nell'immagine.**  La cattura di schermata seguente mostra le dimensioni di VM consigliate che un cliente vedrà nel portale di Azure.


![Recommended VM Sizes (Dimensioni VM consigliate)](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>Open Ports (Porte aperte)

Specificare le porte che devono essere aperte e disponibili. Queste porte vengono aperte durante la distribuzione delle macchine virtuali.

### <a name="adding-vm-images"></a>Aggiunta delle immagini di VM

Nel passaggio successivo viene aggiunta un'immagine di macchina virtuale per la SKU. È possibile aggiungere fino a 8 versioni del disco per ogni SKU. Solo il numero più alto di versione del disco per un particolare SKU verrà visualizzato in Azure Marketplace. Gli altri saranno visibili tramite API.

In **Versione disco** selezionare **+ Nuova versione**. Vengono visualizzati i campi seguenti, che è necessario compilare.

#### <a name="vm-image-version"></a>Versione dell'immagine di VM

La versione dell'immagine di VM deve seguire il formato della [versione semantica](http://semver.org/). Le versioni devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Le immagini in SKU diversi possono avere versioni principali e secondarie diverse. Le versioni in uno SKU dovrebbero essere solo modifiche incrementali, che aumentano la versione della patch (da X.Y.Z a Z).

#### <a name="os-vhd-url"></a>OS VHD URL (URL DEL DISCO RIGIDO VIRTUALE DEL SISTEMA OPERATIVO)

Immettere l'[URI di firma di accesso condiviso](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) creato per il disco rigido virtuale del sistema operativo.

Se sono presenti dischi dati associati a questa SKU, è possibile scegliere di aggiungerli selezionando il collegamento **+ New data disk** (+ Nuovo disco dati). Con questa azione vengono visualizzati altri campi da compilare.

#### <a name="lun-vhd-url"></a>LUN VHD URL (URL DEL DISCO RIGIDO VIRTUALE DEL LUN)

Immettere l'[URI di firma di accesso condiviso](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#52-get-the-shared-access-signature-uri-for-your-vm-images) per il disco dati.

#### <a name="lun-number"></a>LUN Number (Numero LUN)

Assegnare un numero a questo LUN. Questo numero sarà riservato per il disco dati nella SKU.

>[!Note]
>Dopo aver pubblicato una SKU con una determinata versione di VM e dischi dati, questi vengono bloccati e non possono essere cambiati. Per le altre eventuali versioni di VM che vengono aggiunte alla SKU non è possibile cambiare il numero di dischi dati che devono essere supportati.

#### <a name="common-sas-url-issues--fixes"></a>Problemi e correzioni comuni dell'URL SAS

| Problema                                                                 | Message                                                                           | Correzione                                                           |  Collegamento alla documentazione                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| Errore durante la copia di immagini: "?" non è presente nell'URL SAS                | Errore: copia di immagini. Non è possibile scaricare il BLOB usando l'URI SAS fornito.       | Aggiornare l'URL SAS con gli strumenti consigliati                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Errore durante la copia di immagini: parametri "st" e "se" non presenti nell'URL SAS   | Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.        | Aggiornare le date di inizio e fine nell'URL SAS             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Errore durante la copia di immagini: "sp=rl" non è presente nell'URL SAS                    | Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS         | Aggiornare l'URL SAS con le autorizzazioni impostate come "Lettura" ed "Elenco"     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Errore nella copia di immagini: l'URL SAS presenta spazi vuoti nel nome del file con estensione vhd     | Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.        | Aggiornare l'URL SAS eliminando gli spazi vuoti                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Errore nella copia di immagini: errore di autorizzazione URL SAS               | Errore: copia di immagini. Non è possibile scaricare il BLOB a causa dell'errore di autorizzazione     | Rigenerare l'URL SAS                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Per configurare il Marketplace

Usare la vista Marketplace per configurare i campi visualizzati per l'offerta in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Preview Subscription Ids (ID sottoscrizione di anteprima)

Elenco di ID di sottoscrizione di Azure che devono avere accesso all'offerta dopo la sua pubblicazione. Queste sottoscrizioni consentite permettono di testare l'offerta in anteprima prima di pubblicarla. Il portale Cloud Partner consente di inserire nell'elenco degli elementi consentiti fino a 100 sottoscrizioni.

### <a name="suggested-categories"></a>Suggested Categories (Categorie suggerite)

Nell'elenco fornito selezionare fino a 5 categorie con cui l'offerta può essere associata in modo ottimale. Le categorie selezionate saranno usate per il mapping dell'offerta alle categorie di prodotti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

Gli esempi seguenti mostrano informazioni sul marketplace in Azure Marketplace e nel portale di Azure.

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Portale di Azure**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)



![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>Linee guida per il logo

Seguire queste linee guida per qualsiasi logo caricato nel portale Cloud Partner:

-   La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.

-   I colori del tema del portale di Azure sono il bianco e il nero. Evitare di usare questi colori per lo sfondo del logo. Usare un colore che faccia risaltare il logo nel portale di Azure. Si consiglia di usare colori primari semplici.

    >[!Note] 
    >Se si usa uno sfondo trasparente, verificare che il logo e il testo non siano di colore bianco, nero o blu.

-   Non usare uno sfondo sfumato sul logo.

-   Evitare di inserire testo sul logo. Incluso il nome della società o del marchio. L'aspetto del logo deve essere *semplice* e senza sfumature.

-   Il logo non deve essere allungato.

#### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. Tuttavia, una volta caricato, il logo non può essere eliminato. Il partner deve seguire le istruzioni di Azure Marketplace per le icone del logo alto.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Linee guida per l'icona del logo alto

-   Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il colore carattere bianco. Evitare di usare un colore chiaro per lo sfondo. Sfondi neri, bianchi e trasparenti non sono consentiti per le icone del logo alto.

-   Il nome visualizzato dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e il pulsante Crea vengono incorporati a livello di codice all'interno del logo alto quando l'offerta viene presentata. Non inserire alcun testo durante la progettazione del logo alto. Lasciare uno spazio vuoto a destra del logo. Questo spazio deve avere dimensioni di 415 x 100 pixel ed essere scostato con un offset di 370 px da sinistra.

![Esempio di logo alto](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>Gestione dei lead

Per configurare le impostazioni di gestione dei lead dell'offerta, seguire [queste istruzioni](./cloud-partner-portal-get-customer-leads.md).

## <a name="to-configure-support"></a>Per configurare il supporto

Usare la vista Assistenza per specificare le informazioni seguenti:

- Contatti del servizio di assistenza dell'azienda, ad esempio il reparto progettazione.
- Contatti dell'assistenza clienti.

## <a name="to-publish-the-offer"></a>Per pubblicare l'offerta

Il passaggio finale consiste nella pubblicazione dell'offerta. Seguire [queste istruzioni per pubblicare l'offerta](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md).

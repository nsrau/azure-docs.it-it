---
title: Pubblicare una macchina virtuale in Azure Marketplace | Microsoft Docs
description: Questo articolo illustra i dettagli per la pubblicazione di una macchina virtuale tramite il portale per Cloud Partner
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6e5b1eb51c10bb51762f6815eda72add1341a4ad
ms.lasthandoff: 04/18/2017


---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Pubblicare una macchina virtuale in Azure Marketplace

Questo articolo elenca i vari passaggi necessari per la pubblicazione di una macchina virtuale in Azure Marketplace.

## <a name="what-are-pre-requisites-for-publishing-a-vm"></a>Quali sono i prerequisiti per la pubblicazione di una macchina virtuale (VM)

Prerequisiti per l'inserimento in Azure Marketplace

1.  Tecnici

    -   [Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

    -   [Creazione e caricamento di un disco rigido virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    -   [Creare e testare una VM Linux da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

    -   [Creazione e caricamento di un disco rigido virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

    -   [Creare e testare una VM Windows da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

    -   [Come risolvere problemi comuni incontrati durante la creazione del disco rigido virtuale](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


2.  Non tecnici (requisiti aziendali)

    -   La società (o la sua affiliata) deve avere sede in uno dei paesi di origine della vendita supportati da Azure Marketplace

    -   Il prodotto deve essere concesso in licenza in modo da essere compatibile con i modelli di fatturazione supportati da Azure Marketplace

    -   Il partner ha la responsabilità di rendere il supporto tecnico disponibile per i clienti in una modalità ragionevole dal punto di vista commerciale, che si tratti di supporto gratuito, a pagamento o attraverso la community.

    -   Il partner ha la responsabilità di concedere in licenza il software e le dipendenze da software di terze parti.

    -   È necessario offrire contenuti che soddisfino i criteri affinché l'offerta sia inserita in Azure Marketplace e nel portale di gestione di Azure

    -   È necessario accettare le condizioni delle Politiche di partecipazione a Microsoft Azure Marketplace e del Contratto per gli editori

    - È necessario accettare le [Condizioni per l'utilizzo del sito Web di Microsoft Azure](https://azure.microsoft.com/support/legal/website-terms-of-use/), l'[Informativa sulla privacy di Microsoft](http://www.microsoft.com/privacystatement/default.aspx) e il [Contratto del Programma Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-vm-offer"></a>Come creare un nuova offerta di VM

Dopo aver soddisfatto tutti i prerequisiti, è possibile avviare la creazione dell'offerta di macchina virtuale. Prima di iniziare, ecco una breve panoramica di un'offerta e di uno SKU

### <a name="offer"></a>Offerta
Un'offerta di macchina virtuale corrisponde a una classe di offerta di prodotti di un editore. Se si dispone di un nuovo tipo di prodotto/macchina virtuale che si desidera rendere disponibile in Azure Marketplace, una nuova offerta è la soluzione giusta. Un'offerta è una raccolta di SKU. Ogni offerta viene visualizzata come entità a sé stante in Azure Marketplace. 

### <a name="sku"></a>SKU
Uno SKU è la più piccola unità acquistabile di un'offerta. All'interno della stessa classe di prodotti (offerta), gli SKU consentono di distinguere tra diverse funzionalità supportate, tipi di immagini di VM e modelli di fatturazione supportati. 

Aggiungere più SKU se si desidera supportare modelli di fatturazione diversi, ad esempio Bring Your Own License (BYOL), con pagamento in base al consumo (Pay-as-you-Go, PAYG) e così via. Aggiungere più SKU quando ogni SKU supporta un set di funzionalità diverso e per ognuno di essi i prezzi vengono determinati in modo diverso. Aggiungere più SKU anche se si dispone di immagini di VM diverse per cloud pubblici e sovrani. 

Uno SKU viene visualizzato sotto l'offerta padre in Azure Marketplace, mentre viene visualizzato come entità acquistabile a sé stante nel portale di Azure. 

1.  Accedere al [portale per Cloud Partner](http://cloudpartner.azure.com/).

2.  Nella barra di spostamento a sinistra scegliere "+ New Offer" (+ Nuova offerta) e selezionare "Macchine virtuali".

    ![publishvm1][1]

3.  Viene ora aperta la vista "Editor" della nuova offerta in cui è possibile iniziare la creazione.

   ![publishvm2][2]

4.  I moduli da compilare sono a sinistra all'interno della vista "Editor". Ogni modulo è costituito da un set di campi da compilare. I campi obbligatori sono contrassegnati con un asterisco rosso (\*).

> Esistono 4 moduli principali per la creazione di una VM

-   Impostazioni dell'offerta

-   SKU

-   Marketplace

-   Supporto

## <a name="how-to-fill-out-the-offer-settings-form"></a>Come compilare il modulo delle impostazioni dell'offerta
Il modulo delle impostazioni dell'offerta è un modulo di base in cui specificare le impostazioni di offerta. I diversi campi sono descritti di seguito.

### <a name="offer-id"></a>Offer ID (ID offerta)
Identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID sarà visibile negli URL dei prodotti, nei modelli ARM e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Si noti che questo campo è bloccato dopo la pubblicazione dell'offerta.

Ad esempio, se un editore **contoso** pubblica un'offerta con ID **sample-vm**, verrà visualizzata in Azure Marketplace come **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**

### <a name="publisher-id"></a>Publisher ID (ID editore)
Questo elenco a discesa consente di scegliere il profilo di pubblicazione in cui si desidera pubblicare l'offerta. Si noti che questo campo è bloccato dopo la pubblicazione dell'offerta.

### <a name="name"></a>Nome
Nome visualizzato dell'offerta. È il nome che verrà visualizzato in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/). Può contenere massimo 50 caratteri. Le linee guida prevedono qui l'inclusione di un nome di marchio riconoscibile per il prodotto. Non includere il nome della società, a meno che non corrisponda al nome con cui viene commercializzato. Se si sta proponendo questa offerta sul proprio sito Web, assicurarsi che il nome corrisponda esattamente a quello con cui viene visualizzato nel sito Web.

Fare clic su "Salva" per salvare le voci immesse. Il passaggio successivo sarà quello di aggiungere gli SKU dell'offerta.

## <a name="how-to-create-skus"></a>Come creare gli SKU
Fare clic sul modulo "SKU". Qui è disponibile un'opzione "Add a SKU" (Aggiungi uno SKU) che, se selezionata, consente di immettere l'ID dello SKU.

![publishvm4][4]

Tale ID è l'identificatore univoco dello SKU all'interno di un'offerta. Questo ID sarà visibile negli URL dei prodotti, nei modelli ARM e nei report di fatturazione. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Si noti che questo campo è bloccato dopo la pubblicazione dell'offerta. All'interno di un'offerta possono essere presenti più SKU. È necessario uno SKU per ogni immagine che si prevede di pubblicare.

Dopo essere stato aggiunto, lo SKU verrà visualizzato nell'elenco di SKU all'interno del modulo "SKU". Fare clic sul nome dello SKU per i dettagli del determinato SKU. Ecco i dettagli relativi ad alcuni campi.

### <a name="hide-this-sku"></a>Hide this SKU (Nascondi questo SKU)
Questo flag consente di definire se questo SKU specifico debba essere visibile o meno ai clienti in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/). È consigliabile nascondere lo SKU se si desidera che sia disponibile solo tramite modelli di soluzione e non per l'acquisto singolo.

### <a name="cloud-availability"></a>Cloud Availability (Disponibilità cloud) 
Questo campo consente di definire la disponibilità dello SKU nei diversi cloud di Azure.

#### <a name="public-azure"></a>Public Azure (Azure pubblico) 

Questo SKU potrà essere distribuito ai clienti in tutte le aree di Azure pubbliche dotate di integrazione con il Marketplace.

#### <a name="azure-government-cloud"></a>Azure Government Cloud (Cloud Azure per enti pubblici) 

Questo SKU potrà essere distribuito sul cloud di Azure per enti pubblici. Prima della pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), è consigliabile testare la propria soluzione e verificare che funzioni come previsto. Per lo staging e il test richiedere un account di prova [qui](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). Si noti che Microsoft Azure per enti pubblici è un cloud della community governativa con accesso controllato per i clienti degli enti federali, statali e locali degli Stati Uniti E dei partner che forniscono servizi a tali enti.

### <a name="countryregion-availability"></a>Country/Region availability (Disponibilità paese/area geografica)
Questo campo determina le aree in cui lo SKU sarà disponibile per l'acquisto. I paesi in cui rendere disponibile lo SKU vanno considerati con attenzione. Alcuni paesi sono classificati come "Microsoft Tax Remitted Country" (Paese a rimessa imposte Microsoft).

-   In questi paesi è Microsoft a riscuotere le imposte dai clienti e a versare (rimettere) le imposte al governo.

-   Negli altri, sono i partner a essere responsabili della riscossione delle imposte dai clienti e del versamento delle imposte al governo. Se si sceglie di vendere in questi ultimi, è necessario avere la capacità di calcolare e versare le imposte in tali paesi.

![publishvm5][5]

### <a name="pricing"></a>Prezzi 
Esistono attualmente 2 tipi di prezzi modelli supportati.

#### <a name="bring-your-own-license-byol"></a>Bring-Your-Own-License (BYOL)

Il partner gestisce le licenze del software in esecuzione nella macchina virtuale. Microsoft addebiterà solo i costi di infrastruttura.

#### <a name="usage-based-monthly-billed-sku"></a>SKU fatturato mensilmente in base all'utilizzo

I clienti ricevono un addebito su base oraria secondo le tariffe stabilite dagli editori in base alle dimensioni della macchina virtuale. In caso di **fatturazione oraria** degli SKU, il prezzo totale sarà la somma del costo del software addebitato dall'editore più il costo dell'infrastruttura addebitato da Microsoft. Questo costo totale sarà visualizzato al cliente come prezzo orario e mensile quando prende in considerazione l'acquisto. La fatturazione in questo caso sarà su base mensile.

All'interno del modello basata sull'utilizzo, è necessario specificare altre impostazioni.

##### <a name="free-trial"></a>Versione di valutazione gratuita

È possibile specificare se si desidera fornire una versione di valutazione gratuita per i clienti. In questo caso al cliente non viene addebitato il costo del software per i primi 30 giorni (gratuiti) dopo la distribuzione della VM. Dopo 30 giorni i clienti ricevono un addebito su base oraria secondo le tariffe stabilite dagli editori nel modello orario.

##### <a name="per-core-pricing"></a>Prezzo per core

È possibile impostare il prezzo per ogni core dello SKU. A tale scopo, è sufficiente immettere un prezzo di base per un singolo core e automaticamente verranno calcolati i prezzi per gli altri core. Immettere i prezzi in USD nel portale: i prezzi per le altre aree verranno calcolati automaticamente. È possibile verificare i prezzi nelle altre aree tramite la funzionalità di **esportazione dei dati dei prezzi**

![publishvm6][6]

##### <a name="discrete-pricing"></a>Prezzi singoli

Se si desidera definire il prezzo di ogni core separatamente, è possibile impostare singolarmente il prezzo di ogni set di core.

![publishvm7][7]

##### <a name="export-import-pricing"></a>Esportazione/importazione dei prezzi

È possibile esportare i prezzi configurati tramite il portale per apportare modifiche tramite l'interfaccia di Excel. Ciò consente anche di verificare i prezzi per ogni area e i prezzi nelle valute locali. Facendo clic sull'opzione di **esportazione dei prezzi**, verrà scaricato un file di Excel prepopolato con i dettagli dei prezzi. Sarà quindi possibile modificarli all'interno di Excel e quindi usare l'opzione di **importazione dei prezzi** per importare le modifiche apportate. I prezzi importati si rifletteranno anche nel portale.

All'interno dell'Excel, i prezzi per le diverse aree sono elencati nella valuta locale. Il tasso di cambio che utilizziamo viene aggiornato quotidianamente.

![publishvm8][8]

##### <a name="important-notes-about-pricing"></a>Note importanti sui prezzi

-   I prezzi non sono modificabili dopo la pubblicazione dell'offerta. È comunque possibile aggiungere o rimuovere aree geografiche supportate.

-   Il prezzo viene addebitato all'utente in aggiunta ai [prezzi di macchine virtuali di Azure](http://aka.ms/vmpricingdetails).

-   I prezzi sono impostati per tutte le aree in valuta locale ai tassi di cambio disponibili al momento dell'impostazione dei prezzi.

-   Per impostare o visualizzare il prezzo di ogni area geografica singolarmente, esportare il foglio di calcolo dei prezzi e importarlo con i prezzi personalizzati.

### <a name="vm-images"></a>VM Images (Immagini di VM)
La sezione successiva da completare sarà quella delle immagini di VM. Prima di passare a questa sezione, è necessario che il disco rigido virtuale che si desidera pubblicare sia pronto. Ecco di seguito alcuni collegamenti utili per la creazione del disco rigido virtuale:

-   [Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Creazione e caricamento di un disco rigido virtuale Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Creare e testare una VM Linux da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Creazione e caricamento di un disco rigido virtuale Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Creare e testare una VM Windows da un'immagine](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Come risolvere problemi comuni incontrati durante la creazione del disco rigido virtuale](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


Quando il disco rigido virtuale è pronto, è possibile avviare la compilazione di questa sezione. Ecco i dettagli relativi ad alcuni campi.

#### <a name="recommended-vm-sizes"></a>Recommended VM Sizes (Dimensioni VM consigliate)

Selezionare fino a sei dimensioni della macchina virtuale. Questi consigli vengono visualizzati dal cliente in Azure Marketplace e nel pannello Piano tariffario del portale di Azure quando sceglie di acquistare e distribuire l'immagine. **Si tratta esclusivamente di consigli. Il cliente può selezionare qualsiasi dimensione di macchina virtuale appropriata ai dischi specificati nell'immagine.** Di seguito è illustrato come le dimensioni della VM appariranno al cliente nel portale di Azure.

![publishvm9][9]

#### <a name="open-ports"></a>Open Ports (Porte aperte)

Specificare le porte che devono essere aperte e disponibili. Queste porte vengono aperte durante la distribuzione delle macchine virtuali.

#### <a name="adding-vm-images"></a>Aggiunta delle immagini di VM

Il passaggio successivo prevede l'aggiunta di un'immagine di VM per lo SKU. È possibile aggiungere fino a 8 versioni del disco per ogni SKU. Solo il numero più alto di versione del disco per un particolare SKU verrà visualizzato in Azure Marketplace. Gli altri saranno visibili tramite API. 

Iniziare facendo clic su "+ New version" (+ Nuova versione) nella sezione dedicata alla **versione disco**. Verrà visualizzata una raccolta di campi da compilare.

##### <a name="vm-image-version"></a>Versione dell'immagine di VM

La versione dell'immagine di VM deve seguire il formato della [versione semantica](http://semver.org/). Le versioni devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Le immagini in SKU diversi possono avere versioni principali e secondarie diverse. Le versioni in uno SKU dovrebbero essere solo modifiche incrementali, che aumentano la versione della patch (da X.Y.Z a Z).

##### <a name="os-vhd-url"></a>OS VHD URL (URL DEL DISCO RIGIDO VIRTUALE DEL SISTEMA OPERATIVO)

Immettere l'[URI di firma di accesso condiviso](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) creato per il disco rigido virtuale del sistema operativo.

Se sono presenti dischi dati associati a questo SKU, è possibile scegliere di aggiungerli questa facendo clic sul collegamento "+ New data disk" (+ Nuovo disco dati). Verranno visualizzati altri campi da compilare.

##### <a name="lun-vhd-url"></a>LUN VHD URL (URL DEL DISCO RIGIDO VIRTUALE DEL LUN)

Immettere l'[URI di firma di accesso condiviso](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) per il disco dati.

##### <a name="lun-number"></a>LUN Number (Numero LUN)

Assegnare un numero a questo LUN. Tale numero sarà riservato per il disco dati all'interno dello SKU.

Si noti che dopo aver pubblicato uno SKU con una determinata versione di VM e dischi dati, questi vengono bloccati e non possono essere modificati. Per le altre eventuali versioni di VM che vengono aggiunte allo SKU non è possibile modificare il numero di dischi dati che devono essere supportati.

##### <a name="common-sas-url-issues--fixes"></a>Problemi e correzioni comuni dell'URL SAS

| **Problema**                                                           | **Messaggio di errore**                                                           | **Correzione**                                                   | **Link alla documentazione**                                                                                    |
|---------------------------------------------------------------------|-------------------------------------------------------------------------------|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| Errore durante la copia di immagini: "?" non è presente nell'URL SAS             | Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.    | Aggiornare l'URL SAS con gli strumenti consigliati                | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Errore durante la copia di immagini: parametri "st" e "se" non presenti nell'URL SAS | Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.    | Aggiornare le date di inizio e fine nell'URL SAS         | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Errore durante la copia di immagini: "sp=rl" non è presente nell'URL SAS                  | Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS     | Aggiornare l'URL SAS con le autorizzazioni impostate come "Lettura" ed "Elenco" | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Errore nella copia di immagini: l'URL SAS presenta spazi vuoti nel nome del file con estensione vhd   | Errore: copia di immagini. Non è possibile scaricare il BLOB fornito nell'URI SAS.    | Aggiornare l'URL SAS eliminando gli spazi vuoti                   | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |
| Errore nella copia di immagini: errore di autorizzazione URL SAS             | Errore: copia di immagini. Non è possibile scaricare il BLOB a causa dell'errore di autorizzazione | Rigenerare l'URL SAS                                    | <https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/> |

## <a name="marketplace-form"></a>Modulo Marketplace
Il modulo Marketplace all'interno di un'offerta di macchina virtuale richiede la compilazione di campi che verranno visualizzati in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/). Ecco i dettagli relativi ad alcuni campi.

#### <a name="preview-subscription-ids"></a>Preview Subscription Ids (ID sottoscrizione di anteprima)

Immettere qui un elenco di ID di sottoscrizione di Azure che devono avere accesso all'offerta dopo la sua pubblicazione. Queste sottoscrizioni consentite permetteranno di testare l'offerta in anteprima prima di pubblicarla. Il portale per i partner permette fino a 100 sottoscrizioni consentite.

#### <a name="suggested-categories"></a>Suggested Categories (Categorie suggerite)

Nell'elenco fornito selezionare fino a 5 categorie con cui l'offerta può essere associata in modo ottimale. Le categorie selezionate saranno usate per il mapping dell'offerta alle categorie di prodotti disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com) e nel [portale di Azure](https://portal.azure.com/).

Ecco alcune delle posizioni in cui vengono visualizzati i dati forniti in questo modulo.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10][10]

![publishvm11][11]

![publishvm15][15]

##### <a name="azure-portal"></a>Portale di Azure

![publishvm12][12]

![publishvm13][13]

##### <a name="logo-guidelines"></a>Linee guida per il logo

Ogni logo caricato nel portale per Cloud Partner deve rispettare le seguenti linee guida:

-   La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.

-   I colori del tema del portale di Azure sono il bianco e il nero. Pertanto evitare di usare questi colori per lo sfondo dei logo. Usare un colore che faccia risaltare i logo nel portale di Azure. Si consiglia di usare colori primari semplici. **Se si usa lo sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.**

-   Non usare uno sfondo sfumato sul logo.

-   Evitare di inserire testo, anche il nome del marchio o della società, sul logo. L'aspetto del logo deve essere semplice e senza sfumature.

-   Il logo non può essere allungato.

##### <a name="hero-logo"></a>Logo alto

Il logo alto è facoltativo. L'autore può scegliere di non caricare un logo alto. Tuttavia, una volta caricata, l'icona del logo alto non può essere eliminata. A quel punto il partner deve seguire le istruzioni di Azure Marketplace per le icone del logo alto.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Linee guida per l'icona del logo alto

-   Il nome visualizzato dell'autore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con il testo bianco. Di conseguenza non è consigliabile usare un colore chiaro come sfondo dell'icona del logo alto. Lo sfondo nero, bianco o trasparente non è ammesso per le icone del logo alto.

-   Il nome visualizzato dell'autore, il titolo del piano, il riepilogo lungo dell'offerta e il pulsante Crea vengono incorporati a livello di codice all'interno del logo alto quando l'offerta viene elencata. Pertanto non è necessario inserire testo mentre si progetta il logo alto. Lasciare uno spazio vuoto a destra, in quanto il testo (ad esempio nome visualizzato dell'entità di pubblicazione, titolo del piano, riepilogo lungo dell'offerta) verrà incluso a livello di codice da Microsoft in questa posizione. Lo spazio vuoto per il testo deve essere 415 x 100 a destra e viene spostato con un offset di 370 px da sinistra.

![publishvm14][14]

#### <a name="lead-management"></a>Gestione dei lead

Per compilare le impostazioni di gestione dei lead dell'offerta, seguire le istruzioni riportate[qui](./cloud-partner-portal-get-customer-leads.md#how-to-connect-your-crm-system-with-the-cloud-partner-portal)

## <a name="support-form"></a>Modulo del supporto
Il modulo successivo da compilare è quello del supporto. Questo modulo richiede di specificare i contatti del supporto fornito dalla società, ad esempio le informazioni di contatto del supporto tecnico e le informazioni di contatto dell'assistenza clienti. 

## <a name="how-to-publish-an-offer"></a>Come pubblicare un'offerta
Ora che la bozza dell'offerta è stata redatta, il passaggio successivo sarà pubblicarla in Azure Marketplace. Seguire le istruzioni per [pubblicare l'offerta in Azure Marketplace](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) 

[1]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png
[2]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png
[3]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm3.png
[4]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png
[5]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png
[6]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png
[7]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png
[8]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png
[9]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png
[10]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png
[11]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png
[12]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png
[13]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png
[14]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png
[15]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png

---
title: Creazione di piani per un'offerta di macchina virtuale in Azure Marketplace
description: Informazioni su come creare piani per un'offerta di macchina virtuale in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 023dc877158c7074f46945893d40291e94ab2f09
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629557"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Come creare piani per un'offerta di macchina virtuale

Nella pagina **panoramica piano** (selezionare dal menu di spostamento a sinistra nel centro per i partner) è possibile fornire diverse opzioni di piano all'interno della stessa offerta. Un'offerta richiede almeno un piano (in precedenza noto come SKU), che può variare in base ai destinatari della monetizzazione, all'area di Azure, alle funzionalità o alle immagini di macchina virtuale.

È possibile creare fino a 100 piani per ogni offerta: fino a 45 di questi possono essere privati. Scopri di più sui piani privati in [offerte private in Microsoft Commercial Marketplace](private-offers.md).

Dopo aver creato i piani, selezionare la scheda **Panoramica del piano** per visualizzare:

- Nomi dei piani
- Modelli di licenza
- Gruppo di destinatari (pubblico o privato)
- Stato attuale di pubblicazione
- Azioni disponibili

Le azioni disponibili nel riquadro **panoramica piano** variano a seconda dello stato corrente del piano.

- Se il piano è in stato di bozza, selezionare **Elimina bozza**.
- Se il piano è pubblicato in tempo reale, selezionare **Stop sell plan** (Interrompi la vendita del piano) o **Sync private audience** (Sincronizza gruppo di destinatari privato).

## <a name="create-a-new-plan"></a>Creare un nuovo piano

Selezionare **+ Crea nuovo piano** nella parte superiore.

Nella finestra di dialogo **nuovo piano** immettere un **ID piano** univoco per ogni piano di questa offerta. Questo ID sarà visibile ai clienti all'indirizzo Web del prodotto. Usare solo lettere minuscole, numeri, trattini o caratteri di sottolineatura e fino a un massimo di 50 caratteri.

> [!NOTE]
> Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

Immettere un **nome di piano**. I clienti visualizzano questo nome quando decidono quale piano scegliere nell'offerta. Creare un nome univoco che indichi chiaramente le differenze tra i piani. Ad esempio, è possibile immettere **Windows Server** con i piani *Con pagamento in base al consumo* , *BYOL* , *Avanzata* ed *Enterprise*.

Selezionare **Create** (Crea). Verrà visualizzata la pagina di **configurazione del piano** .

## <a name="plan-setup"></a>Configurazione del piano

Impostare la configurazione di livello elevato per il tipo di piano, specificare se riutilizza una configurazione tecnica di un altro piano e identificare le aree di Azure in cui rendere disponibile il piano. Le selezioni effettuate qui determinano i campi che verranno visualizzati in altri riquadri dello stesso piano.

### <a name="reuse-technical-configuration"></a>Riutilizza configurazione tecnica

Se si dispone di più di un piano dello stesso tipo con pacchetti identici, è possibile selezionare **This plan reuses technical configuration from another plan** (Questo piano riutilizza la configurazione tecnica di un altro piano). Questa opzione consente di selezionare uno degli altri piani dello stesso tipo per questa offerta e di riutilizzarne la configurazione tecnica.

> [!NOTE]
> Quando si riutilizza la configurazione tecnica di un altro piano, l'intera scheda **Configurazione tecnica** non sarà più visualizzata in questo piano. I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti apportati in futuro, verranno utilizzati anche per questo piano. Questa impostazione non può essere modificata dopo la pubblicazione del piano.

### <a name="azure-regions"></a>Aree di Azure

Il piano deve essere reso disponibile in almeno un'area di Azure.

Selezionare **Azure Global** per rendere il piano disponibile per i clienti in tutte le aree globali di Azure con integrazione del Marketplace commerciale. Per i dettagli, vedere [Disponibilità geografica e supporto delle valute](marketplace-geo-availability-currencies.md).

Selezionare **Azure per enti pubblici** per rendere disponibile il piano nell'area di [Azure per enti pubblici](../azure-government/documentation-government-welcome.md) . Questa area fornisce l'accesso controllato ai clienti di entità federali, statali o locali degli Stati Uniti, nonché ai partner idonei per la loro gestione. In qualità di editore, l'utente è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolate, situate solo negli Stati Uniti.

Prima di eseguire la pubblicazione in [Azure per enti pubblici](../azure-government/documentation-government-manage-marketplace-partners.md), testare e convalidare il piano nell'ambiente, perché alcuni endpoint potrebbero essere diversi. Per configurare e testare il piano, richiedere un account di prova nella pagina [Versione di valutazione di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo aver pubblicato e reso disponibile il piano in un'area di Azure specifica, non è possibile rimuovere tale area.

### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubblici

Questa opzione è visibile solo se **Azure per enti pubblici** è stato selezionato come area di Azure nella sezione precedente.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e regolamenti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per aumentare la consapevolezza sulle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti descrittivi. Questi possono essere collegamenti diretti alla presentazione del programma o collegamenti alle descrizioni della conformità di questi ultimi sui propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu piano di spostamento a sinistra, **elenco piano**.

## <a name="plan-listing"></a>Presentazione del piano

Configurare i dettagli dell'elenco del piano. In questo riquadro vengono visualizzate informazioni specifiche, che possono differire dagli altri piani nella stessa offerta.

### <a name="plan-name"></a>Nome piano

Questo campo viene compilato automaticamente con il nome assegnato al piano al momento della creazione. Il nome viene visualizzato in Azure Marketplace come titolo del piano. La lunghezza massima è di 100 caratteri.

### <a name="plan-summary"></a>Riepilogo piano

Specificare un breve riepilogo del piano, non dell'offerta. Questo riepilogo è limitato a 100 caratteri.

### <a name="plan-description"></a>Descrizione piano

Descrivere le caratteristiche uniche del piano software, nonché le differenze tra i piani all'interno dell'offerta. Descrivere solo il piano, non l'offerta. La descrizione del piano può contenere un massimo di 2.000 caratteri.

Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu piano di spostamento a sinistra, **prezzi e disponibilità**.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

In questo riquadro è possibile configurare:

- I mercati in cui è disponibile questo piano. Ogni piano deve essere disponibile in almeno un [mercato](marketplace-geo-availability-currencies.md).
- Il prezzo all'ora.
- Se rendere il piano visibile a tutti o solo a specifici clienti (gruppo di destinatari privato).

### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Per impostazione predefinita, la maggior parte dei mercati è selezionata. Per modificare l'elenco, selezionare **modifica mercati** e selezionare o deselezionare le caselle di controllo per ogni località di mercato in cui il piano deve essere disponibile per l'acquisto. Gli utenti dei mercati selezionati possono comunque distribuire l'offerta a tutte le aree di Azure selezionate nella sezione ["pianificazione dell'installazione"](#plan-setup) .

Selezionare **Seleziona solo le imposte Microsoft** inviate per selezionare solo i paesi/aree geografiche in cui Microsoft ha fatturato le vendite e usa le imposte per conto dell'utente. La pubblicazione in Cina è limitata ai piani *Gratuiti* o *Bring Your Own License* (BYOL).

Se i prezzi del proprio piano sono già stati definiti in dollari americani (USD) e si aggiunge un altro mercato, il prezzo per il nuovo mercato viene calcolato in base ai tassi di cambio correnti. Esaminare sempre il prezzo per ogni mercato prima di procedere con la pubblicazione. Per esaminare i prezzi, selezionare **Export prices (xlsx)** (Esporta prezzi (xlsx)) dopo aver salvato le modifiche.

Quando si rimuove un mercato, i clienti di quest'ultimo che usano distribuzioni attive non potranno creare nuove distribuzioni o aumentare le prestazioni delle distribuzioni esistenti. Le distribuzioni esistenti non sono interessate.

Selezionare **Save (Salva** ) per continuare.

### <a name="pricing"></a>Prezzi

Per il **modello di licenza** , selezionare **piano di fatturazione mensile basato sull'utilizzo** per configurare i prezzi per questo piano o **Bring your own License** per consentire ai clienti di utilizzare questo piano con la licenza esistente.

Per un piano di fatturazione mensile basato sull'utilizzo, utilizzare una delle tre opzioni di immissione dei prezzi seguenti:

- **Per core** : fornire i prezzi per core in USD. Microsoft calcola i prezzi per dimensioni core e li converte nelle valute locali basandosi sul tasso di cambio corrente.
- **Dimensioni per core** : offrono i prezzi per ogni dimensione di core in USD. Microsoft calcola i prezzi e li converte nelle valute locali basandosi sul tasso di cambio corrente.
- Per **mercato e dimensioni core** : offrono i prezzi per ogni dimensione di base per tutti i mercati. È possibile importare i prezzi da un foglio di calcolo.

Immettere un **Prezzo per core** , quindi selezionare **Price per core size** per visualizzare una tabella dei calcoli relativi a prezzi e ore.

> [!NOTE]
> Salvare le modifiche ai prezzi per abilitare l'esportazione dei dati sui prezzi. In seguito alla pubblicazione, non sarà più possibile modificare il prezzo per un mercato del piano. Per assicurarsi che i prezzi siano corretti prima di procedere con la pubblicazione, esportare il foglio di calcolo dei prezzi ed esaminare i prezzi per ogni mercato.

### <a name="free-trial"></a>Versione di valutazione gratuita

Puoi offrire una **versione di valutazione gratuita** di una, tre o sei mesi ai tuoi clienti.

### <a name="plan-visibility"></a>Plan Visibility (Visibilità del piano)

È possibile progettare ogni piano in modo che sia visibile a tutti o soltanto a un gruppo di destinatari preselezionato. Assegnare le appartenenze a questo gruppo di destinatari con limitazioni usando gli ID sottoscrizione di Azure.

**Pubblico** : il piano può essere visualizzato da tutti.

**Privato** : rendere il piano visibile solo a destinatari preselezionati. In seguito alla pubblicazione del piano come privato, è possibile aggiornare il gruppo di destinatari o impostarlo come pubblico. Dopo aver reso pubblico un piano, è necessario che rimanga tale. Non può essere impostato nuovamente come privato.

Assegnare i destinatari che avranno accesso a questo piano privato usando gli **ID sottoscrizione di Azure**. Facoltativamente, includere una **Descrizione** di ogni ID sottoscrizione di Azure assegnato. Aggiungere fino a 10 ID sottoscrizione manualmente o fino a 20.000 se si sta importando un foglio di calcolo CSV. Gli ID sottoscrizione di Azure sono rappresentati come GUID e tutte le lettere devono essere minuscole.

> [!NOTE]
> Il gruppo di destinatari privato o con limitazioni è diverso dal gruppo di destinatari dell'anteprima definito nel riquadro **Anteprima**. Il gruppo di destinatari dell'anteprima può accedere all'offerta *prima* della sua pubblicazione in Azure Marketplace. Sebbene la scelta del gruppo di destinatari privato si applichi solo a un piano specifico, il gruppo di destinatari dell'anteprima può visualizzare tutti i piani privati e pubblici per scopi di convalida.

Le offerte private non sono supportate con le sottoscrizioni di Azure stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

### <a name="hide-plan"></a>Nascondi piano

Se la macchina virtuale è destinata a essere usata solo indirettamente quando un altro modello di soluzione o un'altra applicazione gestita le fanno riferimento, selezionare questa casella di controllo per pubblicare la macchina virtuale, ma non renderla visibile ai clienti che potrebbero cercarla o accedervi direttamente.

I piani nascosti non supportano i collegamenti all'anteprima.

Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu piano di spostamento a sinistra, **configurazione tecnica**.

## <a name="technical-configuration"></a>Configurazione tecnica

Fornire le immagini e altre proprietà tecniche associate a questo piano.

> [!NOTE]
> Questa scheda non viene visualizzata se il piano è stato configurato per riutilizzare i pacchetti da un altro nella scheda **Imposta piano** .

### <a name="operating-system"></a>Sistema operativo

Selezionare la famiglia di sistemi operativi **Windows** o **Linux** .

Selezionare la **versione** di Windows o il **Fornitore** di Linux.

Immettere un **nome descrittivo** del sistema operativo per il sistema operativo. Questo nome è visibile ai clienti.

### <a name="recommended-vm-sizes"></a>Recommended VM Sizes (Dimensioni VM consigliate)

Selezionare il collegamento per scegliere fino a sei dimensioni consigliate per le macchine virtuali da visualizzare in Azure Marketplace.

### <a name="open-ports"></a>Aprire le porte

Aggiungere le porte aperte pubbliche o private in una macchina virtuale distribuita.

### <a name="properties"></a>Proprietà

Selezionare se la VM **supporta la rete accelerata**. Per informazioni dettagliate, vedere [rete accelerata](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Generazioni

La generazione di una macchina virtuale definisce l'hardware virtuale usato. In base alle esigenze del cliente, è possibile pubblicare una macchina virtuale di prima generazione, una VM di seconda generazione o entrambe.

1. Quando si crea una nuova offerta, selezionare un **tipo di generazione** e immettere i dettagli richiesti:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="Visualizzazione della casella di riepilogo a discesa generazione.":::

2. Per aggiungere un'altra generazione a un piano, selezionare **Aggiungi generazione**...

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Visualizzazione del collegamento ' Aggiungi generazione '.":::

    ... e immettere i dettagli richiesti:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="Visualizzazione della finestra Dettagli generazione.":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. Per aggiornare una macchina virtuale esistente con una generazione 1 già pubblicata, modificare i dettagli in questa pagina di **configurazione tecnica** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-updating.png" alt-text="Visualizzazione della pagina di configurazione tecnica del piano.":::

Per altre informazioni sulle differenze tra le funzionalità di generazione 1 e seconda generazione, vedere [supporto per le macchine virtuali di seconda generazione in Azure](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>Immagini di macchine virtuali

Specificare una versione del disco e l'URI di firma di accesso condiviso (SAS, Shared Access Signature) per le immagini delle macchine virtuali. Aggiungere fino a 16 dischi dati per ogni immagine di macchina virtuale. Specificare solo una nuova versione dell'immagine per piano in un determinato invio. Dopo essere stata pubblicata, l'immagine non potrà essere modificata, ma potrà essere eliminata. Se una versione viene eliminata, gli utenti nuovi ed esistenti non potranno distribuire una nuova istanza di tale versione eliminata.

Questi due campi obbligatori vengono mostrati nell'immagine precedente:

- **Versione disco** : versione dell'immagine che si sta fornendo.
- **Collegamento VHD del sistema operativo** : posizione nell'account di archiviazione di Azure per il VHD del sistema operativo. Per informazioni su come ottenere un URI SAS, vedere [ottenere l'URI della firma di accesso condiviso per l'immagine di macchina virtuale](azure-vm-get-sas-uri.md).

I dischi dati (selezionare **Aggiungi disco dati (massimo 16)** ) sono anche URI di firma di accesso condiviso VHD archiviati negli account di archiviazione di Azure. Aggiungere solo un'immagine per invio in un piano.

Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. In fase di distribuzione i clienti non possono rimuovere dischi che fanno parte di un'immagine, ma possono sempre aggiungerne altri durante o dopo la distribuzione.

Selezionare **Salva bozza** , quindi selezionare **← panoramica piano** in alto a sinistra per visualizzare il piano appena creato.

## <a name="next-steps"></a>Passaggi successivi

- [Rivendere tramite CSP](azure-vm-create-resell-csp.md)

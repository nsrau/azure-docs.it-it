---
title: Creazione di piani per un'offerta di macchina virtuale in Azure Marketplace
description: Informazioni su come creare piani per un'offerta di macchina virtuale in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 922f4a2785a0b7260bcd0aae19c0172a2fa7a782
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284335"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Come creare piani per un'offerta di macchina virtuale

Nel Centro per i partner è possibile specificare diverse opzioni per il piano all'interno della stessa offerta. Un'offerta richiede almeno un piano (in precedenza noto come SKU), che può variare in base ai destinatari della monetizzazione, all'area di Azure, alle funzionalità o alle immagini di macchina virtuale.

È possibile creare fino a 100 piani per ogni offerta: fino a 45 di questi possono essere privati. Scopri di più sui piani privati in [offerte private in Microsoft Commercial Marketplace](private-offers.md).

Dopo aver creato i piani, selezionare la scheda **Panoramica del piano** per visualizzare:

- Nomi dei piani
- Modelli di licenza
- Gruppo di destinatari (pubblico o privato)
- Stato attuale di pubblicazione
- Azioni disponibili

Le azioni disponibili nel riquadro **Panoramica del piano** variano a seconda dello stato attuale di quest'ultimo.

- Se il piano è in stato di bozza, selezionare **Elimina bozza**.
- Se il piano è pubblicato in tempo reale, selezionare **Stop sell plan** (Interrompi la vendita del piano) o **Sync private audience** (Sincronizza gruppo di destinatari privato).

## <a name="create-a-new-plan"></a>Creare un nuovo piano

Selezionare **Crea nuovo piano** nella parte superiore. Viene visualizzata la finestra di dialogo **Nuovo piano**.

Nella casella **ID piano** creare un ID univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti all'indirizzo Web del prodotto. Usare solo lettere minuscole, numeri, trattini o caratteri di sottolineatura e fino a un massimo di 50 caratteri.

> [!NOTE]
> Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

Nella casella **Nome piano** immettere un nome per il piano. I clienti visualizzano questo nome quando decidono quale piano scegliere nell'offerta. Creare un nome univoco che indichi chiaramente le differenze tra i piani. Ad esempio, è possibile immettere **Windows Server** con i piani *Con pagamento in base al consumo*, *BYOL*, *Avanzata* ed *Enterprise*.

Selezionare **Create** (Crea).

## <a name="plan-setup"></a>Configurazione del piano

Impostare la configurazione di livello elevato per il tipo di piano, specificare se riutilizza una configurazione tecnica di un altro piano e identificare le aree di Azure in cui rendere disponibile il piano. Le selezioni effettuate qui determinano i campi che verranno visualizzati in altri riquadri dello stesso piano.

### <a name="reuse-a-technical-configuration"></a>Riutilizzare una configurazione tecnica

Se si dispone di più di un piano dello stesso tipo con pacchetti identici, è possibile selezionare **This plan reuses technical configuration from another plan** (Questo piano riutilizza la configurazione tecnica di un altro piano). Questa opzione consente di selezionare uno degli altri piani dello stesso tipo per questa offerta e di riutilizzarne la configurazione tecnica.

> [!NOTE]
> Quando si riutilizza la configurazione tecnica di un altro piano, l'intera scheda **Configurazione tecnica** non sarà più visualizzata in questo piano. I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti apportati in futuro, verranno utilizzati anche per questo piano. Questa impostazione non può essere modificata dopo la pubblicazione del piano.

### <a name="azure-regions"></a>Aree di Azure

Il piano deve essere reso disponibile in almeno un'area di Azure.

Selezionare l'opzione **Azure globale** per rendere il piano disponibile per i clienti di tutte le aree di Azure globale con integrazione per il marketplace commerciale. Per i dettagli, vedere [Disponibilità geografica e supporto delle valute](marketplace-geo-availability-currencies.md).

Selezionare l'opzione **Azure per enti pubblici** per rendere il piano disponibile nell'area di [Azure per enti pubblici](../azure-government/documentation-government-welcome.md). Questa area fornisce l'accesso controllato ai clienti di entità federali, statali o locali degli Stati Uniti, nonché ai partner idonei per la loro gestione. In qualità di editore, l'utente è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolate, situate solo negli Stati Uniti.

Prima di eseguire la pubblicazione in [Azure per enti pubblici](../azure-government/documentation-government-manage-marketplace-partners.md), testare e convalidare il piano nell'ambiente, perché alcuni endpoint potrebbero essere diversi. Per configurare e testare il piano, richiedere un account di prova nella pagina [Versione di valutazione di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo aver pubblicato e reso disponibile il piano in un'area di Azure specifica, non è possibile rimuovere tale area.

### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubblici

Questa opzione è visibile solo se **Azure per enti pubblici** è stato selezionato come area di Azure nella sezione precedente.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e regolamenti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per aumentare la consapevolezza sulle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti descrittivi. Questi possono essere collegamenti diretti alla presentazione del programma o collegamenti alle descrizioni della conformità di questi ultimi sui propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

Prima di continuare, selezionare **Salva bozza**.

## <a name="plan-listing"></a>Presentazione del piano

In questa sezione vengono configurati i dettagli della presentazione del piano. In questo riquadro vengono visualizzate informazioni specifiche, che possono differire dagli altri piani nella stessa offerta.

### <a name="plan-name"></a>Nome piano

Questo campo viene compilato automaticamente con il nome assegnato al piano al momento della creazione. Il nome viene visualizzato in Azure Marketplace come titolo del piano. La lunghezza massima è di 100 caratteri.

### <a name="plan-summary"></a>Riepilogo piano

Specificare un breve riepilogo del piano, non dell'offerta. Questo riepilogo è limitato a 100 caratteri.

### <a name="plan-description"></a>Descrizione piano

Descrivere le caratteristiche uniche del piano software, nonché le differenze tra i piani all'interno dell'offerta. Descrivere solo il piano, non l'offerta. La descrizione del piano può contenere un massimo di 2.000 caratteri.

Prima di continuare, selezionare **Salva bozza**.

## <a name="pricing-and-availability"></a>Prezzi e disponibilità

In questo riquadro è possibile configurare:

- I mercati in cui è disponibile questo piano. Ogni piano deve essere disponibile in almeno un [mercato](marketplace-geo-availability-currencies.md).
- Il prezzo all'ora.
- Se rendere il piano visibile a tutti o solo a specifici clienti (gruppo di destinatari privato).

### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per ogni mercato in cui il piano deve essere disponibile per l'acquisto. (Gli utenti di questi mercati possono comunque distribuire l'offerta in tutte le aree di Azure selezionate nella sezione ["Configurazione del piano"](#plan-setup).) Il pulsante **Imposte versate** mostra i Paesi o le aree geografiche in cui Microsoft versa l'IVA e l'imposta sui beni durevoli per conto dei partner. La pubblicazione in Cina è limitata ai piani *Gratuiti* o *Bring Your Own License* (BYOL).

Se i prezzi del proprio piano sono già stati definiti in dollari americani (USD) e si aggiunge un altro mercato, il prezzo per il nuovo mercato viene calcolato in base ai tassi di cambio correnti. Esaminare sempre il prezzo per ogni mercato prima di procedere con la pubblicazione. Per esaminare i prezzi, selezionare **Export prices (xlsx)** (Esporta prezzi (xlsx)) dopo aver salvato le modifiche.

Quando si rimuove un mercato, i clienti di quest'ultimo che usano distribuzioni attive non potranno creare nuove distribuzioni o aumentare le prestazioni delle distribuzioni esistenti. Le distribuzioni esistenti non sono interessate.

### <a name="pricing"></a>Prezzi

Per **Modello di licenza** selezionare **Piano di fatturazione mensile basato sull'utilizzo** per configurare i prezzi per questo piano oppure selezionare **Bring Your Own License** per consentire ai clienti di utilizzare questo piano con la licenza esistente.

Per i piani di fatturazione mensile basati sull'utilizzo, usare una delle tre opzioni di immissione dei prezzi seguenti:

- **Per core**: consente di specificare i prezzi per core in USD. Microsoft calcola i prezzi per dimensioni core e li converte nelle valute locali basandosi sul tasso di cambio corrente.
- **Per dimensioni core**: consente di specificare i prezzi per dimensioni core in USD. Microsoft calcola i prezzi e li converte nelle valute locali basandosi sul tasso di cambio corrente.
- **Per mercato e dimensioni core**: consente di specificare i prezzi per ogni dimensione core per tutti i mercati. È possibile importare i prezzi da un foglio di calcolo.

> [!NOTE]
> Salvare le modifiche ai prezzi per abilitare l'esportazione dei dati sui prezzi. In seguito alla pubblicazione, non sarà più possibile modificare il prezzo per un mercato del piano. Per assicurarsi che i prezzi siano corretti prima di procedere con la pubblicazione, esportare il foglio di calcolo dei prezzi ed esaminare i prezzi per ogni mercato.

### <a name="free-trial"></a>Versione di valutazione gratuita

Puoi offrire una *versione di valutazione gratuita* di un mese o di tre mesi o di sei mesi ai tuoi clienti.

### <a name="visibility"></a>Visibilità

È possibile progettare ogni piano in modo che sia visibile a tutti o soltanto a un gruppo di destinatari preselezionato. Assegnare le appartenenze a questo gruppo di destinatari con limitazioni usando gli ID sottoscrizione di Azure.

**Pubblico**: il piano può essere visualizzato da tutti.

**Gruppo di destinatari privato**: consente di rendere il piano visibile solo a un gruppo di destinatari preselezionato. In seguito alla pubblicazione del piano come privato, è possibile aggiornare il gruppo di destinatari o impostarlo come pubblico. Dopo aver reso pubblico un piano, è necessario che rimanga tale. Non può essere impostato nuovamente come privato.

> [!NOTE]
> Il gruppo di destinatari privato o con limitazioni è diverso dal gruppo di destinatari dell'anteprima definito nel riquadro **Anteprima**. Il gruppo di destinatari dell'anteprima può accedere all'offerta _prima_ della sua pubblicazione in Azure Marketplace. Sebbene la scelta del gruppo di destinatari privato si applichi solo a un piano specifico, il gruppo di destinatari dell'anteprima può visualizzare tutti i piani privati e pubblici per scopi di convalida.

**Gruppo di destinatari con limitazioni (ID sottoscrizione di Azure)** : consente di assegnare il gruppo di destinatari che avrà accesso a questo piano privato tramite gli ID sottoscrizione di Azure. Facoltativamente, includere una descrizione di ogni ID sottoscrizione di Azure assegnato. È possibile aggiungere manualmente fino a 10 ID sottoscrizione o fino a 20.000 se si importa un foglio di calcolo CSV. Gli ID sottoscrizione di Azure sono rappresentati come GUID e tutte le lettere devono essere minuscole.

>[!Note]
>Le offerte private non sono supportate con le sottoscrizioni di Azure stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

### <a name="hide-a-plan"></a>Nascondere un piano

Se la macchina virtuale è destinata a essere usata solo indirettamente quando un altro modello di soluzione o un'altra applicazione gestita le fanno riferimento, selezionare questa casella di controllo per pubblicare la macchina virtuale, ma non renderla visibile ai clienti che potrebbero cercarla o accedervi direttamente.

> [!NOTE]
> I piani nascosti non supportano i collegamenti all'anteprima.

Prima di continuare, selezionare **Salva bozza**.

## <a name="technical-configuration"></a>Configurazione tecnica

Inserire le immagini e altre proprietà tecniche associate a questo piano. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione dell'offerta di VM dettagli](azure-vm-create-listing.md).

> [!NOTE]
> La scheda **Configurazione tecnica** non viene visualizzata se il piano è stato configurato sul riutilizzo dei pacchetti di un altro piano nella scheda **Configurazione del piano**.

### <a name="operating-system"></a>Sistema operativo

Nel riquadro **Sistema operativo** eseguire le operazioni seguenti:

- Per **Famiglia di sistemi operativi** selezionare il sistema operativo **Windows** o **Linux**.
- Per **Release** o **Fornitore** selezionare la versione di Windows o il fornitore di Linux.
- Per **Nome descrittivo del sistema operativo** immettere un nome descrittivo del sistema operativo. Questo nome è visibile ai clienti.

### <a name="recommended-vm-sizes"></a>Recommended VM Sizes (Dimensioni VM consigliate)

Selezionare fino a sei dimensioni consigliate per le macchine virtuali da visualizzare in Azure Marketplace.

### <a name="open-ports"></a>Aprire le porte

Aprire porte pubbliche o private in una macchina virtuale distribuita.

### <a name="storage-option-for-deployment"></a>Opzione di archiviazione per la distribuzione

Per l'**opzione di distribuzione del disco** selezionare il tipo di distribuzione del disco che i clienti possono usare per la macchina virtuale. Microsoft consiglia di limitare la distribuzione esclusivamente alla **distribuzione di dischi gestiti**.

### <a name="properties"></a>Proprietà

Per **Support Accelerated Networking** (Supporta rete accelerata) scegliere se la macchina virtuale deve supportare la [rete accelerata](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="vm-images"></a>Immagini di macchine virtuali

Specificare una versione del disco e l'URI di firma di accesso condiviso (SAS, Shared Access Signature) per le immagini delle macchine virtuali. Aggiungere fino a 16 dischi dati per ogni immagine di macchina virtuale. Specificare solo una nuova versione dell'immagine per piano in un determinato invio. Dopo essere stata pubblicata, l'immagine non potrà essere modificata, ma potrà essere eliminata. Se una versione viene eliminata, gli utenti nuovi ed esistenti non potranno distribuire una nuova istanza di tale versione eliminata.

- **Versione disco**: versione dell'immagine che si sta fornendo.
- **URI di firma di accesso condiviso**: percorso nell'account di archiviazione di Azure in cui è stato archiviato il disco rigido virtuale del sistema operativo. Per informazioni su come ottenere un URI SAS, vedere [ottenere l'URI della firma di accesso condiviso per l'immagine di macchina virtuale](azure-vm-get-sas-uri.md).
- Le immagini del disco dati sono anche gli URI di firma di accesso condiviso del disco rigido virtuale archiviati negli account di archiviazione di Azure.
- Aggiungere solo un'immagine per invio in un piano.

Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. In fase di distribuzione i clienti non possono rimuovere dischi che fanno parte di un'immagine, ma possono sempre aggiungerne altri durante o dopo la distribuzione.

Selezionare **Salva bozza** prima di continuare e tornare a **Panoramica del piano**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere un gruppo di destinatari dell'anteprima](azure-vm-create-preview.md)

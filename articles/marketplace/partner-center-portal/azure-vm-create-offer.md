---
title: Creare un'offerta di macchina virtuale di Azure in Azure Marketplace
description: Informazioni su come creare un'offerta di macchina virtuale in Azure Marketplace con lo SKU richiesto.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: a64908eb639a44c6bc9d742e84ebb4d01082d49d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847923"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Creare un'offerta di macchina virtuale di Azure in Azure Marketplace

Questo articolo descrive come creare e pubblicare un'offerta di macchina virtuale di Azure in [Azure Marketplace](https://azuremarketplace.microsoft.com/). Fa riferimento alle macchine virtuali basate su Windows e a quelle basate su Linux con un sistema operativo, un disco rigido virtuale e fino a 16 dischi dati. 

Prima di iniziare, [creare un account per il marketplace commerciale nel Centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account). Assicurarsi che l'account sia registrato al programma del marketplace commerciale.

## <a name="introduction"></a>Introduzione

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Vantaggi della pubblicazione in Azure Marketplace

La pubblicazione delle offerte in Azure Marketplace consente di:

- Promuovere la propria azienda usando il marchio Microsoft.
- Raggiungere più di 100 milioni di utenti di Office 365 e Dynamics 365 e più di 200.000 organizzazioni.
- Acquisire clienti potenziali di alta qualità da questi marketplace.
- Far promuovere i propri servizi dai team di vendita sul campo e di telemarketing di Microsoft.

### <a name="before-you-begin"></a>Prima di iniziare

Se non lo si è già fatto, consultare la [Guida alla pubblicazione di offerte di macchine virtuali](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) e il materiale di riferimento sulle macchine virtuali di Azure seguente:

- Guide di avvio rapido
  - [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)
  - [Modelli di avvio rapido di Azure in GitHub](https://github.com/azure/azure-quickstart-templates)
- Esercitazioni
  - [Macchine virtuali di Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Macchine virtuali di Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Esempi
  - [Esempi dell'interfaccia della riga di comando di Azure per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell per macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Esempi dell'interfaccia della riga di comando di Azure per macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell per macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Conoscenze tecniche fondamentali

Il processo di progettazione, creazione e test delle offerte richiede tempo e competenze relative alla piattaforma di Azure e alle tecnologie usate per la creazione dell'offerta.

Il team tecnico deve avere una conoscenza di base e pratica delle tecnologie Microsoft seguenti:

- [Servizi di Azure](https://azure.microsoft.com/services/)
- [Progettazione e architettura delle applicazioni di Azure](https://azure.microsoft.com/solutions/architecture/)
- [Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), [Archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage#storage) e [Rete di Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel riquadro a sinistra selezionare **Marketplace commerciale** > **Panoramica**.
3. Nella pagina **Panoramica** selezionare **Nuova offerta** > **Macchina virtuale di Azure**.

    ![Screenshot delle opzioni di menu del riquadro sinistro e del pulsante "Nuova offerta".](./media/new-offer-azure-vm.png)

> [!NOTE]
> Le eventuali modifiche apportate dal Centro per i partner all'offerta in seguito alla sua pubblicazione vengono visualizzate in Azure Marketplace solo dopo che l'offerta viene ripubblicata. Assicurarsi di ripubblicare sempre un'offerta dopo averla modificata.

## <a name="new-offer"></a>Nuova offerta

Immettere un ID in **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web dell'offerta di Azure Marketplace, in Azure PowerShell e nell'interfaccia della riga di comando di Azure, se applicabile.
- Usare solo lettere minuscole e numeri. L'ID può includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1**, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

Immettere un alias in **Alias offerta**. L'alias dell'offerta corrisponde al nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato in Azure Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati dai clienti.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="offer-setup"></a>Configurazione dell'offerta

### <a name="test-drive"></a>Test drive

Un *test drive* è un ottimo modo per presentare l'offerta ai clienti potenziali, poiché offre la possibilità di "provare prima di acquistare", contribuendo ad aumentare le conversioni e a generare clienti potenziali altamente qualificati. Per altre informazioni, vedere [Informazioni sul test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Per abilitare un test drive per un periodo di tempo fisso, selezionare la casella di controllo **Abilita un test drive**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Risorse aggiuntive sui test drive:

- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Procedure consigliate di marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Scaricare il file PDF della panoramica dei test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (assicurarsi che il blocco popup sia disattivato).

### <a name="customer-leads"></a>Clienti potenziali

Quando si pubblica l'offerta nel marketplace commerciale tramite il Centro per i partner, connetterla al sistema Customer Relationship Management (CRM) per ricevere le informazioni di contatto del cliente non appena questo esprime interesse o usa il prodotto. La connessione al sistema CRM è obbligatoria se si vuole abilitare un test drive (vedere la sezione precedente). In caso contrario, la connessione a tale sistema è facoltativa.

1. Selezionare la destinazione a cui si desidera che vengano inviati i lead. Il Centro per i partner supporta i sistemi CRM seguenti:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) per l'engagement degli utenti
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se il sistema CRM non è elencato qui, usare [Archivio tabelle di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) o un [endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) per archiviare i dati dei clienti potenziali. Esportare quindi i dati nel sistema CRM.

1. Quando si pubblica l'offerta nel Centro per i partner, connetterla alla destinazione del cliente potenziale.
1. Verificare che la connessione alla destinazione del cliente potenziale sia configurata correttamente. Dopo la pubblicazione nel Centro per i partner, Microsoft convaliderà la connessione e invierà un cliente potenziale di test. Quando si visualizza l'offerta in anteprima prima della pubblicazione, è anche possibile testare la connessione al cliente potenziale tramite una distribuzione di prova dell'offerta nell'ambiente di anteprima.
1. Assicurarsi che la connessione alla destinazione dei clienti potenziali rimanga aggiornata in modo da mantenerli tutti.

1. Prima di continuare, selezionare **Salva bozza**.

## <a name="properties"></a>Proprietà

Nella pagina **Proprietà** è possibile definire le categorie e i settori usati per raggruppare l'offerta in Azure Marketplace, la versione dell'applicazione e i contratti legali a supporto dell'offerta.

### <a name="categories"></a>Categorie

Selezionare un minimo di una e un massimo di cinque categorie. Usare queste categorie per inserire l'offerta nelle aree di ricerca appropriate di Azure Marketplace. Nella descrizione dell'offerta spiegare in che modo questa è pertinente alle categorie selezionate. Le offerte di macchine virtuali vengono visualizzate nella categoria **Calcolo** di Azure Marketplace.

### <a name="legal"></a>Note legali

È necessario fornire i termini e le condizioni dell'offerta ai clienti. Sono disponibili due opzioni:

- **Usare termini e condizioni personalizzati**

   Per usare termini e condizioni personalizzati, immettere fino a 10.000 caratteri nella casella **Termini e condizioni**. Se occorre una descrizione più lunga, immettere un unico indirizzo Web per i termini e condizioni che verrà visualizzato dai clienti come collegamento attivo.

   I clienti devono accettare questi termini prima di poter provare l'offerta.

- **Usare il contratto standard per il marketplace commerciale di Microsoft**

   Per semplificare il processo di approvvigionamento ai clienti e ridurre la complessità a livello giuridico per i fornitori del software, Microsoft mette a disposizione un contratto standard per il marketplace commerciale. Se si decide di distribuire il software tramite il contratto standard, i clienti devono leggerlo e accettarlo una sola volta e non dovranno creare termini e condizioni personalizzati.

   Usare il contratto standard selezionando la casella di controllo **Use the Standard Contract for Microsoft's commercial marketplace** (Usa il contratto standard per il marketplace commerciale di Microsoft) e quindi, nella finestra popup selezionare **Accetta** (potrebbe essere necessario scorrere verso l'alto per visualizzare il pulsante).

   ![Screenshot del riquadro Note legali del Centro per i partner con la casella di controllo "Use the Standard Contract for Microsoft's commercial marketplace" (Usa il contratto standard per il marketplace commerciale di Microsoft).](media/use-standard-contract.png)

  > [!NOTE]
  > Quando un'offerta viene pubblicata con il contratto standard per il marketplace commerciale, non è possibile usare i termini e condizioni personalizzati. È possibile offrire la propria soluzione con il contratto standard o con i propri termini e condizioni.

  Per altre informazioni, vedere [Contratto standard per il marketplace commerciale Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Scaricare il file PDF del [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assicurarsi che il blocco popup sia disattivato).

  **Modifiche del contratto standard**

  Le modifiche del contratto standard consentono di selezionare le condizioni del contratto standard per semplicità e creare quelle relative al proprio prodotto o alla propria azienda. I clienti devono leggere le modifiche apportate al contratto solo se hanno già letto e accettato il contratto standard Microsoft. Esistono due tipi di modifiche:

  * **Modifiche universali**: queste modifiche vengono applicate in modo universale al contratto standard per tutti i clienti. Vengono mostrate a ogni cliente dell'offerta nel flusso di acquisto. I clienti devono accettare le condizioni del contratto standard e le relative modifiche prima di poter usare l'offerta. È possibile apportare una singola modifica universale per offerta. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante l'individuazione e il flusso di acquisto.

  * **Modifiche personalizzate**: si tratta di modifiche speciali apportate al contratto standard destinate a specifici clienti tramite gli ID tenant di Azure. È possibile scegliere il tenant di destinazione. Solo ai clienti del tenant verranno presentate le condizioni di modifica personalizzate nel flusso di acquisto dell'offerta. I clienti devono accettare le condizioni del contratto standard e le relative modifiche prima di poter usare l'offerta.

    1. Per iniziare, selezionare **Add custom amendment terms (Max 10)** (Aggiungi condizioni di modifica personalizzate (max 10)). È possibile specificare fino a dieci condizioni di modifica personalizzate per offerta. Eseguire le operazioni seguenti:

       a. Immettere le condizioni di modifica nella casella **Condizioni di modifica personalizzate**. È possibile immettere un numero illimitato di caratteri. Solo i clienti degli ID tenant selezionati visualizzeranno queste condizioni personalizzate nel flusso di acquisto dell'offerta nel portale di Azure.

       b. (Obbligatorio) Specificare gli **ID tenant**. Ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una modifica personalizzata, è necessario specificare almeno un ID tenant che identifichi il cliente su Azure. Il cliente può trovarlo in autonomia su Azure selezionando **Azure Active Directory** > **Proprietà**. Il valore dell'ID directory corrisponde all'ID tenant (ad esempio 50c464d3-4930-494c-963c-1e951d15360e). È possibile trovare l'ID tenant dell'organizzazione del cliente anche immettendo il relativo indirizzo Web del nome di dominio in [Qual è l'ID tenant di Microsoft Azure e Office 365?](https://www.whatismytenantid.com/).

       c. (Facoltativo) Immettere una **descrizione** dell'ID tenant che agevoli l'identificazione del cliente a cui è rivolta la modifica.

        > [!NOTE]
        > Questi due tipi di modifiche sono abbinate tra loro. Durante l'acquisto, i clienti destinatari delle modifiche personalizzate visualizzeranno anche le modifiche universali apportate al contratto standard.

    1. Prima di continuare, selezionare **Salva bozza**.

## <a name="offer-listing"></a>Presentazione dell'offerta

Nella pagina **Presentazione dell'offerta** è possibile definire i dettagli dell'offerta, come il nome, la descrizione, i collegamenti e i contatti.

> [!NOTE]
> Il contenuto della presentazione dell'offerta (ad esempio descrizione, documenti, screenshot e condizioni per l'utilizzo) non deve essere in lingua inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in \<lingua non inglese>". È anche possibile specificare un URL per il collegamento a un sito in cui i contenuti dell'offerta sono disponibili in una lingua diversa da quella usata nella presentazione.

### <a name="marketplace-details"></a>Dettagli del marketplace

#### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato dai clienti come titolo della presentazione dell'offerta. Questo campo viene compilato automaticamente con il nome immesso nella casella **Alias offerta** al momento della creazione dell'offerta e può essere modificato in un secondo momento. Il nome:

- Può essere un marchio registrato. Può includere marchi e simboli di copyright.
- Non può contenere più di 50 caratteri.
- Non può includere emoji.

#### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Immettere una breve descrizione dell'offerta da visualizzare nei risultati della ricerca di Azure Marketplace. Può contenere fino a 100 caratteri.

#### <a name="long-summary"></a>Riepilogo lungo

Immettere una descrizione più lunga dell'offerta da visualizzare nei risultati della ricerca di Azure Marketplace. Può contenere fino a 256 caratteri.

#### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Collegamento all'Informativa sulla privacy

Immettere l'indirizzo Web (URL) dell'informativa sulla privacy dell'organizzazione. Assicurarsi che l'offerta sia conforme alle leggi e alle normative sulla privacy. È inoltre necessario pubblicare un'informativa sulla privacy valida sul proprio sito Web.

### <a name="useful-links"></a>Collegamenti utili

Specificare altri documenti online sull'offerta. Per aggiungere un collegamento, selezionare **Aggiungi un collegamento** e completare i campi seguenti:

- **Name**: i clienti visualizzeranno il nome nella pagina dei dettagli.
- **Collegamento (URL)** : immettere un collegamento che consenta ai clienti di visualizzare il documento online.

### <a name="customer-support-links"></a>Collegamenti al supporto clienti

Specificare il sito Web di supporto che i clienti possono visitare per contattare il team di supporto.

- Sito Web del supporto Azure globale
- Sito Web del supporto di Azure per enti pubblici

### <a name="partner-support-contact"></a>Contatto per il supporto per i partner

Immettere le informazioni di contatto che i partner Microsoft devono usare quando i clienti aprono un ticket di supporto. Queste informazioni non sono indicate in Azure Marketplace.

- Nome
- Email
- Telefono

### <a name="engineering-contact"></a>Contatto tecnico

Immettere le informazioni di contatto che Microsoft dovrà usare in caso di problemi con l'offerta, inclusi i problemi di certificazione. Queste informazioni non sono indicate in Azure Marketplace.

- Nome
- Email
- Telefono

### <a name="azure-marketplace-media"></a>File multimediali di Azure Marketplace

Inserire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate saranno rifiutate.

>[!Note]
>In caso di problemi durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio *https://upload.xboxlive.com* usato dal Centro per i partner.

#### <a name="azure-marketplace-logos"></a>Logo di Azure Marketplace

Inserire i file PNG del logo dell'offerta con le quattro dimensioni di immagine seguenti:

- **Piccola** (48 &times; 48 pixel)
- **Media** (90 &times; 90 pixel)
- **Grande** (216 &times; 216 pixel)
- **Molto grande** (255 &times; 115 pixel)

Sono necessari tutti e quattro i logo, che verranno visualizzati in diverse presentazioni in Azure Marketplace.

#### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere fino a cinque screenshot che illustrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 &times; 720 pixel ed essere in formato PNG. Ogni screenshot deve includere una didascalia.

#### <a name="videos"></a>Video

Aggiungere fino a cinque video di descrizione dell'offerta. I video devono essere ospitati su un servizio video esterno. Immettere il nome di ogni video, l'indirizzo Web e un'immagine PNG di anteprima di dimensioni pari a 1280 &times; 720 pixel.

Per risorse aggiuntive relative alle presentazioni nel marketplace, vedere [Procedure consigliate per la presentazione di offerte nel marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Prima di continuare, selezionare **Salva bozza**.

## <a name="preview"></a>Anteprima

Selezionare la scheda **Anteprima**, quindi selezionare un **gruppo di destinatari dell'anteprima** limitato per la convalida dell'offerta prima della sua pubblicazione per il più ampio pubblico del marketplace commerciale.

> [!IMPORTANT]
> Dopo aver controllato l'offerta nel riquadro **Anteprima**, selezionare **Go Live** per pubblicare l'offerta per il pubblico del marketplace commerciale.

Il gruppo di destinatari dell'anteprima viene identificato tramite i GUID dell'ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi viene visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **Sottoscrizioni** del portale di Azure.

Aggiungere almeno un ID sottoscrizione di Azure, singolarmente (fino a 10 ID) o caricando un file CSV (fino a 100 ID). Aggiungendo questi ID sottoscrizione, è possibile definire chi può visualizzare l'anteprima dell'offerta prima che venga pubblicata. Se l'offerta è già stata pubblicata, è possibile comunque definire un gruppo di destinatari dell'anteprima per testare le modifiche o gli aggiornamenti dell'offerta.

> [!NOTE]
> Il gruppo di destinatari dell'anteprima è diverso dal gruppo di destinatari privato. Il gruppo di destinatari dell'anteprima può accedere all'offerta _prima_ che venga pubblicata in Azure Marketplace. Il gruppo di destinatari dell'anteprima può visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo a un gruppo di destinatari privato dopo la pubblicazione dell'offerta in Azure Marketplace. Il gruppo di destinatari privato (definito nel riquadro **Prezzi e disponibilità** del piano) dispone di accesso esclusivo a un piano specifico.

Prima di passare alla sezione successiva, selezionare **Salva bozza**.

## <a name="plan-overview"></a>Panoramica del piano

Nel Centro per i partner è possibile specificare diverse opzioni per il piano all'interno della stessa offerta. Questi piani erano noti in precedenza come SKU. Per un'offerta occorre almeno un piano, che può variare in base ai destinatari della monetizzazione, all'area di Azure, alle funzionalità o alle immagini di macchine virtuali.

Dopo aver creato i piani, selezionare la scheda **Panoramica del piano** per visualizzare:

- Nomi dei piani
- Modelli di licenza
- Gruppo di destinatari (pubblico o privato)
- Stato attuale di pubblicazione
- Azioni disponibili

Le azioni disponibili nel riquadro **Panoramica del piano** variano a seconda dello stato attuale di quest'ultimo.

- Se il piano è in stato di bozza, selezionare **Elimina bozza**.
- Se il piano è pubblicato in tempo reale, selezionare **Stop sell plan** (Interrompi la vendita del piano) o **Sync private audience** (Sincronizza gruppo di destinatari privato).

### <a name="create-a-new-plan"></a>Creare un nuovo piano

Selezionare **Crea nuovo piano** nella parte superiore. Viene visualizzata la finestra di dialogo **Nuovo piano**.

Nella casella **ID piano** creare un ID univoco per ogni piano dell'offerta. Questo ID sarà visibile ai clienti all'indirizzo Web del prodotto. Usare solo lettere minuscole, numeri, trattini o caratteri di sottolineatura e fino a un massimo di 50 caratteri.

> [!NOTE]
> Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.

Nella casella **Nome piano** immettere un nome per il piano. I clienti visualizzano questo nome quando decidono quale piano scegliere nell'offerta. Creare un nome univoco che indichi chiaramente le differenze tra i piani. Ad esempio, è possibile immettere **Windows Server** con i piani *Con pagamento in base al consumo*, *BYOL*, *Avanzata* ed *Enterprise*.

Selezionare **Create** (Crea).

### <a name="plan-setup"></a>Configurazione del piano

Impostare la configurazione di livello elevato per il tipo di piano, specificare se riutilizza una configurazione tecnica di un altro piano e identificare le aree di Azure in cui rendere disponibile il piano. Le selezioni effettuate qui determinano i campi che verranno visualizzati in altri riquadri dello stesso piano.

#### <a name="reuse-a-technical-configuration"></a>Riutilizzare una configurazione tecnica

Se si dispone di più di un piano dello stesso tipo con pacchetti identici, è possibile selezionare **This plan reuses technical configuration from another plan** (Questo piano riutilizza la configurazione tecnica di un altro piano). Questa opzione consente di selezionare uno degli altri piani dello stesso tipo per questa offerta e di riutilizzarne la configurazione tecnica.

> [!NOTE]
> Quando si riutilizza la configurazione tecnica di un altro piano, l'intera scheda **Configurazione tecnica** non sarà più visualizzata in questo piano. I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti apportati in futuro, verranno utilizzati anche per questo piano. Questa impostazione non può essere modificata dopo la pubblicazione del piano.

#### <a name="azure-regions"></a>Aree di Azure

Il piano deve essere reso disponibile in almeno un'area di Azure.

Selezionare l'opzione **Azure globale** per rendere il piano disponibile per i clienti di tutte le aree di Azure globale con integrazione per il marketplace commerciale. Per i dettagli, vedere [Disponibilità geografica e supporto delle valute](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Selezionare l'opzione **Azure per enti pubblici** per rendere il piano disponibile nell'area di [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). Questa area fornisce l'accesso controllato ai clienti di entità federali, statali o locali degli Stati Uniti, nonché ai partner idonei per la loro gestione. In qualità di editore, l'utente è responsabile di eventuali controlli di conformità, misure di sicurezza e procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolate, situate solo negli Stati Uniti.

Prima di eseguire la pubblicazione in [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), testare e convalidare il piano nell'ambiente, perché alcuni endpoint potrebbero essere diversi. Per configurare e testare il piano, richiedere un account di prova nella pagina [Versione di valutazione di Microsoft Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Dopo aver pubblicato e reso disponibile il piano in un'area di Azure specifica, non è possibile rimuovere tale area.

#### <a name="azure-government-certifications"></a>Certificazioni di Azure per enti pubblici

Questa opzione è visibile solo se **Azure per enti pubblici** è stato selezionato come area di Azure nella sezione precedente.

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e regolamenti governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per aumentare la consapevolezza sulle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti descrittivi. Questi possono essere collegamenti diretti alla presentazione del programma o collegamenti alle descrizioni della conformità di questi ultimi sui propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

Prima di continuare, selezionare **Salva bozza**.

### <a name="plan-listing"></a>Presentazione del piano

In questa sezione vengono configurati i dettagli della presentazione del piano. In questo riquadro vengono visualizzate informazioni specifiche, che possono differire dagli altri piani nella stessa offerta.

#### <a name="plan-name"></a>Nome piano

Questo campo è compilato automaticamente con il nome assegnato al piano al momento della creazione. Il nome viene visualizzato in Azure Marketplace come titolo del piano. La lunghezza massima è di 100 caratteri.

#### <a name="plan-summary"></a>Riepilogo piano

Specificare un breve riepilogo del piano, non dell'offerta. Questo riepilogo è limitato a 100 caratteri.

#### <a name="plan-description"></a>Descrizione piano

Descrivere le caratteristiche uniche del piano software, nonché le differenze tra i piani all'interno dell'offerta. Descrivere solo il piano, non l'offerta. La descrizione del piano può contenere un massimo di 2.000 caratteri.

Prima di continuare, selezionare **Salva bozza**.

### <a name="pricing-and-availability"></a>Prezzi e disponibilità

In questo riquadro è possibile configurare:

- I mercati in cui è disponibile questo piano.
- Il prezzo all'ora.
- Se rendere il piano visibile a tutti o solo a specifici clienti (gruppo di destinatari privato).

#### <a name="markets"></a>Mercati

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per ogni mercato in cui il piano deve essere disponibile per l'acquisto. (Gli utenti di questi mercati possono comunque distribuire l'offerta in tutte le aree di Azure selezionate nella sezione ["Configurazione del piano"](#plan-setup).) Il pulsante **Imposte versate** mostra i Paesi o le aree geografiche in cui Microsoft versa l'IVA e l'imposta sui beni durevoli per conto dei partner. La pubblicazione in Cina è limitata ai piani *Gratuiti* o *Bring Your Own License* (BYOL).

Se i prezzi del proprio piano sono già stati definiti in dollari americani (USD) e si aggiunge un altro mercato, il prezzo per il nuovo mercato viene calcolato in base ai tassi di cambio correnti. Esaminare sempre il prezzo per ogni mercato prima di procedere con la pubblicazione. Per esaminare i prezzi, selezionare **Export prices (xlsx)** (Esporta prezzi (xlsx)) dopo aver salvato le modifiche.

Quando si rimuove un mercato, i clienti di quest'ultimo che usano distribuzioni attive non potranno creare nuove distribuzioni o aumentare le prestazioni delle distribuzioni esistenti. Le distribuzioni esistenti non sono interessate.

#### <a name="pricing"></a>Prezzi

Per **Modello di licenza** selezionare **Piano di fatturazione mensile basato sull'utilizzo** per configurare i prezzi per questo piano oppure selezionare **Bring Your Own License** per consentire ai clienti di utilizzare questo piano con la licenza esistente.

Per i piani di fatturazione mensile basati sull'utilizzo, usare una delle tre opzioni di immissione dei prezzi seguenti:

- **Per core**: consente di specificare i prezzi per core in USD. Microsoft calcola i prezzi per dimensioni core e li converte nelle valute locali basandosi sul tasso di cambio corrente.
- **Per dimensioni core**: consente di specificare i prezzi per dimensioni core in USD. Microsoft calcola i prezzi e li converte nelle valute locali basandosi sul tasso di cambio corrente.
- **Per mercato e dimensioni core**: consente di specificare i prezzi per ogni dimensione core per tutti i mercati. È possibile importare i prezzi da un foglio di calcolo.

> [!NOTE]
> Salvare le modifiche ai prezzi per abilitare l'esportazione dei dati sui prezzi. In seguito alla pubblicazione, non sarà più possibile modificare il prezzo per un mercato del piano. Per assicurarsi che i prezzi siano corretti prima di procedere con la pubblicazione, esportare il foglio di calcolo dei prezzi ed esaminare i prezzi per ogni mercato.

#### <a name="free-trial"></a>Versione di valutazione gratuita

È possibile offrire una *versione di valutazione gratuita* di un mese o di tre mesi ai clienti.

#### <a name="visibility"></a>Visibilità

È possibile progettare ogni piano in modo che sia visibile a tutti o soltanto a un gruppo di destinatari preselezionato. Assegnare le appartenenze a questo gruppo di destinatari con limitazioni usando gli ID sottoscrizione di Azure.

**Pubblico**: il piano può essere visualizzato da tutti.

**Gruppo di destinatari privato**: consente di rendere il piano visibile solo a un gruppo di destinatari preselezionato. In seguito alla pubblicazione del piano come privato, è possibile aggiornare il gruppo di destinatari o impostarlo come pubblico. Dopo aver reso pubblico un piano, è necessario che rimanga tale. Non può essere impostato nuovamente come privato.

**Gruppo di destinatari con limitazioni (ID sottoscrizione di Azure)** : consente di assegnare il gruppo di destinatari che avrà accesso a questo piano privato tramite gli ID sottoscrizione di Azure. Facoltativamente, includere una descrizione di ogni ID sottoscrizione di Azure assegnato. È possibile aggiungere manualmente fino a 10 ID sottoscrizione o fino a 20.000 se si importa un foglio di calcolo CSV. Gli ID sottoscrizione di Azure sono rappresentati come GUID e tutte le lettere devono essere minuscole.

> [!NOTE]
> Il gruppo di destinatari privato o con limitazioni è diverso dal gruppo di destinatari dell'anteprima definito nel riquadro **Anteprima**. Il gruppo di destinatari dell'anteprima può accedere all'offerta _prima_ della sua pubblicazione in Azure Marketplace. Sebbene la scelta del gruppo di destinatari privato si applichi solo a un piano specifico, il gruppo di destinatari dell'anteprima può visualizzare tutti i piani privati e pubblici per scopi di convalida.

#### <a name="hide-a-plan"></a>Nascondere un piano

Se la macchina virtuale è destinata a essere usata solo indirettamente quando un altro modello di soluzione o un'altra applicazione gestita le fanno riferimento, selezionare questa casella di controllo per pubblicare la macchina virtuale, ma non renderla visibile ai clienti che potrebbero cercarla o accedervi direttamente.

> [!NOTE]
> I piani nascosti non supportano i collegamenti all'anteprima.

Prima di continuare, selezionare **Salva bozza**.

### <a name="technical-configuration"></a>Configurazione tecnica

Inserire le immagini e altre proprietà tecniche associate a questo piano. Per altre informazioni, vedere [Creare un asset tecnico per macchine virtuali di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> La scheda **Configurazione tecnica** non viene visualizzata se il piano è stato configurato sul riutilizzo dei pacchetti di un altro piano nella scheda **Configurazione del piano**.

#### <a name="operating-system"></a>Sistema operativo

Nel riquadro **Sistema operativo** eseguire le operazioni seguenti:

- Per **Famiglia di sistemi operativi** selezionare il sistema operativo **Windows** o **Linux**.
- Per **Release** o **Fornitore** selezionare la versione di Windows o il fornitore di Linux.
- Per **Nome descrittivo del sistema operativo** immettere un nome descrittivo del sistema operativo. Questo nome è visibile ai clienti.

#### <a name="recommended-vm-sizes"></a>Recommended VM Sizes (Dimensioni VM consigliate)

Selezionare fino a sei dimensioni consigliate per le macchine virtuali da visualizzare in Azure Marketplace.

#### <a name="open-ports"></a>Aprire le porte

Aprire porte pubbliche o private in una macchina virtuale distribuita.

#### <a name="storage-option-for-deployment"></a>Opzione di archiviazione per la distribuzione

Per l'**opzione di distribuzione del disco** selezionare il tipo di distribuzione del disco che i clienti possono usare per la macchina virtuale. Microsoft consiglia di limitare la distribuzione esclusivamente alla **distribuzione di dischi gestiti**.

#### <a name="properties"></a>Proprietà

Per **Support Accelerated Networking** (Supporta rete accelerata) scegliere se la macchina virtuale deve supportare la [rete accelerata](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Immagini di macchine virtuali

Specificare una versione del disco e l'URI di firma di accesso condiviso (SAS, Shared Access Signature) per le immagini delle macchine virtuali. Aggiungere fino a 16 dischi dati per ogni immagine di macchina virtuale. Specificare solo una nuova versione dell'immagine per piano in un determinato invio. Dopo essere stata pubblicata, l'immagine non potrà essere modificata, ma potrà essere eliminata. Se una versione viene eliminata, gli utenti nuovi ed esistenti non potranno distribuire una nuova istanza di tale versione eliminata.

- **Versione disco**: versione dell'immagine che si sta fornendo.
- **URI di firma di accesso condiviso**: percorso nell'account di archiviazione di Azure in cui è stato archiviato il disco rigido virtuale del sistema operativo.
- Le immagini del disco dati sono anche gli URI di firma di accesso condiviso del disco rigido virtuale archiviati negli account di archiviazione di Azure.
- Aggiungere solo un'immagine per invio in un piano.

Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. In fase di distribuzione i clienti non possono rimuovere dischi che fanno parte di un'immagine, ma possono sempre aggiungerne altri durante o dopo la distribuzione.

Selezionare **Salva bozza** prima di continuare e tornare a **Panoramica del piano**.

## <a name="resell-through-csps"></a>Rivendere tramite CSP

Per espandere la copertura dell'offerta, è possibile renderla disponibile ai partner del programma [Cloud Solution Provider](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Tutti i piani Bring Your Own License (BYOL) fanno automaticamente parte del programma. È anche possibile scegliere di includere i piani non BYOL.

Prima di continuare, selezionare **Salva bozza**.

## <a name="test-drive"></a>Test drive

Configurare una dimostrazione, o *test drive*, per consentire ai clienti di provare l'offerta per un periodo di tempo fisso prima di acquistarla. Per creare un ambiente di dimostrazione per i clienti, vedere [Eseguire il test drive delle offerte nel marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Per abilitare un test drive, selezionare la casella di controllo **Abilita un test drive** nel riquadro **Configurazione dell'offerta**. Per rimuovere il test drive dall'offerta, deselezionare questa casella di controllo.

Risorse aggiuntive sui test drive:

- [Procedure consigliate di marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Procedure consigliate tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- File PDF della [panoramica dei test drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (assicurarsi che il blocco popup sia disattivato)

Prima di continuare, selezionare **Salva bozza**.

## <a name="review-and-publish"></a>Rivedere e pubblicare

Dopo aver completato tutte le sezioni obbligatorie dell'offerta, è possibile inviarla per la revisione e la pubblicazione.

In alto a destra selezionare **Rivedi e pubblica**.

In questo riquadro è possibile:

- Visualizzare lo stato di completamento di ogni sezione dell'offerta:

  - **Non avviato**: la sezione non è stata avviata e deve essere completata.
  - **Dati incompleti**: la sezione contiene errori che devono essere corretti o è necessario inserire altre informazioni. Per materiale sussidiario sull'aggiornamento delle informazioni incomplete, vedere le sezioni precedenti di questo articolo.
  - **Completezza**: la sezione è completa e non sono presenti errori. Prima di poter inviare l'offerta, è necessario completarne tutte le sezioni.
- Fornire al team di certificazione le **Note per la certificazione** per assicurarsi che l'applicazione venga testata correttamente. Includere istruzioni di test ed eventuali note supplementari che possano aiutare il team a comprendere il funzionamento dell'applicazione.

Per inviare l'offerta per la pubblicazione, selezionare **Rivedi e pubblica**.

Microsoft invierà un messaggio di posta elettronica per comunicare all'utente che una versione di anteprima dell'offerta è disponibile per la revisione e l'approvazione. Per rendere l'offerta pubblica (o, se si tratta di un'offerta privata, per pubblicarla per un gruppo di destinatari privato), passare al Centro per i partner, andare alla pagina **Panoramica** dell'offerta e selezionare **Go Live**. Mentre la pubblicazione è in corso, lo stato dell'offerta viene visualizzato nella parte superiore della pagina.

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Il passaggio **Convalida manuale** del processo di pubblicazione consente di rivedere in modo dettagliato l'offerta e le risorse tecniche associate. Se questo processo rileva errori correlati all'offerta, si riceverà un report di certificazione in cui sono descritti nel dettaglio i problemi riscontrati. È sufficiente apportare le correzioni necessarie e ripubblicare l'offerta.

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica offerta** mostra una rappresentazione visiva dei passaggi completati e di quelli in corso, necessari per pubblicare l'offerta e il tempo necessario per completare ognuno di essi.

Questa pagina include anche collegamenti che consentono di lavorare con l'offerta, a seconda del suo stato:

- Se l'offerta è in stato di bozza: [Eliminare la bozza dell'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Se l'offerta è live: [Interrompere la vendita dell'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se l'offerta è in anteprima: [Go Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se non è stata completata la disconnessione dell'editore: [Annullare la pubblicazione](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Esempi di marketplace

Gli esempi seguenti illustrano come viene visualizzata l'offerta in Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Dettagli sull'offerta in Azure Marketplace

![Esempio della pagina dei dettagli dell'offerta in Azure Marketplace](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Risultati della ricerca in Azure Marketplace

![Esempio della pagina dei dettagli della ricerca in Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Dettagli del piano in Azure Marketplace

![Esempio della pagina dei dettagli del piano in Azure Marketplace](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Dettagli dell'offerta sul portale di Azure

![Esempio della pagina dei dettagli dell'offerta sul portale di Azure](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Risultati della ricerca sul portale di Azure

![Esempio di pagina dei risultati della ricerca sul portale di Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Dettagli del piano sul portale di Azure

![Esempio della pagina dei dettagli del piano sul portale di Azure](media/avm-create6.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)

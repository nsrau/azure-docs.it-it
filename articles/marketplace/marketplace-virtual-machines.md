---
title: Pianificare un'offerta di macchina virtuale-Microsoft Commercial Marketplace
description: Questo articolo descrive i requisiti per la pubblicazione di un'offerta di macchina virtuale in Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: a14ccb74e6f0ac2454255d609662706cb53ede31
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129593"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>Come pianificare un'offerta di macchina virtuale

Questo articolo illustra le diverse opzioni e i requisiti per la pubblicazione di un'offerta di macchina virtuale (VM) nel Marketplace commerciale. Le offerte di macchine virtuali sono offerte transazionali distribuite e fatturate tramite Azure Marketplace.

Prima di iniziare, [creare un account Marketplace commerciale nel centro](./partner-center-portal/create-account.md) per i partner e assicurarsi che l'account sia registrato nel programma Commercial Marketplace.

### <a name="technical-fundamentals"></a>Nozioni tecniche

Il processo di progettazione, compilazione e test delle offerte richiede tempo e richiede esperienza nella piattaforma Azure e nelle tecnologie usate per creare l'offerta. Il team di progettazione deve avere una conoscenza professionale delle [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/), dell' [archiviazione di Azure](https://azure.microsoft.com/services/?filter=storage#storage)e della rete di [Azure](https://azure.microsoft.com/services/?filter=networking#networking), nonché delle competenze della [progettazione e dell'architettura delle applicazioni Azure](https://azure.microsoft.com/solutions/architecture/). Vedere le risorse tecniche aggiuntive seguenti: 

- Esercitazioni
  - [Macchine virtuali di Linux](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Macchine virtuali di Windows](../virtual-machines/windows/tutorial-manage-vm.md)

- Esempi
  - [Esempi dell'interfaccia della riga di comando di Azure per macchine virtuali Linux](../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell per macchine virtuali Linux](../virtual-machines/linux/powershell-samples.md)
  - [Esempi dell'interfaccia della riga di comando di Azure per macchine virtuali Windows](../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell per macchine virtuali Windows](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

## <a name="technical-requirements"></a>Requisiti tecnici

Le offerte di VM presentano i requisiti tecnici seguenti:

- È necessario preparare un disco rigido virtuale (VHD) del sistema operativo. I dischi rigidi virtuali del disco dati sono facoltativi. Questa procedura è illustrata in dettaglio di seguito.
- Il cliente può annullare l'offerta in qualsiasi momento.
- È necessario creare almeno un piano per l'offerta. Il piano viene valutato in base all' [opzione di gestione delle licenze](#licensing-options) selezionata.
   > [!IMPORTANT]
   > Ogni immagine di macchina virtuale in un piano deve avere lo stesso numero di dischi dati.

Una macchina virtuale contiene due componenti:

- **VHD operativo** : contiene il sistema operativo e la soluzione distribuiti con l'offerta. Il processo di preparazione del disco rigido virtuale varia a seconda che si tratti di una macchina virtuale basata su Linux, Windows o personalizzata.
- Dischi **rigidi virtuali del disco dati** (facoltativo): archivio permanente dedicato per una macchina virtuale. Non usare il disco rigido virtuale del sistema operativo (ad esempio, l'unità C:) per archiviare informazioni permanenti. 
    - È possibile includere fino a 16 dischi dati.
    - Usare un disco rigido virtuale per ogni disco dati, anche se il disco è vuoto.

    > [!NOTE]
    > Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per la soluzione. In fase di distribuzione i clienti non possono rimuovere dischi che fanno parte di un'immagine ma possono sempre aggiungerne altri durante o dopo la distribuzione.

Per istruzioni dettagliate sulla preparazione delle risorse tecniche, vedere [creare una macchina virtuale usando una base approvata](azure-vm-create-using-approved-base.md) o [creare una macchina virtuale usando un'immagine personalizzata](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Destinatari dell'anteprima

Un pubblico di anteprima può accedere all'offerta di VM prima di essere pubblicata in Azure Marketplace per testare la funzionalità end-to-end prima di pubblicarla in tempo reale. Nella pagina **Anteprima destinatari** è possibile definire un numero limitato di destinatari di anteprima. 

> [!NOTE]
> Un pubblico di anteprima è diverso da un piano privato. Un piano privato è quello reso disponibile solo per un determinato gruppo di destinatari. In questo modo è possibile negoziare un piano personalizzato con clienti specifici. Per ulteriori informazioni, vedere la sezione successiva: piani.

È possibile inviare gli inviti agli indirizzi di posta elettronica dell'account Microsoft (MSA) o Azure Active Directory (Azure AD). Aggiungere fino a 10 indirizzi di posta elettronica manualmente o importare fino a 20 con un file con estensione CSV. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti all'offerta.

## <a name="plans-and-pricing"></a>Piani e prezzi

Le offerte della macchina virtuale richiedono almeno un piano. Un piano definisce l'ambito e i limiti della soluzione e i prezzi associati. È possibile creare più piani per l'offerta per offrire ai clienti diverse opzioni tecniche e di licenza, oltre a versioni di valutazione gratuite. Vedi [piani e prezzi per le offerte del Marketplace commerciale](plans-pricing.md) per indicazioni generali sui piani, inclusi i modelli di prezzi, le versioni di valutazione gratuite e i piani privati. 

Le macchine virtuali sono completamente abilitate per il commercio, usando modelli di licenza con pagamento in base al consumo o bring your own License (BYOL). Microsoft gestisce la transazione commerciale e addebita il costo al cliente per conto dell'utente. Si ottiene il vantaggio di usare la relazione di pagamento preferita tra il cliente e Microsoft, compresi eventuali contratti Enterprise. Per ulteriori informazioni, vedere la pagina relativa alle [funzionalità Transact del Marketplace commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> Gli impegni monetari associati a un Enterprise Agreement possono essere usati per l'utilizzo di Azure della macchina virtuale, ma non per i costi di licenza software.

### <a name="licensing-options"></a>Opzioni di licenza

Quando si prepara la pubblicazione di una nuova offerta di VM, è necessario decidere quale opzione di licenza scegliere. Verranno stabilite le informazioni aggiuntive da fornire in seguito durante la creazione dell'offerta nel centro per i partner.

Queste sono le opzioni di licenza disponibili per le offerte di macchine virtuali:

| Opzione gestione licenze | Processo di transazione |
| --- | --- |
| Versione di prova gratuita | Offri ai tuoi clienti una versione di valutazione gratuita di una, tre o sei mesi. |
| Test drive | Questa opzione consente ai clienti di valutare le macchine virtuali senza costi aggiuntivi. Non è necessario essere un cliente di Azure esistente per partecipare all'esperienza di valutazione. Per informazioni dettagliate, vedere [che cos'è un test drive?](./what-is-test-drive.md) |
| BYOL | L'opzione Bring your own Licensing consente ai clienti di portare le licenze software esistenti in Azure.\* |
| Basato sull'utilizzo | Noto anche come pagamento in base al consumo, questa opzione consente ai clienti di pagare all'ora. |
| Demo interattiva  | Offrire ai clienti un'esperienza guidata della soluzione usando una dimostrazione interattiva. Il vantaggio è che è possibile offrire un'esperienza di valutazione senza dover fornire un'installazione complessa della soluzione complessa. |
|

\* L'editore supporta tutti gli aspetti della transazione di licenza software, tra cui l'ordine, l'evasione, il controllo, la fatturazione, la fatturazione, il pagamento e la raccolta.

Nell'esempio seguente viene illustrata un'offerta di macchina virtuale in Azure Marketplace con prezzi basati sull'utilizzo.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Schermata di offerta della macchina virtuale di esempio.":::

### <a name="private-plans"></a>Piani privati

È possibile limitare l'individuazione e la distribuzione della macchina virtuale a un set specifico di clienti pubblicando l'immagine e i prezzi come piano privato. I piani privati hanno la possibilità di creare offerte esclusive per i clienti più vicini e offrire software e termini personalizzati. Tali condizioni personalizzate consentono di porre in evidenza numerosi scenari, con offerte contenenti condizioni e prezzi particolari, nonché l'accesso anticipato a versioni software limitate. I piani privati consentono di fornire prezzi o prodotti specifici a un set limitato di clienti.

Per altre informazioni, vedere [piani e prezzi per offerte per Marketplace commerciali](plans-pricing.md) e [offerte private in Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).

## <a name="test-drives"></a>Test drive

È possibile scegliere di abilitare un test drive per la macchina virtuale. Le unità di test consentono ai clienti di accedere a un ambiente preconfigurato per un numero fisso di ore. È possibile abilitare le unità di test per qualsiasi opzione di pubblicazione, tuttavia questa funzionalità presenta requisiti aggiuntivi. Per ulteriori informazioni sulle unità di test, vedere [che cos'è un test drive?](what-is-test-drive.md). Per informazioni sulla configurazione di diversi tipi di unità di test, vedere [configurazione tecnica di test drive](test-drive-technical-configuration.md).

> [!TIP]
> Una test drive è diversa da una [versione di valutazione gratuita](plans-pricing.md#free-trials). È possibile offrire un test drive, una versione di valutazione gratuita o entrambi. Entrambi forniscono ai clienti la soluzione per un periodo di tempo fisso. Tuttavia, un test drive include anche una presentazione pratica e autonoma delle funzionalità chiave del prodotto e dei vantaggi illustrati in uno scenario di implementazione reale.

## <a name="customer-leads"></a>Clienti potenziali

Per raccogliere informazioni sui clienti, è necessario connettere l'offerta al sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e al negozio online in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Il Marketplace commerciale supporta un'ampia gamma di sistemi CRM, oltre alla possibilità di usare una tabella di Azure o di configurare un endpoint HTTPS usando Power automatici.

È possibile aggiungere o modificare una connessione CRM in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Contratti legali

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard che è possibile usare per le offerte nel Marketplace commerciale. Quando si offre il software nel contratto standard, i clienti devono leggere e accettare una sola volta e non è necessario creare termini e condizioni personalizzati.

Se si sceglie di utilizzare il contratto standard, si ha la possibilità di aggiungere termini di modifica universale e fino a 10 modifiche personalizzate al contratto standard. È anche possibile usare i propri termini e condizioni anziché il contratto standard. Questi dettagli vengono gestiti nella pagina delle **Proprietà** . Per informazioni dettagliate, vedere [contratto standard per Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il Marketplace commerciale, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard o a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.

## <a name="cloud-solution-providers"></a>Cloud Solution Provider

Quando si crea l'offerta nel centro per i partner, viene visualizzata la scheda **rivendi tramite CSP** . Questa opzione consente ai partner che fanno parte del programma Microsoft Cloud Solution Providers (CSP) di rivendere la macchina virtuale come parte di un'offerta in bundle. Tutti i piani Bring Your Own License (BYOL) fanno automaticamente parte del programma. È anche possibile scegliere di includere i piani non BYOL. Per ulteriori informazioni, vedere il [programma Cloud Solution Provider](cloud-solution-providers.md) . 

> [!NOTE]
> Il consenso esplicito del canale partner Cloud Solution Provider (CSP) è ora disponibile. Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [**Cloud Solution Provider**](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'offerta di macchina virtuale in Azure Marketplace](azure-vm-create.md)
- [Creare una macchina virtuale usando una base approvata](azure-vm-create-using-approved-base.md) o [creare una macchina virtuale usando un'immagine personalizzata](azure-vm-create-using-own-image.md).
- [Procedure consigliate per le inserzioni di offerte](gtm-offer-listing-best-practices.md)
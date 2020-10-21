---
title: Configurare i dettagli dell'elenco di offerte di macchine virtuali in Azure Marketplace
description: Informazioni su come configurare l'offerta di macchine virtuali dettagli su Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 2af96c0c2e850d8e1be67c23e71aa677773c1e03
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284343"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Come configurare i dettagli dell'elenco di offerte di macchine virtuali

Nella pagina **Presentazione dell'offerta** è possibile definire i dettagli dell'offerta, come il nome, la descrizione, i collegamenti e i contatti.

> [!NOTE]
> L'offerta che elenca contenuto, ad esempio la descrizione, i documenti, le schermate e le condizioni per l'utilizzo, non deve essere in inglese, purché la descrizione dell'offerta inizi con la frase "questa applicazione è disponibile solo in \<non-English language> ". È anche possibile specificare un URL per il collegamento a un sito in cui i contenuti dell'offerta sono disponibili in una lingua diversa da quella usata nella presentazione.

## <a name="marketplace-details"></a>Dettagli del marketplace

### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato dai clienti come titolo della presentazione dell'offerta. Questo campo viene compilato automaticamente con il nome immesso nella casella **Alias offerta** al momento della creazione dell'offerta e può essere modificato in un secondo momento. Il nome:

- Può essere un marchio registrato. Può includere marchi e simboli di copyright.
- Non può contenere più di 50 caratteri.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Immettere una breve descrizione dell'offerta da visualizzare nei risultati della ricerca di Azure Marketplace. Può contenere fino a 100 caratteri.

### <a name="long-summary"></a>Riepilogo lungo

Immettere una descrizione più lunga dell'offerta da visualizzare nei risultati della ricerca di Azure Marketplace. Può contenere fino a 256 caratteri.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Usare i tag HTML per formattare la descrizione in modo da renderla più accattivante. Per un elenco dei tag consentiti, vedere [tag HTML supportati](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Collegamento all'Informativa sulla privacy

Immettere l'indirizzo Web (URL) dell'informativa sulla privacy dell'organizzazione. Assicurarsi che l'offerta sia conforme alle leggi e alle normative sulla privacy. È inoltre necessario pubblicare un'informativa sulla privacy valida sul proprio sito Web.

## <a name="useful-links"></a>Collegamenti utili

Specificare altri documenti online sull'offerta. Per aggiungere un collegamento, selezionare **Aggiungi un collegamento** e completare i campi seguenti:

- **Name**: i clienti visualizzeranno il nome nella pagina dei dettagli.
- **Collegamento (URL)** : immettere un collegamento che consenta ai clienti di visualizzare il documento online.

## <a name="customer-support-links"></a>Collegamenti al supporto clienti

Specificare il sito Web di supporto che i clienti possono visitare per contattare il team di supporto.

- Sito Web del supporto Azure globale
- Sito Web del supporto di Azure per enti pubblici

## <a name="partner-support-contact"></a>Contatto per il supporto per i partner

Immettere le informazioni di contatto che i partner Microsoft devono usare quando i clienti aprono un ticket di supporto. Queste informazioni non sono indicate in Azure Marketplace.

- Nome
- Email
- Telefono

## <a name="engineering-contact"></a>Contatto tecnico

Immettere le informazioni di contatto che Microsoft dovrà usare in caso di problemi con l'offerta, inclusi i problemi di certificazione. Queste informazioni non sono indicate in Azure Marketplace.

- Nome
- Email
- Telefono

## <a name="azure-marketplace-media"></a>File multimediali di Azure Marketplace

Inserire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate saranno rifiutate.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>In caso di problemi durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

### <a name="azure-marketplace-logos"></a>Logo di Azure Marketplace

Fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner lo utilizzerà per creare un logo **piccolo** e **medio** . Facoltativamente, è possibile sostituirli con immagini diverse in un secondo momento.

- **Grande** (da 216 x 216 a 350 x 350 px, obbligatorio)
- **Media** (90 x 90 px, facoltativo)
- **Piccolo** (48 x 48 px, facoltativo)

Questi logo vengono usati in posizioni diverse nell'elenco:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Screenshots (Schermate)

Aggiungere fino a cinque screenshot che mostrano il funzionamento dell'offerta. Ogni screenshot deve avere dimensioni pari a 1280 x 720 pixel ed essere in formato PNG. Ogni screenshot deve includere una didascalia.

### <a name="videos"></a>Video

Aggiungere fino a cinque video di descrizione dell'offerta. I video devono essere ospitati su un servizio video esterno. Immettere il nome di ogni video, l'indirizzo Web e un'immagine PNG di anteprima del video a 1280 x 720 pixel.

Per risorse aggiuntive relative alle presentazioni nel marketplace, vedere [Procedure consigliate per la presentazione di offerte nel marketplace](gtm-offer-listing-best-practices.md).

Prima di continuare, selezionare **Salva bozza**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare piani](azure-vm-create-plans.md)

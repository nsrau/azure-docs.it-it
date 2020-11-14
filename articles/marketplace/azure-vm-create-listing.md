---
title: Configurare i dettagli dell'elenco di offerte di macchine virtuali in Azure Marketplace
description: Informazioni su come configurare l'offerta di macchine virtuali dettagli su Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 7030ffeb49c0a3919894f6dcf81e7252777267c5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629598"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Come configurare i dettagli dell'elenco di offerte di macchine virtuali

Nella pagina di presentazione dell' **offerta** (selezionare dal menu di spostamento a sinistra nel centro per i partner) definire i dettagli dell'offerta, ad esempio nome dell'offerta, descrizione, collegamenti e contatti.

> [!NOTE]
> L'offerta che elenca contenuto, ad esempio la descrizione, i documenti, le schermate e le condizioni per l'utilizzo, non deve essere in inglese, purché la descrizione dell'offerta inizi con la frase "questa applicazione è disponibile solo in \<non-English language> ". È anche possibile fornire un indirizzo Web per il collegamento a un sito che offre contenuto in una lingua diversa da quella usata nell'offerta che elenca il contenuto.

## <a name="marketplace-details"></a>Dettagli del marketplace

### <a name="name"></a>Nome

Il nome immesso qui verrà visualizzato dai clienti come titolo della presentazione dell'offerta. Questo campo viene compilato automaticamente con il nome immesso nella casella **alias offerta** al momento della creazione dell'offerta. Il nome:

- Può includere i simboli del copyright e del marchio.
- Il numero di caratteri deve essere inferiore a 50.
- Non può includere emoji.

### <a name="search-results-summary"></a>Riepilogo dei risultati della ricerca

Fornire una breve descrizione dell'offerta da visualizzare nei risultati della ricerca di Azure Marketplace. Può contenere fino a 100 caratteri.

### <a name="short-description"></a>Breve descrizione

Fornire una descrizione più lunga dell'offerta da visualizzare nei risultati della ricerca di Azure Marketplace. Può contenere fino a 256 caratteri.

### <a name="description"></a>Descrizione

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Usare i tag HTML per formattare la descrizione in modo da renderla più accattivante. Per un elenco dei tag consentiti, vedere [tag HTML supportati](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Collegamento all'Informativa sulla privacy

Immettere l'indirizzo Web (URL) dell'informativa sulla privacy dell'organizzazione. Assicurarsi che l'offerta sia conforme alle leggi e alle normative sulla privacy. È inoltre necessario pubblicare un'informativa sulla privacy valida sul proprio sito Web.

## <a name="useful-links"></a>Collegamenti utili

Specificare altri documenti online sull'offerta. Per aggiungere un collegamento, selezionare **Aggiungi un collegamento** e completare i campi seguenti:

- **Name** : i clienti visualizzeranno il nome nella pagina dei dettagli.
- **Collegamento** : immettere un indirizzo Web che consente ai clienti di visualizzare il documento online.

## <a name="customer-support-links"></a>Collegamenti al supporto clienti

Specificare il sito Web di supporto che i clienti possono visitare per contattare il team di supporto.

- Sito Web del supporto Azure globale
- Sito Web del supporto di Azure per enti pubblici

## <a name="partner-support-contact"></a>Contatto per il supporto per i partner

Fornire il **nome** , l' **indirizzo di posta elettronica** e il **telefono** per i partner Microsoft da usare quando i clienti aprono un ticket di supporto. Queste informazioni non sono indicate in Azure Marketplace.

## <a name="engineering-contact"></a>Contatto tecnico

Fornire il **nome** , l' **indirizzo di posta elettronica** e il **telefono** per Microsoft da usare in caso di problemi con l'offerta, inclusi i problemi di certificazione. Queste informazioni non sono indicate in Azure Marketplace.

## <a name="cloud-solution-provider-program-contact"></a>Contatto del programma Cloud Solution Provider

Fornire il **nome** , l' **indirizzo di posta elettronica** e il **telefono** della persona che gestisce il programma CSP (se applicabile). Queste informazioni non sono indicate in Azure Marketplace. Per i **materiali di marketing del programma CSP** , immettere l'indirizzo Web in cui i partner del programma possono visualizzare i materiali di marketing.

## <a name="marketplace-media"></a>File multimediali del marketplace

Fornire i logo e le immagini da usare con l'offerta. Tutte le immagini devono essere in formato PNG. Le immagini sfocate saranno rifiutate.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>In caso di problemi durante il caricamento dei file, assicurarsi che la rete locale non blocchi il servizio https://upload.xboxlive.com usato dal Centro per i partner.

### <a name="logos"></a>Loghi

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

Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu di spostamento a sinistra, **Anteprima destinatari**.

## <a name="next-steps"></a>Passaggi successivi

- [Creazione di un gruppo di destinatari di anteprima](azure-vm-create-preview.md)

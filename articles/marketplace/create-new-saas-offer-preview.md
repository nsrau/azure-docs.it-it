---
title: Come aggiungere un pubblico di anteprima per l'offerta SaaS in Microsoft Commercial Marketplace
description: Come aggiungere un pubblico di anteprima per l'offerta SaaS (Software as a Service) nel centro per i partner Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: ed0c7ef98e70774350c9a3ff12b0cc3f4186e1bb
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380982"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Come aggiungere un pubblico di anteprima per l'offerta SaaS

Quando si crea un'offerta di Software as a Service (SaaS) nel centro per i partner, è necessario definire un pubblico di anteprima che può rivedere l'elenco di offerte prima che diventi Live. Questo articolo illustra come configurare un gruppo di destinatari di anteprima.

> [!NOTE]
> Se si sceglie di elaborare le transazioni in modo indipendente, questa opzione non verrà visualizzata. È invece possibile passare a [come commercializzare l'offerta SaaS](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Definire un pubblico di anteprima

Nella scheda **Anteprima destinatari** è possibile definire un gruppo di destinatari limitato che può rivedere l'offerta SaaS prima di pubblicarla in un gruppo più ampio di destinatari del Marketplace. È possibile inviare gli inviti agli indirizzi di posta elettronica dell'account Microsoft (MSA) o Azure Active Directory (Azure AD). Aggiungere un minimo di uno e un massimo di 10 indirizzi di posta elettronica manualmente o importare fino a 20 con un file con estensione CSV. È possibile aggiornare l'elenco di destinatari di anteprima in qualsiasi momento.

> [!NOTE]
> Il gruppo di destinatari dell'anteprima è diverso dal gruppo di destinatari privato. Un pubblico di anteprima è autorizzato ad accedere all'offerta prima che venga pubblicata Live negli archivi online. È anche possibile scegliere di creare un piano e renderlo disponibile solo per un gruppo di destinatari privato. I piani privati sono descritti in [come creare piani per l'offerta SaaS](create-new-saas-offer-plans.md).

### <a name="add-email-addresses-manually"></a>Aggiungere manualmente gli indirizzi di posta elettronica

1. Nella pagina **Anteprima destinatari** aggiungere un singolo Azure ad o un indirizzo di posta elettronica MSA e una descrizione facoltativa nelle caselle fornite.
1. Per aggiungere un altro indirizzo di posta elettronica, selezionare il collegamento **Aggiungi un altro messaggio di posta elettronica** .
1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: **configurazione tecnica**.
1. Passare a [come aggiungere i dettagli tecnici per l'offerta SaaS](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>Aggiungere indirizzi di posta elettronica usando il file CSV

1. Nella pagina **Anteprima destinatari** selezionare il collegamento **Esporta destinatari (CSV)** .
1. Aprire. File CSV in un'applicazione, ad esempio Microsoft Excel.
1. Nel. File CSV, nella colonna **ID** , immettere gli indirizzi di posta elettronica che si desidera aggiungere ai destinatari dell'anteprima.
1. Nella colonna **Descrizione** è possibile aggiungere facoltativamente una descrizione per ogni indirizzo di posta elettronica.
1. Nella colonna **tipo** aggiungere **MixedAadMsaId** a ogni riga con un indirizzo di posta elettronica.
1. Salvare il file come. File CSV.
1. Nella pagina **Anteprima destinatari** selezionare il collegamento **Importa destinatari (CSV)** .
1. Nella finestra di dialogo **conferma** selezionare **Sì**.
1. Selezionare il. File CSV, quindi selezionare **Apri**.
1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: **configurazione tecnica**.

## <a name="next-steps"></a>Passaggi successivi

- [Come aggiungere i dettagli tecnici per l'offerta SaaS](create-new-saas-offer-technical.md)

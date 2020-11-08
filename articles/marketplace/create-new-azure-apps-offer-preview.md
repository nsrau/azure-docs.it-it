---
title: Come aggiungere un pubblico di anteprima per l'offerta di applicazione Azure
description: Informazioni su come aggiungere un pubblico di anteprima per l'offerta di applicazione Azure nel centro per i partner.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370252"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Come aggiungere un pubblico di anteprima per l'offerta di applicazione Azure

Questo articolo descrive come configurare un pubblico di anteprima per un'offerta applicazione Azure nel Marketplace commerciale. È necessario definire un pubblico di anteprima che può rivedere l'elenco di offerte prima che diventi disponibile.

## <a name="define-a-preview-audience"></a>Definire un pubblico di anteprima

Nella pagina di **anteprima dei destinatari** è possibile definire un gruppo di destinatari limitato che può rivedere l'offerta applicazione Azure prima di pubblicarla in un gruppo più ampio di destinatari del Marketplace. È possibile definire i destinatari dell'anteprima usando gli ID sottoscrizione di Azure, insieme a una descrizione facoltativa per ognuno di essi. Nessuno di questi campi può essere visualizzato dai clienti. È possibile trovare l'ID sottoscrizione di Azure nella pagina **Sottoscrizioni** nel portale di Azure.

Aggiungere un minimo di uno e un massimo di 10 ID sottoscrizione di Azure, singolarmente (fino a 10) o caricando un file CSV (fino a 100) per definire gli utenti che possono visualizzare l'anteprima dell'offerta prima che venga pubblicata in tempo reale. Se l'offerta è già stata pubblicata, è comunque possibile definire un gruppo di destinatari per l'anteprima che possa testare le modifiche o gli aggiornamenti all'offerta.

> [!NOTE]
> Un gruppo di destinatari per l'anteprima è diverso da un pubblico privato. Un pubblico di anteprima è autorizzato ad accedere all'offerta prima che venga pubblicata Live negli archivi online. Può infatti visualizzare e convalidare tutti i piani, inclusi quelli che saranno disponibili solo per un gruppo di destinatari privato dopo che l'offerta viene pubblicata completamente nel marketplace. È possibile rendere un piano disponibile solo per i destinatari privati. Un pubblico privato (definito nella scheda **disponibilità** di un piano) dispone di accesso esclusivo a un piano specifico.

### <a name="add-email-addresses-manually"></a>Aggiungere manualmente gli indirizzi di posta elettronica

1. Nella pagina **Anteprima destinatari** aggiungere un singolo ID sottoscrizione di Azure e una descrizione facoltativa nelle caselle fornite.
1. Per aggiungere un altro indirizzo di posta elettronica, selezionare il collegamento **Aggiungi ID (max 10)** .
1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: configurazione tecnica.
1. Passare a [passaggi successivi](#next-steps).

### <a name="add-email-addresses-using-the-csv-file"></a>Aggiungere indirizzi di posta elettronica usando il file CSV

1. Nella pagina **Anteprima destinatari** selezionare il collegamento **Esporta destinatari (CSV)** .
1. Aprire. File CSV in un'applicazione, ad esempio Microsoft Excel.
1. Nel. File CSV, nella colonna **ID** , immettere gli ID sottoscrizione di Azure che si desidera aggiungere ai destinatari dell'anteprima.
1. Nella colonna **Descrizione** è possibile aggiungere facoltativamente una descrizione per ogni indirizzo di posta elettronica.
1. Nella colonna **tipo** aggiungere **SubscriptionId** a ogni riga con un indirizzo di posta elettronica.
1. Salvare il file come. File CSV.
1. Nella pagina **Anteprima destinatari** selezionare il collegamento **Importa destinatari (CSV)** .
1. Nella finestra di dialogo **conferma** selezionare **Sì**.
1. Selezionare il. File CSV, quindi selezionare **Apri**.
1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: configurazione tecnica.

## <a name="next-steps"></a>Passaggi successivi

- [Come aggiungere i dettagli tecnici per l'offerta di applicazione Azure](create-new-azure-apps-offer-technical.md)

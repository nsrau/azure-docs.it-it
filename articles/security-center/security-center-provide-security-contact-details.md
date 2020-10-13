---
title: Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: aa35d1325e339af515c8bfc052d1af524b464e09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446009"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Configurare le notifiche tramite posta elettronica per gli avvisi di sicurezza 

Per assicurarsi che le persone giuste dell'organizzazione vengano informate sugli avvisi di sicurezza nell'ambiente, immettere i loro indirizzi di posta elettronica nella pagina di impostazioni **Notifiche tramite posta elettronica**.

Quando si impostano le notifiche, è possibile configurare i messaggi di posta elettronica da inviare a utenti specifici o a chiunque abbia un ruolo specifico di Azure per una sottoscrizione. 

Per evitare un sovraccarico di avvisi, il Centro sicurezza limita il volume dei messaggi di posta elettronica in uscita. Per ogni sottoscrizione, il Centro sicurezza invia:

- Un massimo di **quattro** messaggi di posta elettronica al giorno per avvisi con **gravità alta**
- Un massimo di **due** messaggi di posta elettronica al giorno per avvisi con **gravità media**
- Un massimo di **un** messaggio di posta elettronica al giorno per avvisi con **gravità bassa**


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="Configurazione dei dettagli del contatto che riceverà messaggi di posta elettronica sugli avvisi di sicurezza." :::

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|Livello gratuito|
|Autorizzazioni e ruoli obbligatori:|**Amministrazione della protezione**<br>**Proprietario della sottoscrizione** |
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov (parziale)<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi|
|||


## <a name="set-up-email-notifications-for-alerts"></a>Configurare le notifiche tramite posta elettronica per gli avvisi <a name="email"></a>

È possibile inviare notifiche tramite posta elettronica a singoli utenti o a tutti quelli con ruoli specifici di Azure.

1. Nell'area **Prezzi e impostazioni** del Centro sicurezza scegliere la sottoscrizione appropriata e quindi selezionare **Notifiche tramite posta elettronica**.

1. Definire i destinatari per le notifiche:

    - Selezionare uno o più ruoli disponibili nell'elenco a discesa.
    - E/o immettere specifici indirizzi di posta elettronica separati da virgole. Non sono previsti limiti per il numero di indirizzi di posta elettronica che è possibile immettere.

1. Per applicare le informazioni di contatto per la sicurezza nella sottoscrizione, selezionare **Salva**.


## <a name="see-also"></a>Vedere anche
Per altre informazioni sugli avvisi di sicurezza, vedere gli argomenti seguenti:

* [Guida di riferimento sugli avvisi di sicurezza](alerts-reference.md): informazioni sugli avvisi di sicurezza che possono essere visualizzati nel modulo Protezione dalle minacce del Centro sicurezza di Azure
* [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
* [Automazione del flusso di lavoro](workflow-automation.md): informazioni su come automatizzare le risposte agli avvisi con la logica delle notifiche personalizzate
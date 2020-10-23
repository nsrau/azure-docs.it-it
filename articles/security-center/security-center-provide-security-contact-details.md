---
title: Configurare le notifiche tramite posta elettronica per gli avvisi del Centro sicurezza di Azure
description: Informazioni su come ottimizzare i tipi di messaggi di posta elettronica inviati dal Centro di sicurezza di Azure per gli avvisi di sicurezza.
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
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: ac50b17826f9f3d06d01d3cce9704094fddf1b1d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945618"
---
# <a name="configure-email-notifications-for-security-alerts"></a>Configurare le notifiche tramite posta elettronica per gli avvisi di sicurezza 

Gli avvisi di sicurezza devono essere inviati alle persone giuste dell'organizzazione. Per impostazione predefinita, il Centro sicurezza invia i messaggi ai proprietari delle sottoscrizioni ogni volta che per queste viene generato un avviso con gravità alta. Questa pagina illustra come personalizzare queste notifiche.

Per definire le proprie preferenze per le notifiche tramite posta elettronica, la pagina di impostazioni **Notifiche tramite posta elettronica** del Centro sicurezza di Azure consente di scegliere:

- ***Chi* deve ricevere una notifica**: i messaggi di posta elettronica possono essere inviati a utenti selezionati o a chiunque abbia un ruolo di Azure specificato per una sottoscrizione. 
- ***Cosa* deve essere notificato**: modificare i livelli di gravità per cui il Centro sicurezza dovrà inviare notifiche.

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
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>Personalizzare le notifiche tramite posta elettronica per gli avvisi di sicurezza<a name="email"></a>

È possibile inviare notifiche tramite posta elettronica a singoli utenti o a tutti quelli con ruoli specifici di Azure.

1. Nell'area **Prezzi e impostazioni** del Centro sicurezza selezionare la sottoscrizione appropriata e quindi selezionare **Notifiche tramite posta elettronica**.

1. Definire i destinatari per le notifiche con una o entrambe le opzioni seguenti:

    - Selezionare uno o più ruoli disponibili nell'elenco a discesa.
    - Immettere gli indirizzi di posta elettronica specifici separati da virgole. Non sono previsti limiti per il numero di indirizzi di posta elettronica che è possibile immettere.

1. Per applicare le informazioni di contatto per la sicurezza nella sottoscrizione, selezionare **Salva**.


## <a name="see-also"></a>Vedere anche
Per altre informazioni sugli avvisi di sicurezza, vedere le pagine seguenti:

- [Guida di riferimento sugli avvisi di sicurezza](alerts-reference.md): informazioni sugli avvisi di sicurezza che possono essere visualizzati nel modulo Protezione dalle minacce del Centro sicurezza di Azure
- [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Automazione del flusso di lavoro](workflow-automation.md): informazioni su come automatizzare le risposte agli avvisi con la logica delle notifiche personalizzate
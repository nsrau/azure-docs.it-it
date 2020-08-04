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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 5a2410a5ccaa10867e94b3a2ec10228a006fbe20
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534635"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>Configurare le notifiche di posta elettronica per gli avvisi di sicurezza 

Per assicurarsi che le persone corrette nell'organizzazione ricevano notifiche relative agli avvisi di sicurezza nell'ambiente in uso, immettere gli indirizzi di posta elettronica nella pagina delle impostazioni di **notifica tramite posta elettronica** .

Quando si configurano le notifiche, è possibile configurare i messaggi di posta elettronica da inviare a utenti specifici o a chiunque disponga di un ruolo specifico di Azure per una sottoscrizione. 

Per evitare l'affaticamento degli avvisi, il Centro sicurezza limita il volume dei messaggi in uscita. Per ogni sottoscrizione, il Centro sicurezza invia:

- un massimo di **quattro** messaggi di posta elettronica al giorno per gli avvisi con **gravità elevata**
- un massimo di **due** messaggi di posta elettronica al giorno per gli avvisi con **gravità media**
- un massimo di **un** messaggio di posta elettronica al giorno per gli avvisi con **gravità bassa**

## <a name="availability"></a>Disponibilità

- Stato della versione: **Disponibile a livello generale**
- Ruoli necessari: **amministratore della sicurezza** o **proprietario della sottoscrizione** 
- Cloud: ✔ cloud commerciali ✔ US Gov (parziale) ✘ nazionale/sovrano (Cina gov, altro gov)


## <a name="set-up-email-notifications-for-alerts"></a>Configurare le notifiche di posta elettronica per gli avvisi<a name="email"></a>

È possibile inviare notifiche tramite posta elettronica a singoli utenti o a tutti gli utenti con ruoli specifici di Azure.

1. Dall'area dei prezzi del Centro sicurezza **& impostazioni** , la sottoscrizione pertinente e selezionare **notifiche tramite posta elettronica**.

1. Definire i destinatari per le notifiche:

    - Dall'elenco a discesa selezionare i ruoli disponibili.
    - E/o immetti indirizzi di posta elettronica specifici separati da virgole. Non esiste alcun limite al numero di indirizzi di posta elettronica che è possibile immettere.

1. Per applicare le informazioni di contatto di sicurezza alla sottoscrizione, selezionare **Salva**.


## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sugli avvisi di sicurezza, vedere gli argomenti seguenti:

* [Avvisi di sicurezza: Guida di riferimento](alerts-reference.md) : informazioni sugli avvisi di sicurezza che possono essere visualizzati nel modulo di protezione dalle minacce del Centro sicurezza di Azure
* [Gestire e rispondere agli avvisi di sicurezza nel centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza
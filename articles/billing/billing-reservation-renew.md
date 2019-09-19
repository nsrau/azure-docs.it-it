---
title: Rinnovare automaticamente le prenotazioni di Azure
description: Informazioni su come rinnovare automaticamente le prenotazioni di Azure per continuare a ottenere sconti sulla prenotazione.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "68679458"
---
# <a name="automatically-renew-reservations"></a>Rinnovare automaticamente le prenotazioni

È possibile rinnovare le prenotazioni per acquistare automaticamente una sostituzione alla scadenza di una prenotazione esistente. Il rinnovo automatico offre un modo semplice per continuare a ottenere gli sconti per la prenotazione. Evita inoltre di dover monitorare attentamente la scadenza di una prenotazione. Con il rinnovo automatico si evita di perdere i vantaggi del risparmio. Per impostazione predefinita, il rinnovo è disattivato. Abilitare o disabilitare l'impostazione di rinnovo in qualsiasi momento, fino alla scadenza della prenotazione esistente.

Il rinnovo di una prenotazione crea una nuova prenotazione alla scadenza di quella esistente. Non estende il termine della prenotazione esistente.

Acconsentire esplicitamente al rinnovo automatico in qualsiasi momento. Il prezzo di rinnovo è disponibile 30 giorni prima della scadenza della prenotazione esistente. Quando si abilita il rinnovo più di 30 giorni prima della scadenza della prenotazione, si riceve un messaggio di posta elettronica contenente i costi di rinnovo 30 giorni prima della scadenza. Il prezzo di prenotazione potrebbe variare tra il momento in cui si blocca il prezzo di rinnovo e il momento del rinnovo. In tal caso, il costo del rinnovo è il più basso tra i due costi. È possibile apportare modifiche alla quantità di prenotazione. In tal caso, il rinnovo verrà aggiornato in modo da usare il prezzo di mercato impostato al momento della modifica della quantità.

Non c'è alcun obbligo di rinnovo ed è possibile rifiutare esplicitamente il rinnovo in qualsiasi momento prima della scadenza della prenotazione esistente.

## <a name="set-up-renewal"></a>Configurare il rinnovo

Passare a portale di Azure > **Prenotazioni**.

1. Selezionare la prenotazione.
2. Fare clic su **Rinnovo**.
3. Selezionare **Acquista automaticamente una nuova prenotazione alla scadenza**.  
  ![Esempio che mostra il rinnovo della prenotazione](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Se non si rinnova

I servizi continuano a funzionare normalmente. Alla scadenza della prenotazione, verranno addebitate le tariffe con pagamento in base al consumo per l'utilizzo.

## <a name="required-renewal-permissions"></a>Autorizzazioni di rinnovo obbligatorie

Per rinnovare una prenotazione sono necessarie le condizioni seguenti:

- È necessario essere un proprietario della prenotazione esistente.
- È necessario essere un proprietario della sottoscrizione se l'ambito della prenotazione è limitato a una singola sottoscrizione o a un gruppo di risorse.
- È necessario essere un proprietario della sottoscrizione se ha un ambito condiviso.

## <a name="default-renewal-settings"></a>Impostazioni di rinnovo predefinite

Per impostazione predefinita, il rinnovo eredita tutte le proprietà dalla prenotazione in scadenza. Un acquisto di rinnovo prenotazione ha gli stessi SKU, area, ambito, sottoscrizione fatturazione, termine e quantità.

Tuttavia, è possibile aggiornare la quantità di acquisto della prenotazione di rinnovo per ottimizzare il risparmio.

## <a name="when-the-new-reservation-is-purchased"></a>Quando viene acquistata la nuova prenotazione

Una nuova prenotazione viene acquistata alla scadenza della prenotazione esistente. L'obiettivo è quello di evitare ritardi tra le due prenotazioni. La continuità garantisce che i costi siano prevedibili e che l'utente continui a ottenere sconti.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Modifica della prenotazione padre dopo l'impostazione del rinnovo

Se si apportano le modifiche seguenti alla prenotazione in scadenza, il rinnovo della prenotazione viene annullato:

- Split
- Unione
- Trasferimento della prenotazione da un account a un altro
- Trasferimento della prenotazione da una sottoscrizione WebDirect a una sottoscrizione di Contratto Enterprise o qualsiasi altro metodo di acquisto
- Rinnovare la registrazione

La nuova prenotazione eredita l'impostazione della flessibilità dell'ambito e delle dimensioni dell'istanza dalla prenotazione in scadenza durante il rinnovo.

## <a name="new-reservation-permissions"></a>Autorizzazioni della nuova prenotazione

Azure copia le autorizzazioni dalla prenotazione in scadenza nella nuova prenotazione. Inoltre, l'amministratore dell'account di sottoscrizione dell'acquisto della prenotazione ha accesso alla nuova prenotazione.

## <a name="potential-renewal-problems"></a>Problemi di rinnovo potenziali

Azure potrebbe non elaborare il rinnovo se:

- Non è possibile riscuotere il pagamento
- Si verifica un errore di sistema durante il rinnovo
- Lo SKU in scadenza non è attivo durante il rinnovo
- Il contratto Enterprise viene rinnovato in un altro contratto Enterprise

Si riceverà una notifica via posta elettronica se si verifica una delle condizioni precedenti e il rinnovo viene disattivato.

## <a name="renewal-notification"></a>Notifica di rinnovo

I messaggi di posta elettronica vengono inviati a utenti diversi a seconda del metodo di acquisto:

- Clienti Contratto Enterprise: i messaggi di posta elettronica vengono inviati ai contatti di notifica impostati nel portale del contratto Enterprise.
- Clienti di singole sottoscrizioni con tariffe a consumo: i messaggi di posta elettronica vengono inviati agli utenti configurati come amministratori dell'account.
- Clienti del provider di soluzioni cloud: i messaggi di posta elettronica vengono inviati ai contatti di notifica del partner.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulle prenotazioni di Azure, vedere [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md).

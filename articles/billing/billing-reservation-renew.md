---
title: Rinnova automaticamente le prenotazioni di Azure
description: Informazioni su come rinnovare automaticamente le prenotazioni di Azure per continuare a ottenere sconti sulla prenotazione.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679458"
---
# <a name="automatically-renew-reservations"></a>Rinnova automaticamente le prenotazioni

È possibile rinnovare le prenotazioni per acquistare automaticamente una sostituzione alla scadenza di una prenotazione esistente. Il rinnovo automatico offre un modo semplice per continuare a ottenere gli sconti di prenotazione. Evita inoltre di dover monitorare attentamente la scadenza di una prenotazione. Con il rinnovo automatico, si evita la perdita dei vantaggi del risparmio non è necessario rinnovarli manualmente. Per impostazione predefinita, l'impostazione del rinnovo è disattivata. Abilitare o disabilitare l'impostazione di rinnovo in qualsiasi momento, fino alla scadenza della prenotazione esistente.

Il rinnovo di una prenotazione crea una nuova prenotazione alla scadenza della prenotazione esistente. Non estende il termine della prenotazione esistente.

Acconsenti esplicitamente al rinnovo automatico in qualsiasi momento. Il prezzo di rinnovo è disponibile 30 giorni prima della scadenza della prenotazione esistente. Quando si Abilita il rinnovo per più di 30 giorni prima della scadenza della prenotazione, viene inviato un messaggio di posta elettronica contenente i costi di rinnovo di 30 giorni prima della scadenza. Il prezzo di prenotazione potrebbe variare tra il momento in cui si blocca il prezzo di rinnovo e l'ora del rinnovo. In tal caso, il costo del rinnovo è il più basso tra i due costi. È possibile apportare modifiche alla quantità di prenotazione. In caso contrario, il rinnovo verrà aggiornato in modo da utilizzare il prezzo di mercato impostato al momento della modifica della quantità.

Non c'è alcun obbligo di rinnovo ed è possibile rifiutare esplicitamente il rinnovo in qualsiasi momento prima della scadenza della prenotazione esistente.

## <a name="set-up-renewal"></a>Configurare il rinnovo

Passare a portale di Azure > **prenotazioni**.

1. Selezionare la prenotazione.
2. Fare clic su **rinnovo**.
3. Selezionare **Acquista automaticamente una nuova prenotazione al termine della scadenza**.  
  ![Esempio che mostra il rinnovo della prenotazione](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Se non si rinnova

I servizi continuano a funzionare normalmente. Al termine della prenotazione, verranno addebitate le tariffe con pagamento in base al consumo per l'utilizzo.

## <a name="required-renewal-permissions"></a>Autorizzazioni di rinnovo obbligatorie

Per rinnovare una prenotazione sono necessarie le condizioni seguenti:

- È necessario essere un proprietario della prenotazione esistente.
- È necessario essere un proprietario della sottoscrizione se l'ambito della prenotazione è limitato a una singola sottoscrizione o a un gruppo di risorse.
- È necessario essere un proprietario della sottoscrizione se dispone di un ambito condiviso.

## <a name="default-renewal-settings"></a>Impostazioni di rinnovo predefinite

Per impostazione predefinita, il rinnovo eredita tutte le proprietà dalla prenotazione in scadenza. Un acquisto di rinnovo prenotazione ha lo stesso SKU, area, ambito, sottoscrizione fatturazione, termini e quantità.

Tuttavia, è possibile aggiornare la quantità di acquisto della prenotazione di rinnovo per ottimizzare il risparmio.

## <a name="when-the-new-reservation-is-purchased"></a>Quando viene acquistata la nuova prenotazione

Una nuova prenotazione viene acquistata al termine della prenotazione esistente. Si tenta di evitare un ritardo tra le due prenotazioni. La continuità garantisce che i costi siano prevedibili e continui a ottenere sconti.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Modifica della prenotazione padre dopo l'impostazione del rinnovo

Se si apportano le modifiche seguenti alla prenotazione in scadenza, il rinnovo della prenotazione viene annullato:

- Suddiviso
- Unisci
- Trasferimento della prenotazione da un account a un altro
- Trasferimento della prenotazione da una sottoscrizione WebDirect a una sottoscrizione Enterprise Agreement (EA) o qualsiasi altro metodo di acquisto
- Rinnovare la registrazione

La nuova prenotazione eredita l'impostazione della flessibilità dell'ambito e delle dimensioni dell'istanza dalla prenotazione in scadenza durante il rinnovo.

## <a name="new-reservation-permissions"></a>Nuove autorizzazioni di prenotazione

Azure copia le autorizzazioni dalla prenotazione in scadenza alla nuova prenotazione. Inoltre, l'amministratore dell'account di sottoscrizione dell'acquisto della prenotazione ha accesso alla nuova prenotazione.

## <a name="potential-renewal-problems"></a>Problemi di rinnovo potenziali

Azure non può elaborare il rinnovo se:

- Non è possibile raccogliere i pagamenti
- Si verifica un errore di sistema durante il rinnovo
- Lo SKU in scadenza non è attivo durante il rinnovo
- La EA viene rinnovata in un'altra EA

Si riceverà una notifica tramite posta elettronica se si verifica una delle condizioni precedenti e il rinnovo viene disattivato.

## <a name="renewal-notification"></a>Notifica di rinnovo

I messaggi di posta elettronica vengono inviati a utenti diversi a seconda del metodo di acquisto:

- Clienti EA: i messaggi di posta elettronica vengono inviati ai contatti di notifica impostati nel portale EA.
- Clienti di singoli abbonamenti con tariffe con pagamento in base al consumo-i messaggi di posta elettronica vengono inviati agli utenti configurati come amministratori account.
- Clienti Cloud Solution Provider: i messaggi di posta elettronica vengono inviati al contatto di notifica partner.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulle prenotazioni di Azure, vedere [che cosa sono le prenotazioni di Azure?](billing-save-compute-costs-reservations.md)

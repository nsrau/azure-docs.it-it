---
title: Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f4ae3395c44f175603556711bfe2f90f0f987c94


---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglierà di specificare i dettagli dei contatti di sicurezza per la sottoscrizione di Azure, se non è già stato fatto. Queste informazioni verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Microsoft Security Response Center esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni sulle minacce e segnalazioni di violazioni da terzi.

Alla prima occorrenza giornaliera di un avviso e solo per gli avvisi di elevata gravità viene inviata una notifica tramite posta elettronica. Le preferenze di posta elettronica possono essere configurate solo per i criteri della sottoscrizione. I gruppi di risorse all'interno di una sottoscrizione ereditano queste impostazioni.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Indicazioni** selezionare **Specificare i dettagli dei contatti di sicurezza**.
   ![Specificare contatti per la sicurezza][1]
2. Verrà visualizzato il pannello **Specificare i dettagli dei contatti di sicurezza**. Selezionare la sottoscrizione di Azure per cui specificare le informazioni di contatto.
   ![Specificare i dettagli dei contatti di sicurezza][2]
3. Viene visualizzato un altro pannello **Specificare i dettagli dei contatti di sicurezza** .

   * Immettere l'indirizzo o gli indirizzi di posta elettronica del contatto di sicurezza, separati da virgole. Non è previsto alcun limite per il numero di indirizzi di posta elettronica che è possibile immettere.
   * Immettere un numero di telefono internazionale per il contatto di sicurezza.
   * Per ricevere messaggi di posta elettronica relativi agli avvisi di elevata gravità, attivare l'opzione **Send me emails about alerts**(Invia messaggi di posta elettronica sugli avvisi).
   * In futuro, sarà possibile inviare notifiche tramite posta elettronica ai proprietari di sottoscrizioni. Attualmente questa opzione non è disponibile.
   * Selezionare **OK** per applicare le informazioni di contatto di sicurezza alla sottoscrizione.

## <a name="see-also"></a>Vedere anche
Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png



<!--HONumber=Dec16_HO2-->



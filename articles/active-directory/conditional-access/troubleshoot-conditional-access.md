---
title: Risoluzione dei problemi di accesso con l'accesso condizionale-Azure Active Directory
description: Questo articolo descrive le operazioni da eseguire quando i criteri di accesso condizionale generano risultati imprevisti
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6778b556795f4e079100f1a7bcbb8b9465e9e315
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032969"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Risoluzione dei problemi di accesso con l'accesso condizionale

Le informazioni contenute in questo articolo possono essere usate per risolvere i problemi di accesso imprevisto correlati all'accesso condizionale usando i messaggi di errore e Azure AD log degli accessi.

## <a name="conditional-access-sign-in-interrupt"></a>Interrupt di accesso con accesso condizionale

Il primo consiste nel rivedere il messaggio di errore visualizzato. Per problemi di accesso quando si usa un Web browser, la pagina di errore contiene informazioni dettagliate. Queste informazioni possono descrivere solo la causa del problema e possono suggerire una soluzione.

![Il dispositivo conforme agli errori di accesso è obbligatorio](./media/troubleshoot-conditional-access/image1.png)

Nell'errore precedente il messaggio indica che è possibile accedere all'applicazione solo da dispositivi o applicazioni client che soddisfano i criteri di gestione dei dispositivi mobili della società. In questo caso, l'applicazione e il dispositivo non soddisfano tali criteri.

## <a name="azure-ad-sign-in-events"></a>Eventi di accesso Azure AD

Il secondo metodo per ottenere informazioni dettagliate sull'interruzione dell'accesso consiste nel esaminare gli eventi di accesso Azure AD per vedere quali criteri di accesso condizionale o criteri sono stati applicati e perché.

Per ulteriori informazioni sul problema, fare clic su **altri dettagli** nella pagina errore iniziale. Se si fa clic su **altri dettagli** , le informazioni sulla risoluzione dei problemi risultano utili quando si eseguono ricerche negli eventi di accesso Azure ad per l'evento di errore specifico visualizzato dall'utente o quando si apre una richiesta di assistenza con Microsoft.

![Altri dettagli di un accesso condizionale interrotto per il Web browser.](./media/troubleshoot-conditional-access/image2.png)

Per individuare i criteri di accesso condizionale o i criteri applicati e perché eseguire le operazioni seguenti.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o Reader globale.
1. Passare a **Azure Active Directory**  >  **accessi**.
1. Trovare l'evento per l'accesso da rivedere. Aggiungere o rimuovere filtri e colonne per filtrare le informazioni non necessarie.
   1. Aggiungere i filtri per limitare l'ambito:
      1. **ID di correlazione** quando si dispone di un evento specifico da analizzare.
      1. **Accesso condizionale** per verificare l'esito positivo e negativo del criterio. Definire l'ambito del filtro per visualizzare solo gli errori per limitare i risultati.
      1. **Nome utente** per visualizzare le informazioni correlate a utenti specifici.
      1. **Data** nell'ambito dell'intervallo di tempo in questione.

   ![Selezione del filtro di accesso condizionale nel log degli accessi](./media/troubleshoot-conditional-access/image3.png)

1. Quando l'evento di accesso corrispondente all'errore di accesso dell'utente è stato trovato, selezionare la scheda **accesso condizionale** . Nella scheda accesso condizionale vengono visualizzati i criteri specifici o i criteri che hanno causato l'interruzione dell'accesso.
   1. Le informazioni nella scheda **risoluzione dei problemi e supporto tecnico** possono indicare un motivo chiaro per cui un accesso non è riuscito, ad esempio un dispositivo che non soddisfa i requisiti di conformità.
   1. Per esaminare ulteriormente, eseguire il drill-down nella configurazione dei criteri facendo clic sul **nome del criterio**. Facendo clic sul **nome del criterio** , viene visualizzata l'interfaccia utente di configurazione dei criteri per il criterio selezionato per la revisione e la modifica.
   1. I **dettagli relativi** all' **utente** e al dispositivo client usati per la valutazione dei criteri di accesso condizionale sono disponibili anche nelle schede **informazioni di base**, **località**, **informazioni sul dispositivo**, **Dettagli autenticazione**e **dettagli aggiuntivi** dell'evento di accesso.

### <a name="policy-details"></a>Dettagli criteri

La selezione dei puntini di sospensione sul lato destro del criterio in un evento di accesso Visualizza i dettagli dei criteri. Che fornisce agli amministratori informazioni aggiuntive sui motivi per cui un criterio è stato applicato o meno.

   ![Scheda accesso condizionale evento di accesso](./media/troubleshoot-conditional-access/image5.png)

   ![Dettagli dei criteri (anteprima)](./media/troubleshoot-conditional-access/policy-details.png)

Il lato sinistro fornisce i dettagli raccolti al momento dell'accesso e il lato destro fornisce informazioni dettagliate sull'eventuale conformità dei dettagli ai requisiti dei criteri di accesso condizionale applicati. I criteri di accesso condizionale vengono applicati solo quando tutte le condizioni sono soddisfatte o non configurate.

Se le informazioni nell'evento non sono sufficienti per comprendere i risultati di accesso o modificare i criteri per ottenere i risultati desiderati, è possibile che venga aperta una richiesta di supporto. Passare alla scheda **risoluzione dei problemi e supporto** dell'evento di accesso e selezionare **Crea una nuova richiesta di supporto**.

![Scheda risoluzione dei problemi e supporto dell'evento di accesso](./media/troubleshoot-conditional-access/image6.png)

Quando si invia l'evento imprevisto, fornire l'ID richiesta e l'ora e la data dell'evento di accesso nei dettagli dell'invio dell'evento imprevisto. Queste informazioni consentiranno al supporto tecnico Microsoft di trovare l'evento interessato.

### <a name="conditional-access-error-codes"></a>Codici di errore di accesso condizionale

| Codice di errore di accesso | Stringa di errore |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Passaggi successivi

- [Report delle attività di accesso nel portale di Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Risoluzione dei problemi di Accesso condizionale tramite lo strumento What If](troubleshoot-conditional-access-what-if.md)
- Procedure consigliate per [l'accesso condizionale in Azure Active Directory](best-practices.md)

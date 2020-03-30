---
title: Risoluzione dei problemi di accesso con l'accesso condizionale - Azure Active DirectoryTroubleshooting sign-in problems with Conditional Access - Azure Active Directory
description: In questo articolo vengono descritte le operazioni da eseguire quando i criteri di accesso condizionale generano risultati imprevisti
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337441"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Risoluzione dei problemi di accesso con l'accesso condizionaleTroubleshooting sign-in problems with Conditional Access

Le informazioni contenute in questo articolo possono essere usate per risolvere i risultati imprevisti dell'accesso correlati all'accesso condizionale usando i messaggi di errore e il log degli accessi di Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Interruzione dell'accesso con accesso condizionaleConditional Access sign-in interrupt

Il primo modo consiste nell'esaminare il messaggio di errore visualizzato. Per i problemi di accesso quando si utilizza un browser web, la pagina di errore stessa contiene informazioni dettagliate. Queste informazioni da sole possono descrivere qual è il problema e che possono suggerire una soluzione.

![Errore di accesso - dispositivo conforme richiesto](./media/troubleshoot-conditional-access/image1.png)

Nell'errore precedente, il messaggio indica che l'applicazione è accessibile solo da dispositivi o applicazioni client che soddisfano i criteri di gestione dei dispositivi mobili dell'azienda. In questo caso, l'applicazione e il dispositivo non soddisfano tale criterio.

## <a name="azure-ad-sign-in-events"></a>Eventi di accesso di Azure ADAzure AD sign-in events

Il secondo metodo per ottenere informazioni dettagliate sull'interruzione dell'accesso consiste nell'esaminare gli eventi di accesso di Azure AD per vedere quali criteri o criteri di accesso condizionale sono stati applicati e perché.

Ulteriori informazioni sul problema sono disponibili facendo clic su **Ulteriori dettagli** nella pagina di errore iniziale. Facendo clic su **Altri dettagli** verranno fornite informazioni sulla risoluzione dei problemi utili durante la ricerca degli eventi di accesso di Azure AD per l'evento di errore specifico che l'utente ha visto o quando si apre un evento di supporto con Microsoft.Clicking More Details will reveal troubleshooting information that is helpful when searching the Azure AD sign-in events for the specific failure event the user saw or when opening a support incident with Microsoft.

![Ulteriori dettagli da un accesso al Web browser interrotto con accesso condizionale.](./media/troubleshoot-conditional-access/image2.png)

Per individuare i criteri o i criteri di accesso condizionale applicati e perché eseguire le operazioni seguenti.

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o lettore globale.
1. Passare agli**accessi**di **Azure Active Directory** > .
1. Trova l'evento per l'accesso da esaminare. Aggiungere o rimuovere filtri e colonne per filtrare le informazioni non necessarie.
   1. Aggiungere filtri per restringere l'ambito:
      1. **ID di correlazione** quando si dispone di un evento specifico da analizzare.
      1. **Accesso condizionale** per visualizzare l'esito positivo e negativo dei criteri. Definire l'ambito del filtro in modo da visualizzare solo gli errori per limitare i risultati.
      1. **Nome utente** per visualizzare le informazioni relative a utenti specifici.
      1. **Data** con ambito in base all'intervallo di tempo in questione.

   ![Selezione del filtro di accesso condizionale nel registro degli accessi](./media/troubleshoot-conditional-access/image3.png)

1. Dopo aver trovato l'evento di accesso che corrisponde all'errore di accesso dell'utente, selezionare la scheda **Accesso condizionale.** Nella scheda Accesso condizionale verranno visualizzati i criteri specifici che hanno causato l'interruzione dell'accesso.
   1. Le informazioni nella scheda **Risoluzione dei problemi e supporto** possono fornire un motivo chiaro per cui un accesso non è riuscito, ad esempio un dispositivo che non soddisfa i requisiti di conformità.
   1. Per approfondire, eseguire il drill-down nella configurazione dei criteri facendo clic sul pulsante **Nome criterio**. Facendo clic su **Nome criterio** verrà visualizzata l'interfaccia utente di configurazione dei criteri per il criterio selezionato per la revisione e la modifica.
   1. I **dettagli** **dell'utente client** e del dispositivo utilizzati per la valutazione dei criteri di accesso condizionale sono disponibili anche nelle schede Informazioni **di base,** **Posizione**, Informazioni **sul dispositivo,** **Dettagli autenticazione**e **Dettagli aggiuntivi** dell'evento di accesso.

   ![Scheda Accesso condizionale evento di accesso](./media/troubleshoot-conditional-access/image5.png)

Se le informazioni nell'evento non sono sufficienti per comprendere i risultati dell'accesso o modificare i criteri per ottenere i risultati desiderati, è possibile che venga aperto un evento imprevisto di supporto. Passare alla scheda **Risoluzione dei problemi e supporto** dell'evento di accesso e selezionare Crea una nuova richiesta di **supporto**.

![Scheda Risoluzione dei problemi e supporto dell'evento di accesso](./media/troubleshoot-conditional-access/image6.png)

Quando invii l'evento imprevisto, fornisci l'ID della richiesta, l'ora e la data dell'evento di accesso nei dettagli dell'invio dell'evento. Queste informazioni consentiranno al supporto tecnico Microsoft di trovare l'evento che ti preoccupa.

### <a name="conditional-access-error-codes"></a>Codici di errore di accesso condizionaleConditional Access error codes

| Codice di errore di accesso | Stringa di errore |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess (Accesso bloccato) |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Passaggi successivi

- [Report delle attività di accesso nel portale di Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Risoluzione dei problemi relativi all'accesso condizionale tramite lo strumento What If](troubleshoot-conditional-access-what-if.md)
- Procedure consigliate per [l'accesso condizionale in Azure Active DirectoryBest](best-practices.md) practices for Conditional Access in Azure Active Directory

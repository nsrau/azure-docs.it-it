---
title: Impostazioni Criteri di gruppo e MDM per ESR-Azure Active Directory
description: Impostazioni di gestione per Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85252934"
---
# <a name="group-policy-and-mdm-settings"></a>Impostazioni di Criteri di gruppo e di gestione di dispositivi mobili

Usare queste impostazioni di Criteri di gruppo e di gestione di dispositivi mobili solo nei dispositivi di proprietà aziendale, perché questi criteri vengono applicati all'intero dispositivo dell'utente. L'applicazione di un criterio di gestione di dispositivi mobili per disabilitare la sincronizzazione delle impostazioni per un dispositivo personale di proprietà dell'utente avrà un impatto negativo sull'uso del dispositivo. È anche possibile che altri account utente nel dispositivo siano influenzati dal criterio.

Le aziende che vogliono gestire il roaming per dispositivi personali (non gestiti) possono usare il portale di Azure per abilitare o disabilitare il roaming, invece di usare Criteri di gruppo o gestione di dispositivi mobili.
Le tabelle seguenti illustrano le impostazioni disponibili per i criteri.

> [!NOTE]
> Questo articolo si applica al browser Microsoft Edge legacy basato su HTML avviato con Windows 10 nel 2015 luglio. L'articolo non si applica al nuovo browser Microsoft Edge basato su cromo rilasciato il 15 gennaio 2020. Per altre informazioni sul comportamento di sincronizzazione per il nuovo Microsoft Edge, vedere l'articolo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Impostazioni di gestione di dispositivi mobili

Le impostazioni dei criteri di gestione di dispositivi mobili sono applicabili a Windows 10 e Windows 10 Mobile.  Il supporto per Windows 10 Mobile esiste solo per il roaming basato su account Microsoft tramite l’account OneDrive dell’utente. Per informazioni dettagliate sui dispositivi supportati per la sincronizzazione basata su Azure AD, vedere [dispositivi ed endpoint](enterprise-state-roaming-windows-settings-reference.md) .

| Nome | Descrizione |
| --- | --- |
| Allow Microsoft Account Connection |Consente agli utenti di effettuare l'autenticazione usando un account Microsoft account nel dispositivo |
| Allow Sync My Settings |Consente agli utenti di eseguire il roaming delle impostazioni e dei dati delle app di Windows; la disabilitazione di questo criterio disabiliterà la sincronizzazione, nonché i backup su dispositivi mobili |

## <a name="group-policy-settings"></a>Impostazioni di Criteri di gruppo

Le impostazioni di Criteri di gruppo sono applicabili ai dispositivi Windows 10 aggiunti a un dominio di Active Directory. La tabella include anche le impostazioni legacy che apparentemente gestiscono le impostazioni di sincronizzazione ma non funzionano per il servizio Enterprise State Roaming per Windows 10, che sono contrassegnate con "Non usare" nella descrizione.

Queste impostazioni si trovano nel percorso `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nome | Descrizione |
| --- | --- |
| Account: blocca gli account Microsoft |Questa impostazione dei criteri impedisce agli utenti di aggiungere nuovi account Microsoft nel computer |
| Non sincronizzare |Impedisce agli utenti di eseguire il roaming delle impostazioni e dei dati delle app di Windows |
| Non sincronizzare gruppo personalizza |Disabilita la sincronizzazione del gruppo Temi |
| Non sincronizzare impostazioni browser |Disabilita la sincronizzazione del gruppo Internet Explorer |
| Non sincronizzare password |Disabilita la sincronizzazione del gruppo Password |
| Non sincronizzare altre impostazioni di Windows |Disabilita la sincronizzazione del gruppo Altre impostazioni di Windows |
| Non sincronizzare personalizzazione desktop |Non usare questa impostazione perché non ha alcun effetto |
| Non sincronizzare connessioni a consumo |Disabilita il roaming nelle connessioni a consumo come le connessioni 3G per i cellulari |
| Non sincronizzare le app |Non usare questa impostazione perché non ha alcun effetto |
| Non sincronizzare impostazioni app |Disabilita il roaming dei dati delle app |
| Non sincronizzare impostazioni Start |Non usare questa impostazione perché non ha alcun effetto |

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica, vedere [Panoramica di Enterprise state roaming](enterprise-state-roaming-overview.md).

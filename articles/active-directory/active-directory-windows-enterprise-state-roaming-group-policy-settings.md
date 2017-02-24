
---
title: Impostazioni di Criteri di gruppo e MDM | Documentazione Microsoft
description: "Fornisce informazioni sulle impostazioni di Criteri di gruppo e di gestione di dispositivi mobili che dovrebbero essere usate in dispositivi di proprietà aziendale. Questi criteri vengono applicati all&quot;intero dispositivo dell&quot;utente."
services: active-directory
keywords: informazioni sulle impostazioni di Criteri di gruppo e gestione di dispositivi mobili per Enterprise State Roaming, Enterprise State Roaming, cloud windows
documentationcenter: 
author: markvi
manager: femila
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6bdec7bf35bb4a233200def36c0e697baaf15fec
ms.openlocfilehash: 5ca48cf3cc4b2f4d86463e36c73de47e386cff25


---
# <a name="group-policy-and-mdm-settings"></a>Impostazioni di Criteri di gruppo e di gestione di dispositivi mobili
Usare queste impostazioni di Criteri di gruppo e di gestione di dispositivi mobili solo nei dispositivi di proprietà aziendale, perché questi criteri vengono applicati all'intero dispositivo dell'utente. L'applicazione di un criterio di gestione di dispositivi mobili per disabilitare la sincronizzazione delle impostazioni per un dispositivo personale di proprietà dell'utente avrà un impatto negativo sull'uso del dispositivo. È anche possibile che altri account utente nel dispositivo siano influenzati dal criterio.

Le aziende che vogliono gestire il roaming per dispositivi personali (non gestiti) possono usare il portale di Azure per abilitare o disabilitare il roaming, invece di usare Criteri di gruppo o gestione di dispositivi mobili.
Le tabelle seguenti illustrano le impostazioni disponibili per i criteri.

## <a name="mdm-settings"></a>Impostazioni di gestione di dispositivi mobili
Le impostazioni dei criteri di gestione di dispositivi mobili sono applicabili a Windows 10 e Windows 10 Mobile.  Il supporto per Windows 10 Mobile esiste solo per il roaming basato su account Microsoft tramite l’account OneDrive dell’utente.  Fare riferimento alla sezione "Dispositivi ed endpoint" per informazioni dettagliate sui dispositivi sono supportati per la sincronizzazione basata su Azure AD.

| Nome | Descrizione |
| --- | --- |
| Allow Microsoft Account Connection |Consente agli utenti di effettuare l'autenticazione usando un account Microsoft account nel dispositivo |
| Allow Sync My Settings |Consente agli utenti di eseguire il roaming delle impostazioni e dei dati delle app di Windows; la disabilitazione di questo criterio disabiliterà la sincronizzazione, nonché i backup su dispositivi mobili |

## <a name="group-policy-settings"></a>Impostazioni di Criteri di gruppo
Le impostazioni di Criteri di gruppo sono applicabili ai dispositivi Windows 10 aggiunti a un dominio di Active Directory. La tabella include anche le impostazioni legacy che apparentemente gestiscono le impostazioni di sincronizzazione ma non funzionano per il servizio Enterprise State Roaming per Windows 10, che sono contrassegnate con "Non usare" nella descrizione.

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

## <a name="related-topics"></a>Argomenti correlati
* [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Abilitare Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Risoluzione dei problemi](active-directory-windows-enterprise-state-roaming-troubleshooting.md)




<!--HONumber=Jan17_HO1-->



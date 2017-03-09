---
title: Risoluzione dei problemi di Enterprise State Roaming in Azure Active Directory | Documentazione Microsoft
description: Fornisce le risposte ad alcune possibili domande degli amministratori IT in merito alle impostazioni e alla sincronizzazione dei dati delle app.
services: active-directory
keywords: impostazioni di enterprise state roaming, cloud windows, domande frequenti su enterprise state roaming
documentationcenter: 
author: ningtan
manager: swadhwa
editor: 
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 58a583a761a400d8fa0488fbc4fbfec35ec62c41
ms.openlocfilehash: ebdf73ad8a7a2f2690a404676e0c81ee01e77357
ms.lasthandoff: 01/10/2017


---
#<a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Risoluzione dei problemi di Enterprise State Roaming in Azure Active Directory

Questo argomento descrive come risolvere e diagnosticare i problemi di Enterprise State Roaming e riporta un elenco di problemi noti.

## <a name="preliminary-steps-for-troubleshooting"></a>Operazioni preliminari per la risoluzione dei problemi 
Prima di iniziare la risoluzione dei problemi, verificare che l'utente e il dispositivo siano stati configurati correttamente e che siano soddisfatti tutti i requisiti di Enterprise State Roaming per il dispositivo e l'utente. 

1. Windows 10 con gli aggiornamenti più recenti e la versione 1511 (build del sistema operativo 10586) o versione successiva è installato nel dispositivo. 
2. Il dispositivo è aggiunto ad Azure AD o aggiunto a un dominio e registrato con Azure AD.
3. Nel portale di Azure Active Directory, **Enterprise State Roaming** è abilitato per la directory in **Configura** > **Dispositivi** > **Gli utenti possono sincronizzare le impostazioni e i dati delle app aziendali**. Sono selezionati tutti gli utenti oppure l'utente è abilitato per la sincronizzazione tramite l'opzione selezionata e incluso nel gruppo di sicurezza.
4. L'utente dispone di una sottoscrizione di Azure Active Directory Premium.  
5. Il dispositivo è stato riavviato e l'utente ha effettuato l'accesso dopo l'abilitazione di Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Informazioni da includere quando è necessaria assistenza
Se non è possibile risolvere il problema con le indicazioni fornite di seguito, è possibile contattare il personale del supporto tecnico. Al momento di contattarli, è consigliabile includere le informazioni seguenti:

- **Descrizione generale dell'errore**. Vengono visualizzati messaggi di errore? Se non è stato visualizzato alcun messaggio di errore, descrivere dettagliatamente il comportamento imprevisto riscontrato. Quali funzionalità sono abilitate per la sincronizzazione e l'utente cosa si aspetta di sincronizzare? La mancata sincronizzazione riguarda più funzionalità o è limitata a una sola?
- **Utenti interessati**: la sincronizzazione sta funzionando o non sta funzionando per uno o più utenti? Quanti sono i dispositivi interessati per utente? Nessuno viene sincronizzato o ne vengono sincronizzati alcuni e non altri?
- **Informazioni sull'utente**. Quale identità sta usando l'utente per accedere al dispositivo? In che modo l'utente esegue l'accesso al dispositivo? Fa parte di un gruppo di sicurezza selezionato a cui è consentita la sincronizzazione? 
- **Informazioni sul dispositivo**. Questo dispositivo è aggiunto ad Azure AD o a un dominio? Qual è la build del dispositivo? Quali sono gli aggiornamenti più recenti?
- **Data/ora/fuso orario**. Quali sono la data e ora precise in cui è stato visualizzato l'errore (includere il fuso orario)?
- Queste informazioni consentiranno di risolvere il problema nel più breve tempo possibile.

## <a name="troubleshooting-and-diagnosing-issues"></a>Diagnosi e risoluzione dei problemi
Questa sezione offre suggerimenti su come risolvere e diagnosticare i problemi relativi a Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Verificare la sincronizzazione e la pagina di impostazioni "Sincronizza le impostazioni" 

1. Dopo l'aggiunta del PC Windows 10 a un dominio che sia configurato per consentire Enterprise State Roaming, effettuare l'accesso con l'account aziendale. Passare a **Impostazioni** > **Account** > **Sincronizza le impostazioni** e verificare che la sincronizzazione e le singole impostazioni siano attivate e che la parte superiore della pagina delle impostazioni indichi che si sta eseguendo la sincronizzazione con l'account aziendale. Verificare che lo stesso account sia utilizzato anche come account di accesso in **Impostazioni** > **Account** > **Le tue info**. 
2. Verificare che la sincronizzazione possa operare tra più macchine apportando alcune modifiche nel computer originale, ad esempio spostando la barra delle applicazioni sul lato destro o superiore della schermata. Osservare la modifica che viene propagata al secondo computer entro 5 minuti. 
 - A volte bloccare e sbloccare lo schermo (WIN + L) può aiutare ad avviare la sincronizzazione.
 - Perché la sincronizzazione funzioni, è necessario usare lo stesso account di accesso su entrambi i computer, in quanto Enterprise State Roaming è associato all'account utente e non all'account del computer.

**Potenziale problema**: la pagina delle impostazioni ha i controlli in grigio e invece di vedere un account si vede il testo "Alcune funzionalità di Windows sono disponibili solo se si utilizza un account Microsoft o un account aziendale". Questo problema può verificarsi per i dispositivi che sono stati configurati per essere aggiunti a un dominio e registrati in Azure AD, ma il dispositivo non è stato autenticato con successo in Azure AD. Una possibile causa è data dal fatto che i devono essere applicati i criteri del dispositivo, ma questa applicazione viene eseguita in modo asincrono e potrebbe avere un ritardo di alcune ore. Per verificare il problema, eseguire i passaggi per verificare lo stato di registrazione del dispositivo.

### <a name="verify-the-device-registration-status"></a>Verificare lo stato di registrazione del dispositivo
Enterprise State Roaming richiede che il dispositivo sia registrato con Azure AD. Sebbene non siano specifiche per Enterprise State Roaming, con le istruzioni riportate di seguito è possibile verificare che il client Windows 10 sia registrato, nonché confermare l'identificazione personale, l'URL delle impostazioni di Azure AD, lo stato NGC e altre informazioni.

1.    Aprire il prompt dei comandi con privilegi non elevati. Per fare questo in Windows, aprire la finestra Esegui (WIN+R) e digitare "cmd".
2.    Dopo aver aperto il prompt dei comandi, digitare "*dsregcmd.exe /status*".
3.    Per ottenere l'output previsto, il valore del campo **AzureAdJoined** deve essere "YES", il valore del campo **WamDefaultSet** deve essere "YES" e il valore del campo **WamDefaultGUID** deve essere un GUID con "(AzureAd)" alla fine.

**Potenziale problema**: **WamDefaultSet** e **AzureAdJoined** presentano entrambi "NO" come valore del campo, il dispositivo è stato aggiunto al dominio e registrato con Azure AD e il dispositivo non viene sincronizzato. Se viene visualizzato questo, il dispositivo potrebbe dover attendere che i criteri siano applicati oppure l'autenticazione del dispositivo non è riuscita durante la connessione ad Azure AD. L'utente potrebbe dover attendere alcune ore per l'applicazione dei criteri. Altri passaggi di risoluzione dei problemi potrebbero includere un nuovo tentativo di registrazione automatica mediante disconnessione e riconnessione o l'avvio dell'attività nell'Utilità di pianificazione. In alcuni casi eseguendo "*dsregcmd.exe /leave*" in una finestra del prompt dei comandi con privilegi elevati, riavviando e ripetendo il tentativo di registrazione si può risolvere il problema.


**Potenziale problema**: il campo **AzureAdSettingsUrl** è vuoto e il dispositivo non viene sincronizzato. L'utente potrebbe aver eseguito l'ultimo accesso al dispositivo prima dell'abilitazione di Enterprise State Roaming nel portale di Azure Active Directory. Nel portale provare a fare in modo che l'amministratore IT disattivi e riattivi Gli utenti possono sincronizzare le impostazioni e i dati delle app aziendali. Una volta riattivata l'opzione, riavviare il dispositivo e fare in modo che l'utente esegua l'accesso. 

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming e Multi-Factor Authentication 
In determinate condizioni, l'Enterprise State Roaming potrebbe non riuscire a sincronizzare i dati se è configurata la funzione Azure Multi-Factor Authentication. Per altre informazioni su questi problemi, vedere il documento di supporto [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potenziale problema**: se nel portale di Azure Active Directory il dispositivo è configurato per richiedere la Multi-Factor Authentication, potrebbe non essere possibile sincronizzare le impostazioni durante l'accesso tramite password a un dispositivo Windows 10. Questo tipo di configurazione Multi-Factor Authentication è pensata per proteggere gli account di amministratore di Azure. Gli utenti amministratori potrebbero riuscire comunque a sincronizzare l'accesso ai dispositivi Windows 10 tramite il PIN Microsoft Passport for Work o eseguendo la Multi-Factor Authentication durante l'accesso ad altri servizi Azure, come Office 365.

**Potenziale problema**: la sincronizzazione può non riuscire se l'amministratore configura in Active Directory Federation Services il criterio di accesso condizionale Multi-Factor Authentication e il token di accesso al dispositivo scade. Per accedere e disconnettersi, usare sempre il PIN Microsoft Passport for Work oppure eseguire l'autenticazione Multi-Factor Authentication quando si accede ad altri servizi Azure come Office 365.

###<a name="event-viewer"></a>Aprire il Visualizzatore eventi
Per procedure di risoluzione dei problemi avanzate, è possibile usare il Visualizzatore eventi per trovare errori specifici. Questi sono descritti nella tabella seguente. Gli eventi sono disponibili in Visualizzatore eventi > Registri applicazioni e servizi > **Microsoft** > **Windows** > **SettingSync** e quelli riguardanti problemi di sincronizzazione relativi all'identità in **Microsoft** > **Windows** > **Azure AD**.


## <a name="known-issues"></a>Problemi noti

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>La sincronizzazione non funziona sui dispositivi che hanno app caricate in sideload con software MDM

Influisce sui dispositivi che eseguono Windows 10 Anniversary Update (versione 1607). Nel Visualizzatore eventi, nei registri SettingSync-Azure, è presente di frequente l'ID evento 6013 con l'errore 80070259.

**Azione consigliata**  
Assicurarsi che il client Windows 10 v1607 disponga dell'aggiornamento cumulativo del 23 agosto 2016 (build [KB3176934](https://support.microsoft.com/kb/3176934) del sistema operativo 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>I preferiti di Internet Explorer non vengono sincronizzati

Influisce sui dispositivi che eseguono l'aggiornamento di Windows 10 di novembre (versione 1511).

**Azione consigliata**  
Assicurarsi che il client Windows 10 v1511 disponga dell'aggiornamento cumulativo di luglio 2016 (build [KB3172985](https://support.microsoft.com/kb/3172985) del sistema operativo 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Il tema non viene sincronizzato e neppure i dati protetti con Windows Information Protection 

Per impedire la perdita di dati, i dati protetti con [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) non vengono sincronizzati tramite Enterprise State Roaming per i dispositivi con Windows 10 Anniversary Update.



**Azione consigliata**  
Nessuna. È possibile che questo problema venga risolto da aggiornamenti futuri di Windows.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Le impostazioni di data, ora e area non vengono sincronizzate in un dispositivo aggiunto a un dominio 
  
Nei dispositivi aggiunti a un dominio non verranno sincronizzati la data, l'ora e l'ora automatica dell'area. L'uso dell'ora automatica potrebbe sovrascrivere le altre impostazioni di data, ora e area e causare la mancata sincronizzazione di queste impostazioni. 

**Azione consigliata**  
Nessuna. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Richieste di Controllo dell'account utente durante la sincronizzazione delle password

Influisce sui dispositivi che eseguono l'aggiornamento di Windows 10 di novembre (versione 1511) con una scheda di rete wireless configurata per sincronizzare le password.

**Azione consigliata**  
Assicurarsi che il client Windows 10 v1511 disponga dell'aggiornamento cumulativo (build [KB3140743](https://support.microsoft.com/kb/3140743) del sistema operativo 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>La sincronizzazione non funziona sui dispositivi che usano smart card per l'accesso
Se si tenta di accedere al dispositivo Windows utilizzando una smart card, anche virtuale, la sincronizzazione delle impostazioni cesserà di funzionare.     

**Azione consigliata**  
Nessuna. È possibile che questo problema venga risolto da aggiornamenti futuri di Windows.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Un dispositivo aggiunto a un dominio non viene sincronizzato dopo che è uscito dalla rete aziendale     
I dispositivi aggiunti a un dominio e registrati in Azure AD possono generare un errore di sincronizzazione se il dispositivo è fuori sede per lunghi periodi di tempo e non è possibile completare l'autenticazione nel dominio.

**Azione consigliata**  
Connettere il dispositivo a una rete aziendale in modo da riprendere la sincronizzazione.

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID evento 6065: 80070533 L'utente non può eseguire l'accesso perché questo account è attualmente disabilitato    
Nel Visualizzatore eventi, nei registri SettingSync/Debug, questo errore può verificarsi quando il tenant non ha ottenuto il provisioning automatico di AzureRMS. 

**Azione consigliata**  
Procedere con i passaggi elencati in [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID evento 1098: Errore: 0xCAA5001C Operazione broker del token non riuscita    
Nel Visualizzatore eventi, nei registri AAD/Operational, questo errore può essere presente con Evento 1104: chiamata del plug-in AAD Cloud Il token Get ha restituito l'errore: 0xC000005F. Questo problema si verifica se mancano autorizzazioni o attributi di proprietà.     

**Azione consigliata**  
Procedere con i passaggi elencati in [KB3196528](https://support.microsoft.com/kb/3196528).  



## <a name="next-steps"></a>Passaggi successivi

- Usare il forum di [suggerimenti degli utenti](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) per fornire commenti e suggerimenti su come migliorare Enterprise State Roaming.

- Per altre informazioni, vedere [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md). 

## <a name="related-topics"></a>Argomenti correlati
* [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Abilitare Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)


<properties
	pageTitle="Impostazioni di Criteri di gruppo e di gestione di dispositivi mobili | Microsoft Azure"
	description="Fornisce informazioni sulle impostazioni di Criteri di gruppo e di gestione di dispositivi mobili che dovrebbero essere usate in dispositivi di proprietà aziendale. Questi criteri vengono applicati all'intero dispositivo dell'utente."
	services="active-directory"
    keywords="informazioni sulle impostazioni di Criteri di gruppo e gestione di dispositivi mobili per Enterprise State Roaming, Enterprise State Roaming, cloud windows"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Impostazioni di Criteri di gruppo e di gestione di dispositivi mobili

Usare queste impostazioni di Criteri di gruppo e di gestione di dispositivi mobili solo nei dispositivi di proprietà aziendale, perché questi criteri vengono applicati all'intero dispositivo dell'utente. L'applicazione di un criterio di gestione di dispositivi mobili per disabilitare la sincronizzazione delle impostazioni per un dispositivo personale di proprietà dell'utente avrà un impatto negativo sull'uso del dispositivo. È anche possibile che altri account utente nel dispositivo siano influenzati dal criterio.

Le aziende che vogliono gestire il roaming per dispositivi personali (non gestiti) possono usare il portale di Azure per abilitare o disabilitare il roaming, invece di usare Criteri di gruppo o gestione di dispositivi mobili. Le tabelle seguenti illustrano le impostazioni disponibili per i criteri.

## Impostazioni di gestione di dispositivi mobili
Le impostazioni dei criteri di gestione di dispositivi mobili sono applicabili a Windows 10 e Windows 10 Mobile.

| Nome | Descrizione |
|------------------------------------|----------------------------------------------------------------------|
| Allow Microsoft Account Connection | Consente agli utenti di effettuare l'autenticazione usando un account Microsoft account nel dispositivo |
| Allow Sync My Settings | Consente agli utenti di eseguire il roaming delle impostazioni e dei dati delle app di Windows |
 
## Impostazioni di Criteri di gruppo
Le impostazioni di Criteri di gruppo sono applicabili ai dispositivi Windows 10 aggiunti a un dominio di Active Directory. La tabella include le impostazioni legacy che apparentemente gestiscono le impostazioni di sincronizzazione ma non funzionano per il servizio Enterprise State Roaming per Windows 10.

| Nome | Descrizione |
|-------------------------------------|-------------|
| Account: blocca gli account Microsoft |Questa impostazione dei criteri impedisce agli utenti di aggiungere nuovi account Microsoft nel computer|
| Non sincronizzare |Consente agli utenti di eseguire il roaming delle impostazioni e dei dati delle app di Windows|
| Non sincronizzare gruppo personalizza |Disabilita la sincronizzazione del gruppo Temi|
| Non sincronizzare impostazioni browser |Disabilita la sincronizzazione del gruppo Internet Explorer|
| Non sincronizzare password |Disabilita la sincronizzazione del gruppo Password|
| Non sincronizzare altre impostazioni di Windows |Disabilita la sincronizzazione del gruppo Altre impostazioni di Windows|
| Non sincronizzare personalizzazione desktop |Non usare questa impostazione perché non ha alcun effetto|
| Non sincronizzare connessioni a consumo |Disabilita il roaming nelle connessioni a consumo come le connessioni 3G per i cellulari|
| Non sincronizzare le app |Non usare questa impostazione perché non ha alcun effetto|
|Non sincronizzare impostazioni app |Disabilita il roaming dei dati delle app|
|Non sincronizzare impostazioni Start |Non usare questa impostazione perché non ha alcun effetto|


## Argomenti correlati
- [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Abilitare Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->
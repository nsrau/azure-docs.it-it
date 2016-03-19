<properties
	pageTitle="Panoramica di Enterprise State Roaming | Microsoft Azure"
	description="Fornisce informazioni sulle impostazioni del servizio Enterprise State Roaming nei dispositivi Windows. Enterprise State Roaming offre agli utenti un'esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo."
	services="active-directory"
    keywords="informazioni su Enterprise State Roaming, sincronizzazione aziendale, cloud windows"
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

# Panoramica di Enterprise State Roaming

Con Windows 10 gli utenti di [Azure Active Directory (Azure AD)](active-directory-whatis.md) ottengono la possibilità di sincronizzare in modo sicuro le proprie impostazioni utente e i dati delle impostazioni dell'applicazione sul cloud. Enterprise State Roaming offre agli utenti un'esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo. Enterprise State Roaming funziona in modo simile alla [sincronizzazione delle impostazioni degli utenti](http://windows.microsoft.com/it-IT/windows-8/sync-settings-pcs) standard introdotta per la prima volta in Windows 8. Il servizio Enterprise State Roaming offre anche i vantaggi seguenti:

- **Separazione di dati aziendali e dell'utente**: le organizzazioni mantengono i controlli dei propri dati e non esiste alcuna combinazione di dati aziendali in un account cloud di utenti o di dati utente in un account cloud aziendale. 
- **Sicurezza migliorata**: i dati vengono crittografati automaticamente prima di lasciare il dispositivo Windows 10 dell'utente mediante Azure Rights Management (Azure RMS) e i dati inattivi rimangono crittografati sul cloud. Tutto il contenuto inattivo rimane crittografato sul cloud, ad eccezione degli spazi dei nomi, ad esempio i nomi delle impostazioni e i nomi delle app Windows.  
- **Servizi di gestione e monitoraggio**: maggiore controllo e maggiore visibilità sugli utenti che sincronizzano le impostazioni nell'organizzazione e sui relativi dispositivi. 
- **Posizione geografica dei dati nel cloud**: i dati verranno archiviati in un'area di Azure in base al paese del dominio di Azure AD. 



| Articolo | Descrizione |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Abilitare Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) | Il servizio Enterprise State Roaming è disponibile per qualsiasi organizzazione con una sottoscrizione Premium di Azure Active Directory (Azure AD). Per altre informazioni su come ottenere una sottoscrizione di Azure AD, vedere la pagina del prodotto Azure AD. |
| [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md) | Questo argomento fornisce le risposte ad alcune possibili domande degli amministratori IT in merito alle impostazioni e alla sincronizzazione dei dati delle app. |
| [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) | Windows 10 offre impostazioni relative a Criteri di gruppo e ai criteri di gestione di dispositivi mobili per limitare la sincronizzazione delle impostazioni. |
| [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) | Di seguito è riportato un elenco completo di tutte le impostazioni di cui verrà effettuato il roaming e/o il backup in Windows 10. |

<!---HONumber=AcomDC_0204_2016-->
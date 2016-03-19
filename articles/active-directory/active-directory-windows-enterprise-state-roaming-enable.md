<properties
    pageTitle="Abilitare Enterprise State Roaming in Azure Active Directory | Microsoft Azure"
    description="Domande frequenti sulle impostazioni del servizio Enterprise State Roaming nei dispositivi Windows. Enterprise State Roaming offre agli utenti un'esperienza unificata in tutti i dispositivi Windows e riduce il tempo necessario per la configurazione di un nuovo dispositivo."
    services="active-directory"
    keywords="enterprise state roaming, cloud windows, come abilitare il servizio enterprise state roaming"
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

 

# Abilitare Enterprise State Roaming in Azure Active Directory

Il servizio Enterprise State Roaming è disponibile per qualsiasi organizzazione con una sottoscrizione Premium di Azure Active Directory (Azure AD). Per altre informazioni su come ottenere una sottoscrizione di Azure AD, vedere la [pagina del prodotto Azure AD](https://azure.microsoft.com/services/active-directory).

Quando si abilita il servizio Enterprise State Roaming, all'organizzazione verranno concesse automaticamente licenze per una sottoscrizione gratuita di Azure Rights Management. Questa sottoscrizione gratuita è limitata solo alla crittografia e decrittografia dei dati delle impostazioni aziendali. Per usare le funzionalità complete di Azure Rights Management, è necessario avere una sottoscrizione a pagamento.

Dopo avere ottenuto una sottoscrizione di Azure AD, seguire questa procedura per abilitare il servizio Enterprise State Roaming:
 
1. Accedere al portale di Azure classico. 
2. Nella parte sinistra selezionare **ACTIVE DIRECTORY**, quindi selezionare la directory per cui si vuole abilitare il servizio Enterprise State Roaming. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)	 
3. Passare alla scheda **Configura** nella parte superiore. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4.	Scorrere verso il basso e selezionare **Gli utenti possono sincronizzare le impostazioni e i dati delle app aziendali**, quindi fare clic su **Salva**. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Per consentire a un dispositivo Windows 10 di eseguire il roaming delle impostazioni con il servizio Enterprise State Roaming, è necessario che il dispositivo effettui l'autenticazione con un'identità di Azure AD. Per i dispositivi aggiunti ad Azure AD, l'accesso primario dell'utente è l'identità di Azure AD, quindi non sono necessarie operazioni aggiuntive di configurazione. Per i dispositivi che usano un'istanza locale tradizionale di Active Directory, l'amministratore IT deve connettere l'istanza locale di Active Directory ad Azure AD usando [Azure AD Connect](active-directory-aadconnect.md) e deve quindi configurare Criteri di gruppo per imporre ai dispositivi client la sincronizzazione automatica dei dati utente con Azure.

## Archiviazione dei dati di sincronizzazione
I dati del servizio Enterprise State Roaming sono ospitati in una o più [aree di Azure](https://azure.microsoft.com/regions/) ottimali per il valore relativo al paese impostato nell'istanza di Azure AD. Ad esempio, i clienti il cui valore di paese è impostato su "Francia" verranno ospitati in una o più aree di Azure in Europa, mentre i dati dei clienti che impostano il proprio valore relativo al paese in Azure AD su "USA" verranno ospitati in una o più aree di Azure negli Stati Uniti. Il valore relativo al paese viene impostato come parte del processo di creazione del dominio Azure AD e non può essere modificato successivamente.

Se sono necessarie altre informazioni sulla posizione di archiviazione dei dati, inviare un ticket al [supporto tecnico Azure](https://azure.microsoft.com/support/options/).

## Gestire il servizio Enterprise State Roaming
Gli amministratori del tenant di Azure AD possono abilitare e disabilitare il servizio Enterprise State Roaming nel portale di Azure classico. ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

Gli amministratori del tenant possono anche visualizzare un report relativo allo stato della sincronizzazione per ogni utente e possono limitare la sincronizzazione delle impostazioni a gruppi di sicurezza specifici.

##Conservazione dei dati
I dati sincronizzati in Azure tramite il servizio Enterprise State Roaming verranno mantenuti per un periodo illimitato, a meno che non venga eseguita un'operazione di eliminazione manuale o che si determini che i dati specifici sono obsoleti.
 
- **Eliminazione manuale dei dati**: se l'amministratore di Azure AD vuole eliminare manualmente i dati delle impostazioni, può inviare un ticket al [supporto tecnico Azure](https://azure.microsoft.com/support/options/).
 
 - **Eliminazione dell'utente**: quando un utente viene eliminato in Azure AD, lo stato dell'account utente sarà Disabilitato per 30 giorni. Dopo 30 giorni l'account verrà eliminato e i dati delle impostazioni associati saranno sottoposti a eliminazione.
 - **Eliminazione del tenant (directory)**: l'eliminazione di un'intera directory in Azure AD è un'operazione immediata. Tutti i dati delle impostazioni associati alla directory saranno sottoposti a eliminazione. 
 - **Eliminazione delle impostazioni**: se l'amministratore di Azure AD vuole eliminare immediatamente i dati delle impostazioni di un utente specifico, può inviare un ticket al supporto tecnico Azure. 
- **Dati obsoleti**: i dati a cui non è stato effettuato l'accesso per un anno ("periodo di conservazione") verranno considerati obsoleti e potranno essere eliminati da Azure. I dati obsoleti possono essere un set specifico di impostazioni di Windows/dell'applicazione o tutte le impostazioni per un utente. Ad esempio: 
 - Se nessun dispositivo accede a una raccolta specifica di impostazioni, ad esempio se un'applicazione viene rimossa dal dispositivo o un gruppo di impostazioni come "Tema" viene disabilitato per tutti i dispositivi di un utente, la raccolta diventerà obsoleta dopo il periodo di conservazione e potrà essere eliminata. 
 - Se un utente ha disattivato la sincronizzazione delle impostazioni in tutti i propri dispositivi, non verrà effettuato l'accesso ad alcun dato delle impostazioni e tutti i dati delle impostazioni per tale utente diventeranno obsoleti e potranno essere eliminati allo scadere del periodo di conservazione. 
 - Se l'amministratore della directory di Azure AD disattiva il servizio Enterprise State Roaming per l'intera directory, tutti gli utenti in tale directory non sincronizzeranno più le impostazioni e tutti i dati delle impostazioni per tutti gli utenti diventeranno obsoleti e potranno essere eliminati allo scadere del periodo di conservazione. 

- **Recupero dei dati eliminati**: il criterio relativo alla conservazione dei dati non può essere configurato. Dopo l'eliminazione definitiva, non sarà possibile recuperare i dati. È tuttavia importante notare che i dati delle impostazioni verranno eliminati solo da Azure, non dal dispositivo dell'utente finale. Se un dispositivo si riconnette successivamente al servizio Enterprise State Roaming, le impostazioni verranno sincronizzate e archiviate di nuovo in Azure.

## Argomenti correlati
- [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Riferimento alle impostazioni di roaming di Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->
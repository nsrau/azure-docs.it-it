<properties
   pageTitle="Eventi del report di controllo di Azure Active Directory | Microsoft Azure"
   description="Eventi controllati disponibili per la visualizzazione e il download dalla propria istanza di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="kenhoff"/>

# Eventi del report di controllo di Azure Active Directory
Il report di controllo di Azure Active Directory consente ai clienti di identificare le azioni con privilegi che si sono verificate nella propria istanza di Azure Active Directory. Le azioni con privilegi includono modifiche di elevazione dei privilegi, ad esempio la creazione dei ruoli o le reimpostazioni delle password, la modifica delle configurazioni dei criteri, ad esempio i criteri delle password o le modifiche alla configurazione della directory, ad esempio le modifiche alle impostazioni di federazione del dominio. Nei report è incluso il record di controllo per il nome dell'evento, l'attore che ha eseguito l'azione, la risorsa di destinazione interessata dalla modifica e la data e l'ora (UTC). I clienti possono recuperare l'elenco degli eventi di controllo per la propria istanza di Azure Active Directory tramite il [portale di gestione di Azure](https://manage.windowsazure.com/), come descritto nell'articolo relativo alla [visualizzazione dei report di utilizzo e di accesso](active-directory-view-access-usage-reports.md).

## Conservazione dei report di controllo
Gli eventi nel report di controllo di Azure Active Directory vengono conservati per 180 giorni. Per altre informazioni sulla conservazione dei report, vedere la pagina relativa ai [criteri di conservazione dei report di Azure Active Directory](active-directory-reporting-retention.md).

Per i clienti interessati alla conservazione gli eventi di controllo per periodi più lunghi, l'API di creazione report consente di eseguire regolarmente il pull degli eventi di controllo in un archivio dati separato. Per i dettagli, vedere la pagina relativa all'[introduzione all'API di creazione report](active-directory-reporting-api-getting-started.md).

## Proprietà incluse in ogni evento di controllo

Proprietà | Descrizione
------------- | --------------------------------------------------------------
Data e ora | Data e ora in cui si è verificato l'evento
Attore | Utente o entità servizio che ha eseguito l'azione
Azione | Azione eseguita
Destinazione | Utente o entità servizio su cui è stata eseguita l'azione

## Elenco degli eventi del report di controllo
<!--- audit event descriptions should be in the past tense --->

Eventi | Descrizione evento
------------------------------------ | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Eventi degli utenti** |
Aggiunta di un utente | È stato aggiunto un utente alla directory.
Eliminazione di un utente. | È stato eliminato un utente dalla directory.
Impostazione delle proprietà della licenza | Sono state impostate le proprietà della licenza per un utente nella directory.
Reimpostazione password utente | È stata reimpostata la password per un utente nella directory.
Modifica password utente | È stata modificata la password per un utente nella directory.
Modifica licenza utente | È stata modificata la licenza assegnata a un utente nella directory.
Aggiornamento utente | È stato aggiornato un utente nella directory.
Impostazione forzatura per la modifica delle password utente | È stata impostata la proprietà che obbliga un utente a modificare la password all'accesso.
**Eventi del gruppo** |
Creare un gruppo | È stato creato un gruppo nella directory.
Aggiornare un gruppo | È stato aggiornato un gruppo nella directory
Eliminare gruppo | È stato eliminato un gruppo dalla directory.
Aggiungere un membro al gruppo | È stato aggiunto un membro a un gruppo nella directory.
Rimuovere un membro dal gruppo | È stato rimosso un membro da un gruppo nella directory.
**Eventi dell'applicazione** |
Aggiunta di un'entità servizio | È stata aggiunta un'entità servizio alla directory.
Rimozione di un'entità servizio | È stata rimossa un'entità servizio dalla directory.
Aggiunta delle credenziali dell'entità servizio | Sono state aggiunte le credenziali a un'entità servizio.
Rimozione delle credenziali dell'entità servizio | Sono state rimosse le credenziali da un'entità servizio.
Aggiunta di una voce di delega | È stata aggiunta una voce di delega alla directory.
Impostazione voce di delega | È stata aggiornata una voce di delega nella directory.
Rimozione voce di delega | È stata rimossa una voce di delega dalla directory.
**Eventi del ruolo** |
Aggiunta di un membro del ruolo al ruolo | È stato aggiunto un utente a un ruolo della directory.
Rimozione di un membro ruolo dal ruolo | È stato rimosso un utente da un ruolo della directory.
Impostazione delle informazioni di contatto della società | Sono state impostate le preferenze di contatto a livello aziendale. Sono inclusi gli indirizzi di posta elettronica per le attività di marketing, oltre alle notifiche tecniche relative ai Microsoft Online Services.
**Eventi della directory** |
Aggiunta di un partner alla società | È stato aggiunto un partner alla directory.
Rimozione di un partner dalla società | È stato rimosso un partner dalla directory.
Aggiunta di un dominio alla società | È stato aggiunto un dominio alla directory.
Rimozione di un dominio dalla società | È stato rimosso un dominio dalla directory.
Aggiornamento dominio | È stato aggiornato un dominio nella directory.
Impostazione dell'autenticazione del dominio | È stata modificata l'impostazione del dominio predefinita per la società.
Configurazione delle impostazioni di federazione nel dominio | Sono state aggiornate le impostazioni di federazione per un dominio.
Verifica dominio | È stato verificato un dominio nella directory.
Verifica del dominio tramite la verifica di posta elettronica | È stato verificato un dominio nella directory tramite la verifica di posta elettronica.
Impostazione del flag DirSyncEnabled per la società | È stata impostata la proprietà che abilita una directory per Azure AD Sync.
Impostazione criteri password | Sono stati impostati vincoli di lunghezza e caratteri per le password utente.
Impostazione informazioni società | Sono state aggiornate le impostazioni a livello aziendale. Per altre informazioni, vedere il cmdlet di PowerShell [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx).

<!---

List of events that still need descriptions:

Restore Application
Set String Auth Policy
Promote tenant to partner

--->

### Attributi utente inclusi nell'evento di controllo di aggiornamento utente
L'evento di controllo di aggiornamento dell'utente include informazioni aggiuntive sugli attributi utente che sono stati aggiornati. Per ogni attributo, sono inclusi sia il valore precedente che quello nuovo.

Attributo | Descrizione
------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | L'utente è autorizzato ad accedere.
AssignedLicense | Tutte le licenze assegnate all'utente.
AssignedPlan | Piani di servizio risultanti dalle licenze assegnate all'utente.
LicenseAssignmentDetail | Dettagli sulle licenze assegnate all'utente. Ad esempio, in caso di concessione di licenze basata su gruppo, sarebbe incluso il gruppo che ha concesso la licenza.
Mobile | Telefono cellulare dell'utente.
OtherMail | Indirizzo di posta elettronica alternativo dell'utente.
OtherMobile | Telefono cellulare alternativo dell'utente.
StrongAuthenticationMethod | Elenco di metodi di verifica configurato dall'utente per l'autenticazione a più fattori, ad esempio chiamata vocale, SMS o codice di verifica da un'app per dispositivi mobili.
StrongAuthenticationRequirement | Se l'autenticazione a più fattori è imposta, abilitata o disabilitata per questo utente.
StrongAuthenticationUserDetails | Numero di telefono dell'utente, numero di telefono alternativo e indirizzo di posta elettronica usati per la verifica dell'autenticazione a più fattori e della reimpostazione della password.
TelephoneNumber | Numero di telefono dell'utente.

I record di controllo rappresentano un controllo obbligatorio per molte normative per la conformità. Per i clienti che usano i report di controllo di Azure Active Directory per garantire la conformità alle normative, è consigliabile inviare una copia di questo argomento della guida insieme al report di controllo esportato del cliente per fornire una descrizione delle informazioni contenute nel report. Per altre informazioni sulle normative di conformità attualmente soddisfatte da Azure, suggerire al revisore di visitare la pagina relativa alla [conformità](http://azure.microsoft.com/support/trust-center/compliance/) del Centro protezione Microsoft Azure.

<!---HONumber=Sept15_HO4-->
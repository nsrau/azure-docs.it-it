<properties 
	pageTitle="Assegnazione dei ruoli di amministratore in Azure AD" 
	description="Argomento che illustra i ruoli di amministratore disponibili con Azure AD e come assegnarli." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Assegnazione dei ruoli di amministratore in Azure AD

A seconda delle dimensioni della società, potrebbe essere opportuno designare più amministratori per le diverse funzioni. Questi amministratori hanno accesso a diverse funzionalità nel portale di Azure e, a seconda del ruolo, possono, tra le altre cose, creare o modificare gli utenti, assegnare ruoli amministrativi ad altri, reimpostare le password utente, gestire le licenze utente e gestire i domini.

È importante comprendere che un utente a cui è assegnato un ruolo amministrativo disporrà delle stesse autorizzazioni in tutti i servizi cloud sottoscritti dall'organizzazione, indipendentemente dal fatto che il ruolo venga assegnato nel portale di Office 365 o nel portale di Azure oppure usando il modulo di Azure AD per Windows PowerShell.

Sono disponibili i ruoli di amministratore seguenti:

- **Amministratore fatturazione**: effettua acquisti, gestisce le sottoscrizioni, gestisce i ticket di supporto e monitora l'integrità del servizio.
- **Amministratore globale**: dispone di accesso a tutte le funzionalità amministrative. La persona che effettua l'iscrizione per l'account di Azure diventa amministratore globale. Solo gli amministratori globali possono assegnare altri ruoli di amministratore. In una società possono essere presenti più amministratori globali.
- **Amministratore password**: reimposta le password, gestisce le richieste di servizio e monitora l'integrità del servizio. Gli amministratori password possono reimpostare le password solo per gli utenti e gli altri amministratori password.
- **Amministratore del servizio**: gestisce le richieste di servizio e monitora l'integrità del servizio.
    > [AZURE.NOTE] 
- **Amministratore utenti**: reimposta le password, monitora l'integrità del servizio e gestisce gli account utente, i gruppi di utenti e le richieste di servizio. Alle autorizzazioni di un amministratore Gestione utenti si applicano alcune limitazioni. Ad esempio, non possono eliminare un amministratore globale o creare altri amministratori. Non possono inoltre reimpostare le password per gli amministratori fatturazione, globali e del servizio.

## Autorizzazioni degli amministratori

### Amministratore fatturazione

Operazione consentita | Operazione non consentita
------------- | -------------
<p>Visualizzare le informazioni della società e degli utenti</p><p>Gestire i ticket di supporto di Office</p><p>Eseguire attività di fatturazione e acquisto per i prodotti Office</p> | <p>Reimpostare le password utente</p><p>Creare e gestire viste utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente</p><p>Gestire i domini</p><p>Gestire le informazioni della società</p><p>Delegare ruoli amministrativi ad altri utenti</p><p>Usare la sincronizzazione della directory</p>

### Amministratore globale

Operazione consentita | Operazione non consentita
------------- | -------------
<p>Visualizzare le informazioni della società e degli utenti</p><p>Gestire i ticket di supporto di Office</p><p>Eseguire attività di fatturazione e acquisto per i prodotti Office</p> <p>Reimpostare le password utente</p><p>Creare e gestire viste utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente</p><p>Gestire i domini</p><p>Gestire le informazioni della società</p><p>Delegare ruoli amministrativi ad altri utenti</p><p>Usare la sincronizzazione della directory</p> | N/D

### Amministratore password

Operazione consentita | Operazione non consentita
------------- | -------------
<p>Visualizzare le informazioni della società e degli utenti</p><p>Gestire i ticket di supporto di Office</p><p>Reimpostare le password utente</p> | <p>Eseguire attività di fatturazione e acquisto per i prodotti Office</p><p>Creare e gestire viste utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente</p><p>Gestire i domini</p><p>Gestire le informazioni della società</p><p>Delegare ruoli amministrativi ad altri utenti</p><p>Usare la sincronizzazione della directory</p>

### Amministratore del servizio

Operazione consentita | Operazione non consentita
------------- | -------------
<p>Visualizzare le informazioni della società e degli utenti</p><p>Gestire i ticket di supporto di Office</p> | <p>Reimpostare le password utente</p><p>Eseguire attività di fatturazione e acquisto per i prodotti Office</p><p>Creare e gestire viste utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente</p><p>Gestire i domini</p><p>Gestire le informazioni della società</p><p>Delegare ruoli amministrativi ad altri utenti</p><p>Usare la sincronizzazione della directory</p>

### Amministratore utenti

Operazione consentita | Operazione non consentita
------------- | -------------
<p>Visualizzare le informazioni della società e degli utenti</p><p>Gestire i ticket di supporto di Office</p><p>Reimpostare le password utente, con alcune limitazioni. Può reimpostare le password per gli amministratori fatturazione, globali e del servizio.</p><p>Creare e gestire viste utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente, con alcune limitazioni. Non può eliminare un amministratore globale o creare altri amministratori.</p> | <p>Eseguire attività di fatturazione e acquisto per i prodotti Office</p><p>Gestire i domini</p><p>Gestire le informazioni della società</p><p>Delegare ruoli amministrativi ad altri utenti</p><p>Usare la sincronizzazione della directory</p>

## Dettagli sul ruolo di amministratore globale

L'amministratore globale ha accesso a tutte le funzionalità amministrative. Per impostazione predefinita, la persona che effettua l'iscrizione per una sottoscrizione di Azure riceve il ruolo di amministratore globale per la directory. Solo gli amministratori globali possono assegnare altri ruoli di amministratore.

## Assegnare o rimuovere ruoli di amministratore 


1. Nel portale di gestione fare clic su **Active Directory** e quindi sul nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic sul nome visualizzato dell'utente da modificare.
3. Selezionare il menu a discesa **Ruolo aziendale** e quindi scegliere il ruolo di amministratore da assegnare all'utente oppure selezionare **Utente** per rimuovere un ruolo di amministratore esistente. 
4. Nella casella **Indirizzo di posta elettronica alternativo** digitare un indirizzo di posta elettronica. Questo indirizzo viene usato per notifiche importanti, inclusa la reimpostazione self-service della password, pertanto l'utente deve poter accedere all'account di posta elettronica indipendentemente dal fatto di poter accedere o meno ad Azure.
5. Selezionare **Consenti** o **Blocca** per specificare se l'utente è autorizzato o meno a effettuare l'accesso e ad accedere ai servizi. 
6. Specificare una località nell'elenco a discesa **Località di utilizzo**.
7. Al termine, fare clic su **Salva**.

## Passaggi successivi

- [Gestire gli utenti](../active-directory-manage-users.md)
- [Gestire le password](active-directory-manage-passwords.md)
- [Gestire i gruppi](active-directory-manage-groups.md)


 

<!---HONumber=July15_HO3-->
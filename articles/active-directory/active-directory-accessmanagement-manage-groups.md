<properties

	pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage groups to manage Azure users using Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/10/2016"
	ms.author="curtand"/>  


# Gestione dei gruppi in Azure Active Directory

Una delle funzionalità di gestione utente di Azure Active Directory (Azure AD) è la possibilità di creare gruppi di utenti. I gruppi vengono usati per eseguire attività di gestione come l'assegnazione di licenze o autorizzazioni per diversi utenti contemporaneamente. È anche possibile usare i gruppi per assegnare l'autorizzazione di accesso a

- Risorse, ad esempio oggetti nella directory.
- Risorse esterne alla directory, ad esempio applicazioni SaaS, servizi di Azure, siti di SharePoint o risorse locali.

Il proprietario di una risorsa può anche assegnare l'accesso a una risorsa a un gruppo di Azure AD di proprietà di altri. Questa assegnazione concede l'accesso alla risorsa ai membri di tale gruppo. Il proprietario del gruppo gestisce quindi l'appartenenza al gruppo. Di fatto, il proprietario della risorsa delega al proprietario del gruppo l'autorizzazione ad assegnare gli utenti alla risorsa.

## Come si crea un gruppo?

A seconda dei servizi a cui l'organizzazione ha effettuato la sottoscrizione, è possibile creare un gruppo usando uno degli strumenti seguenti:
- portale di Azure classico
- portale dell'account di Office 365
- portale dell'account di Windows Intune

Questo articolo descrive l'esecuzione delle attività nel portale di Azure classico. Per altre informazioni sull'uso di portali non di Azure per gestire una directory di Azure AD, vedere [Amministrare la directory di Azure AD](active-directory-administer.md).

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi**.

3. Selezionare **Aggiungi gruppo**.

4. Nella finestra **Aggiungi gruppo** specificare il nome e la descrizione di un gruppo.


## Come aggiungere o rimuovere singoli utenti in un gruppo di sicurezza?

**Per aggiungere un singolo utente a un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi**.

3. Aprire il gruppo a cui si vogliono aggiungere membri. Se non è già visualizzata, aprire la scheda **Membri** del gruppo selezionato.

4. Selezionare **Aggiungi membri**.

5. Nella pagina **Aggiungi membri** selezionare il nome dell'utente o di un gruppo che si vuole aggiungere come membro del gruppo corrente. Verificare che questo nome venga aggiunto al riquadro **Selezionato**.


**Per rimuovere un singolo utente da un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi**.

3. Aprire il gruppo da cui si vogliono rimuovere i membri.

4. Selezionare la scheda **Membri** e quindi il nome del membro che si vuole rimuovere dal gruppo e infine fare clic su **Rimuovi**.

6. Al prompt confermare che si vuole rimuovere questo membro dal gruppo.


## Come è possibile gestire l'appartenenza di un gruppo in modo dinamico?

In Azure AD è molto facile configurare una regola semplice per determinare quali utenti devono essere membri del gruppo. Una regola semplice è una regola che esegue un solo confronto. Se un gruppo è assegnato a un'applicazione SaaS, ad esempio, è possibile configurare una regola per aggiungere gli utenti con la posizione di "Rappresentante". Questa regola concede l'accesso all'applicazione SaaS a tutti gli utenti con tale posizione nella directory.

> [AZURE.NOTE] È possibile configurare una regola per l'appartenenza dinamica nei gruppi di sicurezza o nei gruppi di Office 365. Le appartenenze a gruppi annidati non sono attualmente supportate per l'assegnazione alle applicazioni in base al gruppo.
>
> Le appartenenze dinamiche ai gruppi richiedono che venga assegnata una licenza Azure AD Premium a:
>
> - L'amministratore che gestisce la regola in un gruppo
> - Tutti i membri del gruppo

**Per abilitare l'appartenenza dinamica per un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi** e aprire il gruppo che si vuole modificare.

3. Selezionare la scheda **Configura** e quindi impostare **Abilita appartenenze dinamiche** su **Sì**.

4. Configurare una singola regola semplice per il gruppo per controllare il funzionamento dell'appartenenza dinamica per questo gruppo. Assicurarsi che l’opzione **Aggiungi utenti in** sia selezionata e quindi selezionare una proprietà utente dall’elenco, ad esempio department, jobTitle e così via.

5. Selezionare una condizione (Non uguale a, Uguale a, Non inizia con, Inizia con, Non contiene, Contiene, Non corrispondente, Corrispondente).

6. Specificare un valore di confronto per la proprietà utente selezionata.

Per informazioni su come creare regole *avanzate* (ovvero regole che possono contenere più confronti) per l'appartenenza dinamica ai gruppi, vedere [Uso di attributi per la creazione di regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Informazioni aggiuntive

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlets for configuring group settings](active-directory-accessmanagement-groups-settings-cmdlets.md) (Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo)

* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0817_2016-->
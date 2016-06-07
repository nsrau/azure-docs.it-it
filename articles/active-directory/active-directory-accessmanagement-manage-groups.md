<properties

	pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage groups to manage Azure users using Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/26/2016"
	ms.author="curtand"/>


# Gestione dei gruppi in Azure Active Directory

Una delle funzionalità di gestione utente di Azure Active Directory (Azure AD) è la possibilità di creare gruppi di utenti. Sarà quindi possibile usare un gruppo per assegnare le licenze a una classe di utenti. È anche possibile usare i gruppi per assegnare l'autorizzazione di accesso a

- Risorse, ad esempio oggetti nella directory.
- Risorse esterne alla directory, ad esempio le applicazioni SaaS, i servizi di Azure, i siti di SharePoint o le risorse locali.

Il proprietario di una risorsa può anche assegnare a un gruppo di Azure AD l'accesso a una risorsa, concedendo così ai membri di tale gruppo l'accesso alla risorsa. Il proprietario del gruppo gestisce quindi l'appartenenza al gruppo. Di fatto, il proprietario della risorsa delega al proprietario del gruppo l'autorizzazione ad assegnare gli utenti alla risorsa.

## Come si crea un gruppo?

Questa attività può essere completata usando il portale dell'account di Office 365, il portale dell'account Windows Intune o il portale di Azure classico, a seconda dei servizi sottoscritti dall'organizzazione. Per altre informazioni sull'uso di portali non di Azure per gestire Azure Active Directory, vedere [Amministrazione della directory di Azure AD](active-directory-administer.md).

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi**.

3. Selezionare **Aggiungi gruppo**.

4. Nella finestra **Aggiungi gruppo** specificare il nome e la descrizione di un gruppo.


## Come aggiungere o rimuovere singoli utenti in un gruppo di sicurezza?

**Per aggiungere un singolo utente a un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi**.

3. Aprire il gruppo a cui si vogliono aggiungere membri. Per impostazione predefinita, verrà visualizzata la scheda **Membri** del gruppo selezionato.

4. Selezionare **Aggiungi membri**.

5. Nella pagina **Aggiungi membri** selezionare il nome dell'utente o gruppo che si vuole aggiungere come membro del gruppo corrente e verificare che questo nome venga aggiunto al riquadro **Selezionato**.


**Per rimuovere un singolo utente da un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi**.

3. Aprire il gruppo da cui si vogliono rimuovere i membri.

4. Selezionare la scheda **Membri**, selezionare il nome del membro che si vuole rimuovere dal gruppo e quindi fare clic su **Rimuovi**.

6. Al prompt confermare che si vuole rimuovere questo membro dal gruppo.


## Come è possibile gestire l'appartenenza di un gruppo in modo dinamico?

In Azure AD è molto facile configurare una semplice regola (una regola che esegue un solo confronto) per determinare quali utenti devono essere membri del gruppo. Se, ad esempio, un gruppo viene assegnato a un'applicazione SaaS e si configura una regola per aggiungere gli utenti con la posizione di "Rappresentante", tutti gli utenti nella directory di Azure AD con tale posizione avranno accesso a questa applicazione SaaS.

> [AZURE.NOTE] È possibile configurare una regola per l'appartenenza dinamica nei gruppi di sicurezza o nei gruppi di Office 365. In questo momento, le appartenenze ai gruppi annidati non sono supportate per l'assegnazione alle applicazioni in base al gruppo.
>
> Le appartenenze dinamiche ai gruppi richiedono che venga assegnata una licenza Azure AD Premium a:
>
> - L'amministratore che gestisce la regola in un gruppo
> - Tutti gli utenti che vengono selezionati in base alla regola come membro del gruppo

**Per abilitare l'appartenenza dinamica per un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.

2. Selezionare la scheda **Gruppi** e aprire il gruppo che si vuole modificare.

3. Selezionare la scheda **Configura** e quindi impostare **Abilita appartenenze dinamiche** su **Sì**.

4. Configurare una regola semplice per il gruppo, che consenta di controllare il funzionamento dell'appartenenza dinamica per questo gruppo. Assicurarsi che l’opzione **Aggiungi utenti in** sia selezionata e quindi selezionare una proprietà utente dall’elenco, ad esempio department, jobTitle e così via.

5. Selezionare una condizione (Non uguale a, Uguale a, Non inizia con, Inizia con, Non contiene, Contiene, Non corrispondente, Corrispondente) e quindi specificare un valore per la proprietà utente selezionata.

Per informazioni su come creare regole *avanzate*, che possono contenere più confronti, per l'appartenenza dinamica a un gruppo, vedere [Uso di attributi per la creazione di regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Informazioni aggiuntive

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0601_2016-->
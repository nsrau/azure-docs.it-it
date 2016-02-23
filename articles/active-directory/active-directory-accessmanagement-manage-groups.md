<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
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
	ms.date="02/10/2016"
	ms.author="curtand"/>


#Gestione dei gruppi di sicurezza in Azure Active Directory

In Azure Active Directory (Azure AD) una delle caratteristiche principali è la possibilità di gestire l'accesso alle risorse. Queste risorse possono far parte della directory, come nel caso delle autorizzazioni per gestire oggetti tramite i ruoli nella directory, o risorse che sono esterne alla directory, come ad esempio le applicazioni SaaS, i servizi di Azure e i siti di SharePoint o in risorse locali. Un gruppo può essere assegnato a una risorsa dal proprietario della risorsa, così facendo, si garantisce ai membri di quel gruppo l'accesso alla risorsa. L’appartenenza al gruppo può quindi essere gestita dal proprietario del gruppo. Il proprietario della risorsa delega in modo efficace l'autorizzazione ad assegnare agli utenti la loro risorsa al proprietario del gruppo.


##Come creare e gestire un gruppo di sicurezza

**Per creare un gruppo nel portale di Azure**

1. Nel portale di Azure fare clic su **Active Directory** e poi sul nome della directory dell'organizzazione.
2. Fare clic sulla scheda **Gruppi**.
3. Nella pagina Gruppi fare clic su **Aggiungi gruppo**.
4. Nella finestra **Aggiungi gruppo** specificare il nome e la descrizione di un gruppo.
5. Questa attività può essere completata usando il portale dell'account di Office 365, il portale dell'account Windows Intune o il portale di Azure, a seconda dei servizi sottoscritti dall'organizzazione. Per altre informazioni sull'uso dei portali per gestire Azure Active Directory, vedere [Amministrazione della directory di Azure AD](active-directory-administer).

## Come assegnare o rimuovere utenti in un gruppo di sicurezza

**Per aggiungere un membro a un gruppo nel portale di Azure**

1. Nel portale di Azure fare clic su **Active Directory** e poi sul nome della directory dell'organizzazione.
2. Fare clic sulla scheda **Gruppi**.
3. Nella pagina **Gruppi** fare clic sul nome del gruppo a cui si desidera aggiungere membri. Per impostazione predefinita, verrà visualizzata la scheda **Membri** del gruppo selezionato.
4. Nella pagina del gruppo fare clic su**Aggiungi membri**.
5. Nella pagina **Aggiungi membri** fare clic sul nome dell'utente o gruppo che si desidera aggiungere come membro del gruppo corrente e verificare che questo nome venga aggiunto al riquadro Selezionato.


**Per rimuovere un membro da un gruppo nel portale di Azure**

1. Nel portale di Azure fare clic su **Active Directory** e poi sul nome della directory dell'organizzazione.
2. Fare clic sulla scheda **Gruppi**.
3. Nella pagina Gruppi fare clic sul nome del gruppo da cui si desidera rimuovere i membri.
4. Nella pagina del gruppo fare clic sulla scheda **Membri**.
5. Nella pagina del gruppo fare clic sul nome del membro che si desidera rimuovere dal gruppo e quindi fare clic su **Rimuovi**.
6. Verificare che si desidera rimuovere il membro dal gruppo facendo clic su **Sì** come risposta alla domanda di conferma dell'azione.


## Uso di una regola per gestire in modo dinamico i membri di un gruppo di sicurezza

**Per abilitare l'appartenenza dinamica per un determinato gruppo, effettuare le seguenti operazioni:**

1. Nel portale di Azure, nella scheda **Gruppi** selezionare il gruppo che si desidera modificare e quindi nella scheda **Configura** di questo gruppo impostare l'opzione **Abilita appartenenze dinamiche** su **Sì**.
2. A questo punto, è possibile impostare una regola semplice per il gruppo che consenta di controllare il funzionamento dell'appartenenza dinamica per questo gruppo. Assicurarsi che il pulsante di opzione **Aggiungi utenti in** sia selezionato e quindi selezionare una proprietà dal menu a discesa (ad esempio department, jobTitle e così via),
3. Selezionare una condizione (Non uguale a, Uguale a, Non inizia con, Inizia con, Non contiene, Contiene, Non corrispondente, Corrispondente) e quindi specificare un valore per la proprietà utente selezionata.

Ad esempio, se un gruppo viene assegnato a un'applicazione SaaS (per altre informazioni, vedere Assegnare a un gruppo l'accesso a un'applicazione SaaS in Azure AD) e si abilitano le appartenenze dinamiche per questo gruppo impostando una regola in base alla quale l'opzione Aggiungi utenti in viene impostata sul titolo mansione (jobTitle) uguale a (-eq) Rappresentante, tutti gli utenti all'interno della directory di Azure AD la cui mansione è impostata su Rappresentante avranno accesso all'applicazione SaaS.

## Informazioni aggiuntive

Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->
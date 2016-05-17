<properties
	pageTitle="Controllo degli accessi in base al ruolo di Azure Active Directory | Microsoft Azure"
	description="Introduzione alla gestione degli accessi con il Controllo degli accessi in base al ruolo di Azure nel portale di Azure. Usare le assegnazioni di ruolo per assegnare autorizzazioni nella directory."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/03/2016"
	ms.author="kgremban"/>

# Introduzione alla gestione degli accessi nel portale di Azure

Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il Controllo degli accessi in base al ruolo di Azure, è possibile separare compiti all'interno del team DevOps e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro. Questo articolo illustra le nozioni di base della gestione degli accessi, quindi consente di iniziare a usare subito il Controllo degli accessi in base al ruolo nel portale di Azure.

## Nozioni fondamentali della gestione degli accessi in Azure
Ogni sottoscrizione di Azure è associata a una directory di Azure Active Directory (AD). Gli utenti, i gruppi e le applicazioni da tale directory possono gestire le risorse nella sottoscrizione di Azure. Questi diritti di accesso vengono concessi tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

L'accesso viene concesso assegnando i ruoli Controllo degli accessi in base al ruolo appropriati a utenti, gruppi e applicazioni in un ambito specifico. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. Un ruolo assegnato a un ambito padre concede anche l'accesso agli elementi figlio contenuti al suo interno. Ad esempio, un utente con accesso a un gruppo di risorse può gestire tutte le risorse che contiene, come siti Web, macchine virtuali e subnet.

![Diagramma della relazione tra gli elementi di Azure Active Directory](./media/role-based-access-control-what-is/rbac_aad.png)

Il ruolo RBAC assegnato determina quali risorse l'utente, il gruppo o l'applicazione può gestire in tale ambito.

## Ruoli predefiniti
Il Controllo degli accessi in base al ruolo di Azure include di tre ruoli di base che si applicano a tutti i tipi di risorsa:

- **Proprietario**: ha accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti.
- **Collaboratore**: può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti.
- **Lettore**: può visualizzare le risorse di Azure esistenti.

Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore Macchina virtuale consente all'utente di creare e gestire macchine virtuali, ma non concede l'accesso alla rete virtuale o alla subnet a cui la macchina virtuale si connette.

La pagina [Controllo degli accessi in base al ruolo: ruoli predefiniti](role-based-access-built-in-roles.md) elenca i ruoli disponibili in Azure. Specifica le operazioni e l'ambito che ogni ruolo predefinito concede agli utenti. Per definire ruoli personalizzati per un controllo ancora maggiore, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md).

## Gerarchia delle risorse ed ereditarietà dell'accesso
- Ogni **sottoscrizione** in Azure appartiene a una sola directory.
- Ogni **gruppo di risorse** appartiene a una sola sottoscrizione.
- Ogni **risorsa** appartiene a un solo gruppo di risorse.

L’accesso che si concede all’ambito padre viene ereditato dall’ambito figlio. Ad esempio:

- Si assegna il ruolo Lettore a un gruppo di Azure AD nell'ambito della sottoscrizione. I membri di tale gruppo possono visualizzare ogni gruppo di risorse e ogni risorsa nella sottoscrizione.
- Si assegna il ruolo Collaboratore a un'applicazione nell'ambito del gruppo di risorse. Può gestire risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

## Controllo degli accessi in base al ruolo di Azure e Amministratore sottoscrizione classico
I ruoli Amministratore sottoscrizione classico e Coamministratore hanno l'accesso completo alla sottoscrizione di Azure. Possono gestire le risorse con il [portale di Azure](https://portal.azure.com) e le API di Azure Resource Manager o il [portale di Azure classico](https://manage.windowsazure.com) e le API di gestione del servizio Microsoft Azure. Nel modello RBAC, agli amministratori classici viene assegnato il ruolo di Proprietario nell'ambito della sottoscrizione.

Solo il portale di Azure e le nuove API di Azure Resource Manager supportano il Controllo degli accessi in base al ruolo di Azure. Gli utenti e le applicazioni a cui vengono assegnati ruoli Controllo degli accessi in base al ruolo di Azure non possono usare il portale di gestione classico e le API di gestione del servizio Microsoft Azure.

## Autorizzazioni per le operazioni di gestione e per le operazioni sui dati
Il Controllo degli accessi in base al ruolo di Azure supporta solo operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Azure Resource Manager. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, possono essere gestiti con il Controllo degli accessi in base al ruolo gli account di archiviazione, ma non i BLOB o le tabelle in un account di archiviazione. Analogamente, può essere gestito un database SQL, ma non le tabelle in esso contenute.

## Passaggi successivi
- Introduzione al [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md) nel portale di Azure.
- Vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](role-based-access-built-in-roles.md)
- Definire i [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md).

<!---HONumber=AcomDC_0504_2016-->
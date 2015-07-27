
<properties 
	pageTitle="Uso di un gruppo per gestire l'accesso ad applicazioni SaaS| Microsoft Azure" 
	description="Un argomento che illustra come usare i gruppi in Azure AD Premium per assegnare l'accesso ad applicazioni SaaS integrate in Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" identity
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Uso di un gruppo per gestire l'accesso ad applicazioni SaaS

Con Azure AD Premium è possibile usare i gruppi per assegnare l'accesso a un'applicazione SaaS integrata in Azure AD. Ad esempio, se si desidera assegnare al reparto marketing l'accesso per l'uso di cinque applicazioni SaaS diverse, è possibile creare un gruppo contenente gli utenti del reparto marketing e quindi assegnare a tale gruppo le cinque applicazioni SaaS necessarie. In questo modo è possibile velocizzare le operazioni grazie alla gestione dei membri del reparto marketing in un'unica posizione. Gli utenti vengono quindi assegnati all'applicazione quando vengono aggiunti come membri del gruppo marketing. In modo analogo, le relative assegnazioni vengono rimosse dall'applicazione quando gli utenti vengono rimossi dal gruppo marketing.

Questa funzionalità può essere usata con centinaia di applicazioni che è possibile aggiungere dalla raccolta di applicazioni Azure AD.

**Per assegnare a un gruppo l'accesso a un'applicazione SaaS**


1. Aprire il browser desiderato e accedere al portale di gestione di Azure. Nel portale di gestione di Azure trovare l'estensione Active Directory nella barra di spostamento a sinistra. Nella scheda **Directory** fare clic sulla directory in cui si desidera assegnare a un gruppo l'accesso a un'applicazione Saas.


2. Fare clic sulla scheda Applicazioni relativa alla directory desiderata. Fare clic su un'applicazione aggiunta dalla raccolta di applicazioni e quindi fare clic sulla scheda Utenti e gruppi.

3. Nel campo Che inizia con della scheda Utenti e gruppi immettere il nome del gruppo a cui si desidera assegnare l'accesso e fare clic sul segno di spunta in alto a destra. È sufficiente digitare la prima parte del nome del gruppo. Fare clic sul gruppo per evidenziarlo, fare clic sul pulsante **Assegna accesso** e quindi su **Sì** quando viene visualizzato il messaggio di conferma.


4. È inoltre possibile visualizzare quali utenti sono assegnati all'applicazione direttamente o in base all'appartenenza a un gruppo. A tale scopo, impostare **Mostra elenco a discesa da "Gruppi"** su **"Tutti gli utenti"**. Nell'elenco sono visualizzati gli utenti nella directory ed è indicato se ogni utente è assegnato o meno all'applicazione. Nell'elenco è inoltre indicato se gli utenti sono assegnati direttamente all'applicazione (il tipo di assegnazione è indicato come 'Diretto') oppure in base all'appartenenza al gruppo (il tipo di assegnazione è indicato come 'Ereditato').


> [AZURE.NOTE]La scheda Utenti e gruppi viene visualizzata solo dopo aver abilitato Azure AD Premium.

Di seguito sono elencati alcuni argomenti contenenti informazioni aggiuntive su Azure Active Directory

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Informazioni su Azure Active Directory](active-directory-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=July15_HO3-->
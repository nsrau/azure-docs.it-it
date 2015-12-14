<properties
	pageTitle="# Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio | Microsoft Azure"
	description="Procedura per configurare i dispositivi Windows 7 aggiunti a un dominio per la registrazione automatica con Azure AD e procedura per distribuire il pacchetto software per la registrazione dei dispositivi nei dispositivi Windows 7 aggiunti a un dominio tramite un sistema di distribuzione software, ad esempio System Center Configuration Manager."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>

# Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio

In qualità di amministratore IT, è possibile configurare i dispositivi Windows 7 aggiunti a un dominio per la registrazione automatica in Azure AD. A tale scopo, è necessario distribuire il pacchetto software per la registrazione dei dispositivi nei dispositivi Windows 7 aggiunti a un dominio tramite un sistema di distribuzione software, ad esempio System Center Configuration Manager. Assicurarsi di leggere e completare i prerequisiti elencati nell'articolo relativo alla registrazione automatica di dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio.

##Installazione del pacchetto software per la registrazione dei dispositivi nei dispositivi Windows 7 aggiunti a un dominio

Il software per la registrazione dei dispositivi per Windows 7 è disponibile come [pacchetto MSI scaricabile](https://connect.microsoft.com/site1164). Il pacchetto deve essere installato nei computer Windows 7 aggiunti a un dominio di Active Directory. È necessario distribuire il pacchetto usando un sistema di distribuzione software, ad esempio System Center Configuration Manager. Il pacchetto MSI supporta le opzioni standard di installazione invisibile all'utente usando il parametro /quiet. Il pacchetto software è disponibile per il download dal [sito Web Microsoft Connect](https://connect.microsoft.com/site1164). Qui sarà possibile selezionare e scaricare il software per l'aggiunta all'area di lavoro per Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## Aggiunta all'area di lavoro con Azure Active Directory
La registrazione dei dispositivi per i dispositivi Windows 7 aggiunti a un dominio non richiede o include un'interfaccia utente. Una volta installato il software nel computer, qualsiasi utente di dominio che accede al computer verrà automaticamente registrato con un oggetto dispositivo in Azure AD. In Azure AD sarà quindi presente un oggetto dispositivo per ogni utente del dispositivo fisico registrato.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto dell'utente e attivata al momento dell'accesso dell'utente. Una volta completato l'accesso, l'attività registra automaticamente l'utente e il dispositivo in Azure AD. L'attività pianificata è disponibile nella libreria dell'Utilità di pianificazione in **Microsoft** > **Aggiunta all'area di lavoro**. L'attività verrà eseguita e registrerà tutti gli utenti di Active Directory che accedono al computer. La figura seguente illustra la procedura dettagliata per la registrazione automatica dei dispositivi.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Un utente (Information Worker) accede a un computer client Windows 7 usando le credenziali del dominio di Active Directory.
1. Viene eseguita l'attività pianificata di aggiunta all'area di lavoro.
1. Viene eseguita l'autenticazione invisibile all'utente con Active Directory Federation Services tramite l'autenticazione integrata di Windows.
1. Il PC Windows 7 viene registrato nell'utente in Azure AD.
1. In Azure AD vengono creati un oggetto dispositivo e un certificato. L'oggetto rappresenta il user@device.
1. Il certificato di aggiunta all'area di lavoro è archiviato nel computer.

## Annullamento della registrazione dei dispositivi Windows 7 aggiunti a un dominio

Per annullare la registrazione dei dispositivi Windows 7 aggiunti a un dominio, disinstallare il pacchetto software per l'aggiunta all'area di lavoro dai dispositivi Windows 7 aggiunti a un dominio usando un sistema di distribuzione software, ad esempio System Center Configuration Manager.

Aprire quindi un prompt dei comandi nel computer Windows 7 ed eseguire il comando seguente per annullare la registrazione del dispositivo:
    
    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]Questo comando deve essere eseguito nel contesto di ogni utente di dominio che ha eseguito l'accesso al computer. Visualizzatore eventi ed errori per i dispositivi Windows 7 aggiunti a un dominio.

Nel registro eventi del computer Windows 7 verranno visualizzati i messaggi relativi all'aggiunta all'area di lavoro. Saranno disponibili i messaggi per gli eventi relativi all'aggiunta all'area di lavoro riusciti e non. Il registro eventi si trova nel Visualizzatore eventi in Registri applicazioni e servizi > Microsoft > Aggiunta all'area di lavoro.

## Argomenti aggiuntivi

- [Panoramica di Registrazione dispositivo di Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)
- [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration.md)
- [Configurazione della registrazione automatica per i dispositivi appartenenti a un dominio di Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

 

<!---HONumber=AcomDC_1203_2015-->
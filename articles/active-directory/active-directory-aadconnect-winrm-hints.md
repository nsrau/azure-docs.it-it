<properties 
	pageTitle="Azure AD Connect - Suggerimenti su Windows Remote Managed" 
	description="Azure AD Connect - Suggerimenti su Windows Remote Managed da utilizzare con ADFS." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect - Suggerimenti su Windows Remote Managed


Quando si utilizza Azure AD Connect per distribuire Active Directory Federation Services o il Proxy applicazione Web, verificare i requisiti di seguito per garantire l’esito positivo della connettività e della configurazione:

- Se il server di destinazione fa parte del dominio, verificare che Windows Remote Managed sia abilitato 
	* In una finestra di comando PSH con privilegi elevati, utilizzare il comando "Enable-PSRemoting –force" 

- Se il server di destinazione è un computer WAP non appartenente al dominio, è necessario considerare alcuni requisiti aggiuntivi
	- Nel computer di destinazione (computer WAP). 

- Verificare che il servizio winrm (Windows Remote Management / WS-Management) sia in esecuzione tramite lo snap-in Servizi

- In una finestra di comando PSH con privilegi elevati, utilizzare il comando "Enable-PSRemoting –force"
	- Nel computer in cui viene eseguita la procedura guidata (se il computer di destinazione non appartiene a un dominio o appartiene a un dominio non attendibile): 

- In una finestra di comando PSH con privilegi elevati, utilizzare il comando "Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate"
	- In Server Manager:
		- Aggiungere l'host DMZ WAP al pool di computer (scheda gestione server -> Gestisci -> Aggiungi server...usa DNS) 
		- Scheda Gestione server Tutti i server: fare clic con il pulsante destro del mouse sul server WAP e scegliere Gestisci come, immettere le credenziali locali (non di dominio) per il computer WAP 
		- Per convalidare la connettività PSH remota, nella scheda Gestione server Tutti i server: fare clic con il pulsante destro del mouse sul server WAP e scegliere Windows PowerShell. Si dovrebbe aprire una sessione remota di PSH per garantire sia possibile stabilire sessioni remote di PowerShell. 

**Risorse aggiuntive**


* [Ulteriori informazioni sugli account e le autorizzazioni Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect in MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=62-->
<properties
	pageTitle="Impostare i criteri di scadenza della password in Active Directory di Azure | Microsoft Azure"
	description="Informazioni su come controllare i criteri di scadenza e modificare la password utente scaduta singolarmente o in blocco per le password di Azure Active directory"
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
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="curtand"/>


# Impostare i criteri di scadenza della password in Azure Active Directory
> [AZURE.NOTE] In questo argomento vengono fornite informazioni per i servizi cloud, quali Microsoft Intune e Office 365, che si basano su Microsoft Azure Active Directory per i servizi di identità e directory.

In qualità di amministratore generale per un servizio cloud Microsoft, è possibile utilizzare il modulo della directory di Microsoft Azure Active per Windows PowerShell per impostare delle password utente che non scadano. È inoltre possibile utilizzare cmdlet Windows PowerShell per rimuovere la configurazione senza scadenza, o per vedere quali password utente vengono impostate senza scadenza.

  > [AZURE.NOTE] Solo le password degli account utente che non sono sincronizzate tramite sincronizzazione della directory possono essere configurate senza scadenza. Per ulteriori informazioni sulla sincronizzazione della directory, vedere l'elenco di argomenti in [Roadmap sulla sincronizzazione della Directory](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Per utilizzare i cmdlet di Windows PowerShell, è innanzitutto necessario installarli.

## Come procedere

- [Come controllare i criteri di scadenza per una password](#how-to-check-expiration-policy-for-a-password)

- [Impostare una scadenza della password](#set-a-password-to-expire)

- [Impostare una password senza scadenza](#set-a-password-to-never-expire)

## Come controllare i criteri di scadenza per una password

1.  Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.

2.  Eseguire una di queste operazioni:

	- Per vedere se la password di un singolo utente è impostata per non scadere mai, eseguire il cmdlet seguente usando il nome principale utente (UPN) (ad esempio, aprilr@contoso.onmicrosoft.com) o l'ID utente dell'utente che si desidera controllare: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

	- Per visualizzare l'impostazione "Nessuna scadenza per la Password" per tutti gli utenti, eseguire il cmdlet seguente: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## Impostare una scadenza della password

1.  Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.

2.  Eseguire una di queste operazioni:

	- Per impostare la password di un utente in modo che la password scada, eseguire il cmdlet seguente usando il nome principale utente (UPN) o l'ID utente dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$false`

	- Per impostare le password di tutti gli utenti dell'organizzazione in modo che scadano, utilizzare il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$false`

## Impostare una password senza scadenza

1. Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.

2.  Eseguire una di queste operazioni:

	- Per impostare la password di un utente senza scadenza, eseguire il cmdlet seguente usando il nome principale utente (UPN) o l'ID utente dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$true`

	- Per impostare le password degli utenti in un'organizzazione in modo che non scadano mai, eseguire il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$true`

<!---HONumber=AcomDC_0706_2016-->
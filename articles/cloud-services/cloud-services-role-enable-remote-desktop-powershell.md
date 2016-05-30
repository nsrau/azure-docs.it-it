<properties 
pageTitle="Abilitare una connessione Desktop remoto per un ruolo in Servizi cloud di Azure con PowerShell" 
description="Come configurare l'applicazione del servizio cloud di Azure utilizzando PowerShell per consentire le connessioni Desktop remoto" 
services="cloud-services" 
documentationCenter="" 
authors="thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="05/17/2016" 
ms.author="adegeo"/>

# Abilitare una connessione Desktop remoto per un ruolo in Servizi cloud di Azure con PowerShell

>[AZURE.SELECTOR]
- [Portale di Azure classico](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile usare una connessione Desktop remoto per risolvere e diagnosticare i problemi dell'applicazione mentre è in esecuzione.

Questo articolo descrive come abilitare Desktop remoto nei ruoli dei servizi cloud con PowerShell. Per i prerequisiti necessari per questo articolo, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). PowerShell usa l'approccio basato sull'estensione di Desktop remoto per poter abilitare Desktop remoto anche dopo la distribuzione dell'applicazione.


## Configurare Remote Desktop da PowerShell

Il cmdlet [Set AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) consente di abilitare Desktop remoto su tutti o specifici ruoli di distribuzione del servizio cloud. Il cmdlet permette di specificare il nome utente e la password per l'utente Desktop remoto tramite il parametro *Credential* che accetta un oggetto PSCredential.

Se si usa PowerShell in modo interattivo è possibile impostare facilmente l'oggetto PSCredential chiamando il cmdlet [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx).

```
$remoteusercredentials = Get-Credential
```

Verrà visualizzata una finestra di dialogo in cui immettere il nome utente e password per l'utente remoto in modo sicuro.

Poiché PowerShell verrà prevalentemente usato per gli scenari di automazione, è anche possibile configurare l'oggetto PSCredential in modo che non richieda l'interazione dell'utente. In questo caso, è necessario impostare una password sicura. Per iniziare, specificare una password non crittografata e convertirla in una stringa sicura con [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Quindi è necessario convertire questa stringa sicura in una stringa standard crittografata con [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Ora è possibile salvare questa stringa crittografata standard in un file con [Set-Content](https://technet.microsoft.com/library/ee176959.aspx). Quando si crea l'oggetto PSCredential, è possibile leggere da questo file per impostare la password in modo sicuro senza doverla specificare a un prompt o archiviarla come testo normale.

Usare il codice di PowerShell seguente per creare un file della password sicura:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

Una volta creato il file della password (password.txt), si userà solo questo file e non sarà necessario specificare la password in testo normale. Se è necessario aggiornare la password, è possibile eseguire di nuovo il codice di PowerShell precedente con la nuova password per generare un nuovo file password.txt.

>[AZURE.IMPORTANT] Quando si imposta la password, assicurarsi che soddisfi i [requisiti di complessità](https://technet.microsoft.com/library/cc786468.aspx).

Per creare l'oggetto credential dal file della password sicura, è necessario leggere il contenuto del file e convertirlo si nuovo in una stringa sicura con [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Oltre alle credenziali, il cmdlet [Set AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) accetta anche un parametro *Expiration* che specifica un valore DateTime in cui l'account utente scadrà. Per impostare questo valore, specificare una data e un'ora statiche oppure scegliere semplicemente di far scadere l'account pochi giorni dopo la data corrente.

Questo esempio di PowerShell illustra come impostare l'estensione di Desktop remoto in un servizio cloud:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
È anche possibile specificare lo slot di distribuzione e i ruoli per i quali si vuole abilitare Desktop remoto. Se questi parametri non sono specificati, il cmdlet userà per impostazione predefinita lo slot di distribuzione Produzione e abiliterà Desktop remoto su tutti i ruoli nella distribuzione di produzione.

L'estensione per il Desktop remoto è associata a una distribuzione. Se si crea una nuova distribuzione per il servizio, si dovrà abilitare di nuovo Desktop remoto sulla nuova distribuzione. Se si vuole che Desktop remoto sia sempre abilitato nelle proprie distribuzioni, considerare la possibilità di integrare gli script di PowerShell per abilitare Desktop remoto nel flusso di lavoro di distribuzione.


## Desktop remoto in un'istanza del ruolo
Il cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) può essere usato per integrare Desktop remoto in una specifica istanza del ruolo del servizio cloud. È possibile usare il parametro *LocalPath* nel cmdlet per scaricare il file RDP in locale oppure usare il parametro *Launch* per avviare direttamente la finestra di dialogo Connessione Desktop remoto per accedere all'istanza del ruolo del servizio cloud.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## Verificare se l'estensione di Desktop remoto è attivata in un servizio 
Il cmdlet [Get AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) mostra se Desktop remoto è abilitato in una distribuzione del servizio. Il cmdlet restituirà il nome utente per l'utente Desktop remoto e i ruoli sui quali l'estensione di Desktop remoto è abilitata. È possibile specificare facoltativamente uno slot di distribuzione con il valore predefinito Produzione.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## Rimuovere l'estensione di Desktop remoto da un servizio 
Se già stata abilitata l'estensione di Desktop remoto in una distribuzione ed è necessario aggiornare le impostazioni di Desktop remoto, occorre prima di tutto rimuovere l'estensione di Desktop remoto e quindi riabilitarla con le nuove impostazioni. Ad esempio, se si vuole impostare una nuova password per l'account utente remoto o se l'account utente è scaduto, è necessario rimuovere l'estensione e quindi riaggiungerla con la nuova password o la nuova scadenza. Questa operazione è necessaria solo nelle distribuzioni esistenti in cui l'estensione di Desktop remoto è abilitata. Per le nuove distribuzioni è semplicemente possibile applicare l'estensione direttamente.

Per rimuovere l'estensione di Desktop remoto dalla distribuzione del servizio, è possibile usare il cmdlet [Remove AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx). È anche possibile specificare lo slot di distribuzione e il ruolo dal quale si vuole rimuovere l'estensione di Desktop remoto.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```  

>[AZURE.NOTE] Per rimuovere completamente la configurazione dell'estensione, è necessario chiamare il cmdlet *remove* con il parametro **UninstallConfiguration**.
>
>Il parametro **UninstallConfiguration** disinstallerà qualsiasi configurazione dell'estensione applicata al servizio. La configurazione dell'estensione è associata alla configurazione del servizio per attivare l'estensione con una distribuzione, quindi la distribuzione deve essere associata a tale configurazione dell'estensione. Se si chiama il cmdlet *remove* senza **UninstallConfiguration** la distribuzione verrà dissociata dalla configurazione dell'estensione, rimuovendo in effetti l'estensione dalla distribuzione. La configurazione dell'estensione rimarrà comunque associata al servizio.



## Risorse aggiuntive

[Come configurare i servizi cloud](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_0518_2016-->
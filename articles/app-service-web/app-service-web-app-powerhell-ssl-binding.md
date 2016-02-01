<properties
	pageTitle="Binding di certificati SSL con PowerShell"
	description="Informazioni su come associare certificati SSL a un'app Web con PowerShell."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# Binding di certificati SSL del servizio App di Azure con PowerShell #

Con il rilascio di Microsoft Azure PowerShell versione 1.1.0 è stato aggiunto un nuovo cmdlet che consente all'utente di associare i certificati SSL nuovi o esistenti a un'app Web esistente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]


## Caricamento e binding di un nuovo certificato SSL ##

Scenario: l'utente vuole associare un certificato SSL a una delle proprie app Web.

Conoscendo il nome del gruppo di risorse che contiene l'app Web, il nome dell'app Web, il percorso del file PFX del certificato nel computer dell'utente, la password per il certificato e il nome host personalizzato, è possibile usare il comando di PowerShell seguente per creare il binding SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

## Caricamento e binding di un certificato SSL esistente ##

Scenario: l'utente vuole associare un certificato SSL caricato in precedenza a una delle proprie app Web.

È possibile ottenere l'elenco dei certificati già caricati in un gruppo di risorse specifico usando il comando seguente:

	Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Si noti che i certificati sono locali rispetto a un percorso e un gruppo di risorse specifici, quindi l'utente deve ricaricare il certificato se l'app Web configurata si trova in un percorso e in gruppo di risorse diversi da quelli del certificato richiesto.

Conoscendo il nome del gruppo di risorse che contiene l'app Web, il nome dell'app Web, l'identificazione personale del certificato e il nome host personalizzato, è possibile usare il comando di PowerShell seguente per creare il binding SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## Eliminazione di un binding SSL esistente  ##

Scenario: l'utente vuole eliminare un binding SSL esistente.

Conoscendo il nome del gruppo di risorse che contiene l'app Web, il nome dell'app Web e il nome host personalizzato, è possibile usare il comando di PowerShell seguente per rimuovere il binding SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Si noti che se il binding SSL rimosso era l'ultimo binding che usava quel certificato in quel percorso, per impostazione predefinita il certificato viene eliminato. Se l'utente vuole mantenere il certificato, può usare invece l'opzione DeleteCertificate.

	Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### Riferimenti ###
- [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)
- [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0121_2016-->
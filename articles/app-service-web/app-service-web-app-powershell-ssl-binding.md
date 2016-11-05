---
title: Binding di certificati SSL con PowerShell
description: Informazioni su come associare certificati SSL a un'app Web con PowerShell.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: ahmedelnably

---
# Binding di certificati SSL del servizio App di Azure con PowerShell
Con il rilascio di Microsoft Azure PowerShell versione 1.1.0 è stato aggiunto un nuovo cmdlet che consente all'utente di associare i certificati SSL nuovi o esistenti a un'app Web esistente.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Per informazioni su come usare cmdlet di Azure PowerShell basati su Azure Resource Manager per gestire le app Web, vedere [Uso di comandi di PowerShell basati su Azure Resource Manager per la gestione di app Web di Azure](app-service-web-app-azure-resource-manager-powershell.md)

## Caricamento e binding di un nuovo certificato SSL
Scenario: l'utente vuole associare un certificato SSL a una delle proprie app Web.

Conoscendo il nome del gruppo di risorse che contiene l'app Web, il nome dell'app Web, il percorso del file PFX del certificato nel computer dell'utente, la password per il certificato e il nome host personalizzato, è possibile usare il comando di PowerShell seguente per creare il binding SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Si noti che prima di aggiungere un binding SSL a un'app Web, è necessario avere un nome host (dominio personalizzato) già configurato. Se il nome host non è configurato, durante l'esecuzione di New-AzureRmWebAppSSLBinding verrà visualizzato un errore per indicare che "nomehost" non esiste. È possibile aggiungere un nome host direttamente dal portale o con Azure PowerShell. Per configurare il nome host prima di eseguire New-AzureRmWebAppSSLBinding, è possibile usare il frammento di PowerShell seguente.

    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   

È importante tenere presente che il cmdlet Set-AzureRmWebApp sovrascrive i nomi host per l'app Web. Perciò il frammento di PowerShell precedente aggiunge una voce all'elenco di nomi host per l'app Web esistente.

## Caricamento e binding di un certificato SSL esistente
Scenario: l'utente vuole associare un certificato SSL caricato in precedenza a una delle proprie app Web.

È possibile ottenere l'elenco dei certificati già caricati in un gruppo di risorse specifico usando il comando seguente:

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Si noti che i certificati sono locali rispetto a un percorso e un gruppo di risorse specifici, quindi l'utente deve ricaricare il certificato se l'app Web configurata si trova in un percorso e in gruppo di risorse diversi da quelli del certificato richiesto.

Conoscendo il nome del gruppo di risorse che contiene l'app Web, il nome dell'app Web, l'identificazione personale del certificato e il nome host personalizzato, è possibile usare il comando di PowerShell seguente per creare il binding SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## Eliminazione di un binding SSL esistente
Scenario: l'utente vuole eliminare un binding SSL esistente.

Conoscendo il nome del gruppo di risorse che contiene l'app Web, il nome dell'app Web e il nome host personalizzato, è possibile usare il comando di PowerShell seguente per rimuovere il binding SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Si noti che se il binding SSL rimosso era l'ultimo binding che usava quel certificato in quel percorso, per impostazione predefinita il certificato viene eliminato. Se l'utente vuole mantenere il certificato, può usare invece l'opzione DeleteCertificate.

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### Riferimenti
* [Uso di comandi di PowerShell basati su Azure Resource Manager per la gestione di app Web di Azure](app-service-web-app-azure-resource-manager-powershell.md)
* [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)
* [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->
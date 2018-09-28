---
title: Preparazione per l'host dell'estensione per Azure Stack | Microsoft Docs
description: Informazioni su come preparare host dell'estensione, che viene abilitato automaticamente tramite un pacchetto di Azure Stack aggiornamenti futuro.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 09/26/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: 3e35b0d9ba697b54b0fb85096caceeaa024b1f3d
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405241"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Preparazione per l'host dell'estensione per Azure Stack

L'host dell'estensione protegge Azure Stack, riducendo il numero di porte TCP/IP necessarie. Questo articolo esamina la preparazione di Azure Stack per l'host dell'estensione, che viene abilitato automaticamente tramite un pacchetto di aggiornamento di Azure Stack dopo l'aggiornamento 1808.

## <a name="certificate-requirements"></a>Requisiti dei certificati

L'host dell'estensione implementa due nuovi domini spazi dei nomi per garantire le voci host univoci per ogni estensione del portale. I nuovi spazi dei nomi di dominio richiedono due certificati con caratteri jolly aggiuntivi per garantire comunicazioni sicure.

La tabella illustra i nuovi spazi dei nomi e i certificati associati:

| Cartella di distribuzione | Soggetto certificato necessarie e nomi alternativi del soggetto (SAN) | Ambito (per area) | Spazio dei nomi di sottodominio |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Host dell'estensione amministratore | *.adminhosting. \<area >. \<fqdn > (certificati SSL con caratteri jolly) | Host dell'estensione amministratore | adminhosting. \<area >. \<fqdn > |
| Host dell'estensione pubblica | * .hosting. \<area >. \<fqdn > (certificati SSL con caratteri jolly) | Host dell'estensione pubblica | Hosting. \<area >. \<fqdn > |

I requisiti del certificato dettagliati sono reperibili nel [requisiti dei certificati di infrastruttura a chiave pubblica di Azure Stack](azure-stack-pki-certs.md) articolo.

## <a name="create-certificate-signing-request"></a>Crea richiesta di firma del certificato

Lo strumento di controllo dello stato di preparazione di Azure Stack offre la possibilità di creare un certificato di firma richiesta per i due certificati SSL nuovi, obbligatorio. Seguire i passaggi nell'articolo [firma richiedere la generazione di certificati di Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> È possibile ignorare questo passaggio a seconda del modo in cui richiesto i certificati SSL.

## <a name="validate-new-certificates"></a>Convalidare i nuovi certificati

1. Aprire PowerShell con privilegi elevati dispone dell'autorizzazione per l'host del ciclo di vita dell'hardware o la workstation di gestione di Azure Stack.
2. Eseguire il cmdlet seguente per installare lo strumento di controllo di conformità di Azure Stack.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Eseguire lo script seguente per creare la struttura di cartelle necessaria:

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Se si distribuisce con Azure Active Directory Federated Services (ADFS) è necessario aggiungere le seguenti directory alla **$directories** nello script: `ADFS`, `Graph`.

4. Eseguire i cmdlet seguenti per avviare la verifica del certificato:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD -ExtensionHostFeature
    ```

5. Inserire i certificati nelle directory appropriate.

6. Controllare l'output e tutti i certificati superano tutti i test.


## <a name="import-extension-host-certificates"></a>Importare i certificati di host di estensione

Usare un computer in grado di connettersi all'endpoint di Azure Stack con privilegi per i passaggi successivi. Assicurarsi di avere accesso per i nuovi file di certificato da quel computer.

1. Usare un computer in grado di connettersi all'endpoint di Azure Stack con privilegi per i passaggi successivi. Assicurarsi che si accede ai nuovi file di certificato da tale computer.
2. Aprire PowerShell ISE per eseguire lo script successivo si blocca
3. Importare il certificato per l'hosting dell'endpoint. Modificare lo script per adattarli all'ambiente.
4. Importare il certificato per l'host dell'endpoint di amministrazione.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
5. Importare il certificato per l'endpoint di hosting.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```



### <a name="update-dns-configuration"></a>Aggiornare la configurazione del DNS

> [!Note]  
> Questo passaggio non è necessario usare la delega delle Zone DNS di integrazione DNS.
Se i record A singolo host sono stati configurati per pubblicare gli endpoint di Azure Stack, è necessario creare due record host aggiuntivo:

| IP | Nome host | type |
|----|------------------------------|------|
| \<IP &GT; | Adminhosting. <Region>.<FQDN> | Una  |
| \<IP &GT; | Hosting. <Region>.<FQDN> | Una  |

Gli indirizzi IP allocati può essere recuperato tramite endpoint con privilegi eseguendo il cmdlet **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Porte e protocolli

L'articolo [integrazione di Data Center di Azure Stack - pubblicano endpoint](azure-stack-integrate-endpoints.md), vengono illustrate le porte e protocolli che richiedono la comunicazione in ingresso per la pubblicazione di Azure Stack prima dell'implementazione host estensione.

### <a name="publish-new-endpoints"></a>Pubblicare nuovi endpoint

Sono disponibili due nuovi endpoint deve essere pubblicato tramite il firewall. Gli indirizzi IP allocato dal pool di indirizzi VIP pubblici può essere recuperato tramite il cmdlet **Get-AzureStackStampInformation**.

> [!Note]  
> Apportare questa modifica prima di abilitare l'host dell'estensione. In questo modo i portali di Azure Stack sia costantemente accessibile.

| Endpoint (indirizzo VIP) | Protocollo | Porte |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Aggiornare le regole di pubblicazione esistente (abilitazione della Post dell'host dell'estensione)

> [!Note]  
> 1808 il pacchetto di aggiornamento di Azure Stack viene **non** abilitare ancora host dell'estensione. Consente di prepararsi host dell'estensione mediante l'importazione dei certificati richiesti. Non chiudere le porte prima di host dell'estensione viene abilitata automaticamente tramite un pacchetto di aggiornamento di Azure Stack dopo l'aggiornamento 1808.

Le regole del firewall esistente è necessario chiudere le seguenti porte di endpoint esistente.

> [!Note]  
> Si consiglia di chiudere tali porte al termine della convalida.

| Endpoint (indirizzo VIP) | Protocollo | Porte |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portale (amministratore) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| Portale (utente) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (amministratore) | HTTPS | 30024 |
| Azure Resource Manager (utente) | HTTPS | 30024 |

## <a name="next-steps"></a>Passaggi successivi

- Scopri [Firewall integrazione](azure-stack-firewall.md).
- Informazioni su [firma richiedere la generazione di certificati di Azure Stack](azure-stack-get-pki-certs.md)

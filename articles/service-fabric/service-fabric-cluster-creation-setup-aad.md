---
title: Configurare Azure Active Directory per l'autenticazione client di Service Fabric | Microsoft Docs
description: Informazioni su come configurare Azure Active Directory (Azure AD) per autenticare i client per i cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2019
ms.author: atsenthi
ms.openlocfilehash: 77814d04daca0ebb649ffa2e8ff46becddec4f0f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901512"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurare Azure Active Directory per l'autenticazione client

Per i cluster in esecuzione in Azure, è consigliabile proteggere l'accesso agli endpoint di gestione tramite Azure Active Directory (Azure AD). Questo articolo descrive come configurare Azure AD per autenticare i client per un cluster di Service Fabric.

In questo articolo viene usato il termine "applicazione" per fare riferimento alle [applicazioni Azure Active Directory](../active-directory/develop/developer-glossary.md#client-application), non Service Fabric le applicazioni; la distinzione verrà fatta laddove necessario. Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso degli utenti alle applicazioni.

Un cluster di Service Fabric offre diversi punti di accesso alle proprie funzionalità di gestione, tra cui [Service Fabric Explorer][service-fabric-visualizing-your-cluster] e [Visual Studio][service-fabric-manage-application-in-visual-studio] basati sul Web. Si creeranno quindi due Azure AD applicazioni per controllare l'accesso al cluster: un'applicazione Web e un'applicazione nativa. Una volta create le applicazioni, gli utenti verranno assegnati ai ruoli di amministratore e di sola lettura.

> [!NOTE]
> In Linux è necessario completare i passaggi seguenti prima di creare il cluster. In Windows è inoltre possibile [configurare l'autenticazione Azure ad per un cluster esistente](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory][active-directory-howto-tenant].

Per semplificare alcuni dei passaggi richiesti per la configurazione di Azure AD con un cluster di Service Fabric è stato creato un set di script di Windows PowerShell.

1. [Clonare il repository](https://github.com/Azure-Samples/service-fabric-aad-helpers) nel computer.
2. [Assicurarsi di disporre di tutti i prerequisiti](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) per gli script installati.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Creare applicazioni Azure AD e assegnare gli utenti ai ruoli

Verranno usati gli script per creare due Azure AD applicazioni per controllare l'accesso al cluster: un'applicazione Web e un'applicazione nativa. Dopo aver creato le applicazioni per rappresentare il cluster, verranno creati utenti per i [ruoli supportati da Service Fabric](service-fabric-cluster-security-roles.md): sola lettura e amministratore.

Eseguire `SetupApplications.ps1` e indicare l'ID tenant, il nome del cluster e l'URL di risposta dell'applicazione Web come parametri.  Specificare anche i nomi utente e le password per gli utenti. ad esempio:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Per i cloud nazionali (ad esempio, Azure per enti pubblici, Azure Cina o Azure Germania), specificare anche il parametro `-Location`.

È possibile trovare il *TenantId* eseguendo il comando di PowerShell `Get-AzureSubscription`. Eseguendo questo comando, viene visualizzato il TenantId per ogni sottoscrizione.

*ClusterName* viene usato come prefisso per le applicazioni Azure AD create dallo script. Non è necessario che corrisponda esattamente al nome effettivo del cluster. Ha solo lo scopo di facilitare il mapping degli elementi di Azure AD al cluster di Service Fabric con cui vengono usati.

*WebApplicationReplyUrl* è l'endpoint predefinito che Azure AD restituisce agli utenti dopo che hanno completato l'accesso. Impostare questo endpoint come endpoint Service Fabric Explorer per il cluster. Se si sta creando Azure AD applicazioni per rappresentare un cluster esistente, assicurarsi che l'URL corrisponda all'endpoint del cluster esistente. Se si creano applicazioni per un nuovo cluster, pianificare l'endpoint che avrà il cluster e assicurarsi di non usare l'endpoint di un cluster esistente. Per impostazione predefinita, l'endpoint Service Fabric Explorer è:

https://&lt;cluster_domain&gt;:19080/Explorer

Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant Azure AD. Dopo avere eseguito l'accesso, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Tra le applicazioni del tenant nel [portale di Azure][azure-portal] verranno visualizzate due nuove voci:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Lo script stampa il codice JSON richiesto dal modello di Azure Resource Manager quando si [Crea il cluster AAD abilitato](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), quindi è consigliabile lasciare aperta la finestra di PowerShell.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Risoluzione dei problemi di configurazione di Azure Active Directory
La configurazione di Azure AD e il suo utilizzo possono comportare difficoltà, pertanto di seguito verranno fornite alcune indicazioni per risolvere gli eventuali problemi.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer richiede di selezionare un certificato
#### <a name="problem"></a>Problema
Dopo avere eseguito l'accesso ad Azure AD in Service Fabric Explorer, il browser torna alla home page, ma un messaggio chiede di selezionare un certificato.

![Finestra di dialogo del certificato di SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
All'utente non è assegnato un ruolo nell'applicazione cluster Azure AD. Di conseguenza, l'autenticazione di Azure AD non riesce nel cluster di Service Fabric. Service Fabric Explorer ritorna all'autenticazione del certificato.

#### <a name="solution"></a>Soluzione
Seguire le istruzioni per la configurazione di Azure AD e assegnare i ruoli utente. È anche consigliabile attivare "Assegnazione utente obbligatoria per l'accesso all'app", come in `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>La connessione a PowerShell non riesce con un errore: "Le credenziali specificate non sono valide"
#### <a name="problem"></a>Problema
Quando si usa PowerShell per connettersi al cluster con la modalità di sicurezza "AzureActiveDirectory", eseguito l'accesso ad Azure AD, la connessione ha esito negativo con un errore: "Le credenziali specificate non sono valide".

#### <a name="solution"></a>Soluzione
Questa soluzione è identica a quella precedente.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer restituisce un errore durante l'accesso: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando si prova a eseguire l'accesso ad Azure AD in Service Fabric Explorer, la pagina restituisce l'errore AADSTS50011, che indica che l'&lt;URL&gt; dell'indirizzo di risposta non corrisponde agli indirizzi configurati per l'applicazione: &lt;GUID&gt;.

![Indirizzo di risposta di SFX non corrispondente][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
L'applicazione cluster (Web) che rappresenta Service Fabric Explorer prova a eseguire l'autenticazione per Azure AD e come parte della richiesta indica l'URL di reindirizzamento restituito. L'URL non è presente nell'elenco degli **URL DI RISPOSTA** dell'applicazione Azure AD.

#### <a name="solution"></a>Soluzione
Selezionare "Registrazioni per l'app" nella pagina di Azure Active Directory, selezionare l'applicazione cluster e quindi fare clic sul pulsante **URL di risposta**. Nella pagina URL di risposta aggiungere l'URL di Service Fabric Explorer all'elenco o sostituire una delle voci dell'elenco. Al termine, salvare la modifica.

![URL di risposta dell'applicazione Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Connettere il cluster usando l'autenticazione di Azure AD tramite PowerShell
Per connettere il cluster di Service Fabric, usare il comando di PowerShell di esempio seguente:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Per altre informazioni, vedere il [cmdlet Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>È possibile usare di nuovo lo stesso tenant di Azure AD in più cluster?
Sì. Ricordarsi però di aggiungere l'URL di Service Fabric Explorer all'applicazione cluster (Web). In caso contrario, Service Fabric Explorer non funzionerà.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Perché è ancora necessario un certificato del server se Azure AD è abilitato?
FabricClient e FabricGateway eseguono un'autenticazione reciproca. Durante l'autenticazione Azure AD, Azure AD integrazione fornisce un'identità client al server e il certificato del server viene utilizzato dal client per verificare l'identità del server. Per ulteriori informazioni sui certificati Service Fabric, vedere [certificati X. 509 e Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato le applicazioni Azure Active Directory e aver impostato i ruoli per gli utenti, [configurare e distribuire un cluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png

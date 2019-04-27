---
title: Configurare Azure Active Directory per l'autenticazione client di Service Fabric | Microsoft Docs
description: Informazioni su come configurare Azure Active Directory (Azure AD) per autenticare i client per i cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2019
ms.author: aljo
ms.openlocfilehash: c02e38880fdf8e8f1a2229f009b343d6431af853
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125137"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurare Azure Active Directory per l'autenticazione client

Per i cluster in esecuzione in Azure, è consigliabile proteggere l'accesso agli endpoint di gestione tramite Azure Active Directory (Azure AD).  Questo articolo descrive come configurare Azure AD per l'autenticazione dei client per un cluster di Service Fabric. Tale configurazione deve essere eseguita prima della [creazione del cluster](service-fabric-cluster-creation-via-arm.md).  Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso degli utenti alle applicazioni. Alcune applicazioni sono caratterizzate da un'interfaccia utente di accesso basata sul Web, altre invece da un'esperienza client nativa. 

I cluster di Service Fabric offrono numerosi punti di ingresso alle relative funzionalità di gestione, tra cui [Service Fabric Explorer][service-fabric-visualizing-your-cluster], basato sul Web, e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Si creano quindi due applicazioni Azure AD per controllare l'accesso al cluster: un'applicazione Web e un'applicazione nativa.  Dopo aver creato le applicazioni, si assegnano gli utenti ai ruoli di sola lettura e di amministratore.

> [!NOTE]
> È necessario completare i passaggi seguenti prima di creare il cluster. Poiché per gli script sono previsti nomi ed endpoint dei cluster, i valori devono essere pianificati e non quelli già creati.

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che sia già stato creato un tenant. In caso contrario, vedere prima di tutto [Come ottenere un tenant di Azure Active Directory][active-directory-howto-tenant].

Per semplificare alcuni dei passaggi richiesti per la configurazione di Azure AD con un cluster di Service Fabric è stato creato un set di script di Windows PowerShell.

1. [Scaricare gli script](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) nel computer.
2. Fare clic con il pulsante destro del mouse sul file ZIP, scegliere **Proprietà**, selezionare la casella di controllo **Sblocca** e quindi fare clic su **Applica**.
3. Estrarre il file con estensione zip.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Creare applicazioni Azure AD e assegnare gli utenti ai ruoli
Creare due applicazioni Azure AD per controllare l'accesso al cluster: un'applicazione Web e un'applicazione nativa. Dopo aver creato le applicazioni per rappresentare il cluster, assegnare gli utenti ai [ruoli supportati da Service Fabric](service-fabric-cluster-security-roles.md): sola lettura e amministratore.

Eseguire `SetupApplications.ps1` e indicare l'ID tenant, il nome del cluster e l'URL di risposta dell'applicazione Web come parametri.  Specificare anche i nomi utente e le password per gli utenti.  Ad esempio: 

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Per i cloud nazionali (ad esempio, Azure per enti pubblici, Azure Cina o Azure Germania), specificare anche il parametro `-Location`.

È possibile trovare il *TenantId* eseguendo il comando di PowerShell `Get-AzureSubscription`. Eseguendo questo comando, viene visualizzato il TenantId per ogni sottoscrizione.

*ClusterName* viene usato come prefisso per le applicazioni Azure AD create dallo script. Non è necessario che corrisponda esattamente al nome effettivo del cluster. Ha solo lo scopo di facilitare il mapping degli elementi di Azure AD al cluster di Service Fabric con cui vengono usati.

*WebApplicationReplyUrl* è l'endpoint predefinito che Azure AD restituisce agli utenti dopo che hanno completato l'accesso. Impostare questo endpoint come endpoint di Service Fabric Explorer per il cluster, che per impostazione predefinita è:

https://&lt;cluster_domain&gt;:19080/Explorer

Verrà richiesto di accedere a un account con privilegi amministrativi per il tenant Azure AD. Dopo avere eseguito l'accesso, lo script crea l'applicazione Web e l'applicazione nativa per rappresentare il cluster di Service Fabric. Tra le applicazioni del tenant nel [portale di Azure][azure-portal] dovrebbero essere visualizzate due nuove voci:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

È consigliabile tenere aperta la finestra di PowerShell perché, durante la [creazione del cluster](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), lo script visualizza il codice JSON richiesto dal modello di Azure Resource Manager.

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

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>La connessione a PowerShell ha esito negativo con un errore: "Le credenziali specificate non sono valide"
#### <a name="problem"></a>Problema
Quando si usa PowerShell per connettersi al cluster con la modalità di sicurezza "AzureActiveDirectory", eseguito l'accesso ad Azure AD, la connessione ha esito negativo con un errore: "Le credenziali specificate non sono valide".

#### <a name="solution"></a>Soluzione
Questa soluzione è identica a quella precedente.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer restituisce un errore durante l'accesso: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando si tenta di accedere ad Azure AD in Service Fabric Explorer, la pagina restituisce un errore: "AADSTS50011: L'indirizzo per la risposta &lt;url&gt; non corrisponde agli indirizzi per le risposte configurati per l'applicazione: &lt;guid&gt;."

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
FabricClient e FabricGateway eseguono un'autenticazione reciproca. Durante l'autenticazione di Azure AD, l'integrazione di Azure AD consente di assegnare un'identità del client al server e il certificato del server viene usato per verificare l'identità del server. Per altre informazioni sui certificati di Service Fabric, vedere [Certificati X.509 e Service Fabric][x509-certificates-and-service-fabric].

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

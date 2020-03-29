---
title: Configurare un lab per usare Gateway Desktop remoto in Azure DevTest LabsConfigure a lab to use Remote Desktop Gateway in Azure DevTest Labs
description: Informazioni su come configurare un lab in Laboratori DevTest di Azure con un gateway desktop remoto per garantire l'accesso sicuro alle macchine virtuali lab senza dover esporre la porta RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88daecdf4490ffd4eef45e6cd664a16f86bad113
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170295"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configurare il lab in Azure DevTest Labs per l'uso di un gateway di desktop remotoConfigure your lab in Azure DevTest Labs to use a remote desktop gateway
In Laboratori DevTest di Azure è possibile configurare un gateway desktop remoto per il lab per garantire l'accesso sicuro alle macchine virtuali lab (VM) senza dover esporre la porta RDP. Il lab offre agli utenti del lab una posizione centrale in cui gli utenti del lab possono visualizzare e connettersi a tutte le macchine virtuali a cui hanno accesso. Il pulsante **Connetti** nella pagina **Macchina virtuale** crea un file RDP specifico del computer che è possibile aprire per connettersi al computer. È possibile personalizzare e proteggere ulteriormente la connessione RDP connettendo il lab a un gateway di desktop remoto. 

Questo approccio è più sicuro perché l'utente del lab esegue l'autenticazione direttamente nel computer gateway o può usare le credenziali aziendali in un computer gateway aggiunto al dominio per connettersi ai propri computer. Il lab supporta inoltre l'utilizzo dell'autenticazione token nel computer gateway che consente agli utenti di connettersi alle macchine virtuali lab senza esporre la porta RDP a Internet. Questo articolo illustra un esempio su come configurare un lab che usa l'autenticazione token per connettersi ai computer lab.

## <a name="architecture-of-the-solution"></a>Architettura della soluzione

![Architettura della soluzione](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. L'azione [Ottieni contenuto file RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) viene chiamata quando si seleziona il pulsante **Connetti.1.** 
1. L'azione Get RDP `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` file contents richiama per richiedere un token di autenticazione.
    1. `{gateway-hostname}`è il nome host del gateway specificato nella pagina Impostazioni lab per il lab nel portale di Azure.Is the gateway hostname specified on the **Lab Settings** page for your lab in the Azure portal. 
    1. `{lab-machine-name}`è il nome della macchina che si sta tentando di connettere.
    1. `{port-number}`è la porta su cui è necessario connettersi. Di solito questa porta è 3389. Se la macchina virtuale del lab usa la funzionalità [IP condiviso](devtest-lab-shared-ip.md) in DevTest Labs, la porta sarà diversa.
1. Il gateway desktop remoto rinvia la chiamata da `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` a una funzione di Azure per generare il token di autenticazione. Il servizio DevTest Labs include automaticamente il tasto funzione nell'intestazione della richiesta. Il tasto funzione deve essere salvato nell'insieme di credenziali delle chiavi del lab. Nome del segreto da visualizzare come **segreto** del token gateway nella pagina **Impostazioni lab** per il lab.
1. La funzione di Azure deve restituire un token per l'autenticazione basata su token basata su certificati nel computer gateway.  
1. L'azione Ottieni contenuto file RDP restituisce quindi il file RDP completo, incluse le informazioni di autenticazione.
1. Si apre il file RDP utilizzando il programma di connessione RDP preferito. Tenere presente che non tutti i programmi di connessione RDP supportano l'autenticazione token. Il token di autenticazione ha una data di scadenza, impostata dall'app per le funzioni. Effettuare la connessione alla macchina virtuale lab prima della scadenza del token.
1. Una volta che il computer gateway desktop remoto autentica il token nel file RDP, la connessione viene inoltrata al computer lab.

### <a name="solution-requirements"></a>Requisiti della soluzione
Per utilizzare la funzionalità di autenticazione token DevTest Labs, esistono alcuni requisiti di configurazione per i computer gateway, i servizi DNS (Domain Name Services) e le funzioni.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisiti per i computer gateway desktop remoto
- Il certificato SSL deve essere installato nel computer gateway per gestire il traffico HTTPS. Il certificato deve corrispondere al nome di dominio completo (FQDN) del servizio di bilanciamento del carico per la farm gateway o al nome di dominio completo del computer stesso se è presente un solo computer. I certificati SSL con caratteri jolly non funzionano.  
- Un certificato di firma installato nei computer gateway. Creare un certificato di firma utilizzando lo script [Create-SigningCertificate.ps1.Create](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) a signing certificate by using Create-SigningCertificate.ps1 script.
- Installare il modulo [Autenticazione collegabile](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) che supporta l'autenticazione token per il gateway desktop remoto. Un esempio di tale `RDGatewayFedAuth.msi` modulo è che viene fornito con le immagini di [System Center Virtual Machine Manager (VMM).](/system-center/vmm/install-console?view=sc-vmm-1807) Per ulteriori informazioni su System Center, vedere [documentazione di System Center](https://docs.microsoft.com/system-center/) e [dettagli sui prezzi](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Il server gateway è `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`in grado di gestire le richieste effettuate a .

    Il nome host del gateway è il nome di dominio completo del servizio di bilanciamento del carico della farm gateway o il nome di dominio completo del computer stesso se è presente un solo computer. Il `{lab-machine-name}` è il nome del computer lab che si `{port-number}` sta tentando di connettere e la porta is su cui verrà stabilita la connessione.  Per impostazione predefinita, questa porta è 3389.By default, this port is 3389.  Tuttavia, se la macchina virtuale utilizza la funzionalità [IP condiviso](devtest-lab-shared-ip.md) in DevTest Labs, la porta sarà diversa.
- Il modulo [Richiesta di routing dell'applicazione](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) per Internet `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` Information Server (IIS) può essere utilizzato per reindirizzare le richieste alla funzione azure, che gestisce la richiesta per ottenere un token per l'autenticazione.


## <a name="requirements-for-azure-function"></a>Requisiti per la funzione di AzureRequirements for Azure function
La funzione di Azure `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` gestisce la richiesta con il formato di e restituisce il token di autenticazione in base allo stesso certificato di firma installato nei computer gateway. Il `{function-app-uri}` è l'URI utilizzato per accedere alla funzione. Il tasto funzione viene passato automaticamente nell'intestazione della richiesta. Per una funzione [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)di esempio, vedere . 


## <a name="requirements-for-network"></a>Requisiti per la rete

- Il DNS per il nome di dominio completo associato al certificato SSL installato nei computer gateway deve indirizzare il traffico al computer gateway o al servizio di bilanciamento del carico della farm di macchine gateway.
- Se il computer lab utilizza indirizzi IP privati, deve essere presente un percorso di rete dal computer gateway al computer lab, tramite la condivisione della stessa rete virtuale o l'utilizzo di reti virtuali con peered.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configurare il lab per l'utilizzo dell'autenticazione tokenConfigure the lab to use token authentication 
In questa sezione viene illustrato come configurare un lab per l'utilizzo di un computer gateway desktop remoto che supporta l'autenticazione token. In questa sezione non viene illustrato come configurare una farm gateway desktop remoto. Per ulteriori informazioni, vedere la sezione [Esempio per creare un gateway di desktop remoto](#sample-to-create-a-remote-desktop-gateway) alla fine di questo articolo. 

Prima di aggiornare le impostazioni lab, archiviare la chiave necessaria per eseguire correttamente la funzione per restituire un token di autenticazione nell'insieme di credenziali delle chiavi del lab. È possibile ottenere il valore del tasto funzione nella pagina Gestisci per la funzione nel portale di Azure.You can get the function key value in the **Manage** page for the function in the Azure portal. Per ulteriori informazioni su come salvare un segreto in un insieme di credenziali delle chiavi, vedere [Aggiungere un segreto all'insieme di](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)credenziali delle chiavi . Salvare il nome del segreto per un uso successivo.

Per trovare l'ID dell'insieme di credenziali delle chiavi del lab, eseguire il comando dell'interfaccia della riga di comando di Azure seguente:To find the ID of the lab's key vault, run the following Azure CLI command: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configurare il lab per l'utilizzo dell'autenticazione token attenendosi alla procedura seguente:Configure the lab to use the token authentication by using these steps:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco dei laboratori selezionare il **lab**.
1. Nella pagina del lab selezionare **Configurazione e criteri**.
1. Nel menu a sinistra, nella sezione **Impostazioni,** selezionare **Impostazioni lab.**
1. Nella sezione **Desktop remoto** immettere il nome di dominio completo (FQDN) o l'indirizzo IP della macchina o della farm gateway di Servizi desktop remoti per il campo Nome **host gateway.** Questo valore deve corrispondere al nome di dominio completo del certificato SSL utilizzato nei computer gateway.

    ![Opzioni di Desktop remoto nelle impostazioni lab](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Nella sezione **Desktop remoto,** per **Segreto token gateway,** immettere il nome del segreto creato in precedenza. Questo valore non è la chiave della funzione stessa, ma il nome del segreto nell'insieme di credenziali delle chiavi del lab che contiene la chiave della funzione.

    ![Segreto del token del gateway nelle impostazioni lab](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Salva** Cambiamenti.

    > [!NOTE] 
    > Facendo clic su **Salva**, si accettano le [condizioni](https://www.microsoft.com/licensing/product-licensing/products)di licenza di Gateway Desktop remoto . Per ulteriori informazioni sul gateway remoto, vedere [Introduzione a Servizi Desktop remoto](https://aka.ms/rds) e Distribuire [l'ambiente desktop remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se è preferibile configurare il lab tramite l'automazione, vedere [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) per uno script di PowerShell di esempio per impostare le impostazioni del **nome host del gateway** e del **segreto del token del gateway.** Il repository GitHub di [Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) fornisce anche un modello di Azure Resource Manager che crea o aggiorna un lab con il nome host del **gateway** e le impostazioni del token del **gateway.**

## <a name="configure-network-security-group"></a>Configurare il gruppo di sicurezza di rete
Per proteggere ulteriormente il lab, è possibile aggiungere un gruppo di sicurezza di rete (NSG) alla rete virtuale usata dalle macchine virtuali lab. Per istruzioni su come configurare un gruppo di sicurezza di rete, vedere [Creare, modificare o eliminare un gruppo](../virtual-network/manage-network-security-group.md)di sicurezza di rete.

Di seguito è riportato un gruppo di sicurezza di rete di esempio che consente solo il traffico che passa per primo attraverso il gateway per raggiungere i computer lab. L'origine in questa regola è l'indirizzo IP del computer singolo gateway o l'indirizzo IP del servizio di bilanciamento del carico davanti ai computer gateway.

![Gruppo di sicurezza di rete - regole](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Esempio per creare un gateway di desktop remoto

> [!NOTE] 
> Utilizzando i modelli di esempio, si accettano le condizioni di [licenza di Gateway Desktop remoto.](https://www.microsoft.com/licensing/product-licensing/products) Per ulteriori informazioni sul gateway remoto, vedere [Introduzione a Servizi Desktop remoto](https://aka.ms/rds) e Distribuire [l'ambiente desktop remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

Il repository GitHub di Azure DevTest Labs fornisce alcuni esempi per configurare le risorse necessarie per usare l'autenticazione token e il gateway desktop remoto con DevTest Labs.The [Azure DevTest Labs GitHub repository](https://github.com/Azure/azure-devtestlab) provides a few samples to help setup the resources needed to use token authentication and remote desktop gateway with DevTest Labs. Questi esempi includono i modelli di Azure Resource Manager per i computer gateway, le impostazioni lab e l'app per le funzioni.

Seguire questi passaggi per configurare una soluzione di esempio per la farm gateway desktop remoto.

1. Creare un certificato di firma.  Eseguire [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Salvare l'identificazione personale, la password e la codifica Base64 del certificato creato.
2. Ottenere un certificato SSL. Il nome di dominio completo associato al certificato SSL deve essere per il dominio che si controlla. Salvare l'identificazione personale, la password e la codifica Base64 per questo certificato. Per ottenere l'identificazione personale tramite PowerShell, usare i comandi seguenti.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Per ottenere la codifica Base64 tramite PowerShell, usare il comando seguente.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Scaricare file [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)da .

    Il modello richiede l'accesso ad alcuni altri modelli di Resource Manager e alle risorse correlate allo stesso URI di base. Copiare tutti [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) i file da e RDGatewayFedAuth.msi in un contenitore BLOB in un account di archiviazione.  
4. Distribuire **azuredeploy.json** da [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Il modello accetta i seguenti parametri:
    - adminUsername – Obbligatorio.  Nome utente amministratore per i computer gateway.
    - adminPassword – Obbligatorio. Password per l'account amministratore per i computer gateway.
    - instanceCount: numero di computer gateway da creare.  
    - alwaysOn: indica se mantenere l'app Funzioni di Azure creata in uno stato caldo o meno. Mantenere l'app Funzioni di Azure eviterà ritardi quando gli utenti tentano per la prima volta di connettersi alla macchina virtuale lab, ma ha implicazioni sui costi.  
    - tokenLifetime: periodo di validità del token creato. Il formato è HH:MM:SS.
    - sslCertificate: codifica Base64 del certificato SSL per il computer gateway.
    - sslCertificatePassword: password del certificato SSL per il computer gateway.
    - sslCertificateThumbprint: identificazione personale del certificato per l'identificazione nell'archivio certificati locale del certificato SSL.
    - signCertificate: codifica Base64 per il certificato di firma per il computer gateway.
    - signCertificatePassword: password per il certificato di firma per il computer gateway.
    - signCertificateThumbprint: identificazione personale del certificato per l'identificazione nell'archivio certificati locale del certificato di firma.
    - _artifactsLocation: percorso URI in cui sono disponibili tutte le risorse di supporto. Questo valore deve essere un UIR completo, non un percorso relativo.
    - _artifactsLocationSasToken: token firma di accesso condiviso (SAS) usato per accedere alle risorse di supporto, se il percorso è un account di archiviazione di Azure.For an Domain: The Shared Access Signature (SAS) token used to access supporting resources, if the location is an Azure storage account.

    Il modello può essere distribuito usando l'interfaccia della riga di comando di Azure usando il comando seguente:The template can be deployed using the Azure CLI by using the following command:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Ecco le descrizioni dei parametri:

    - È possibile ottenere l'endpoint dell'account `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`di archiviazione eseguendo .  Il nome di archiviazione è il nome dell'account di archiviazione che contiene i file caricati.  
    - Il nome del contenitore è il nome del contenitore nel nome di archiviazione che contiene i file caricati.  
    - È possibile ottenere il token sas `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`eseguendo . 
        - Il nome di archiviazione è il nome dell'account di archiviazione che contiene i file caricati.  
        - Il nome del contenitore è il nome del contenitore nel nome di archiviazione che contiene i file caricati.  
        - La data di scadenza utc è la data UTC in cui il token di firma di accesso condiviso scadrà e il token di firma di accesso condiviso non potrà più essere usato per accedere all'account di archiviazione.

    Registrare i valori per gatewayFQDN e gatewayIP dall'output di distribuzione del modello. È inoltre necessario salvare il valore del tasto funzione per la funzione appena creata, che si trova nella scheda [Impostazioni dell'app Funzione.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Configurare DNS in modo che FQDN del certificato SSL indirizzi all'indirizzo IP del gatewayIP del passaggio precedente.

    Dopo aver creato la farm di Gateway Desktop remoto e aver apportato gli aggiornamenti DNS appropriati, è possibile utilizzarla da un lab in DevTest Labs. Le impostazioni del **nome host del gateway** e del **segreto del token del gateway** devono essere configurate per l'utilizzo dei computer gateway distribuiti. 

    > [!NOTE]
    > Se il computer lab utilizza indirizzi IP privati, deve essere presente un percorso di rete dal computer gateway al computer lab, tramite la condivisione della stessa rete virtuale o l'utilizzo di una rete virtuale con peered.

    Dopo aver configurato sia il gateway che il lab, il file di connessione creato quando l'utente del lab fa clic su **Connetti** includerà automaticamente le informazioni necessarie per connettersi tramite l'autenticazione token.     

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Servizi Desktop remoto, la documentazione di [Servizi Desktop remoto,](/windows-server/remote/remote-desktop-services/Welcome-to-rds) vedere l'articolo seguente



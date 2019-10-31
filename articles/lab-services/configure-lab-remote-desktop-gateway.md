---
title: Configurare un Lab per l'uso di Desktop remoto Gateway in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare un Lab in Azure DevTest Labs con Gateway Desktop remoto per garantire l'accesso sicuro alle macchine virtuali del Lab senza dover esporre la porta RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 0f879a6389c7a77708e8041dd8b82dc3785679fa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162626"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configurare il Lab in Azure DevTest Labs per l'uso di un Gateway Desktop remoto
In Azure DevTest Labs, è possibile configurare un Gateway Desktop remoto per il Lab per garantire l'accesso sicuro alle macchine virtuali (VM) Lab senza dover esporre la porta RDP. Il Lab fornisce una posizione centralizzata in cui gli utenti del Lab possono visualizzare e connettersi a tutte le macchine virtuali a cui hanno accesso. Il pulsante **Connetti** nella pagina **macchina virtuale** crea un file RDP specifico del computer che è possibile aprire per connettersi al computer. È possibile personalizzare ulteriormente e proteggere la connessione RDP connettendo il Lab a un Gateway Desktop remoto. 

Questo approccio è più sicuro perché l'utente del Lab esegue l'autenticazione direttamente nel computer del gateway o può usare le credenziali aziendali in un computer gateway aggiunto al dominio per connettersi ai computer. Il Lab supporta anche l'uso dell'autenticazione basata su token nel computer gateway che consente agli utenti di connettersi alle macchine virtuali del Lab senza avere la porta RDP esposta a Internet. Questo articolo illustra un esempio di come configurare un Lab che usa l'autenticazione basata su token per connettersi ai computer lab.

## <a name="architecture-of-the-solution"></a>Architettura della soluzione

![Architettura della soluzione](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. L'azione [Ottieni contenuto file RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) viene chiamata quando si seleziona il pulsante **Connetti** . 1. 
1. L'azione Ottieni contenuto file RDP richiama `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` per richiedere un token di autenticazione.
    1. `{gateway-hostname}` è il nome host del gateway specificato nella pagina **impostazioni Lab** per il lab nel portale di Azure. 
    1. `{lab-machine-name}` è il nome del computer che si sta tentando di connettere.
    1. `{port-number}` è la porta su cui deve essere effettuata la connessione. In genere questa porta è 3389. Se la macchina virtuale del Lab usa la funzionalità [IP condivisa](devtest-lab-shared-ip.md) in DevTest Labs, la porta sarà diversa.
1. Il Gateway Desktop remoto rinvia la chiamata da `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` a una funzione di Azure per generare il token di autenticazione. Il servizio DevTest Labs include automaticamente il tasto funzione nell'intestazione della richiesta. Il tasto funzione deve essere salvato nell'insieme di credenziali delle chiavi del Lab. Nome del segreto da visualizzare come chiave privata del **token del gateway** nella pagina **impostazioni Lab** per il Lab.
1. Si prevede che la funzione di Azure restituisca un token per l'autenticazione del token basata sui certificati nel computer del gateway.  
1. L'azione Ottieni contenuto file RDP restituisce quindi il file RDP completo, incluse le informazioni di autenticazione.
1. Aprire il file RDP utilizzando il programma di connessione RDP preferito. Tenere presente che non tutti i programmi di connessione RDP supportano l'autenticazione basata su token. Il token di autenticazione ha una data di scadenza, impostata dall'app per le funzioni. Eseguire la connessione alla VM Lab prima della scadenza del token.
1. Quando il computer Gateway Desktop remoto autentica il token nel file RDP, la connessione viene trasmessa al computer lab.

### <a name="solution-requirements"></a>Requisiti della soluzione
Per usare la funzionalità di autenticazione dei token di DevTest Labs, esistono alcuni requisiti di configurazione per i computer gateway, Domain Name Services (DNS) e le funzioni.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisiti per i computer Gateway Desktop remoto
- Il certificato SSL deve essere installato nel computer del gateway per gestire il traffico HTTPS. Il certificato deve corrispondere al nome di dominio completo (FQDN) del servizio di bilanciamento del carico per la farm del gateway o al nome di dominio completo (FQDN) del computer stesso se è presente un solo computer. I certificati SSL Wild Card non funzionano.  
- Un certificato di firma installato nei computer gateway. Creare un certificato di firma tramite lo script [create-SigningCertificate. ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) .
- Installare il modulo di [autenticazione collegabile](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) che supporta l'autenticazione basata su token per Gateway Desktop remoto. Un esempio di tale modulo è `RDGatewayFedAuth.msi` incluso nelle [immagini System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807). Per ulteriori informazioni su System Center, vedere la [documentazione di System Center](https://docs.microsoft.com/system-center/) e [i dettagli relativi ai prezzi](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Il server gateway è in grado di gestire le richieste effettuate ai `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`.

    Gateway-hostname è il nome di dominio completo del servizio di bilanciamento del carico della farm del gateway o il nome di dominio completo (FQDN) del computer se è presente un solo computer. Il `{lab-machine-name}` è il nome del computer lab a cui si sta tentando di connettersi e il `{port-number}` è la porta su cui verrà effettuata la connessione.  Per impostazione predefinita, questa porta è 3389.  Tuttavia, se la macchina virtuale usa la funzionalità [IP condivisa](devtest-lab-shared-ip.md) in DevTest Labs, la porta sarà diversa.
- Il modulo [Application routing request](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) per Internet Information Server (IIS) può essere usato per reindirizzare le richieste di `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` alla funzione di Azure, che gestisce la richiesta per ottenere un token per l'autenticazione.


## <a name="requirements-for-azure-function"></a>Requisiti per la funzione di Azure
La funzione di Azure gestisce la richiesta con formato `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` e restituisce il token di autenticazione basato sullo stesso certificato di firma installato nei computer gateway. Il `{function-app-uri}` è l'URI utilizzato per accedere alla funzione. Il tasto funzione viene passato automaticamente nell'intestazione della richiesta. Per una funzione di esempio, vedere [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Requisiti per la rete

- Il DNS per il nome FQDN associato al certificato SSL installato nei computer gateway deve indirizzare il traffico al computer gateway o al servizio di bilanciamento del carico della farm di computer gateway.
- Se il computer lab USA indirizzi IP privati, è necessario che sia presente un percorso di rete dal computer gateway al computer lab, tramite la condivisione della stessa rete virtuale o l'uso di reti virtuali con peering.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configurare il Lab per l'uso dell'autenticazione basata su token 
In questa sezione viene illustrato come configurare un Lab per l'utilizzo di un computer Gateway Desktop remoto che supporta l'autenticazione del token. Questa sezione non illustra come configurare una farm di Gateway Desktop remoto. Per informazioni, vedere la sezione [esempio per creare un Gateway Desktop remoto](#sample-to-create-a-remote-desktop-gateway) alla fine di questo articolo. 

Prima di aggiornare le impostazioni del Lab, archiviare la chiave necessaria per eseguire correttamente la funzione per restituire un token di autenticazione nell'insieme di credenziali delle chiavi del Lab. È possibile ottenere il valore della chiave della funzione nella pagina **Gestisci** per la funzione nell'portale di Azure. Per altre informazioni su come salvare un segreto in un insieme di credenziali delle chiavi, vedere [aggiungere un segreto a Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Salvare il nome del segreto per un uso successivo.

Per trovare l'ID dell'insieme di credenziali delle chiavi del Lab, eseguire il comando dell'interfaccia della riga di comando di Azure seguente: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configurare il Lab per l'uso dell'autenticazione del token usando la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Dall'elenco dei Lab selezionare il **Lab**.
1. Nella pagina del Lab selezionare **configurazione e criteri**.
1. Nel menu a sinistra, nella sezione **Impostazioni** , selezionare **impostazioni Lab**.
1. Nella sezione **Desktop remoto** immettere il nome di dominio completo (FQDN) o l'indirizzo IP del computer gateway Servizi Desktop remoto o della farm per il campo nome **host gateway** . Questo valore deve corrispondere al nome di dominio completo del certificato SSL usato nei computer gateway.

    ![Opzioni di desktop remoto nelle impostazioni del Lab](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Nella sezione **Desktop remoto** , per il segreto del **token del gateway** , immettere il nome del segreto creato in precedenza. Questo valore non è la chiave della funzione stessa, ma il nome del segreto nell'insieme di credenziali delle chiavi del Lab che include il tasto funzione.

    ![Segreto del token del gateway nelle impostazioni del Lab](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Salva** Modifiche.

    > [!NOTE] 
    > Facendo clic su **Salva**, si accettano le [condizioni di licenza di desktop remoto Gateway](https://www.microsoft.com/licensing/product-licensing/products). Per ulteriori informazioni su gateway remoto, vedere la pagina relativa [all'introduzione a Servizi Desktop remoto e alla](https://aka.ms/rds) [distribuzione dell'ambiente desktop remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se è preferibile configurare il Lab tramite automazione, vedere [set-DevTestLabGateway. ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) per uno script di PowerShell di esempio per impostare le impostazioni del **nome host** e del **segreto** del gateway del gateway. Il [repository GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) fornisce anche un modello di Azure Resource Manager che crea o aggiorna un Lab con le impostazioni del **nome host del gateway** e del **segreto del token del gateway** .

## <a name="configure-network-security-group"></a>Configurare un gruppo di sicurezza di rete
Per proteggere ulteriormente il Lab, è possibile aggiungere un gruppo di sicurezza di rete (NSG) alla rete virtuale usata dalle macchine virtuali del Lab. Per istruzioni su come configurare un NSG, vedere [creare, modificare o eliminare un gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md).

Di seguito è riportato un esempio di NSG che consente solo il traffico che passa per primo attraverso il gateway per raggiungere i computer lab. L'origine in questa regola è l'indirizzo IP del computer del gateway singolo o l'indirizzo IP del servizio di bilanciamento del carico davanti ai computer del gateway.

![Gruppo di sicurezza di rete-regole](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Esempio per la creazione di un Gateway Desktop remoto

> [!NOTE] 
> Usando i modelli di esempio, si accettano le [condizioni di licenza del Gateway Desktop remoto](https://www.microsoft.com/licensing/product-licensing/products). Per ulteriori informazioni su gateway remoto, vedere la pagina relativa [all'introduzione a Servizi Desktop remoto e alla](https://aka.ms/rds) [distribuzione dell'ambiente desktop remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

Il [repository GitHub Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) fornisce alcuni esempi che consentono di configurare le risorse necessarie per l'uso dell'autenticazione basata su token e di Gateway Desktop remoto con DevTest Labs. Questi esempi includono Azure Resource Manager modelli per i computer gateway, le impostazioni Lab e l'app per le funzioni.

Per configurare una soluzione di esempio per la farm di Gateway Desktop remoto, attenersi alla seguente procedura.

1. Creare un certificato di firma.  Eseguire [create-SigningCertificate. ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Salvare l'identificazione personale, la password e la codifica Base64 del certificato creato.
2. Ottenere un certificato SSL. Il nome FQDN associato al certificato SSL deve essere per il dominio controllato. Salvare l'identificazione personale, la password e la codifica Base64 per questo certificato. Per ottenere l'identificazione personale tramite PowerShell, usare i comandi seguenti.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Per ottenere la codifica Base64 usando PowerShell, usare il comando seguente.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Scaricare i file da [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway).

    Il modello richiede l'accesso ad altri modelli di Gestione risorse e risorse correlate nello stesso URI di base. Copiare tutti i file da [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) e RDGatewayFedAuth. msi a un contenitore BLOB in un account di archiviazione.  
4. Distribuire **file azuredeploy. JSON** da [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Il modello accetta i parametri seguenti:
    - Con valori AdminUsername: obbligatorio.  Nome utente dell'amministratore per i computer gateway.
    - adminPassword: obbligatorio. Password per l'account amministratore per i computer gateway.
    - instanceCount: numero di computer gateway da creare.  
    - alwaysOn: indica se lasciare o meno l'app funzioni di Azure creata in uno stato di riscaldamento. Mantenendo l'app funzioni di Azure si eviteranno ritardi quando gli utenti tentano di connettersi alla macchina virtuale del Lab, ma hanno implicazioni sui costi.  
    - tokenLifetime: periodo di tempo in cui il token creato sarà valido. Il formato è HH: MM: SS.
    - sslCertificate: codifica Base64 del certificato SSL per il computer del gateway.
    - sslCertificatePassword: la password del certificato SSL per il computer del gateway.
    - sslCertificateThumbprint: identificazione personale del certificato per l'identificazione nell'archivio certificati locale del certificato SSL.
    - signCertificate: codifica Base64 per la firma del certificato per il computer gateway.
    - signCertificatePassword: password per la firma del certificato per il computer del gateway.
    - signCertificateThumbprint: identificazione personale del certificato per l'identificazione nell'archivio certificati locale del certificato di firma.
    - _artifactsLocation: percorso URI in cui è possibile trovare tutte le risorse di supporto. Questo valore deve essere un UIR completo, non un percorso relativo.
    - _artifactsLocationSasToken: token di firma di accesso condiviso usato per accedere alle risorse di supporto, se il percorso è un account di archiviazione di Azure.

    Il modello può essere distribuito usando l'interfaccia della riga di comando di Azure usando il comando seguente:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Di seguito sono riportate le descrizioni dei parametri:

    - È possibile ottenere {storage-account-endpoint} eseguendo `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  {Storage-Acct-Name} è il nome dell'account di archiviazione che include i file caricati.  
    - {Container-Name} è il nome del contenitore in {Storage-Acct-Name} che include i file caricati.  
    - È possibile ottenere {SAS-token} eseguendo `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - {Storage-Acct-Name} è il nome dell'account di archiviazione che include i file caricati.  
        - {Container-Name} è il nome del contenitore in {Storage-Acct-Name} che include i file caricati.  
        - {UTC-Expire-Date} è la data, in formato UTC, in cui il token SAS scadrà e il token di firma di accesso condiviso non potrà più essere usato per accedere all'account di archiviazione.

    Registrare i valori per gatewayFQDN e gatewayIP dall'output della distribuzione del modello. Sarà anche necessario salvare il valore della chiave funzione per la funzione appena creata, disponibile nella scheda [impostazioni app](../azure-functions/functions-how-to-use-azure-function-app-settings.md) per le funzioni.
5. Configurare DNS in modo che il nome di dominio completo del certificato SSL venga indirizzato all'indirizzo IP di gatewayIP dal passaggio precedente.

    Dopo la creazione della farm Desktop remoto Gateway e l'aggiornamento del DNS appropriato, è possibile usarlo in un Lab in DevTest Labs. Il **nome host del gateway** e le impostazioni del **segreto del token** del gateway devono essere configurate per usare i computer gateway distribuiti. 

    > [!NOTE]
    > Se il computer lab USA indirizzi IP privati, è necessario che sia presente un percorso di rete dal computer gateway al computer lab, tramite la condivisione della stessa rete virtuale o con una rete virtuale con peering.

    Una volta configurati sia il gateway che il Lab, il file di connessione creato quando l'utente del Lab fa clic su **Connetti** includerà automaticamente le informazioni necessarie per la connessione tramite l'autenticazione del token.     

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per altre informazioni su Servizi Desktop remoto: [Servizi Desktop remoto documentazione](/windows-server/remote/remote-desktop-services/Welcome-to-rds)



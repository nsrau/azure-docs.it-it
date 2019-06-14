---
title: Configurare un lab per l'uso di Gateway Desktop remoto in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare un lab in Azure DevTest Labs con un gateway desktop remoto per garantire l'accesso sicuro per le macchine virtuali del lab senza dover esporre la porta RDP.
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
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079002"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configura il lab in Azure DevTest Labs per usare un gateway desktop remoto
In Azure DevTest Labs, è possibile configurare un gateway desktop remoto per l'ambiente lab garantire l'accesso sicuro alle macchine virtuali lab (macchine virtuali) senza dover esporre la porta RDP. Il lab offre una posizione centrale per gli utenti del lab visualizzare e connettersi a tutte le macchine virtuali di che cui hanno accesso. Il **Connect** pulsante il **macchina virtuale** pagina Crea un file RDP specifiche del computer che è possibile aprire per connettersi al computer. È inoltre possibile personalizzare e proteggere la connessione RDP tramite la connessione nel lab per un gateway desktop remoto. 

Questo approccio è più sicuro perché l'utente del lab autentica direttamente al computer del gateway o è possibile usare le credenziali aziendali in un computer aggiunto al dominio gateway per connettersi alle macchine. Il lab supporta anche l'uso dell'autenticazione token al computer del gateway che consente agli utenti di connettersi alle macchine virtuali di lab senza che sia la porta RDP esposta a internet. Questo articolo illustra un esempio su come configurare un ambiente lab che utilizza l'autenticazione basata su token per connettersi al computer nel laboratorio.

## <a name="architecture-of-the-solution"></a>Architettura della soluzione

![Architettura della soluzione](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Il [il contenuto del file RDP ottenere](/rest/api/dtl/virtualmachines/getrdpfilecontents) azione viene chiamata quando si seleziona il **Connect** button.1. 
1. Richiama l'azione di contenuto di file RDP ottenere `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` per richiedere un token di autenticazione.
    1. `{gateway-hostname}` è il nome host di gateway specificato nella **delle impostazioni di Lab** pagina per l'ambiente lab nel portale di Azure. 
    1. `{lab-machine-name}` è il nome del computer in cui si sta provando a connettersi.
    1. `{port-number}` è la porta in cui deve essere effettuata la connessione. In genere questa porta è 3389. Se il lab VM Usa il [condiviso IP](devtest-lab-shared-ip.md) funzionalità in DevTest Labs, la porta sarà diverso.
1. Il gateway desktop remoto rinvia la chiamata da `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` a una funzione di Azure per generare il token di autenticazione. Il servizio DevTest Labs include automaticamente la chiave di funzione nell'intestazione della richiesta. La chiave di funzione deve essere salvato nell'insieme di credenziali delle chiavi del lab. Il nome per tale chiave privata da visualizzare come **segreto del token Gateway** nel **impostazioni Lab** pagina per l'ambiente lab.
1. La funzione di Azure deve restituire un token per l'autenticazione di token basata su certificati in computer del gateway.  
1. Il file RDP ottenere contenuto azione, quindi restituisce il file RDP completo, incluse le informazioni di autenticazione.
1. Si apre il file RDP tramite il programma di connessione RDP preferito. Tenere presente che non tutti i programmi di connessione RDP supportano l'autenticazione tramite token. Il token di autenticazione hanno una data di scadenza, l'impostazione di app per le funzioni. Stabilire la connessione alla macchina virtuale lab prima che il token scade.
1. Una volta che il computer gateway desktop remoto autentica il token nel file RDP, la connessione viene inoltrata nel computer di laboratorio.

### <a name="solution-requirements"></a>Requisiti della soluzione
Per usare la funzionalità di autenticazione basata su token DevTest Labs, esistono alcuni requisiti di configurazione per il computer gateway, servizi di nomi di dominio (DNS) e funzioni.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisiti per le macchine di gateway desktop remoto
- Certificato SSL deve essere installato nel computer del gateway per gestire il traffico HTTPS. Se è presente un solo computer, il certificato deve corrispondere il nome di dominio completo (FQDN) del servizio di bilanciamento del carico per la farm di gateway o il nome di dominio completo della macchina stessa. I certificati SSL con caratteri jolly non funzionano.  
- Un certificato di firma installato in macchine virtuali gateway. Creare un certificato di firma usando [Create SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) script.
- Installare il [Pluggable Authentication](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) modulo che supporta l'autenticazione basata su token per il gateway desktop remoto. È un esempio di questo tipo di un modulo `RDGatewayFedAuth.msi` che include [le immagini di System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807). Per altre informazioni su System Center, vedere [documentazione di System Center](https://docs.microsoft.com/system-center/) e [i dettagli sui prezzi](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Il server gateway può gestire le richieste effettuate al `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`.

    Il gateway-hostname è il nome FQDN del servizio di bilanciamento del carico della farm di gateway o il nome FQDN del computer stesso se è presente un solo computer. Il `{lab-machine-name}` è il nome del computer di laboratorio che si sta provando a connettersi, e il `{port-number}` è la porta su cui verrà effettuata la connessione.  Per impostazione predefinita, questa porta è 3389.  Tuttavia, se la macchina virtuale Usa il [condiviso IP](devtest-lab-shared-ip.md) funzionalità in DevTest Labs, la porta sarà diverso.
- Il [richiesta di Routing dell'applicazione](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) modulo per Internet Information Server (IIS) può essere usato per reindirizzare `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` richieste per la funzione di azure, che gestisce la richiesta per ottenere un token per l'autenticazione.


## <a name="requirements-for-azure-function"></a>Requisiti per la funzione di Azure
Funzioni di Azure gestisce richieste con formato di `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` e restituisce il token di autenticazione basato sulla stessa firma del certificato installato nel computer gateway. Il `{function-app-uri}` è l'uri usato per accedere alla funzione. La chiave di funzione viene passato automaticamente nell'intestazione della richiesta. Per un esempio di funzione, vedere [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Requisiti per la rete

- Il DNS per il nome di dominio completo associato al certificato SSL installato nel computer gateway deve indirizzare il traffico al computer del gateway o il bilanciamento del carico della farm di computer gateway.
- Se il computer di laboratorio Usa indirizzi IP privati, deve esistere un percorso di rete dal computer del gateway nel computer di laboratorio, tramite la condivisione della stessa rete virtuale o con reti virtuali con peering.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configura il lab per usare l'autenticazione tramite token 
Questa sezione illustra come configurare un lab per usare un computer gateway desktop remoto che supporta l'autenticazione basata su token. In questa sezione non illustra come configurare una farm di gateway desktop remoto stesso. Per ulteriori informazioni, vedere la [esempio per creare un gateway desktop remoto](#sample-to-create-a-remote-desktop-gateway) sezione alla fine di questo articolo. 

Prima di aggiornare le impostazioni di lab, archiviare la chiave necessaria per eseguire correttamente la funzione per restituire un token di autenticazione nell'insieme di credenziali delle chiavi del lab. È possibile ottenere il valore della chiave funzioni **Gestisci** pagina per la funzione nel portale di Azure. Per altre informazioni su come salvare un segreto in un insieme di credenziali delle chiavi, vedere [aggiungere un segreto all'insieme di credenziali chiave](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Salvare il nome del segreto per un uso successivo.

Per trovare l'ID dell'insieme di credenziali delle chiavi del lab, eseguire il comando di Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configura il lab per usare l'autenticazione basata su token attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco di Lab, selezionare i **lab**.
1. Nella pagina del lab, selezionare **configurazione e criteri**.
1. Nel menu a sinistra, nelle **le impostazioni** sezione, selezionare **impostazioni Lab**.
1. Nel **desktop remoto** sezione, immettere il nome di dominio completo (FQDN) o l'indirizzo IP del computer del gateway di servizi desktop remoto o l'azienda per il **nome host di Gateway** campo. Questo valore deve corrispondere al FQDN del certificato SSL usato nel computer gateway.

    ![Opzioni desktop remoto nelle impostazioni di lab](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Nel **desktop remoto** sezione, per **token Gateway** segreto, immettere il nome del segreto creato in precedenza. Questo valore non è la chiave di funzione stessa, ma il nome del segreto nell'insieme credenziali chiavi del lab che contiene la chiave di funzione.

    ![Segreto del token gateway nelle impostazioni di lab](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Salvare** le modifiche.

    > [!NOTE] 
    > Facendo clic **salvare**, si accettano [condizioni di licenza del Gateway di Desktop remoto](https://www.microsoft.com/licensing/product-licensing/products). Per altre informazioni sui gateway remoto, vedere [Benvenuti in Servizi Desktop remoto](https://aka.ms/rds) e [distribuire l'ambiente di desktop remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se si desidera utilizzare la configurazione di lab tramite l'automazione, vedere [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) per uno script di PowerShell di esempio impostare **nome host di gateway** e **segreto del token gateway**delle impostazioni. Il [repository GitHub di Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) fornisce inoltre un modello di Azure Resource Manager che crea o aggiorna un ambiente con la **nome host di gateway** e **segreto del token gateway**delle impostazioni.

## <a name="configure-network-security-group"></a>Configurare il gruppo di sicurezza di rete
Per proteggere ulteriormente il lab, un gruppo di sicurezza di rete (NSG) può essere aggiunti alla rete virtuale usata dalle macchine virtuali di lab. Per istruzioni su come configurare un gruppo di sicurezza, vedere [crea, modifica o elimina un gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md).

Di seguito è riportato un esempio NSG che consenta solo il traffico che passa innanzitutto attraverso il gateway per raggiungere i computer nel laboratorio. L'origine in questa regola è l'indirizzo IP del computer del gateway singolo o l'indirizzo IP del servizio di bilanciamento del carico davanti i computer del gateway.

![Gruppo di sicurezza di rete - regole](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Esempio per creare un gateway desktop remoto

> [!NOTE] 
> Usando i modelli di esempio, si accettano [condizioni di licenza del Gateway di Desktop remoto](https://www.microsoft.com/licensing/product-licensing/products). Per altre informazioni sui gateway remoto, vedere [Benvenuti in Servizi Desktop remoto](https://aka.ms/rds) e [distribuire l'ambiente di desktop remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

Il [repository GitHub di Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) fornisce alcuni esempi per il programma di installazione le risorse necessarie per usare l'autenticazione tramite token e gateway desktop remoto con DevTest Labs. Questi esempi includono i modelli di Azure Resource Manager per computer gateway, le impostazioni di lab e app per le funzioni.

Seguire questi passaggi per configurare una soluzione di esempio per la farm di gateway desktop remoto.

1. Creare un certificato di firma.  Eseguire [SigningCertificate.ps1 creare](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Salvare l'identificazione personale, la password e la codifica Base64 del certificato creato.
2. Ottenere un certificato SSL. Nome di dominio completo associato al certificato SSL deve essere per il dominio che è controllare. Salvare l'identificazione personale, la password e la codifica Base64 per questo certificato. Per ottenere l'identificazione personale tramite PowerShell, usare i comandi seguenti.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Per ottenere la codifica Base64 con PowerShell, usare il comando seguente.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Scaricare i file dalla [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway).

    Il modello richiede l'accesso per alcuni altri modelli di Resource Manager e le risorse correlate in corrispondenza dell'URI di base stessi. Copiare tutti i file dalla [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) e RDGatewayFedAuth.msi in un contenitore blob nell'account di archiviazione.  
4. Distribuire **azuredeploy. JSON** dalla [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Il modello accetta i parametri seguenti:
    - adminUsername: obbligati.  Nome utente amministratore per i computer del gateway.
    - adminPassword-obbligati. Password per l'account amministratore per i computer del gateway.
    - instanceCount: numero di macchine di gateway da creare.  
    - alwaysOn: indica se mantenere l'app funzioni di Azure creata in uno stato a caldo o No. Mantenere l'app funzioni di Azure verrà evitare ritardi quando gli utenti prima di tutto tentano di connettersi al proprio lab della macchina virtuale, ma presenta le implicazioni di costi.  
    - tokenLifetime – il periodo di tempo che è valido il token creato. Il formato è HH: mm:.
    - sslCertificate – Base64 la codifica del certificato SSL per il computer gateway.
    - sslCertificatePassword: la password del certificato SSL per il computer gateway.
    - sslCertificateThumbprint - identificazione digitale del certificato per l'identificazione nell'archivio certificati locale del certificato SSL.
    - signCertificate – Base64 di codifica per la firma del certificato per il computer gateway.
    - signCertificatePassword: la password per la firma del certificato per il computer gateway.
    - signCertificateThumbprint - identificazione digitale del certificato per l'identificazione nell'archivio certificati locale del certificato di firma.
    - artifactslocation – percorso URI in cui sono presenti tutte le risorse di supporto. Questo valore deve essere un URI completo, non un percorso relativo.
    - artifactslocationsastoken – token the (firma di accesso) usato per accedere alle risorse di supporto, se il percorso è un account di archiviazione di Azure.

    Il modello può essere distribuito tramite la CLI di Azure usando il comando seguente:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    Ecco le descrizioni dei parametri:

    - {Storage-account-endpoint} può essere ottenuto eseguendo `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  {Storage-acct-name} è il nome dell'account di archiviazione che contiene i file che è stato caricato.  
    - Il {nome-contenitore} è il nome del contenitore in {storage-acct-name} che contiene i file che è stato caricato.  
    - Il {token di firma di accesso condiviso} può essere ottenuto eseguendo `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - {Storage-acct-name} è il nome dell'account di archiviazione che contiene i file che è stato caricato.  
        - Il {nome-contenitore} è il nome del contenitore in {storage-acct-name} che contiene i file che è stato caricato.  
        - La {utc--data di scadenza} è la data nel formato UTC, in corrispondenza del quale scadrà il token di firma di accesso condiviso e il token di firma di accesso condiviso non è più utilizzabile per accedere all'account di archiviazione.

    Registrare i valori per gatewayFQDN e gatewayIP dall'output della distribuzione modello. Sarà inoltre necessario salvare il valore della chiave di funzione per la funzione appena creata, che può trovarsi nel [impostazioni di app per le funzioni](../azure-functions/functions-how-to-use-azure-function-app-settings.md) scheda.
5. Configurare DNS in modo che tale certificato FQDN di SSL viene indirizzato all'indirizzo IP del gatewayIP dal passaggio precedente.

    Dopo aver creata la farm di Gateway Desktop remoto e vengono apportati appropriato gli aggiornamenti DNS, è pronto per essere usato per un lab in DevTest Labs. Il **gateway nomehost** e **segreto del token gateway** impostazioni devono essere configurate per usare le macchine virtuali gateway è stato distribuito. 

    > [!NOTE]
    > Se il computer di laboratorio Usa indirizzi IP privati, deve esistere un percorso di rete dal computer del gateway nel computer di laboratorio, tramite la condivisione della stessa rete virtuale o tramite una rete virtuale con peering.

    Dopo aver configurati i gateway e lab, il file di connessione creato quando l'utente del lab fa clic sui **Connect** includerà automaticamente le informazioni necessarie per connettersi usando l'autenticazione tramite token.     

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per altre informazioni su Servizi Desktop remoto: [Documentazione di Servizi Desktop remota](/windows-server/remote/remote-desktop-services/Welcome-to-rds)



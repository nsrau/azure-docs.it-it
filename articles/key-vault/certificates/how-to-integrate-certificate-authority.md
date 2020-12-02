---
title: Integrazione di Key Vault con l'autorità di certificazione DigiCert
description: Come integrare Key Vault con l'autorità di certificazione DigiCert
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4d29d7401cf944e8d999db847ce2e1266169ea34
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448407"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrazione di Key Vault con l'autorità di certificazione DigiCert

Azure Key Vault consente di effettuare facilmente il provisioning, la gestione e la distribuzione di certificati digitali per la rete e di abilitare comunicazioni sicure per le applicazioni. Un certificato digitale è una credenziale elettronica che serve a stabilire la prova dell'identità in una transazione elettronica. 

Gli utenti di Azure Key Vault possono generare certificati DigiCert direttamente dalla loro istanza del servizio. Key Vault assicura la gestione end-to-end del ciclo di vita dei certificati emessi da DigiCert tramite una partnership di trust stabilita con l'autorità di certificazione DigiCert.

Per informazioni più generali sui certificati, vedere [Certificati di Azure Key Vault](./about-certificates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, sono necessarie le risorse seguenti.
* Un insieme di credenziali delle chiavi. È possibile usare un insieme di credenziali delle chiavi esistente o crearne uno nuovo seguendo le procedure illustrate in una di queste guide di avvio rapido:
   - [Creare un insieme di credenziali delle chiavi con l'interfaccia della riga di comando di Azure](../general/quick-create-cli.md)
   - [Creare un insieme di credenziali delle chiavi con Azure PowerShell](../general/quick-create-powershell.md)
   - [Creare un insieme di credenziali delle chiavi con il portale di Azure](../general/quick-create-portal.md).
*   È necessario attivare l'account CertCentral di DigiCert. [Iscriversi](https://www.digicert.com/account/signup/) per ricevere un account CertCentral.
*   Autorizzazioni a livello di amministratore negli account.


### <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di avere a portata di mano le informazioni seguenti dell'account CertCentral di DigiCert:
-   ID account CertCentral
-   ID organizzazione
-   Chiave API

## <a name="adding-certificate-authority-in-key-vault"></a>Aggiunta dell'autorità di certificazione in Key Vault 
Dopo aver raccolto le informazioni sopra riportate dall'account CertCentral di DigiCert, è possibile aggiungere DigiCert all'elenco di autorità di certificazione nell'insieme di credenziali delle chiavi.

### <a name="azure-portal"></a>Portale di Azure

1.  Per aggiungere l'autorità di certificazione DigiCert, passare all'insieme di credenziali delle chiavi in cui inserirla. 
2.  Nella pagina delle proprietà di Key Vault selezionare **Certificati**.
3.  Selezionare la scheda **Autorità di certificazione**. ![Selezionare Autorità di certificazione](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Selezionare l'opzione **Aggiungi**.
 ![Aggiungere autorità di certificazione](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Nella schermata **Crea un'autorità di certificazione** scegliere i valori seguenti:
    -   **Name**: aggiungere un nome di autorità emittente identificabile. Esempio: DigicertCA
    -   **Provider**: scegliere DigiCert dal menu.
    -   **ID account**: immettere l'ID dell'account CertCentral di DigiCert
    -   **Password account**: immettere la chiave API generata nell'account CertCentral di DigiCert
    -   **ID organizzazione**: immettere l'ID organizzazione raccolto dall'account CertCentral di DigiCert 
    -   Fare clic su **Crea**.
   
6.  Si noterà che DigicertCA è stata aggiunta all'elenco delle autorità di certificazione.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell viene usato per creare e gestire le risorse di Azure con comandi o script. Azure ospita Azure Cloud Shell, un ambiente di shell interattivo che è possibile usare tramite il portale di Azure nel browser.

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Digitare `$PSVersionTable.PSVersion` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Creare un **gruppo di risorse**

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Creare un **insieme di credenziali delle chiavi**

Per l'insieme di credenziali delle chiavi è necessario usare un nome univoco. In questa guida, il nome è "Contoso-Vaultname".

- **Nome dell'insieme di credenziali** Contoso-Vaultname.
- **Nome del gruppo di risorse** ContosoResourceGroup.
- **Località** Stati Uniti orientali.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Definire le variabili per le informazioni raccolte dall'account CertCentral di DigiCert.

- Definire la variabile per **ID account**
- Definire la variabile per **ID organizzazione**
- Definire la variabile per **Chiave API**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Impostare **Autorità emittente**. Nell'insieme di credenziali delle chiavi verrà aggiunta DigiCert come autorità di certificazione. Per altre informazioni sui parametri, [vedere qui](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Impostazione dei criteri per il certificato ed emissione del certificato** da DigiCert direttamente all'interno di Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Il certificato è stato emesso correttamente dall'autorità di certificazione DigiCert all'interno dell'istanza di Key Vault specificata tramite questa integrazione.

## <a name="troubleshoot"></a>Risolvere problemi

Se il certificato emesso ha lo stato 'disabilitato' nel portale di Azure, vedere **Operazione relativa al certificato** per esaminare il messaggio di errore di DigiCert per il certificato.

 ![Operazione relativa al certificato](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Per altre informazioni, vedere le [operazioni relative ai certificati nell'articolo di riferimento all'API REST di Key Vault](/rest/api/keyvault). Per informazioni sulla definizione delle autorizzazioni, vedere [Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate) (Insiemi di credenziali - Create o Update) e [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy) (Insiemi di credenziali - Update Access Policy).

## <a name="frequently-asked-questions"></a>Domande frequenti

- È possibile generare un certificato con caratteri jolly DigiCert tramite KeyVault? 
   Sì. Dipende dal modo in cui è stato configurato l'account DigiCert.
- Come si crea un certificato **OV-SSL o EV-SSL** con DigiCert? 
   Key Vault supporta la creazione di certificati OV-SSL ed EV-SSL. Durante la creazione di un certificato, fare clic su Configurazione avanzata dei criteri, quindi specificare il Tipo di certificato. I valori supportati sono: OV-SSL, EV-SSL
   
   È possibile creare questo tipo di certificato in Key Vault se l'account Digicert lo consente. Per questo tipo di certificato la convalida viene eseguita da DigiCert. Se la convalida non riesce, il team di supporto di DigiCert dovrebbe fornire assistenza. È possibile aggiungere altre informazioni durante la creazione di un certificato definendole in subjectName.

Esempio
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- È previsto un ritardo durante la creazione di un certificato DigiCert tramite l'integrazione rispetto all'acquisizione di un certificato direttamente tramite DigiCert?
   No. Durante la creazione di un certificato, il processo di verifica richiede più tempo rispetto agli altri e tale verifica dipende dal processo seguito da DigiCert.


## <a name="next-steps"></a>Passaggi successivi

- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
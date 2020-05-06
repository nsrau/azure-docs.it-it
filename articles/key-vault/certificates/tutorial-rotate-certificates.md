---
title: 'Esercitazione: Aggiornamento della frequenza di rotazione automatica del certificato in Key Vault | Microsoft Docs'
description: Esercitazione che illustra come aggiornare la frequenza di rotazione automatica di un certificato in Azure Key Vault usando il portale di Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82107558"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Esercitazione: Configurazione della rotazione automatica del certificato in Key Vault

Azure Key Vault consente di effettuare facilmente il provisioning, la gestione e la distribuzione di certificati digitali. Potrebbero essere certificati SSL/TLS privati e pubblici firmati da un'autorità di certificazione o certificati autofirmati. Key Vault può anche richiedere e rinnovare i certificati attraverso partnership con le autorità di certificazione, fornendo una soluzione affidabile per la gestione del ciclo di vita dei certificati. In questa esercitazione si aggiorneranno gli attributi del certificato: periodo di validità, frequenza di rotazione automatica, autorità di certificazione. Per altre informazioni su Key Vault, vedere la relativa [panoramica](../general/overview.md).

L'esercitazione illustra come:

> [!div class="checklist"]
> * Gestire un certificato usando il portale di Azure
> * Aggiungere un account del provider dell'autorità di certificazione
> * Aggiornare il periodo di validità del certificato
> * Aggiornare la frequenza di rotazione automatica del certificato
> * Aggiornare gli attributi del certificato con Azure PowerShell


Prima di iniziare, leggere i [concetti di base di Key Vault](../general/basic-concepts.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

Creare o selezionare un Key Vault esistente per eseguire le operazioni. [(Passaggi per creare un Key Vault).](../quick-create-portal.md) In questo esempio il nome dell'insieme di credenziali è **Example-Vault**. 

![Output dopo la creazione dell'istanza di Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Creare un certificato in Key Vault

Creare o importare un certificato nell'insieme di credenziali. [(Passaggi per creare un certificato in Key Vault).](../quick-create-portal.md) In questo caso, viene usato un certificato denominato **ExampleCertificate**.

> [!NOTE]
> In Azure Key Vault, gli attributi del ciclo di vita di un certificato possono essere aggiornati sia al momento della creazione del certificato sia dopo che è stato creato. 
## <a name="updating-certificates-life-cycle-attributes"></a>Aggiornamento degli attributi del ciclo di vita del certificato

Un certificato creato nel Key Vault può essere: 
- Un certificato autofirmato
- Un certificato creato con un'autorità di certificazione partner di Key Vault
- Un certificato con un'autorità di certificazione non partner di Key Vault

Le autorità di certificazione seguenti sono attualmente provider partner di Key Vault:
- DigiCert: Key Vault offre certificati TLS/SSL OV con DigiCert.
- GlobalSign: Key Vault offre certificati TLS/SSL OV con GlobalSign.

Azure Key Vault esegue la rotazione automatica dei certificati attraverso partnership con le autorità di certificazione. Attraverso tale partnership consolidata, Key Vault richiede e rinnova automaticamente i certificati. Pertanto, la **funzionalità di rotazione automatica non è applicabile per i certificati creati con le autorità di certificazione che non sono partner di Key Vault.** 

> [!NOTE]
> Un amministratore account per un provider dell'autorità di certificazione crea le credenziali che verranno usate da Key Vault per la creazione, il rinnovo e l'uso di certificati TLS/SSL tramite Key Vault.
![Autorità di certificazione](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>Aggiornamento degli attributi del ciclo di vita del certificato al momento della creazione del certificato

1. Nella pagina delle proprietà di Key Vault selezionare **Certificati**.
2. Fare clic su **Genera/Importa**.
3. Nella schermata **Crea un certificato** aggiornare i valori seguenti:
    

    - **Periodo di validità**: immettere il valore (in mesi). La creazione di certificati di breve durata è una procedura di sicurezza consigliata. Per impostazione predefinita, il valore di validità di un certificato appena creato è 12 mesi.
    - **Tipo di azione relativa alla durata**: selezionare l'azione di rinnovo automatico e di avviso del certificato. In base alla selezione, aggiornare la 'percentuale della durata' o il 'numero di giorni prima della scadenza'. Per impostazione predefinita, il rinnovo automatico di un certificato è impostato sull'80% della durata.<br> Nel menu a discesa selezionare l'opzione:

    |  Rinnova automaticamente in un determinato momento| Invia un messaggio di posta elettronica a tutti i contatti in un determinato momento |
    |-----------|------|
    |Selezionando questa opzione si attiverà la rotazione automatica | Selezionando questa opzione non si attiva la rotazione automatica ma vengono solo avvisati i contatti|
        


4. Fare clic su **Crea**.

![Ciclo di vita del certificato](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Aggiornamento degli attributi del ciclo di vita del certificato archiviato

1. Selezionare il Key Vault.
2. Nella pagina delle proprietà di Key Vault selezionare **Certificati**.
3. Seleziona il certificato da aggiornare. In questo caso, viene usato un certificato denominato **ExampleCertificate**.
4. Selezionare **Criterio di rilascio** nella barra dei menu superiore.

![Proprietà del certificato](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. Nella schermata **Criterio di rilascio** aggiornare i valori seguenti:
- **Periodo di validità**: aggiornare il valore (in mesi).
- **Tipo di azione relativa alla durata**: selezionare l'azione di rinnovo automatico e di avviso del certificato. In base alla selezione, aggiornare la 'percentuale della durata' o il 'numero di giorni prima della scadenza'. 

![Proprietà del certificato](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Fare clic su **Save**.

> [!IMPORTANT]
> La modifica del tipo di azione relativa alla durata per un certificato registrerà immediatamente le modifiche per i certificati esistenti.


### <a name="updating-certificates-attributes-using-powershell"></a>Aggiornamento degli attributi del certificato con Azure PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Per modificare i criteri di rinnovo per un elenco di certificati, immettere File.csv contenente VaultName,CertName <br/>
>  vault1,Cert1 <br/>
>  vault2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Per altre informazioni sui parametri, vedere [qui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni relative a Key Vault si basano su questa guida introduttiva. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'istanza di Key Vault e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiornato il ciclo di vita di un certificato. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

Altre informazioni sulla [Gestione della creazione dei certificati in Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Vedere [Panoramica di Azure Key Vault](../general/overview.md)
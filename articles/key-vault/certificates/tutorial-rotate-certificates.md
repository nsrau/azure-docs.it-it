---
title: Esercitazione - Aggiornamento della frequenza di rotazione automatica dei certificati in Key Vault | Microsoft Docs
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
ms.openlocfilehash: eeceb1279579055bfff33f0a4413f0798418faed
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201523"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Esercitazione: Configurare la rotazione automatica dei certificati in Key Vault

Azure Key Vault consente di effettuare facilmente il provisioning, la gestione e la distribuzione di certificati digitali. Questi possono essere certificati SSL (Secure Sockets Layer)/TLS (Transport Layer Security) privati e pubblici firmati da un'autorità di certificazione (CA) oppure certificati autofirmati. Key Vault può anche richiedere e rinnovare i certificati tramite partnership con autorità di certificazione, offrendo una soluzione affidabile per la gestione del ciclo di vita dei certificati. In questa esercitazione si aggiorneranno il periodo di validità, la frequenza di rotazione automatica e gli attributi dell'autorità di certificazione del certificato.

L'esercitazione illustra come:

> [!div class="checklist"]
> * Gestire un certificato con il portale di Azure.
> * Aggiungere un account di un provider CA.
> * Aggiornare il periodo di validità del certificato.
> * Aggiornare la frequenza di rotazione automatica del certificato.
> * Aggiornare gli attributi del certificato con Azure PowerShell.

Prima di iniziare, leggere i [concetti di base di Key Vault](../general/basic-concepts.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

Per eseguire le operazioni, creare un insieme di credenziali delle chiavi o selezionarne uno esistente. Vedere la [procedura per creare un insieme di credenziali delle chiavi](../quick-create-portal.md). In questo esempio, il nome dell'insieme di credenziali delle chiavi è **Example-Vault**.

![Output al termine della creazione dell'insieme di credenziali delle chiavi](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Creare un certificato in Key Vault

Creare un certificato o importarne uno nell'insieme di credenziali delle chiavi. Vedere la [procedura per creare un certificato in Key Vault](../quick-create-portal.md). In questo caso si userà un certificato denominato **ExampleCertificate**.

## <a name="update-certificate-lifecycle-attributes"></a>Aggiornare gli attributi del ciclo di vita del certificato

In Azure Key Vault, è possibile aggiornare gli attributi del ciclo di vita di un certificato sia prima che dopo la creazione del certificato.

Un certificato creato in Key Vault può essere:

- Un certificato autofirmato.
- Un certificato creato con un'autorità di certificazione partner di Key Vault.
- Un certificato con un'autorità di certificazione non partner di Key Vault.

Attualmente, i provider partner di Key Vault sono le autorità di certificazione seguenti.

- DigiCert: Key Vault offre certificati TLS/SSL OV.
- GlobalSign: Key Vault offre certificati TLS/SSL OV.

Key Vault esegue la rotazione automatica dei certificati tramite le partnership stabilite con le autorità di certificazione. Dato che Key Vault richiede e rinnova automaticamente i certificati tramite la partnership, la funzionalità di rotazione automatica non è applicabile per i certificati creati con autorità di certificazione non partner di Key Vault.

> [!NOTE]
> Un amministratore account per un provider CA crea le credenziali usate da Key Vault per la creazione, il rinnovo e l'uso di certificati TLS/SSL.
![Autorità di certificazione](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Aggiornare gli attributi del ciclo di vita del certificato al momento della creazione

1. Nella pagina delle proprietà di Key Vault selezionare **Certificati**.
1. Selezionare **Genera/Importa**.
1. Nella schermata **Crea un certificato** aggiornare i valori seguenti.

   - **Periodo di validità**: immettere il valore (in mesi). La creazione di certificati di breve durata è una procedura di sicurezza consigliata. Per impostazione predefinita, il valore di validità di un certificato appena creato è 12 mesi.
   - **Tipo di azione relativa alla durata**: selezionare l'azione di rinnovo automatico e di avviso del certificato e quindi aggiornare **Percentuale della durata** o **Numero di giorni prima della scadenza**. Per impostazione predefinita, il rinnovo automatico di un certificato è impostato sull'80% della durata. Selezionare una delle opzioni seguenti nel menu a discesa.

        |  Rinnova automaticamente in un determinato momento| Invia un messaggio di posta elettronica a tutti i contatti in un determinato momento |
        |-----------|------|
        |Selezionando questa opzione *si attiverà* la rotazione automatica. | Selezionando questa opzione *non* verrà eseguita la rotazione automatica e verranno solo avvisati i contatti.|

1. Selezionare **Create** (Crea).

![Ciclo di vita del certificato](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Aggiornare gli attributi del ciclo di vita di un certificato archiviato

1. Selezionare l'insieme di credenziali delle chiavi.
1. Nella pagina delle proprietà di Key Vault selezionare **Certificati**.
1. Selezionare il certificato da aggiornare. In questo caso si userà un certificato denominato **ExampleCertificate**.
1. Selezionare **Criterio di rilascio** nella barra dei menu superiore.

   ![Proprietà del certificato](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Nella schermata **Criterio di rilascio** aggiornare i valori seguenti:

   - **Periodo di validità**: aggiornare il valore (in mesi).
   - **Tipo di azione relativa alla durata**: selezionare l'azione di rinnovo automatico e di avviso del certificato e quindi aggiornare **Percentuale della durata** o **Numero di giorni prima della scadenza**.

   ![Proprietà del certificato](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Selezionare **Salva**.

> [!IMPORTANT]
> La modifica del tipo di azione relativa alla durata per un certificato registrerà immediatamente le modifiche per i certificati esistenti.


### <a name="update-certificate-attributes-by-using-powershell"></a>Aggiornare gli attributi del certificato con PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Per modificare i criteri di rinnovo per un elenco di certificati immettere `File.csv`, che conterrà `VaultName,CertName` come nell'esempio seguente:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Per altre informazioni sui parametri, vedere [az keyvault certificate](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Pulire le risorse

Altre esercitazioni su Key Vault si basano su questa esercitazione. Se si prevede di usare tali esercitazioni, potrebbe essere opportuno mantenere le risorse esistenti.
Quando non sono più necessarie, eliminare il gruppo di risorse per eliminare l'insieme di credenziali delle chiavi e le risorse correlate.

Per eliminare il gruppo di risorse con il portale:

1. Immettere il nome del gruppo di risorse nella casella **Cerca** nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questa guida di avvio rapido, selezionarlo.
1. Selezionare **Elimina gruppo di risorse**.
1. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si sono aggiornati gli attributi del ciclo di vita di un certificato. Per altre informazioni su Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti:

- Vedere altre informazioni sulla [gestione della creazione di certificati in Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios).
- Vedere la [panoramica di Key Vault](../general/overview.md).
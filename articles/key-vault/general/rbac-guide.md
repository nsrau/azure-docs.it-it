---
title: Concedere l'autorizzazione alle applicazioni per accedere a un insieme di credenziali delle chiavi di Azure usando RBAC di Azure | Microsoft Docs
description: Informazioni su come fornire l'accesso a chiavi, segreti e certificati usando il controllo degli accessi in base al ruolo di Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 4f943b11830c19ebb69dd501827deb158cecadf0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336752"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>Fornire l'accesso a chiavi Key Vault, certificati e segreti con un controllo degli accessi in base al ruolo di Azure (anteprima)

> [!NOTE]
> Il **provider di risorse** Key Vault supporta due tipi di risorse: insiemi di credenziali e **HSM gestiti**. Il **controllo di accesso**descritto in questo articolo si applica solo agli insiemi di credenziali. Per altre informazioni sul controllo di accesso per il modulo di protezione hardware gestito, vedere [controllo di accesso HSM gestito](../managed-hsm/access-control.md).

Il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) è un sistema di autorizzazione basato su [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) che offre una gestione degli accessi con granularità fine delle risorse di Azure.

Controllo degli accessi in base al ruolo di Azure consente agli utenti di gestire le autorizzazioni chiave, segreti e certificati Fornisce un'unica posizione per gestire tutte le autorizzazioni in tutti gli insiemi di credenziali delle chiavi. 

Il modello di controllo degli accessi in base al ruolo di Azure offre la possibilità di impostare autorizzazioni su diversi livelli di ambito: gruppo di gestione, sottoscrizione, gruppo di risorse o singole risorse.  Il controllo degli accessi in base al ruolo di Azure per Key Vault consente inoltre di disporre di autorizzazioni separate per chiavi, segreti e certificati singoli

Per altre informazioni, vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Procedure consigliate per chiavi, segreti e certificati singoli

Si consiglia di usare un insieme di credenziali per ogni applicazione per ambiente (sviluppo, pre-produzione e produzione).

Le autorizzazioni per chiavi, segreti e certificati singoli devono essere usate solo per scenari specifici:

-   Applicazioni a più livelli che devono separare il controllo degli accessi tra livelli

-   Insieme di credenziali delle chiavi condiviso con segreti comuni, quando le applicazioni devono accedere a subset di segreti nell'insieme di credenziali delle chiavi

Per ulteriori informazioni sulle linee guida per la gestione di Azure Key Vault, vedere:

- [Procedure consigliate per Azure Key Vault](best-practices.md)
- [Limiti del servizio Azure Key Vault](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>Ruoli predefiniti di Azure per Key Vault operazioni del piano dati (anteprima)
> [!NOTE]
> `Key Vault Contributor` il ruolo è per le operazioni del piano di gestione per gestire gli insiemi di credenziali delle chiavi. Non consente l'accesso a chiavi, segreti e certificati.

| Ruolo predefinito | Descrizione | ID |
| --- | --- | --- |
| Amministratore Key Vault (anteprima) | Eseguire tutte le operazioni del piano dati in un insieme di credenziali delle chiavi e in tutti gli oggetti al suo interno, inclusi i certificati, le chiavi e i segreti. Non è possibile gestire le risorse di Key Vault o gestire le assegnazioni di ruolo. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Ufficiale certificati Key Vault (anteprima) | Eseguire qualsiasi azione sui certificati di un insieme di credenziali delle chiavi, eccetto le autorizzazioni Manage. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto Officer (anteprima)| Eseguire qualsiasi azione sulle chiavi di un insieme di credenziali delle chiavi, eccetto le autorizzazioni Manage. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Crittografia del servizio di crittografia Key Vault (anteprima) | Legge i metadati delle chiavi ed esegue operazioni di wrapping/Unwrap. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Utente di crittografia Key Vault (anteprima) | Eseguire operazioni di crittografia usando chiavi. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Lettore di Key Vault (anteprima)| Leggere i metadati di insiemi di credenziali delle chiavi e i relativi certificati, chiavi e segreti. Non è possibile leggere i valori sensibili come il contenuto del segreto o il materiale della chiave. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Amministratore di Key Vault Secrets (anteprima)| Eseguire qualsiasi azione sui segreti di un insieme di credenziali delle chiavi, eccetto le autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | b86a8fe4-44CE-4948-aee5-eccb2c155cd7 |
| Utente di Key Vault Secrets (anteprima)| Leggere il contenuto del segreto. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "controllo degli accessi in base al ruolo di Azure". | 4633458b-17de-408a-b874-0445c86b69e6 |

Per altre informazioni sulle definizioni dei ruoli predefiniti di Azure, vedere [ruoli predefiniti di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Uso delle autorizzazioni di segreto, chiave e certificato RBAC di Azure con Key Vault

Il nuovo modello di autorizzazione RBAC di Azure per Key Vault fornisce un'alternativa al modello di autorizzazioni per i criteri di accesso all'insieme di credenziali. 

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Abilitare le autorizzazioni RBAC di Azure per Key Vault

> [!IMPORTANT]
> L'impostazione del modello di autorizzazione RBAC di Azure invalida tutte le autorizzazioni per i criteri di accesso. Può causare interruzioni quando i ruoli di Azure equivalenti non vengono assegnati.

1.  Abilitare le autorizzazioni RBAC di Azure per il nuovo insieme di credenziali delle chiavi:

    ![Abilitare le autorizzazioni RBAC-nuovo insieme di credenziali](../media/rbac/image-1.png)

2.  Abilitare le autorizzazioni RBAC di Azure per l'insieme di credenziali delle chiavi esistente:

    ![Abilitare le autorizzazioni RBAC-insieme di credenziali esistente](../media/rbac/image-2.png)

### <a name="assign-role"></a>Assegnare il ruolo

> [!Note]
> È consigliabile utilizzare l'ID del ruolo univoco anziché il nome del ruolo negli script. Di conseguenza, se un ruolo viene rinominato, gli script continueranno a funzionare. Durante l'anteprima ogni ruolo avrebbe il suffisso "(anteprima)", che verrebbe rimosso in un secondo momento. In questo documento il nome del ruolo viene utilizzato solo per migliorare la leggibilità.

Comando dell'interfaccia della riga di comando di Azure per creare un'assegnazione di ruolo:

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

Nella portale di Azure la schermata assegnazioni di ruolo di Azure è disponibile per tutte le risorse nella scheda controllo di accesso (IAM).

![Assegnazione ruolo-scheda (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Assegnazione di ruolo ambito gruppo di risorse

1.  Passare al gruppo di risorse Key Vault.
    ![Assegnazione di ruolo: gruppo di risorse](../media/rbac/image-4.png)

2.  Fare clic su controllo di accesso (IAM) Aggiungi \> assegnazione ruolo \> Aggiungi

3.  Crea il ruolo di lettore Key Vault "Key Vault Reader (anteprima)" per l'utente corrente

    ![Aggiungi ruolo-gruppo di risorse](../media/rbac/image-5.png)

Interfaccia della riga di comando di Azure:
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

L'assegnazione di ruolo precedente consente di elencare gli oggetti dell'insieme di credenziali delle chiavi in Key Vault.

### <a name="key-vault-scope-role-assignment"></a>Assegnazione ruolo ambito Key Vault

1. Vai alla \> scheda controllo di accesso key Vault (IAM)

2. Fare clic su Add-Role Assignment \> Add

3. Creare il ruolo ufficiale dei segreti chiave "Key Vault Office Secrets (anteprima)" per l'utente corrente.

    ![Assegnazione di ruolo-Key Vault](../media/rbac/image-6.png)

 Interfaccia della riga di comando di Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Dopo aver creato l'assegnazione di ruolo, è possibile creare/aggiornare/eliminare i segreti.

4. Creazione di un nuovo segreto (Secrets \> + generate/Import) per il test dell'assegnazione di ruolo a livello di segreto.

    ![Aggiungere un insieme di credenziali delle chiavi del ruolo](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Assegnazione di ruolo per ambito segreto

1. Apri uno dei segreti creati in precedenza, panoramica degli avvisi e controllo di accesso (IAM) (anteprima)

2. Fare clic sulla scheda controllo di accesso (IAM) (anteprima)

    ![Assegnazione di ruolo-segreto](../media/rbac/image-8.png)

3. Creare il ruolo ufficiale dei segreti chiave "Key Vault Office Secrets (anteprima)" per l'utente corrente, come è stato fatto in precedenza per l'Key Vault.

Interfaccia della riga di comando di Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>Test e verifica

> [!NOTE]
> I browser usano la memorizzazione nella cache e l'aggiornamento della pagina è necessario dopo la rimozione delle assegnazioni di ruolo.<br>
> Consenti l'aggiornamento delle assegnazioni di ruolo per diversi minuti

1. Convalidare l'aggiunta di un nuovo segreto senza il ruolo "Key Vault Secret Officer" sul livello di Key Vault.

Passare alla scheda controllo di accesso dell'insieme di credenziali delle chiavi e rimuovere l'assegnazione di ruolo "Key Vault Office Secrets (anteprima)" per questa risorsa.

![Rimuovi assegnazione-insieme di credenziali delle chiavi](../media/rbac/image-9.png)

Passare a Secret creato in precedenza. È possibile visualizzare tutte le proprietà segrete.

![Visualizzazione privata con accesso](../media/rbac/image-10.png)

Crea nuovo segreto (segreti \> + genera/importa) dovrebbe mostrare l'errore seguente:

   ![Crea nuovo segreto](../media/rbac/image-11.png)

2.  Convalidare la modifica segreta senza il ruolo "Key Vault Secret ufficiale" a livello di segreto.

-   Passare alla scheda relativa al controllo di accesso segreto (IAM) creato in precedenza e rimuovere l'assegnazione di ruolo "Key Vault Office Secrets (anteprima)" per questa risorsa.

-   Passare a Secret creato in precedenza. È possibile visualizzare le proprietà segrete.

   ![Visualizzazione segreta senza accesso](../media/rbac/image-12.png)

3. Convalidare i segreti letti senza ruolo lettore sul livello di Key Vault.

-   Passare alla scheda controllo di accesso del gruppo di risorse dell'insieme di credenziali delle chiavi e rimuovere l'assegnazione di ruolo "Key Vault Reader (Preview)".

-   Se si passa alla scheda segreti dell'insieme di credenziali delle chiavi, verrà visualizzato l'errore seguente:

   ![Scheda Secret-errore](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Creazione di ruoli personalizzati 

[comando AZ Role Definition create](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create)

**(Script bash dell'interfaccia della riga di comando)</br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

Per ulteriori informazioni su come creare ruoli personalizzati, vedere:

[Ruoli personalizzati di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

## <a name="known-limits-and-performance"></a>Limiti e prestazioni noti

-   2000 assegnazioni di ruolo di Azure per sottoscrizione

-   Latenza assegnazioni di ruolo: con le prestazioni previste correnti, saranno necessari fino a 10 minuti (600 secondi) dopo la modifica delle assegnazioni di ruolo per l'applicazione del ruolo

## <a name="learn-more"></a>Altre informazioni

- [Panoramica di Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Esercitazione sui ruoli personalizzati](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)

---
title: Proteggere l'accesso a un modulo di protezione hardware gestito - Modulo di protezione hardware gestito di Azure Key Vault
description: Informazioni su come proteggere l'accesso a un modulo di protezione hardware gestito con il controllo degli accessi in base al ruolo di Azure e il controllo degli accessi in base al ruolo del modulo di protezione hardware gestito locale
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 99918d039052c9913400b85ac3caa4a1a5481155
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445320"
---
# <a name="secure-access-to-your-managed-hsms"></a>Proteggere l'accesso ai moduli di protezione hardware gestiti

Il modulo di protezione hardware gestito di Azure Key Vault è un servizio cloud che consente di proteggere le chiavi di crittografia. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere i moduli di protezione hardware gestiti consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo articolo offre una panoramica del modello di controllo di accesso del modulo di protezione hardware gestito. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso ai moduli di protezione hardware gestiti.

Questa esercitazione illustra un semplice esempio che mostra come ottenere la separazione dei compiti e il controllo degli accessi usando il controllo degli accessi in base al ruolo di Azure e il controllo degli accessi in base al ruolo del modulo di protezione hardware gestito locale. Per informazioni sul modello di controllo di accesso del modulo di protezione hardware gestito, vedere [Controllo di accesso del modulo di protezione hardware gestito](access-control.md).

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interfaccia della riga di comando di Azure versione 2.12.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).
* Un modulo di protezione hardware gestito nella sottoscrizione. Vedere [Avvio rapido: Effettuare il provisioning di un modulo di protezione hardware gestito e attivarlo tramite l'interfaccia della riga di comando di Azure](quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure usando l'interfaccia della riga di comando è possibile digitare:

```azurecli
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="example"></a>Esempio

In questo esempio viene sviluppata un'applicazione che usa una chiave RSA a 2048 bit per le operazioni di firma. L'applicazione viene eseguita in una macchina virtuale di Azure con un'[identità gestita](../../active-directory/managed-identities-azure-resources/overview.md). La chiave RSA usata per la firma è archiviata nel modulo di protezione hardware gestito.

Sono stati identificati i ruoli seguenti per specificare chi può gestire, distribuire e controllare l'applicazione:

- **Team responsabile della sicurezza**: personale IT che lavora nell'ufficio del responsabile della sicurezza o collaboratori analoghi. Il team responsabile della sicurezza si occupa della protezione delle chiavi. Chiavi RSA o EC per la firma e chiavi RSA o AES per la crittografia dei dati.
- **Sviluppatori e operatori**: il personale che sviluppa l'applicazione e la distribuisce in Azure. I membri di questo team non fanno parte del personale responsabile della sicurezza. Non devono avere accesso ai dati sensibili, come le chiavi RSA. Solo l'applicazione da loro distribuita deve avere accesso a questi dati sensibili.
- **Revisori**: questo ruolo è per i collaboratori che non sono membri del personale IT generico o di sviluppo. Queste persone verificano l'uso e la gestione di certificati, chiavi e segreti per garantire la conformità agli standard di sicurezza.

C'è un altro ruolo che non rientra nell'ambito dell'applicazione: l'amministratore della sottoscrizione (o del gruppo di risorse). L'amministratore della sottoscrizione configura le autorizzazioni di accesso iniziali per il team responsabile della sicurezza. L'accesso al team responsabile della sicurezza viene concesso tramite un gruppo di risorse che contiene le risorse necessarie per l'applicazione.

È necessario autorizzare le operazioni seguenti per i ruoli:

**Team responsabile della sicurezza**
- Creare il modulo di protezione hardware gestito.
- Scaricare il dominio di sicurezza del modulo di protezione hardware gestito (per il ripristino di emergenza)
- Abilitare la registrazione.
- Generare o importare le chiavi
- Creare i backup del modulo di protezione hardware gestito per il ripristino di emergenza.
- Impostare il controllo degli accessi in base al ruolo locale del modulo di protezione hardware gestito per concedere a utenti e applicazioni le autorizzazioni per eseguire operazioni specifiche.
- Aggiornare periodicamente le chiavi.

**Sviluppatori e operatori**
- Ottenere dal team di sicurezza un riferimento (URI della chiave) usato dalla chiave RSA per la firma.
- Sviluppare e distribuire l'applicazione che accede alla chiave a livello di codice.

**Revisori**
- Esaminare le date di scadenza delle chiavi per verificare che siano aggiornate
- Monitorare le assegnazioni di ruolo per garantire che le chiavi siano accessibili solo a utenti o applicazioni autorizzate
- Esaminare i log del modulo di protezione hardware gestito per verificare che le chiavi siano usate in modo appropriato e conformemente agli standard di sicurezza dei dati.

La tabella seguente riepiloga le assegnazioni di ruolo per i team e le risorse che accedono al modulo di protezione hardware gestito.

| Ruolo | Ruolo del piano di gestione | Ruolo del piano dati |
| --- | --- | --- |
| Team responsabile della sicurezza | Managed HSM Contributor | Managed HSM Administrator |
| Sviluppatori e operatori | nessuno | nessuno |
| Revisori | nessuno | Managed HSM Crypto Auditor |
| Identità gestita della macchina virtuale usata dall'applicazione| nessuno | Managed HSM Crypto User |
| Identità gestita dell'account di archiviazione usato dall'applicazione| nessuno| Managed HSM Service Encryption |

Oltre alle autorizzazioni per il modulo di protezione hardware gestito, i tre i ruoli dei team devono poter accedere ad altre risorse. Per distribuire le macchine virtuali (o la funzionalità App Web di Servizio app di Azure), sviluppatori e operatori necessitano dell'accesso `Contributor` a tali tipi di risorse. I revisori necessitano dell'accesso in lettura all'account di archiviazione in cui vengono archiviati i log del modulo di protezione hardware gestito.

Per assegnare ruoli del piano di gestione (controllo degli accessi in base al ruolo di Azure), è possibile usare il portale di Azure o qualsiasi altra interfaccia di gestione, ad esempio l'interfaccia della riga di comando di Azure o Azure PowerShell. Per assegnare i ruoli del piano dati del modulo di protezione hardware gestito, è necessario usare l'interfaccia della riga di comando di Azure.

I frammenti di codice dell'interfaccia della riga di comando di Azure in questa sezione vengono creati in base ai presupposti seguenti:

- L'amministratore di Azure Active Directory ha creato i gruppi di sicurezza per rappresentare i tre ruoli: Contoso Security Team, Contoso App DevOps e Contoso App Auditors. L'amministratore ha aggiunto gli utenti ai rispettivi gruppi.
- Tutte le risorse si trovano nel gruppo di risorse **ContosoAppRG**.
- I log del modulo di protezione hardware gestito sono archiviati nell'account di archiviazione **contosologstorage**.
- Il modulo di protezione hardware gestito **ContosoKeyVault** e l'account di archiviazione **contosologstorage** si trovano nella stessa località di Azure.

L'amministratore della sottoscrizione assegna il ruolo `Managed HSM Contributor` al team responsabile della sicurezza. Questo ruolo consente al team responsabile della sicurezza di gestire i moduli di protezione hardware gestiti esistenti e crearne di nuovi. Se sono presenti moduli di protezione hardware gestiti esistenti, per poterli gestire sarà necessario assegnare il ruolo "Managed HSM Administrator".

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

Il team responsabile configura la registrazione e assegna i ruoli ai revisori e all'applicazione per le macchine virtuali.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

Questa esercitazione mostra solo le azioni rilevanti per il controllo di accesso nella maggior parte dei casi. Altre azioni e operazioni correlate alla distribuzione di applicazioni nella macchina virtuale, all'attivazione della crittografia con la chiave gestita dal cliente per un account di archiviazione, alla creazione di moduli di protezione hardware gestiti non vengono trattate in questa sede allo scopo di incentrare l'esempio sul controllo di accesso e sulla gestione dei ruoli.

Questo esempio illustra uno scenario semplice. Gli scenari reali possono essere più complessi. È possibile modificare le autorizzazioni per l'insieme di credenziali delle chiavi in base alle esigenze. In questo esempio si presuppone che il team responsabile della sicurezza fornisca i riferimenti a chiavi e segreti (URI e identificazioni personali) usati dal personale DevOps nelle applicazioni. Sviluppatori e operatori non necessitano di alcun accesso al piano dati. In questo articolo è stato analizzato in particolare come proteggere l'insieme di credenziali delle chiavi. Tenere presenti considerazioni simili per la protezione di [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/security/), [account di archiviazione](../../storage/blobs/security-recommendations.md) e altre risorse di Azure.

## <a name="resources"></a>Risorse

- [Documentazione del controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)
- [Controllo degli accessi in base al ruolo Azure: ruoli predefiniti](../../role-based-access-control/built-in-roles.md)
- [Gestire il controllo degli accessi in base al ruolo di Azure con l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione introduttiva per gli amministratori, vedere [Che cos'è il modulo di protezione hardware gestito?](overview.md).

Per altre informazioni sulla registrazione dell'utilizzo per il modulo di protezione hardware gestito, vedere [Registrazione del modulo di protezione hardware gestito](logging.md).

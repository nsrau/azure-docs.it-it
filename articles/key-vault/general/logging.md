---
title: Registrazione di Azure Key Vault | Microsoft Docs
description: Informazioni su come monitorare l'accesso agli insiemi di credenziali delle chiavi mediante l'abilitazione della registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure specificato.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: eef4f6b8ee5821e54b5b7709eee7f8dad8749e63
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488537"
---
# <a name="azure-key-vault-logging"></a>Registrazione di Azure Key Vault

Dopo aver creato una o più insiemi di credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. A questo scopo è possibile abilitare la registrazione per Azure Key Vault, che salva le informazioni in un account di archiviazione di Azure specificato. Per le istruzioni dettagliate sulla configurazione, vedere [Come abilitare la registrazione di Key Vault](howto-logging.md).

È possibile accedere alle informazioni di registrazione dopo massimo 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, ma nella maggior parte dei casi si potrà farlo prima.  La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Per informazioni generali su Key Vault, vedere [Cos'è Azure Key Vault?](overview.md). Per informazioni sulla disponibilità di Key Vault, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/key-vault/). Per informazioni sull'uso, vedere [Monitoraggio di Azure per Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Interpretare i log dell'insieme di credenziali delle chiavi

Quando si abilita la registrazione, per l'account di archiviazione specificato viene automaticamente creato un nuovo contenitore denominato **insights-logs-auditevent**. che può essere usato per raccogliere i log relativi a più insiemi di credenziali delle chiavi.

I singoli BLOB vengono archiviati come testo, formattati come BLOB JSON. Ecco un esempio di voce di registro. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

La tabella seguente elenca i nomi dei campi e le descrizioni:

| Nome campo | Descrizione |
| --- | --- |
| **time** |Data e ora in formato UTC. |
| **resourceId** |ID della risorsa di Azure Resource Manager. Per i log dell'insieme di credenziali delle chiavi questo è sempre l'ID della risorsa insieme di credenziali delle chiavi. |
| **operationName** |Nome dell'operazione, come illustrato nella tabella seguente. |
| **operationVersion** |Versione dell'API REST richiesta dal client. |
| **category** |Tipo di risultato. Per i log di Key Vault, `AuditEvent` è il solo valore disponibile. |
| **resultType** |Risultato della richiesta API REST. |
| **resultSignature** |Stato HTTP. |
| **resultDescription** |Descrizione aggiuntiva del risultato, se disponibile. |
| **durationMs** |Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore. |
| **callerIpAddress** |Indirizzo IP del client che ha eseguito la richiesta. |
| **correlationId** |GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi). |
| **identity** |Identità del token presentato nella richiesta API REST. In genere si tratta di un "utente", una "entità servizio" o una combinazione "utente+appId", come nel caso di una richiesta generata da un cmdlet di Azure PowerShell. |
| **properties** |Informazioni diverse in base all'operazione ( **operationName** ). Nella maggior parte dei casi, questo campo contiene informazioni sul client (la stringa agente dell'utente passata dal client), l'URI esatto della richiesta dell'API REST e il codice di stato HTTP. Inoltre, quando un oggetto viene restituito come risultato di una richiesta, ad esempio **KeyCreate** o **VaultGet** , conterrà anche l'URI della chiave (come `id`), l'URI dell'insieme di credenziali o l'URI del segreto. |

I valori del campo **operationName** sono nel formato *OggettoVerbo*. Ad esempio:

* Tutte le operazioni sull'insieme di credenziali delle chiavi hanno il formato `Vault<action>`, ad esempio `VaultGet` e `VaultCreate`.
* Tutte le operazioni sulle chiavi hanno il formato `Key<action>`, ad esempio `KeySign` e `KeyList`.
* Tutte le operazioni sui segreti hanno il formato `Secret<action>`, ad esempio `SecretGet` e `SecretListVersions`.

La tabella seguente include un elenco di valori **operationName** con il comando API REST corrispondente:

### <a name="operation-names-table"></a>Tabella di nomi di operazioni

| operationName | Comando API REST |
| --- | --- |
| **autenticazione** |Eseguire l'autenticazione tramite l'endpoint di Azure Active Directory |
| **VaultGet** |[Ottenere informazioni su un insieme di credenziali delle chiavi](/rest/api/keyvault/vaults) |
| **VaultPut** |[Creare o aggiornare un insieme di credenziali delle chiavi](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Eliminare un insieme di credenziali delle chiavi](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Aggiornare un insieme di credenziali delle chiavi](/rest/api/keyvault/vaults) |
| **VaultList** |[Elencare tutti gli insiemi di credenziali delle chiavi in un gruppo di risorse](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Rimuovere definitivamente l'insieme di credenziali eliminato](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Recuperare l'insieme di credenziali eliminato|
| **VaultGetDeleted** |[Ottenere l'insieme di credenziali eliminato](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Elencare gli insiemi di credenziali eliminati](/rest/api/keyvault/vaults/listdeleted) |
| **KeyCreate** |[Creare una chiave](/rest/api/keyvault/createkey) |
| **KeyGet** |[Ottenere informazioni su una chiave](/rest/api/keyvault/getkey) |
| **KeyImport** |[Importare una chiave in un insieme di credenziali](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Eliminare una chiave](/rest/api/keyvault/deletekey) |
| **KeySign** |[Firmare con una chiave](/rest/api/keyvault/sign) |
| **KeyVerify** |[Verificare con una chiave](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Eseguire il wrapping di una chiave](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Annullare il wrapping di una chiave](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Crittografare con una chiave](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Decrittografare con una chiave](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Aggiornare una chiave](/rest/api/keyvault/updatekey) |
| **KeyList** |[Elencare le chiavi in un insieme di credenziali](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[Elencare le versioni di una chiave](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[Rimuovere definitivamente una chiave](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Eseguire il backup di una chiave](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Ripristinare una chiave](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[Recuperare una chiave](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Ottenere la chiave eliminata](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[Elencare le chiavi eliminate in un insieme di credenziali](/rest/api/keyvault/getdeletedkeys) |
| **CertificateGet** |[Ottenere informazioni su un certificato](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[Creare un certificato](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[Importare un certificato in un insieme di credenziali](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Aggiornare un certificato](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[Elencare i certificati in un insieme di credenziali](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[Elencare le versioni di un certificato](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Eliminare un certificato](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[Rimuovere definitivamente un certificato](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Eseguire il backup di un certificato](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[Ripristinare un certificato](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[Recuperare un certificato](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Ottenere un certificato eliminato](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[Elencare i certificati eliminati in un insieme di credenziali](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Ottenere i criteri dei certificati](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Aggiornare i criteri dei certificati](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Creare criteri dei certificati](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Ottenere i contatti relativi al certificato](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Impostare i contatti relativi al certificato](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Eliminare i contatti relativi al certificato](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Ottenere l'autorità di certificazione](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Impostare l'autorità di certificazione](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Aggiornare l'autorità di certificazione](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Eliminare l'autorità di certificazione](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[Elencare le autorità di certificazione](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |Registrare un certificato |
| **CertificateRenew** |Rinnovare un certificato |
| **CertificatePendingGet** |Recuperare un certificato in sospeso |
| **CertificatePendingMerge** |Impostare un merge di certificati come in sospeso |
| **CertificatePendingUpdate** |Impostare un aggiornamento di certificati come in sospeso |
| **CertificatePendingDelete** |Eliminare un certificato in sospeso |
| **SecretSet** |[Creare un segreto](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Ottenere un segreto](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Aggiornare un segreto](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Eliminare un segreto](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Elencare i segreti in un insieme di credenziali](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Elencare le versioni di un segreto](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Rimuovere definitivamente un segreto](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Eseguire il backup di un segreto](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Ripristinare un segreto](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Recuperare un segreto](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Ottenere il segreto eliminato](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Elencare i segreti eliminati in un insieme di credenziali](/rest/api/keyvault/getdeletedsecrets) |
| **VaultAccessPolicyChangedEventGridNotification** | Evento di modifica dei criteri di accesso all'insieme di credenziali pubblicato |
| **SecretNearExpiryEventGridNotification** |Evento di segreto vicino alla scadenza pubblicato |
| **SecretExpiredEventGridNotification** |Evento di segreto scaduto pubblicato |
| **KeyNearExpiryEventGridNotification** |Evento di chiave vicina alla scadenza pubblicato |
| **KeyExpiredEventGridNotification** |Evento di chiave scaduta pubblicato |
| **CertificateNearExpiryEventGridNotification** |Evento di certificato vicino alla scadenza pubblicato |
| **CertificateExpiredEventGridNotification** |Evento di certificato scaduto pubblicato |

## <a name="use-azure-monitor-logs"></a>Usare i log di Monitoraggio di Azure

È possibile usare la soluzione Key Vault nei log di Monitoraggio di Azure per esaminare i log `AuditEvent` di Key Vault. Nei log di Monitoraggio di Azure è possibile usare le query di log per analizzare i dati e ottenere le informazioni necessarie. 

Per altre informazioni, anche su come configurare la soluzione, vedere [Azure Key Vault nei log di Monitoraggio di Azure](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- [Come abilitare la registrazione di Key Vault](howto-logging.md)
- Per un'esercitazione sull'uso di Azure Key Vault in un'applicazione Web .NET, vedere [Usare Azure Key Vault da un'app Web](tutorial-net-create-vault-azure-web-app.md).
- Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell'insieme di credenziali chiave Azure](developers-guide.md).
- Per un elenco di cmdlet di Azure PowerShell 1.0 per Azure Key Vault, vedere [Cmdlet per Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
